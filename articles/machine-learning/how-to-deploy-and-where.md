---
title: 部署模型的方式和位置
titleSuffix: Azure Machine Learning
description: 瞭解部署 Azure Machine Learning 模型的方式和位置，包括 Azure 容器實例、Azure Kubernetes Service、Azure IoT Edge 和可現場程式化閘道陣列。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 02/27/2020
ms.custom: seoapril2019
ms.openlocfilehash: 4bb13080d2539610eb7cbf3a3e29ce3090c49f55
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283637"
---
# <a name="deploy-models-with-azure-machine-learning"></a>使用 Azure Machine Learning 部署模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何將您的機器學習模型部署為 Azure 雲端中的 web 服務，或 Azure IoT Edge 裝置。

無論[您部署](#target)模型的位置為何，工作流程都很類似：

1. 註冊模型。
1. 準備部署。 （指定資產、使用量、計算目標）。
1. 將模型部署到計算目標。
1. 測試已部署的模型，也稱為 web 服務。

如需部署工作流程中相關概念的詳細資訊，請參閱[使用 Azure Machine Learning 來管理、部署和監視模型](concept-model-management-and-deployment.md)。

## <a name="prerequisites"></a>Prerequisites

- Azure Machine Learning 工作區。 如需詳細資訊，請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

- 模型。 如果您沒有定型的模型，您可以使用[本教學](https://aka.ms/azml-deploy-cloud)課程中提供的模型和相依性檔案。

- [Machine Learning 服務的 Azure CLI 擴充](reference-azure-machine-learning-cli.md)功能、[適用于 PYTHON 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或[Azure Machine Learning Visual Studio Code 延伸](tutorial-setup-vscode-extension.md)模組。

## <a name="connect-to-your-workspace"></a>連接到您的工作區

下列程式碼示範如何使用快取至本機開發環境的資訊，連接到 Azure Machine Learning 的工作區：

+ **使用 SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  如需使用 SDK 連線到工作區的詳細資訊，請參閱[適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace)檔。

+ **使用 CLI**

   使用 CLI 時，請使用 `-w` 或 `--workspace-name` 參數來指定命令的工作區。

+ **使用 VS Code**

   當您使用 VS Code 時，您可以使用圖形化介面來選取工作區。 如需詳細資訊，請參閱 VS Code 擴充功能檔中的[部署和管理模型](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)。

## <a id="registermodel"></a>註冊您的模型

已註冊的模型是組成模型的一或多個檔案的邏輯容器。 例如，如果您有儲存在多個檔案中的模型，您可以在工作區中將其註冊為單一模型。 註冊檔案之後，您就可以下載或部署已註冊的模型，並接收您註冊的所有檔案。

> [!TIP]
> 當您註冊模型時，您會提供雲端位置（從定型回合）或本機目錄的路徑。 此路徑只是為了在註冊過程中，尋找要上傳的檔案。 它不需要符合輸入腳本中使用的路徑。 如需詳細資訊，請參閱[在您的輸入腳本中尋找模型](#locate-model-files-in-your-entry-script)檔案。

機器學習模型會在您的 Azure Machine Learning 工作區中註冊。 模型可以來自 Azure Machine Learning 或其他地方。 下列範例示範如何註冊模型。

### <a name="register-a-model-from-an-experiment-run"></a>從實驗執行註冊模型

本節中的程式碼片段會示範如何從定型回合註冊模型：

> [!IMPORTANT]
> 若要使用這些程式碼片段，您必須先執行定型回合，而且必須能夠存取 `Run` 物件（SDK 範例）或執行識別碼值（CLI 範例）。 如需定型模型的詳細資訊，請參閱[設定模型定型的計算目標](how-to-set-up-training-targets.md)。

+ **使用 SDK**

  當您使用 SDK 來定型模型時，您可以接收[執行](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py)物件或[AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun)物件，視您如何訓練模型而定。 每個物件都可以用來註冊實驗執行所建立的模型。

  + 從 `azureml.core.Run` 物件註冊模型：
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` 參數會參考模型的雲端位置。 在此範例中，會使用單一檔案的路徑。 若要在模型註冊中包含多個檔案，請將 `model_path` 設定為包含檔案的資料夾路徑。 如需詳細資訊，請參閱[Run. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)檔。

  + 從 `azureml.train.automl.run.AutoMLRun` 物件註冊模型：

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    在此範例中，未指定 `metric` 和 `iteration` 參數，因此將會註冊具有最佳主要度量的反復專案。 會使用從執行傳回的 `model_id` 值，而不是模型名稱。

    如需詳細資訊，請參閱[AutoMLRun。 register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)檔。

+ **使用 CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  `--asset-path` 參數會參考模型的雲端位置。 在此範例中，會使用單一檔案的路徑。 若要在模型註冊中包含多個檔案，請將 `--asset-path` 設定為包含檔案的資料夾路徑。

+ **使用 VS Code**

  使用[VS Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)擴充功能，透過任何模型檔案或資料夾來註冊模型。

### <a name="register-a-model-from-a-local-file"></a>從本機檔案註冊模型

您可以藉由提供模型的本機路徑來註冊模型。 您可以提供資料夾或單一檔案的路徑。 您可以使用這個方法來註冊使用 Azure Machine Learning 定型的模型，然後下載。 您也可以使用這個方法來註冊在 Azure Machine Learning 外部定型的模型。

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **使用 SDK 和 ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  若要在模型註冊中包含多個檔案，請將 `model_path` 設定為包含檔案的資料夾路徑。

+ **使用 CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  若要在模型註冊中包含多個檔案，請將 `-p` 設定為包含檔案的資料夾路徑。

**估計時間**：大約10秒。

如需詳細資訊，請參閱[模型類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)的檔。

如需使用 Azure Machine Learning 外部定型模型的詳細資訊，請參閱[如何部署現有的模型](how-to-deploy-existing-model.md)。

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>單一與多模型端點
Azure ML 支援在單一端點後方部署單一或多個模型。

多模型端點會使用共用容器來裝載多個模型。 這有助於降低額外負荷成本、改善使用率，並讓您將模組結合成整體。 您在部署腳本中指定的模型會掛接並在服務容器的磁片上提供，您可以視需要將其載入記憶體中，並根據在計分時間要求的特定模型進行評分。

如需顯示如何在單一容器化端點後方使用多個模型的 E2E 範例，請參閱[此範例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model)

## <a name="prepare-to-deploy"></a>準備部署

若要將模型部署為服務，您需要下列元件：

* **專案腳本 & 原始程式碼**相依性。 此腳本會接受要求，使用模型來評分要求，並傳回結果。

    > [!IMPORTANT]
    > * 專案腳本是您的模型特有的。 它必須瞭解傳入要求資料的格式、您的模型所預期的資料格式，以及傳回給用戶端的資料格式。
    >
    >   如果要求資料的格式無法供您的模型使用，腳本就可以將它轉換成可接受的格式。 它也可以在將回應傳回給用戶端之前，先將它轉換。
    >
    > * Web 服務和 IoT Edge 部署無法存取工作區資料存放區或資料集。 如果您已部署的服務需要存取儲存在部署外部的資料，例如 Azure 儲存體帳戶中的資料，您必須使用相關的 SDK 開發自訂程式碼解決方案。 例如，[適用于 Python 的 AZURE 儲存體 SDK](https://github.com/Azure/azure-storage-python)。
    >
    >   可能適用于您案例的替代方法是[批次預測](how-to-use-parallel-run-step.md)，這會在計分期間提供資料存放區的存取權。

* **推斷**設定。 推斷設定會指定將模型當做服務執行所需的環境設定、輸入腳本和其他元件。

當您擁有必要的元件之後，您就可以分析將因部署模型而建立的服務，以瞭解其 CPU 和記憶體需求。

### <a id="script"></a>1. 定義您的輸入腳本和相依性

專案腳本會接收提交至已部署 web 服務的資料，並將它傳遞至模型。 然後，它會採用模型傳回的回應，並將該回應傳回至用戶端。 *此腳本專屬於您的模型*。 它必須瞭解模型預期和傳回的資料。

此腳本包含兩個載入和執行模型的函式：

* `init()`：此函式通常會將模型載入至全域物件。 當您的 web 服務的 Docker 容器已啟動時，此函式只會執行一次。

* `run(input_data)`：此函式會根據輸入資料，使用模型來預測值。 執行的輸入和輸出通常會使用 JSON 進行序列化和還原序列化。 您也可以使用原始的二進位資料。 您可以先轉換資料，然後再將它傳送至模型，或將它傳回給用戶端。

#### <a name="locate-model-files-in-your-entry-script"></a>在您的輸入腳本中尋找模型檔案

有兩種方式可在您的輸入腳本中尋找模型：
* `AZUREML_MODEL_DIR`：包含模型位置路徑的環境變數。
* `Model.get_model_path`：會使用已註冊的模型名稱傳回模型檔案路徑的 API。

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR 是在服務部署期間建立的環境變數。 您可以使用這個環境變數來尋找已部署之模型的位置。

下表描述 AZUREML_MODEL_DIR 的值，視部署的模型數目而定：

| 部署 | 環境變數值 |
| ----- | ----- |
| 單一模型 | 包含模型的資料夾路徑。 |
| 多個模型 | 包含所有模型之資料夾的路徑。 模型是以名稱和版本在此資料夾中找到（`$MODEL_NAME/$VERSION`） |

在模型註冊和部署期間，模型會放在 AZUREML_MODEL_DIR 路徑中，並保留其原始檔案名。

若要在您的輸入腳本中取得模型檔案的路徑，請將環境變數與您要尋找的檔案路徑結合。

**單一模型範例**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**多個模型範例**
```python
# Example when the model is a file, and the deployment contains multiple models
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model', '1', 'sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

當您註冊模型時，您會提供用來在登錄中管理模型的模型名稱。 您可以使用此名稱搭配[Model. get_model_path （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-)方法來抓取模型檔案的路徑或本機檔案系統上的檔案。 如果您註冊資料夾或檔案集合，此 API 會傳回包含這些檔案的目錄路徑。

當您註冊模型時，會為它命名。 名稱會對應至模型的放置位置，不論是在本機或在服務部署期間。

> [!IMPORTANT]
> 如果您使用自動化機器學習來定型模型，則會使用 `model_id` 值做為模型名稱。 如需註冊和部署使用自動化機器學習服務定型之模型的範例，請參閱 GitHub 上的[Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features) 。

下列範例會傳回名為 `sklearn_mnist_model.pkl` 的單一檔案路徑（以 `sklearn_mnist`的名稱註冊）：

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>選擇性自動產生架構

若要自動產生 web 服務的架構，請在其中一個已定義的類型物件的函式中提供輸入和/或輸出的範例。 型別和範例用來自動建立架構。 Azure Machine Learning 接著會在部署期間建立 web 服務的[OpenAPI](https://swagger.io/docs/specification/about/) （Swagger）規格。

目前支援下列類型：

* `pandas`
* `numpy`
* `pyspark`
* 標準 Python 物件

若要使用架構產生，請在相依性檔案中包含 `inference-schema` 套件。 如需此套件的詳細資訊，請參閱[https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)。 在 `input_sample` 和 `output_sample` 變數中定義輸入和輸出範例格式，其代表 web 服務的要求和回應格式。 在 `run()` 函式的 input 和 output 函數裝飾專案中使用這些範例。 下列 scikit-learn 學習範例會使用架構產生。

##### <a name="example-entry-script"></a>範例專案腳本

下列範例示範如何接受並傳回 JSON 資料：

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

下列範例示範如何使用資料框架，將輸入資料定義為 `<key: value>` 字典。 此方法支援從 Power BI 使用已部署的 web 服務。 （[深入瞭解如何使用 Power BI 的 web 服務](https://docs.microsoft.com/power-bi/service-machine-learning-integration)）。

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

如需更多範例，請參閱下列腳本：

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>二進位資料

如果您的模型接受二進位資料（例如影像），您必須修改用於部署的 `score.py` 檔案，以接受原始的 HTTP 要求。 若要接受原始資料，請在您的輸入腳本中使用 `AMLRequest` 類別，並將 `@rawhttp` 裝飾專案新增至 `run()` 函數。

以下是接受二進位資料的 `score.py` 範例：

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest` 類別是在 `azureml.contrib` 命名空間中。 當我們改善服務時，此命名空間中的實體會經常變更。 此命名空間中的任何專案都應視為不受 Microsoft 完全支援的預覽。
>
> 如果您需要在本機開發環境中測試此項，您可以使用下列命令來安裝元件：
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>跨原始來源資源分享（CORS）

跨原始資源分享是允許從另一個網域要求網頁上資源的一種方式。 CORS 的運作方式是透過與用戶端要求一起傳送的 HTTP 標頭，並傳回服務回應。 如需 CORS 和有效標頭的詳細資訊，請參閱維琪百科中的[跨原始資源分享](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)。

若要將您的模型部署設定為支援 CORS，請在您的輸入腳本中使用 `AMLResponse` 類別。 這個類別可讓您在回應物件上設定標頭。

下列範例會針對輸入腳本中的回應設定 `Access-Control-Allow-Origin` 標頭：

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` 類別是在 `azureml.contrib` 命名空間中。 當我們改善服務時，此命名空間中的實體會經常變更。 此命名空間中的任何專案都應視為不受 Microsoft 完全支援的預覽。
>
> 如果您需要在本機開發環境中測試此項，您可以使用下列命令來安裝元件：
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inference-configuration"></a>2. 定義推斷設定

推斷設定說明如何設定包含模型的 web 服務。 它不是您輸入腳本的一部分。 它會參考您的輸入腳本，並用來尋找部署所需的所有資源。 稍後當您部署模型時，就會用到它。

推斷設定會使用 Azure Machine Learning 環境來定義您的部署所需的軟體相依性。 環境可讓您建立、管理及重複使用定型和部署所需的軟體相依性。 您可以從自訂相依性檔案建立環境，或使用其中一個策劃 Azure Machine Learning 環境。 下列 YAML 是用於推斷的 Conda 相依性檔案範例。 請注意，您必須以 pip 相依性的形式來表示版本 > = 1.0.45 的 azureml 預設值，因為它包含裝載模型做為 web 服務所需的功能。 如果您想要使用自動產生架構，您的輸入腳本也必須匯入 `inference-schema` 的封裝。

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> 如果您的相依性可透過 Conda 和 pip （來自 PyPi）取得，Microsoft 建議使用 Conda 版本，因為 Conda 套件通常會隨附預先建立的二進位檔，讓安裝更可靠。
>
> 如需詳細資訊，請參閱[瞭解 Conda 和 Pip](https://www.anaconda.com/understanding-conda-and-pip/)。
>
> 若要檢查您的相依性是否可透過 Conda 取得，請使用 `conda search <package-name>` 命令，或使用位於[https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo)和[https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo)的封裝索引。

您可以使用相依性檔案來建立環境物件，並將它儲存到您的工作區，以供日後使用：

```python
from azureml.core.environment import Environment


myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

下列範例示範如何從您的工作區載入環境，然後將它與推斷設定搭配使用：

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

如需環境的詳細資訊，請參閱[建立和管理用於定型和部署的環境](how-to-use-environments.md)。

如需有關推斷設定的詳細資訊，請參閱[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)類別檔。

如需有關使用自訂 Docker 映射搭配推斷設定的詳細資訊，請參閱[如何使用自訂 docker 映射部署模型](how-to-deploy-custom-docker-image.md)。

#### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig 的 CLI 範例

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

下列命令示範如何使用 CLI 來部署模型：

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

在此範例中，設定會指定下列設定：

* 此模型需要 Python。
* [輸入腳本](#script)，用來處理傳送至已部署服務的 web 要求。
* Conda 檔案，描述推斷所需的 Python 套件。

如需有關使用自訂 Docker 映射搭配推斷設定的詳細資訊，請參閱[如何使用自訂 docker 映射部署模型](how-to-deploy-custom-docker-image.md)。

### <a id="profilemodel"></a>3. 分析模型以判斷資源使用率

在您註冊模型並備妥其部署所需的其他元件之後，您可以判斷所部署服務所需的 CPU 和記憶體。 分析會測試執行模型的服務，並傳回如 CPU 使用量、記憶體使用量和回應延遲的資訊。 它也會根據資源使用量提供 CPU 和記憶體的建議。

若要分析您的模型，您需要：
* 已註冊的模型。
* 根據您的輸入腳本和推斷環境定義的推斷設定。
* 單一資料行表格式資料集，其中每個資料列都包含代表範例要求資料的字串。

> [!IMPORTANT]
> 此時，我們只支援將預期其要求資料為字串的服務進行分析，例如：字串序列化 json、文字、字串序列化影像等。資料集的每個資料列內容（字串）都會放入 HTTP 要求的主體中，並傳送至封裝模型以進行評分的服務。

以下是如何建立輸入資料集來分析服務的範例，其預期其傳入要求資料會包含序列化 json。 在此情況下，我們建立了一個以資料集為基礎的100實例，其具有相同的要求資料內容。 在真實世界的案例中，我們建議您使用包含各種輸入的較大型資料集，特別是當您的模型資源使用方式/行為與輸入相依時更是如此。

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

一旦您的資料集包含備妥範例要求資料，請建立推斷設定。 推斷設定是以 score.py 和環境定義為基礎。 下列範例示範如何建立推斷設定並執行分析：

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

下列命令示範如何使用 CLI 來分析模型：

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

## <a name="deploy-to-target"></a>部署至目標

部署會使用推斷設定部署設定來部署模型。 無論計算目標為何，部署程式都很類似。 部署至 AKS 稍有不同，因為您必須提供 AKS 叢集的參考。

### <a name="choose-a-compute-target"></a>選擇計算目標

您可以使用下列計算目標或計算資源來裝載您的 web 服務部署：

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

### <a name="define-your-deployment-configuration"></a>定義您的部署設定

在部署模型之前，您必須先定義部署設定。 *部署設定適用于將裝載 web 服務的計算目標。* 例如，當您在本機部署模型時，您必須指定服務接受要求的埠。 部署設定不屬於您的輸入腳本。 它是用來定義將裝載模型和專案腳本之計算目標的特性。

您可能也需要建立計算資源（例如，您的工作區尚未有相關聯的 Azure Kubernetes Service （AKS）實例）。

下表提供建立每個計算目標之部署設定的範例：

| 計算目標 | 部署設定範例 |
| ----- | ----- |
| 本機 | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

本機、Azure 容器實例和 AKS web 服務的類別可以從 `azureml.core.webservice`匯入：

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-ssl"></a>使用 SSL 保護部署

如需如何保護 web 服務部署的詳細資訊，請參閱[使用 SSL 保護 web 服務](how-to-secure-web-service.md#enable)。

### <a id="local"></a>本機部署

若要在本機部署模型，您必須在本機電腦上安裝 Docker。

#### <a name="using-the-sdk"></a>使用 SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

如需詳細資訊，請參閱[LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py)、 [Model. deploy （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)和[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)的檔。

#### <a name="using-the-cli"></a>使用 CLI

若要使用 CLI 部署模型，請使用下列命令。 將 `mymodel:1` 取代為已註冊模型的名稱和版本：

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

如需詳細資訊，請參閱[az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)檔。

### <a name="understanding-service-state"></a>瞭解服務狀態

在模型部署期間，您可能會在完全部署時看到服務狀態變更。

下表描述不同的服務狀態：

| Webservice 狀態 | 描述 | 最終狀態？
| ----- | ----- | ----- |
| 正在 | 服務正在進行部署。 | 否 |
| 狀況不良 | 服務已部署，但目前無法連線。  | 否 |
| 設無法排程 | 因為缺少資源，所以目前無法部署服務。 | 否 |
| 失敗 | 因為發生錯誤或損毀，所以服務無法部署。 | 是 |
| Healthy | 服務狀況良好，且端點可供使用。 | 是 |

### <a id="notebookvm"></a>計算實例 web 服務（開發/測試）

請參閱[將模型部署到 Azure Machine Learning 計算實例](how-to-deploy-local-container-notebook-vm.md)。

### <a id="aci"></a>Azure 容器實例（開發/測試）

請參閱[部署至 Azure 容器實例](how-to-deploy-azure-container-instance.md)。

### <a id="aks"></a>Azure Kubernetes Service （開發/測試和生產）

請參閱[部署至 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)。

### <a name="ab-testing-controlled-rollout"></a>A/B 測試（受控首度發行）
如需詳細資訊，請參閱[ML 模型的受控推出](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview)。

## <a name="consume-web-services"></a>取用 Web 服務

每個已部署的 web 服務都會提供 REST 端點，因此您可以使用任何程式設計語言來建立用戶端應用程式。
如果您已為服務啟用以金鑰為基礎的驗證，您必須提供服務金鑰做為要求標頭中的權杖。
如果您已為您的服務啟用權杖型驗證，則需要提供 Azure Machine Learning 的 JSON Web 權杖（JWT）做為要求標頭中的持有人權杖。 

主要差異在於**金鑰是靜態的，而且可以手動**重新產生，而且**權杖必須在到期時**重新整理。 Azure 容器實例和 Azure Kubernetes Service 部署的 web 服務支援以金鑰為基礎的驗證，而權杖型驗證**僅**適用于 Azure Kubernetes Service 部署。 如需詳細資訊和特定程式碼範例，請參閱操作[說明](how-to-setup-authentication.md#web-service-authentication)驗證。

> [!TIP]
> 您可以在部署服務之後，取得架構 JSON 檔。 使用已部署 web 服務（例如 `service.swagger_uri`）中的 [ [swagger_uri] 屬性](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)，以取得本機 web 服務之 swagger 檔案的 uri。

### <a name="request-response-consumption"></a>要求-回應耗用量

以下範例說明如何在 Python 中叫用您的服務：
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

如需詳細資訊，請參閱[建立用戶端應用程式以使用 web 服務](how-to-consume-web-service.md)。

### <a name="web-service-schema-openapi-specification"></a>Web 服務架構（OpenAPI 規格）

如果您在部署中使用自動產生架構，您可以使用[swagger_uri 屬性](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)取得服務的 OpenAPI 規格位址。 （例如，`print(service.swagger_uri)`）。使用 GET 要求，或在瀏覽器中開啟 URI 以取得規格。

下列 JSON 檔是針對部署所產生的架構（OpenAPI 規格）範例：

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

如需詳細資訊，請參閱[OpenAPI 規格](https://swagger.io/specification/)。

如需可從規格建立用戶端程式庫的公用程式，請參閱[swagger-codegen](https://github.com/swagger-api/swagger-codegen)。

### <a id="azuremlcompute"></a>批次推斷
Azure Machine Learning 計算目標是由 Azure Machine Learning 建立和管理。 它們可以用於 Azure Machine Learning 管線的批次預測。

如需使用 Azure Machine Learning 計算進行批次推斷的逐步解說，請參閱[如何執行批次預測](tutorial-pipeline-batch-scoring-classification.md)。

### <a id="iotedge"></a>IoT Edge 推斷
支援部署至 edge 目前處於預覽階段。 如需詳細資訊，請參閱[將 Azure Machine Learning 部署為 IoT Edge 模組](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)。


## <a id="update"></a>更新 web 服務

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>持續部署模型

您可以使用[Azure DevOps](https://azure.microsoft.com/services/devops/)的 Machine Learning 延伸模組，持續部署模型。 在 Azure Machine Learning 工作區中註冊新的機器學習模型時，您可以使用 Azure DevOps 的 Machine Learning 擴充功能來觸發部署管線。

1. 註冊[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)，讓您的應用程式持續整合並傳遞至任何平臺或雲端。 （請注意，Azure Pipelines 與[Machine Learning 管線](concept-ml-pipelines.md#compare)不同）。

1. [建立 Azure DevOps 專案。](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. 安裝[Azure Pipelines 的 Machine Learning 延伸](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)模組。

1. 使用服務連線來設定與您 Azure Machine Learning 工作區的服務主體連線，以便您可以存取您的構件。 移至 專案設定，選取 **服務連接**，然後選取  **Azure Resource Manager**：

    [![選取 Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. 在 [**範圍層級**] 清單中，選取 [ **AzureMLWorkspace**]，然後輸入其餘的值：

    ![選取 AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. 若要使用 Azure Pipelines 持續部署機器學習模型，請在 [管線] 底下，選取 [**發行**]。 新增 [成品]，然後選取 [ **AzureML 模型**成品] 和您稍早建立的服務連接。 選取要觸發部署的模型和版本：

    [![選取 AzureML 模型](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. 在您的模型成品上啟用模型觸發程式。 當您開啟觸發程式時，每次在工作區中註冊該模型的指定版本（也就是最新版本）時，就會觸發 Azure DevOps 發行管線。

    [![啟用模型觸發程式](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

如需更多範例專案和範例，請參閱 GitHub 中的下列範例存放庫：

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>下載模型
如果您想要下載您的模型以在自己的執行環境中使用，您可以使用下列 SDK/CLI 命令來執行此動作：

SDK
```python
model_path = Model(ws,'mymodel').download()
```

CLI：
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>預覽無程式碼模型部署

無程式碼模型部署目前為預覽狀態，並支援下列機器學習架構：

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel 格式
Tensorflow 模型必須以**SavedModel 格式**註冊，才能與無程式碼模型部署搭配使用。

如需如何建立 SavedModel 的相關資訊，請參閱[此連結](https://www.tensorflow.org/guide/saved_model)。

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>ONNX 模型

任何 ONNX 推斷圖形都支援 ONNX 模型註冊和部署。 目前不支援前置處理和 postprocess 步驟。

以下是如何註冊和部署 MNIST ONNX 模型的範例：

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

如果您使用 Pytorch，將[模型從 Pytorch 匯出到 ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) ，會有轉換和限制的詳細資料。 

### <a name="scikit-learn-models"></a>Scikit-learn-學習模型

所有內建的 scikit-learn-學習模型類型都不支援任何程式碼模型部署。

以下範例示範如何註冊和部署 sklearn 模型，而不需要額外的程式碼：

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

注意：這些相依性會包含在預先建立的 sklearn 推斷容器中：

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>封裝模型

在某些情況下，您可能會想要建立 Docker 映射，而不部署模型（例如，您打算[部署至 Azure App Service](how-to-deploy-app-service.md)）。 或者，您可能會想要下載映射，並在本機 Docker 安裝上加以執行。 您甚至可能會想要下載用來建立映射、加以檢查、修改，以及手動建立映射的檔案。

模型封裝可讓您執行這些動作。 它會封裝裝載模型做為 web 服務所需的所有資產，並可讓您下載完全建立的 Docker 映射或建立元件所需的檔案。 有兩種方式可以使用模型封裝：

**下載封裝的模型：** 下載包含模型的 Docker 映射，以及將其裝載為 web 服務所需的其他檔案。

**產生 Dockerfile：** 下載建立 Docker 映射所需的 Dockerfile、模型、專案腳本和其他資產。 接著，您可以在本機建立映射之前，先檢查檔案或進行變更。

這兩個套件都可以用來取得本機 Docker 映射。

> [!TIP]
> 建立封裝與部署模型類似。 您可以使用已註冊的模型和推斷設定。

> [!IMPORTANT]
> 若要下載完整的映射，或在本機建立映射，您必須在開發環境中安裝[Docker](https://www.docker.com) 。

### <a name="download-a-packaged-model"></a>下載封裝的模型

下列範例會建立映射，它會在您工作區的 Azure container registry 中註冊：

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

建立封裝之後，您可以使用 `package.pull()` 將映射提取到您的本機 Docker 環境。 此命令的輸出會顯示映射的名稱。 例如： 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`第 1 課：建立 Windows Azure 儲存體物件{2}。 

下載模型之後，請使用 `docker images` 命令來列出本機映射：

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

若要根據此映射啟動本機容器，請使用下列命令，從 shell 或命令列啟動已命名的容器。 將 `<imageid>` 值取代為 `docker images` 命令所傳回的映射識別碼。

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

此命令會啟動名為 `myimage`之映射的最新版本。 它會將本機埠6789對應至 web 服務所接聽之容器中的埠（5001）。 它也會將名稱 `mycontainer` 指派給容器，讓容器更容易停止。 啟動容器之後，您可以將要求提交至 `http://localhost:6789/score`。

### <a name="generate-a-dockerfile-and-dependencies"></a>產生 Dockerfile 和相依性

下列範例示範如何下載 Dockerfile、模型和其他在本機建立映射所需的資產。 `generate_dockerfile=True` 參數表示您想要檔案，而不是完整建立的映射。

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

此程式碼會將建立映射所需的檔案下載到 `imagefiles` 目錄。 儲存檔案中所包含的 Dockerfile 會參考存放在 Azure container registry 中的基底映射。 當您在本機 Docker 安裝上建立映射時，您必須使用位址、使用者名稱和密碼來向登錄進行驗證。 使用下列步驟，使用本機 Docker 安裝來建立映射：

1. 在 shell 或命令列會話中，使用下列命令來向 Azure container registry 驗證 Docker。 以 `package.get_container_registry()`所抓取的值取代 `<address>`、`<username>`和 `<password>`。

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. 若要建立映射，請使用下列命令。 將 `<imagefiles>` 取代為 `package.save()` 儲存檔案的目錄路徑。

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    此命令會將映射名稱設定為 `myimage`。

若要驗證映射是否已建立，請使用 `docker images` 命令。 您應該會在清單中看到 [`myimage`] 映射：

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

若要根據此映射啟動新的容器，請使用下列命令：

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

此命令會啟動名為 `myimage`之映射的最新版本。 它會將本機埠6789對應至 web 服務所接聽之容器中的埠（5001）。 它也會將名稱 `mycontainer` 指派給容器，讓容器更容易停止。 啟動容器之後，您可以將要求提交至 `http://localhost:6789/score`。

### <a name="example-client-to-test-the-local-container"></a>測試本機容器的範例用戶端

下列程式碼是可搭配容器使用的 Python 用戶端範例：

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

例如，如需其他程式設計語言的用戶端，請參閱[使用部署為 web 服務的模型](how-to-consume-web-service.md)。

### <a name="stop-the-docker-container"></a>停止 Docker 容器

若要停止容器，請從不同的 shell 或命令列使用下列命令：

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>清除資源

若要刪除已部署的 Web 服務，請使用 `service.delete()`。
若要刪除已註冊的模型，請使用 `model.delete()`。

如需詳細資訊，請參閱[WebService. delete （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)和[Model. delete （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)的檔。

## <a name="next-steps"></a>後續步驟

* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [部署疑難排解](how-to-troubleshoot-deployment.md)
* [使用 SSL 保護 Azure Machine Learning Web 服務](how-to-secure-web-service.md)
* [使用部署為 Web 服務的 Azure Machine Learning 模型](how-to-consume-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [建立模型部署的事件警示和觸發程式](how-to-use-event-grid.md)

