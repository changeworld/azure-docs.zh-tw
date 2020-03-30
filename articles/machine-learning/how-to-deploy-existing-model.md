---
title: 使用和部署現有模型
titleSuffix: Azure Machine Learning
description: 瞭解如何將 Azure 機器學習與在服務外部訓練的模型一起使用。 可以註冊在 Azure 機器學習之外創建的模型，然後將它們部署為 Web 服務或 Azure IoT 邊緣模組。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/17/2020
ms.openlocfilehash: 924bd2fdba2359e6f1108c39802ad3ce95ebdf07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472370"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>將現有模型與 Azure 機器學習一起使用
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何將現有的機器學習模型與 Azure 機器學習一起使用。

如果機器學習模型在 Azure 機器學習之外進行了培訓，則仍可以使用該服務將模型部署為 Web 服務或 IoT Edge 設備。 

> [!TIP]
> 本文提供有關註冊和部署現有模型的基本資訊。 部署後，Azure 機器學習可為模型提供監視。 它還允許您存儲發送到部署的輸入資料，這些資料可用於資料漂移分析或訓練模型的新版本。
>
> 有關此處使用的概念和術語的詳細資訊，請參閱[管理、部署和監視機器學習模型](concept-model-management-and-deployment.md)。
>
> 有關部署過程的一般資訊，請參閱[使用 Azure 機器學習部署模型](how-to-deploy-and-where.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 有關詳細資訊，請參閱[創建工作區](how-to-manage-workspace.md)。

    > [!TIP]
    > 本文中的 Python 示例假定該`ws`變數已設置為 Azure 機器學習工作區。
    >
    > CLI 示例使用 和`myworkspace``myresourcegroup`的預留位置。 將這些內容替換為工作區的名稱以及包含工作區的資源組。

* [Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。  

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)和[機器學習 CLI 擴展](reference-azure-machine-learning-cli.md)。

* 已定型的模型。 模型必須保存到開發環境中的一個或多個檔。

    > [!NOTE]
    > 為了演示註冊在 Azure 機器學習之外訓練的模型，本文中的示例程式碼片段使用 Paolo Ripamonti 的 Twitter 情緒分析專案創建的模型： [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)。

## <a name="register-the-models"></a>註冊模型

註冊模型允許您在工作區中存儲、版本和跟蹤有關模型的中繼資料。 在下面的 Python 和 CLI`models`示例中，目錄包含`model.h5` `model.w2v`、`encoder.pkl`和`tokenizer.pkl`檔。 本示例將`models`目錄中的檔上載為名為 ：`sentiment`的新模型註冊：

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

有關詳細資訊，請參閱[Model.register（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-)引用。

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> 您還可以將添加`tags`和`properties`字典物件設置為已註冊的模型。 這些值以後可用於説明識別特定模型。 例如，所使用的框架、訓練參數等。

有關詳細資訊，請參閱 az [ml 模型寄存器](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)引用。


有關模型註冊的詳細資訊，請參閱[管理、部署和監視機器學習模型](concept-model-management-and-deployment.md)。

## <a name="define-inference-configuration"></a>定義推理配置

推理配置定義用於運行已部署模型的環境。 推理配置引用以下實體，這些實體用於在部署模型時運行模型：

* 輸入指令碼。 此檔（命名`score.py`）在部署的服務啟動時載入模型。 它還負責接收資料、將其傳遞到模型，然後返回回應。
* Azure 機器學習[環境](how-to-use-environments.md)。 環境定義運行模型和條目腳本所需的軟體依賴項。

下面的示例演示如何使用 SDK 創建環境，然後將其與推理配置一起使用：

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

如需詳細資訊，請參閱下列文章：

+ [如何使用環境](how-to-use-environments.md)。
+ [推理配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)引用。


CLI 從 YAML 檔載入推理配置：

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

使用 CLI 時，conda 環境在推理`myenv.yml`配置引用的檔中定義。 以下 YAML 是此檔的內容：

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

有關推理配置的詳細資訊，請參閱使用[Azure 機器學習部署模型](how-to-deploy-and-where.md)。

### <a name="entry-script"></a>條目腳本

條目腳本只有兩個必需的函數`init()`和`run(data)`。 這些函數用於在啟動時初始化服務，並使用用戶端傳入的請求資料運行模型。 腳本的其餘部分處理載入和運行模型。

> [!IMPORTANT]
> 沒有適用于所有模型的通用條目腳本。 它總是特定于所使用的模型。 它必須瞭解如何載入模型、模型期望的資料格式以及如何使用模型對資料進行評分。

以下 Python 代碼是一個示例條目`score.py`腳本 （ 。

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

有關條目腳本的詳細資訊，請參閱使用[Azure 機器學習部署模型](how-to-deploy-and-where.md)。

## <a name="define-deployment"></a>定義部署

[Web 服務](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py)包包含用於部署的類。 您使用的類確定模型的部署位置。 例如，要在 Azure 庫伯奈斯服務上部署為 Web 服務，請使用[AksWebService.deploy_configuration（）](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)創建部署配置。

以下 Python 代碼為本地部署定義部署配置。 此配置將模型作為 Web 服務部署到本地電腦。

> [!IMPORTANT]
> 本地部署需要在本地電腦上安裝[Docker](https://www.docker.com/)的工作：

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

有關詳細資訊，請參閱[本地 Web 服務.deploy_configuration（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-)引用。

CLI 從 YAML 檔載入部署配置：

```YAML
{
    "computeType": "LOCAL"
}
```

部署到其他計算目標（如 Azure 雲中的 Azure 庫伯內斯服務）與更改部署配置一樣簡單。 有關詳細資訊，請參閱[如何部署模型以及部署模型的位置](how-to-deploy-and-where.md)。

## <a name="deploy-the-model"></a>部署模型

下面的示例載入名為`sentiment`的註冊模型上的資訊，然後將其部署為名為 的服務。 `sentiment` 在部署期間，推理配置和部署配置用於創建和佈建服務環境：

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

有關詳細資訊，請參閱[Model.deploy（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)引用。

要從 CLI 部署模型，請使用以下命令。 此命令使用 存儲在`sentiment:1``inferenceConfig.json`和`deploymentConfig.json`檔中的推理和部署配置部署已註冊模型 （ ） 的版本 1：

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

有關詳細資訊，請參閱 az [ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)引用。

有關部署的詳細資訊，請參閱[如何部署模型以及部署模型的位置](how-to-deploy-and-where.md)。

## <a name="request-response-consumption"></a>請求-回應消耗

部署後，將顯示評分 URI。 用戶端可以使用此 URI 向服務提交請求。 以下示例是向服務提交資料並顯示回應的基本 Python 用戶端：

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

有關如何使用已部署服務的詳細資訊，請參閱[創建用戶端](how-to-consume-web-service.md)。

## <a name="next-steps"></a>後續步驟

* [使用應用程式見解監視 Azure 機器學習模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [如何以及在哪裡部署模型](how-to-deploy-and-where.md)
* [如何為已部署的模型創建用戶端](how-to-consume-web-service.md)
