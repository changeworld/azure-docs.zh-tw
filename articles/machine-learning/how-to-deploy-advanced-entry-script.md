---
title: 適用于先進案例的作者輸入腳本
titleSuffix: Azure Machine Learning entry script authoring
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95d3570d93aa4966fcf6864838ec01735b8662db
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650290"
---
# <a name="advanced-entry-script-authoring"></a>進階的輸入腳本製作

本文說明如何撰寫特製化使用案例的進入腳本。

## <a name="prerequisites"></a>必要條件

本文假設您已經有想要使用 Azure Machine Learning 部署的已定型機器學習模型。 若要深入瞭解模型部署，請參閱 [此教學](how-to-deploy-and-where.md)課程。

## <a name="automatically-generate-a-swagger-schema"></a>自動產生 Swagger 架構

若要自動產生 web 服務的架構，請在其中一個已定義的類型物件的函式中提供輸入和/或輸出的範例。 型別和範例會用來自動建立架構。 Azure Machine Learning 接著會在部署期間為 web 服務建立 [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 規格。

目前支援下列類型：

* `pandas`
* `numpy`
* `pyspark`
* 標準 Python 物件

若要使用架構產生，請在相依性檔案中包含開放原始碼 `inference-schema` 套件。 如需此封裝的詳細資訊，請參閱 [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) 。 在和變數中定義輸入和輸出範例 `input_sample` 格式 `output_sample` ，這些格式代表 web 服務的要求和回應格式。 在函數的輸入和輸出函式裝飾專案中使用這些範例 `run()` 。 下列 scikit-learn 學習範例會使用架構產生。


## <a name="power-bi-compatible-endpoint"></a>Power BI 相容端點 

下列範例示範如何使用資料框架將輸入資料定義為 `<key: value>` 字典。 從 Power BI 使用已部署的 web 服務時，支援此方法。  ([深入瞭解如何使用 Power BI 的 web 服務](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。 ) 

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

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample, enforce_shape=False))
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


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> 二進位 (即影像) 資料

如果您的模型接受二進位資料（例如影像），您必須修改 `score.py` 用於部署的檔案，以接受原始 HTTP 要求。 若要接受原始資料，請 `AMLRequest` 在您的輸入腳本中使用類別，並將裝飾專案加入至函式 `@rawhttp` `run()` 。

以下是 `score.py` 可接受二進位資料的範例：

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
> `AMLRequest` 類別位於 `azureml.contrib` 命名空間中。 當我們努力改善服務時，此命名空間中的實體會經常變更。 此命名空間中的任何事項都應視為 Microsoft 未完全支援的預覽。
>
> 如果您需要在本機開發環境中測試此項，您可以使用下列命令來安裝元件：
>
> ```shell
> pip install azureml-contrib-services
> ```

`AMLRequest`類別只允許您存取 score.py 中的未經處理張貼資料，沒有用戶端元件。 從用戶端，您可以像平常一樣張貼資料。 例如，下列 Python 程式碼會讀取影像檔案並張貼資料：

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>跨原始資源分享 (CORS) 

跨原始資源分享是一種允許網頁上的資源從另一個網域要求的方式。 CORS 可透過與用戶端要求一起傳送的 HTTP 標頭運作，並隨服務回應傳回。 如需 CORS 和有效標頭的詳細資訊，請參閱維琪百科中的 [跨原始資源分享](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 。

若要將模型部署設定為支援 CORS，請 `AMLResponse` 在您的輸入腳本中使用類別。 這個類別可讓您在回應物件上設定標頭。

下列範例會 `Access-Control-Allow-Origin` 針對來自輸入腳本的回應設定標頭：

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

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` 類別位於 `azureml.contrib` 命名空間中。 當我們努力改善服務時，此命名空間中的實體會經常變更。 此命名空間中的任何事項都應視為 Microsoft 未完全支援的預覽。
>
> 如果您需要在本機開發環境中測試此項，您可以使用下列命令來安裝元件：
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning 只會將 POST 和 GET 要求路由至執行評分服務的容器。 這可能會因為瀏覽器使用對預先傳送 CORS 要求的選項要求而造成錯誤。
> 


## <a name="load-registered-models"></a>載入已註冊的模型

有兩種方式可在您的輸入腳本中尋找模型：
* `AZUREML_MODEL_DIR`：包含模型位置路徑的環境變數。
* `Model.get_model_path`：使用註冊的模型名稱傳回模型檔案路徑的 API。

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR 是在服務部署期間建立的環境變數。 您可以使用此環境變數，找出部署的模型 (s) 的位置。

下表描述 AZUREML_MODEL_DIR 的值，視部署的模型數目而定：

| 部署 | 環境變數值 |
| ----- | ----- |
| 單一模型 | 包含模型之資料夾的路徑。 |
| 多個模型 | 包含所有模型之資料夾的路徑。 模型是依名稱和版本，在此資料夾中 (`$MODEL_NAME/$VERSION`)  |

在模型註冊和部署期間，會將模型放在 AZUREML_MODEL_DIR 路徑中，並保留其原始檔案名。

若要取得輸入腳本中的模型檔案路徑，請將環境變數與您要尋找的檔案路徑結合。

**單一模型範例**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**多模型範例**

在此案例中，會在工作區中註冊兩個模型：

* `my_first_model`： () 包含一個檔案 `my_first_model.pkl` ，而且) 只有一個版本 (`1` 。
* `my_second_model`： () 包含一個檔案 `my_second_model.pkl` ，而且有兩個版本： `1` 和 `2` 。

部署服務時，會在部署作業中提供這兩個模型：

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

在裝載服務的 Docker 映射中， `AZUREML_MODEL_DIR` 環境變數包含模型所在的目錄。
在此目錄中，每個模型都位於的目錄路徑中 `MODEL_NAME/VERSION` 。 其中 `MODEL_NAME` 是已註冊模型的名稱，而 `VERSION` 是模型的版本。 組成註冊模型的檔案會儲存在這些目錄中。

在此範例中，路徑為 `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` 和 `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` 。


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

當您註冊模型時，您會提供用來管理登錄中模型的模型名稱。 您可以使用此名稱搭配 [模型。 get_model_path ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) 方法，以抓取本機檔案系統上的模型檔案或檔案的路徑。 如果您註冊資料夾或檔案集合，此 API 會傳回包含這些檔案之目錄的路徑。

當您註冊模型時，您會為它命名。 名稱會對應至放置模型的位置，不論是在本機或在服務部署期間。

## <a name="next-steps"></a>接下來的步驟

* [針對失敗的部署進行疑難排解](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [建立用戶端應用程式以使用 web 服務](how-to-consume-web-service.md)
* [更新 Web 服務](how-to-deploy-update-web-service.md)
* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [建立模型部署的事件警示和觸發程式](how-to-use-event-grid.md)
