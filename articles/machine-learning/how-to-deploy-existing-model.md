---
title: 使用和部署現有的模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 將本機定型的 ML 模型帶到 Azure 雲端。  您可以註冊在 Azure Machine Learning 之外建立的模型，然後將其部署為 web 服務或 Azure IoT Edge 模組。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 64180320ab57996984f5e886639dfd4977ae5e7c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999069"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>使用 Azure Machine Learning 部署現有的模型


在本文中，您將瞭解如何註冊及部署您在 Azure Machine Learning 之外定型的機器學習模型。 您可以部署為 web 服務或 IoT Edge 裝置。  一旦部署之後，您就可以在 Azure Machine Learning 中監視模型並偵測資料漂移。 

如需本文中概念和詞彙的詳細資訊，請參閱 [管理、部署和監視機器學習模型](concept-model-management-and-deployment.md)。

## <a name="prerequisites"></a>必要條件

* [Azure Machine Learning 工作區](how-to-manage-workspace.md)
  + Python 範例假設 `ws` 變數已設定為您的 Azure Machine Learning 工作區。 如需有關如何連線至工作區的詳細資訊，請參閱 [適用于 Python 的 AZURE MACHINE LEARNING SDK 檔](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true#&preserve-view=trueworkspace)。
  
  + CLI 範例會使用和的預留位置 `myworkspace` `myresourcegroup` ，您應該將其取代為您的工作區名稱和包含它的資源群組。

* [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。  

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)和[Machine Learning CLI 擴充](reference-azure-machine-learning-cli.md)功能。

* 已定型的模型。 模型必須保存在您開發環境中的一或多個檔案。 <br><br>為了示範如何註冊已定型的模型，本文中的範例程式碼會使用 [Paolo Ripamonti 的 Twitter 情感分析專案](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)中的模型。

## <a name="register-the-models"></a>註冊模型 (s) 

註冊模型可讓您在工作區中儲存、版本和追蹤模型的相關中繼資料。 在下列 Python 和 CLI 範例中， `models` 目錄包含 `model.h5` 、 `model.w2v` 、和檔案 `encoder.pkl` `tokenizer.pkl` 。 此範例會將目錄中包含的檔案上傳 `models` 為名為的新模型註冊 `sentiment` ：

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

如需詳細資訊，請參閱 [模型。註冊 ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) 參考。

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> 您也可以將 add `tags` 和 `properties` dictionary 物件設定為已註冊的模型。 這些值稍後可以用來協助識別特定的模型。 例如，使用的架構、訓練參數等等。

如需詳細資訊，請參閱 [az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-register) 參考。


如需模型註冊的一般詳細資訊，請參閱 [管理、部署和監視機器學習模型](concept-model-management-and-deployment.md)。

## <a name="define-inference-configuration"></a>定義推斷設定

推斷設定會定義用來執行已部署模型的環境。 推斷設定會參考下列實體，這些實體是在部署時用來執行模型的：

* 當部署的服務啟動時，名為的專案腳本會 `score.py` 載入模型。 此腳本也會負責接收資料、將資料傳遞至模型，然後傳迴響應。
* Azure Machine Learning 的 [環境](how-to-use-environments.md)。 環境會定義執行模型和進入腳本所需的軟體相依性。

下列範例示範如何使用 SDK 來建立環境，然後使用它搭配推斷設定：

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
+ [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true) 參考。


CLI 會從 YAML 檔案載入推斷設定：

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

使用 CLI 時，conda 環境會定義在推斷設定所參考的檔案中 `myenv.yml` 。 下列 YAML 是此檔案的內容：

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

如需有關推斷設定的詳細資訊，請參閱 [使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

### <a name="entry-script-scorepy"></a>專案腳本 (score.py) 

專案腳本只有兩個必要的函式： `init()` 和 `run(data)` 。 這些函式可用來在啟動時初始化服務，並使用用戶端傳入的要求資料來執行模型。 腳本的其餘部分會處理載入和執行模型 (s) 。

> [!IMPORTANT]
> 沒有適用于所有模型的一般專案腳本。 它一律會特定于所使用的模型。 它必須瞭解如何載入模型、模型所預期的資料格式，以及如何使用模型來評分資料。

下列 Python 程式碼是 () 的範例輸入腳本 `score.py` ：

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

如需輸入腳本的詳細資訊，請參閱[使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

## <a name="define-deployment"></a>定義部署

[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py&preserve-view=true)套件包含用於部署的類別。 您使用的類別會決定模型的部署位置。 例如，若要在 Azure Kubernetes Service 上部署為 web 服務，請使用 [AksWebService.deploy_configuration ( # B1 ](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) 來建立部署設定。

下列 Python 程式碼會定義本機部署的部署設定。 此設定會將模型作為 web 服務部署到您的本機電腦。

> [!IMPORTANT]
> 本機部署需要在您的本機電腦上執行 [Docker](https://www.docker.com/) 的工作安裝：

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

如需詳細資訊，請參閱 [LocalWebservice.deploy_configuration ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-port-none-) 參考。

CLI 會從 YAML 檔案載入部署設定：

```YAML
{
    "computeType": "LOCAL"
}
```

部署至不同的計算目標（例如 Azure 雲端中的 Azure Kubernetes Service）就像變更部署設定一樣簡單。 如需詳細資訊，請參閱 [部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="deploy-the-model"></a>部署模型

下列範例會在名為的註冊模型上載入資訊 `sentiment` ，然後將它部署為名為的服務 `sentiment` 。 在部署期間，會使用推斷設定和部署設定來建立和設定服務環境：

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

如需詳細資訊，請參閱 [模型。部署 ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 參考。

若要從 CLI 部署模型，請使用下列命令。 此命令會 `sentiment:1` 使用儲存在和檔案中的推斷和部署設定，部署第1版的已註冊模型 () `inferenceConfig.json` `deploymentConfig.json` ：

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

如需詳細資訊，請參閱 [az ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-deploy) 參考。

如需部署的詳細資訊，請參閱 [部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="request-response-consumption"></a>要求-回應耗用量

部署之後，就會顯示計分 URI。 用戶端可以使用此 URI 將要求提交給服務。 下列範例是一個簡單的 Python 用戶端，可將資料提交至服務並顯示回應：

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

如需如何使用已部署之服務的詳細資訊，請參閱 [建立用戶端](how-to-consume-web-service.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [如何為已部署的模型建立用戶端](how-to-consume-web-service.md)
