---
title: 部署加密的推斷服務
titleSuffix: Azure Machine Learning
description: 了解如何使用 Microsoft SEAL 來部署適用於映像分類的加密預測服務
author: luisquintanilla
ms.author: luquinta
ms.date: 05/18/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: tracking-python
ms.openlocfilehash: b92293973ac9b5027a9f1a10c2d19fd164c41e3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84560190"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service"></a>如何部署加密的推斷 Web 服務

在 [Azure 容器執行個體](https://docs.microsoft.com/azure/container-instances/) (ACI) 中，了解如何部署映像分類模型做為加密的推斷 Web 服務。 Web 服務是一個 Docker 容器映像，其中包含模型和評分邏輯。

在本指南中，您會使用 Azure Machine Learning 服務來：

> [!div class="checklist"]
> * 設定您的環境
> * 部署加密的推斷 Web 服務
> * 準備測試資料
> * 進行加密的預測
> * 清除資源

ACI 是很適合用來測試及了解模型部署工作流程的解決方案。 如需可調整的生產環境部署，請考慮使用 Azure Kubernetes Service。 如需詳細資訊，請參閱[部署方式和位置](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)。

此範例中使用的加密方法是 [homomorphic 加密](https://github.com/Microsoft/SEAL#homomorphic-encryption)。 Homomorphic 加密可讓您在加密資料上進行計算，而不需要存取祕密 (解密) 金鑰。 計算的結果會進行加密，且只能藉由秘密金鑰的擁有者顯示。 

## <a name="prerequisites"></a>Prerequisites

本指南假設您已在 Azure Machine Learning 中註冊映像分類模型。 如果不是，請使用[預先定型的模型](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl)來註冊模型，或完成[使用 Azure Machine Learning 教學課程來定型映像分類模型](tutorial-train-models-with-aml.md)，以建立您自己的模型。

## <a name="configure-local-environment"></a>設定本機環境

在 Jupyter Notebook 中

1. 匯入此範例所需的 Python 套件。

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. 安裝適用於安全推斷的 homomorphic 加密程式庫。

    > [!NOTE]
    > `encrypted-inference` 套件目前為預覽狀態。

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference) 是一個程式庫，其中包含以 [Microsoft SEAL](https://github.com/Microsoft/SEAL) 為基礎的加密推斷繫結。

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>設定推斷環境

建立推斷的環境，並新增 `encrypted-inference` 套件做為 conda 相依性。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>部署加密的推斷 Web 服務

將模型部署為 ACI 中裝載的 Web 服務。

若要建置正確的 ACI 環境，請提供下列項目：

* 示範如何使用模型的評分指令碼
* 用於建置 ACI 的設定檔
* 定型的模型

### <a name="create-scoring-script"></a>建立評分指令碼

建立 Web 服務用來推斷的評分指令碼 `score.py`。

您必須在評分指令碼中包含兩個必要函式：

* `init()` 函式，通常會將模型載入全域物件。 此函式只會在 Docker 容器啟動時執行一次。
* `run(input_data)` 函式會使用模型依據輸入資料預測值。 run 的輸入和輸出通常會使用 JSON 進行序列化及還原序列化，但也支援其他格式。 此函式會擷取由服務呼叫者上傳的 homomorphic 加密型公開金鑰。

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>建立組態檔

建立部署設定檔，並指定您 ACI 容器所需要的 CPU 數量及 RAM GB 數。 雖然這取決於您的模型，但預設的 1 核心及 1 GB RAM 通常對許多模型來說便已足夠。 若您稍後需要更多，您需要重新建立映像並重新部署服務。

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>部署至 Azure 容器執行個體

預估完成時間：**2 到 5 分鐘**

設定映像並部署。 下列程式碼會執行這些步驟：

1. 使用環境檔案 (`myenv.yml`) 建立包含模型所需相依性的環境物件
1. 使用下列項目來建立將模型部署為 Web 服務時所需的推斷組態：
   * 評分檔案 (`score.py`)
   * 在上一個步驟中建立的環境物件
1. 將模型部署到 ACI 容器。
1. 取得 Web 服務 HTTP 端點。

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

取得評分 Web 服務的 HTTP 端點，該端點會接受 REST 用戶端呼叫。 此端點可和任何想要測試 Web 服務，或想要整合應用程式與服務的人共用。

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>準備測試資料

1. 下載測試資料。 在此情況下，其會儲存在名為「資料」的目錄中。

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. 從「資料」目錄載入測試資料。

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>進行加密的預測

使用具有模型的測試資料集來取得預測。

若要進行加密的預測：

1. 建立新的 `EILinearRegressionClient`、以 homomorphic 加密為基礎的用戶端和公開金鑰。

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. 將 homomorphic 加密產生的公開金鑰上傳至工作空間預設 Blob 存放區。 這可讓您與推斷伺服器共用金鑰。

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. 加密測試資料

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. 使用 SDK 的 `run` API 來叫用服務，並將測試資料集提供給模型以取得預測。 我們必須將連接字串傳送到已上傳公用金鑰的 Blob 儲存體。

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. 使用用戶端將結果解密。

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>清除資源

刪除此範例中建立的 Web 服務：

```python
service.delete()
```

如果您不再打算使用您所建立的 Azure 資源，請將其刪除。 這可防止您針對仍在執行中但未運用的資源付費。 如需深入了解，請參閱本指南的如何[清除資源](how-to-manage-workspace.md#clean-up-resources)。
