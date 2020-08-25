---
title: 部署使用 GPU 推斷的模型
titleSuffix: Azure Machine Learning
description: 本文將指導您如何使用 Azure Machine Learning 將啟用 GPU 的 Tensorflow 深度學習模型部署為 web 服務。服務和分數推斷要求。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e4c2426d5248582a1255b9d3702bdb1e6d046936
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751651"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>部署深度學習模型以使用 GPU 推斷
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文會教您如何使用 Azure Machine Learning 將已啟用 GPU 功能的模型部署為 web 服務。 本文中的資訊是以 Azure Kubernetes Service (AKS) 上部署模型為基礎。 AKS 叢集會提供用來推斷模型的 GPU 資源。

推斷（或模型計分）是用來進行預測的已部署模型階段。 使用 Gpu 而非 Cpu 可提供高度可平行計算的效能優勢。

> [!IMPORTANT]
> 針對 web 服務部署，僅 Azure Kubernetes Service 支援 GPU 推斷。 針對使用 __機器學習管線__的推斷，只有 Azure Machine Learning 計算才支援 gpu。 如需使用 ML 管線的詳細資訊，請參閱 [執行批次預測](how-to-use-parallel-run-step.md)。 

> [!TIP]
> 雖然本文中的程式碼片段使用 TensorFlow 模型，但您可以將資訊套用至任何支援 Gpu 的機器學習架構。

> [!NOTE]
> 本文中的資訊是以 [如何部署至 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) 一文中的資訊為基礎。 本文通常涵蓋部署至 AKS 的內容，本文涵蓋 GPU 特定部署。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* 已安裝 Azure Machine Learning SDK 的 Python 開發環境。 如需詳細資訊，請參閱 [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。  

* 使用 GPU 的已註冊模型。

    * 若要瞭解如何註冊模型，請參閱 [部署模型](how-to-deploy-and-where.md#registermodel)。

    * 若要建立及註冊用來建立這份檔的 Tensorflow 模型，請參閱 [如何定型 Tensorflow 模型](how-to-train-tensorflow.md)。

* 一般瞭解 [部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="connect-to-your-workspace"></a>連線到您的工作區

若要連接到現有的工作區，請使用下列程式碼：

> [!IMPORTANT]
> 此程式碼片段預期工作區組態會儲存在目前目錄或其父系目錄中。 如需建立工作區的詳細資訊，請參閱[建立和管理 Azure Machine Learning 工作區](how-to-manage-workspace.md)。   如需將組態儲存至檔案的詳細資訊，請參閱[建立工作區組態檔](how-to-configure-environment.md#workspace)。

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>使用 Gpu 建立 Kubernetes 叢集

Azure Kubernetes Service 提供許多不同的 GPU 選項。 您可以使用任何一種來進行模型推斷。 如需完整的功能和成本明細，請參閱 [N 系列 vm 的清單](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) 。

下列程式碼示範如何為您的工作區建立新的 AKS 叢集：

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> 只要 AKS 叢集存在，Azure 就會向您收取費用。 完成時，請務必刪除您的 AKS 叢集。

如需有關搭配 Azure Machine Learning 使用 AKS 的詳細資訊，請參閱 [如何部署至 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)。

## <a name="write-the-entry-script"></a>撰寫輸入腳本

專案腳本會接收提交至 web 服務的資料，並將其傳遞至模型，並傳回評分結果。 下列腳本會在啟動時載入 Tensorflow 模型，然後使用模型來評分資料。

> [!TIP]
> 這是模型特定的輸入指令碼。 例如，腳本必須知道要搭配您的模型、資料格式等使用的架構。

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

這個檔案的名稱為 `score.py` 。 如需有關輸入腳本的詳細資訊，請參閱 [如何和部署的位置](how-to-deploy-and-where.md)。

## <a name="define-the-conda-environment"></a>定義 conda 環境

Conda 環境檔案會指定服務的相依性。 它包含模型和專案腳本所需的相依性。 請注意，您必須以版本 >= 1.0.45 版 azureml-defaults 表示 azureml 預設值，因為它包含將模型裝載為 web 服務所需的功能。 下列 YAML 會定義 Tensorflow 模型的環境。 它 `tensorflow-gpu` 會指定，以利用此部署中使用的 GPU：

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
  - numpy
  - tensorflow-gpu=1.12
channels:
- conda-forge
```

在此範例中，檔案會另存為 `myenv.yml` 。

## <a name="define-the-deployment-configuration"></a>定義部署設定

> [!IMPORTANT]
> AKS 不允許 pod 共用 Gpu，您只能有已啟用 GPU 之 web 服務的複本數目，如同叢集中的 Gpu 一樣。

部署設定會定義用來執行 web 服務的 Azure Kubernetes Service 環境：

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

如需詳細資訊，請參閱 AksService 的參考檔 [。 deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)。

## <a name="define-the-inference-configuration"></a>定義推斷設定

推斷設定會指向進入腳本和環境物件，該物件會使用具有 GPU 支援的 docker 映射。 請注意，用於環境定義的 YAML 檔案必須列出 azureml-預設值，且版本 >= 1.0.45 版 azureml-defaults 為 pip 相依性，因為它包含將模型裝載為 web 服務所需的功能。

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

如需環境的詳細資訊，請參閱 [建立和管理用於定型和部署的環境](how-to-use-environments.md)。
如需詳細資訊，請參閱 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)的參考檔。

## <a name="deploy-the-model"></a>部署模型

將模型部署到您的 AKS 叢集，並等候它建立您的服務。

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

如需詳細資訊，請參閱 [模型](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)的參考檔集。

## <a name="issue-a-sample-query-to-your-service"></a>將範例查詢發出至您的服務

將測試查詢傳送至已部署的模型。 當您將 jpeg 影像傳送至模型時，它會對影像進行評分。 下列程式碼範例會下載測試資料，然後選取要傳送至服務的隨機測試影像。

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

如需建立用戶端應用程式的詳細資訊，請參閱 [建立用戶端以使用已部署的 web 服務](how-to-consume-web-service.md)。

## <a name="clean-up-the-resources"></a>清除資源

如果您已特別針對此範例建立 AKS 叢集，請在完成後刪除您的資源。

> [!IMPORTANT]
> Azure 會根據 AKS 叢集的部署時間進行計費。 完成之後，請務必將其清除。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>後續步驟

* [在 FPGA 上部署模型](how-to-deploy-fpga-web-service.md)
* [使用 ONNX 部署模型](concept-onnx.md#deploy-onnx-models-in-azure)
* [定型 Tensorflow DNN 模型](how-to-train-tensorflow.md)
