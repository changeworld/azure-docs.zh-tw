---
title: 使用 GPU 部署模型以進行推斷
titleSuffix: Azure Machine Learning
description: 本文會教您如何使用 Azure Machine Learning 將已啟用 GPU 的 Tensorflow 深度學習模型部署為 web 服務。服務和分數推斷要求。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: b0fd537d1930e7c9d5f7a33f56ec5d00b1556562
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78398342"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>使用 GPU 部署深度學習模型以進行推斷
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文會教您如何使用 Azure Machine Learning 將已啟用 GPU 的模型部署為 web 服務。 本文中的資訊是以 Azure Kubernetes Service （AKS）上部署模型為基礎。 AKS 叢集會提供一個 GPU 資源，供模型用來進行推斷。

推斷（或模型計分）是用來進行預測的已部署模型的階段。 使用 Gpu 而非 Cpu，可提供高度可平行計算的效能優勢。

> [!IMPORTANT]
> 針對 web 服務部署，只有 Azure Kubernetes Service 支援 GPU 推斷。 針對使用__機器學習管線__的推斷，只有 Azure Machine Learning 計算才支援 gpu。 如需使用 ML 管線的詳細資訊，請參閱[執行批次預測](how-to-use-parallel-run-step.md)。 

> [!TIP]
> 雖然本文中的程式碼片段會使用 TensorFlow 模型，但您可以將此資訊套用至任何支援 Gpu 的機器學習架構。

> [!NOTE]
> 本文中的資訊是[以如何部署至 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)一文中的資訊為基礎。 本文一般會涵蓋部署至 AKS，本文涵蓋 GPU 特定部署。

## <a name="prerequisites"></a>必要條件

* Azure Machine Learning 工作區。 如需詳細資訊，請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* 已安裝 Azure Machine Learning SDK 的 Python 開發環境。 如需詳細資訊，請參閱[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。  

* 使用 GPU 的已註冊模型。

    * 若要瞭解如何註冊模型，請參閱[部署模型](how-to-deploy-and-where.md#registermodel)。

    * 若要建立並註冊用來建立這份檔的 Tensorflow 模型，請參閱[如何訓練 Tensorflow 模型](how-to-train-tensorflow.md)。

* 大致瞭解[部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="connect-to-your-workspace"></a>連接到您的工作區

若要連接到現有的工作區，請使用下列程式碼：

> [!IMPORTANT]
> 此程式碼片段需要將工作區設定儲存在目前目錄或其父系。 如需建立工作區的詳細資訊，請參閱[建立和管理 Azure Machine Learning 工作區](how-to-manage-workspace.md)。   如需將設定儲存至檔案的詳細資訊，請參閱[建立工作區設定檔](how-to-configure-environment.md#workspace)。

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>建立具有 Gpu 的 Kubernetes 叢集

Azure Kubernetes Service 提供許多不同的 GPU 選項。 您可以使用其中任何一個來進行模型推斷。 如需功能和成本的完整細目，請參閱[N 系列 vm 的清單](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)。

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
> 只要 AKS 叢集存在，Azure 就會向您收取費用。 當您完成 AKS 叢集時，請務必將其刪除。

如需有關使用 AKS 搭配 Azure Machine Learning 的詳細資訊，請參閱[如何部署至 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)。

## <a name="write-the-entry-script"></a>撰寫專案腳本

輸入腳本會接收提交至 web 服務的資料，將其傳遞至模型，並傳回評分結果。 下列腳本會在啟動時載入 Tensorflow 模型，然後使用模型來對資料進行計分。

> [!TIP]
> 專案腳本是您的模型特有的。 例如，腳本必須知道要與您的模型、資料格式等搭配使用的架構。

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

此檔案的名稱為 `score.py`。 如需有關輸入腳本的詳細資訊，請參閱[如何和部署位置](how-to-deploy-and-where.md)。

## <a name="define-the-conda-environment"></a>定義 conda 環境

Conda 環境檔案會指定服務的相依性。 它包含模型和專案腳本所需的相依性。 請注意，您必須以 pip 相依性的形式來表示版本 > = 1.0.45 的 azureml 預設值，因為它包含裝載模型做為 web 服務所需的功能。 下列 YAML 會定義 Tensorflow 模型的環境。 它會指定 `tensorflow-gpu`，這會使用此部署中使用的 GPU：

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

在此範例中，檔案會儲存為 `myenv.yml`。

## <a name="define-the-deployment-configuration"></a>定義部署設定

部署設定會定義用來執行 web 服務的 Azure Kubernetes Service 環境：

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

如需詳細資訊，請參閱[Deploy_configuration AksService](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)的參考檔。

## <a name="define-the-inference-configuration"></a>定義推斷設定

推斷設定會指向輸入腳本和環境物件，其使用具有 GPU 支援的 docker 映射。 請注意，用於環境定義的 YAML 檔必須列出以版本 > = 1.0.45 做為 pip 相依性的 azureml 預設值，因為它包含裝載模型做為 web 服務所需的功能。

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

如需環境的詳細資訊，請參閱[建立和管理用於定型和部署的環境](how-to-use-environments.md)。
如需詳細資訊，請參閱[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)的參考檔。

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

> [!NOTE]
> 如果 `InferenceConfig` 物件有 `enable_gpu=True`，則 `deployment_target` 參數必須參考提供 GPU 的叢集。 否則，部署作業將會失敗。

如需詳細資訊，請參閱[Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)的參考檔。

## <a name="issue-a-sample-query-to-your-service"></a>對您的服務發出範例查詢

將測試查詢傳送至已部署的模型。 當您將 jpeg 影像傳送到模型時，它會對影像進行評分。 下列程式碼範例會下載測試資料，然後選取要傳送至服務的隨機測試影像。

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

如需建立用戶端應用程式的詳細資訊，請參閱[建立用戶端以使用已部署的 web 服務](how-to-consume-web-service.md)。

## <a name="clean-up-the-resources"></a>清除資源

如果您特別針對此範例建立了 AKS 叢集，請在完成後刪除您的資源。

> [!IMPORTANT]
> Azure 會根據 AKS 叢集的部署時間來計費。 完成使用之後，請務必將它清除。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>後續步驟

* [在 FPGA 上部署模型](how-to-deploy-fpga-web-service.md)
* [使用 ONNX 部署模型](concept-onnx.md#deploy-onnx-models-in-azure)
* [定型 Tensorflow DNN 模型](how-to-train-tensorflow.md)
