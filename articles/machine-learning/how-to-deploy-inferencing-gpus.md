---
title: 使用 GPU 部署推理模型
titleSuffix: Azure Machine Learning
description: 本文介紹如何使用 Azure 機器學習將啟用 GPU 的 Tensorflow 深度學習模型部署為 Web 服務.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: b0fd537d1930e7c9d5f7a33f56ec5d00b1556562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398342"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>使用 GPU 部署用於推理的深層學習模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介紹如何使用 Azure 機器學習將啟用 GPU 的模型部署為 Web 服務。 本文中的資訊基於在 Azure 庫伯奈斯服務 （AKS） 上部署模型。 AKS 群集提供模型用於推理的 GPU 資源。

推理或模型評分是部署模型用於進行預測的階段。 使用 GPU 而不是 CPU 在高度可並行的計算上提供了性能優勢。

> [!IMPORTANT]
> 對於 Web 服務部署，GPU 推理僅在 Azure 庫伯奈斯服務上支援。 對於使用__機器學習管道__進行推理，GPU 僅在 Azure 機器學習計算中受支援。 有關使用 ML 管道的詳細資訊，請參閱[運行批次處理預測](how-to-use-parallel-run-step.md)。 

> [!TIP]
> 儘管本文中的程式碼片段使用 TensorFlow 模型，但您可以將資訊應用於支援 GPU 的任何機器學習框架。

> [!NOTE]
> 本文中的資訊基於["如何部署到 Azure 庫伯奈斯服務](how-to-deploy-azure-kubernetes-service.md)"一文中的資訊。 如果本文通常介紹對 AKS 的部署，本文將介紹 GPU 特定的部署。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 有關詳細資訊，請參閱創建[Azure 機器學習工作區](how-to-manage-workspace.md)。

* 安裝了 Azure 機器學習 SDK 的 Python 開發環境。 有關詳細資訊，請參閱[Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。  

* 使用 GPU 的已註冊模型。

    * 要瞭解如何註冊模型，請參閱[部署模型](how-to-deploy-and-where.md#registermodel)。

    * 要創建和註冊用於創建本文檔的 Tensorflow 模型，請參閱[如何訓練 TensorFlow 模型](how-to-train-tensorflow.md)。

* 對[如何以及在哪裡部署模型](how-to-deploy-and-where.md)的一般理解。

## <a name="connect-to-your-workspace"></a>連線到您的工作區

要連接到現有工作區，請使用以下代碼：

> [!IMPORTANT]
> 此程式碼片段希望工作區配置將保存在目前的目錄或其父目錄中。 有關創建工作區的詳細資訊，請參閱[創建和管理 Azure 機器學習工作區](how-to-manage-workspace.md)。   有關將設定檔保存到檔的詳細資訊，請參閱[創建工作區設定檔](how-to-configure-environment.md#workspace)。

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>使用 GPU 創建庫伯奈斯群集

Azure 庫伯奈斯服務提供了許多不同的 GPU 選項。 您可以使用其中任何一個進行模型推理。 有關功能和成本的完整細目，請參閱[N 系列 VM 清單](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)。

以下代碼演示如何為工作區創建新的 AKS 群集：

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
> 只要 AKS 群集存在，Azure 就會為您收費。 完成 AKS 群集後，請確保將其刪除。

有關將 AKS 與 Azure 機器學習結合使用的詳細資訊，請參閱[如何部署到 Azure 庫伯奈斯服務](how-to-deploy-azure-kubernetes-service.md)。

## <a name="write-the-entry-script"></a>編寫條目腳本

條目腳本接收提交到 Web 服務的資料，將其傳遞到模型，並返回評分結果。 以下腳本在啟動時載入 Tensorflow 模型，然後使用該模型對資料進行評分。

> [!TIP]
> 這是模型特定的輸入指令碼。 例如，腳本必須知道要與模型、資料格式等一起使用的框架。

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

此檔案名為`score.py`。 有關條目腳本的詳細資訊，請參閱[部署的方式和位置](how-to-deploy-and-where.md)。

## <a name="define-the-conda-environment"></a>定義 conda 環境

conda 環境檔指定服務的依賴項。 它包括模型和條目腳本所需的依賴項。 請注意，您必須指示 azureml 預設值，其中 veriver >= 1.0.45 作為點依賴項，因為它包含將模型託管為 Web 服務所需的功能。 以下 YAML 定義了 Tensorflow 模型的環境。 它指定`tensorflow-gpu`將使用此部署中使用的 GPU 的 ，這將使用 ：

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

在此示例中，檔保存為`myenv.yml`。

## <a name="define-the-deployment-configuration"></a>定義部署配置

部署配置定義用於運行 Web 服務的 Azure 庫伯奈斯服務環境：

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

有關詳細資訊，請參閱[AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)的參考文檔。

## <a name="define-the-inference-configuration"></a>定義推理配置

推理配置指向條目腳本和環境物件，該物件使用支援 GPU 的 Docker 映射。 請注意，用於環境定義的 YAML 檔必須列出版本>= 1.0.45 作為點依賴項的 azureml 預設值，因為它包含將模型託管為 Web 服務所需的功能。

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

有關環境的詳細資訊，請參閱[創建和管理用於培訓和部署的環境](how-to-use-environments.md)。
有關詳細資訊，請參閱[推理配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)的參考文檔。

## <a name="deploy-the-model"></a>部署模型

將模型部署到 AKS 群集，並等待其創建服務。

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
> 如果`InferenceConfig`物件具有`enable_gpu=True`，則`deployment_target`參數必須引用提供 GPU 的群集。 否則，部署作業將會失敗。

有關詳細資訊，請參閱[模型](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)的參考文檔。

## <a name="issue-a-sample-query-to-your-service"></a>向服務發出依例查詢

向部署的模型發送測試查詢。 當您向模型發送 jpeg 圖像時，它會對圖像進行評分。 以下代碼示例下載測試資料，然後選擇隨機測試映射發送到服務。

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

有關創建用戶端應用程式的詳細資訊，請參閱[創建用戶端以使用已部署的 Web 服務](how-to-consume-web-service.md)。

## <a name="clean-up-the-resources"></a>清除資源

如果為此示例專門創建了 AKS 群集，則完成後刪除資源。

> [!IMPORTANT]
> Azure 會根據 AKS 群集的部署時間來存儲費用。 完成後，請務必將其清理乾淨。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>後續步驟

* [在 FPGA 上部署模型](how-to-deploy-fpga-web-service.md)
* [使用 ONNX 部署模型](concept-onnx.md#deploy-onnx-models-in-azure)
* [列車張力 DNN 型號](how-to-train-tensorflow.md)
