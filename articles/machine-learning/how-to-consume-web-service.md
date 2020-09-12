---
title: 針對部署為 web 服務的模型建立用戶端
titleSuffix: Azure Machine Learning
description: 瞭解如何呼叫從 Azure Machine Learning 部署模型時所產生的 web 服務端點。 此端點會公開一個 REST API，您可以呼叫它來執行模型的推斷。 使用您選擇的程式設計語言來建立此 API 的用戶端。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-csharp
ms.openlocfilehash: 3e6b5e2e06e6cd87295b2faf2a426b75b5f6bf10
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650763"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>使用部署為 Web 服務的 Azure Machine Learning 模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

將 Azure Machine Learning 模型部署為 web 服務時，會建立 REST API 端點。 您可以將資料傳送至此端點，並接收模型傳回的預測。 在本文件中，了解如何使用 C#、Go、Java 和 Python 為Web 服務建立用戶端。

當您將模型部署到您的本機環境、Azure 容器實例、Azure Kubernetes Service 或可現場程式化閘道陣列時，會建立 web 服務 (FPGA) 。 您可以使用 [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)來取得用來存取 web 服務的 URI。 如果啟用驗證，您也可以使用 SDK 來取得驗證金鑰或權杖。

建立使用機器學習 Web 服務的用戶端所適用的一般工作流程是：

1. 使用 SDK 取得連線資訊。
1. 判斷模型所使用之要求資料的類型。
1. 建立一個呼叫 Web 服務的應用程式。

> [!TIP]
> 本檔中的範例會以手動方式建立，而不需要使用 OpenAPI (Swagger) 規格。 如果您已為您的部署啟用 OpenAPI 規格，您可以使用 [swagger codegen](https://github.com/swagger-api/swagger-codegen) 之類的工具來建立服務的用戶端程式庫。

## <a name="connection-information"></a>連線資訊

> [!NOTE]
> 使用 Azure Machine Learning SDK 來取得 Web 服務資訊。 這是 Python SDK。 您可以使用任何語言來建立服務的用戶端。

[azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py&preserve-view=true) 類別可提供建立用戶端所需的資訊。 建立用戶端應用程式時，下列 `Webservice` 屬性很有用：

* `auth_enabled` -如果已啟用金鑰驗證， `True` 則為，否則為 `False` 。
* `token_auth_enabled` -如果已啟用權杖驗證， `True` 則為，否則為 `False` 。
* `scoring_uri` - REST API 的位址。
* `swagger_uri` -OpenAPI 規格的位址。 如果您已啟用自動產生架構，則可使用此 URI。 如需詳細資訊，請參閱 [使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

有三種方法可以針對已部署的 Web 服務擷取這項資訊：

* 部署模型時，將會傳回 `Webservice` 物件，其中包含服務的相關資訊：

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* 您可以使用 `Webservice.list` 擷取工作區中已針對模型部署的 Web 服務的清單。 您可以新增篩選來縮小傳回的資訊清單。 如需可篩選項目的詳細資訊，請參閱 [Webservice.list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py&preserve-view=true) 參考文件。

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* 如果您知道已部署服務的名稱，則可以建立 `Webservice` 的新執行個體，並將工作區和服務名稱作為參數提供。 新物件包含已部署服務的相關資訊。

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="secured-web-service"></a>安全的 web 服務

如果您使用 TLS/SSL 憑證來保護已部署的 web 服務，您可以使用 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) ，利用計分或 swagger URI 連接至服務。 HTTPS 可以加密兩者之間的通訊，以協助保護用戶端與 web 服務之間的通訊安全。 加密會使用 [傳輸層安全性 (TLS) ](https://en.wikipedia.org/wiki/Transport_Layer_Security)。 TLS 有時仍稱為 *安全通訊端層* (SSL) ，也就是 tls 的前身。

> [!IMPORTANT]
> Azure Machine Learning 部署的 Web 服務僅支援 TLS 1.2 版。 建立用戶端應用程式時，請確定它支援此版本。

如需詳細資訊，請參閱[使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md) \(部分機器翻譯\)。

### <a name="authentication-for-services"></a>服務的驗證

Azure Machine Learning 提供兩種方式來控制 web 服務的存取權。

|驗證方法|Aci|AKS|
|---|---|---|
|Key|預設為停用| 預設為啟用|
|Token| 無法使用| 預設為停用 |

將要求傳送至使用金鑰或權杖保護的服務時，請使用 __授權__ 標頭來傳遞金鑰或權杖。 金鑰或權杖的格式必須是 `Bearer <key-or-token>` ，其中 `<key-or-token>` 是您的金鑰或權杖值。

金鑰和權杖之間的主要差異在於 **金鑰是靜態的，而且可以手動**重新產生，而權杖必須在 **到期時**重新整理。 Azure 容器實例和 Azure Kubernetes Service 部署的 web 服務都支援金鑰型驗證，且權杖型驗證 **僅** 適用于 Azure Kubernetes Service 部署。 如需詳細資訊和特定程式碼範例，請參閱「 [如何進行](how-to-setup-authentication.md#web-service-authentication) 驗證」。


#### <a name="authentication-with-keys"></a>使用金鑰進行驗證

當您為部署啟用驗證時，您會自動建立驗證金鑰。

* 在部署到 Azure Kubernetes Service 時，預設會啟用驗證。
* 在部署到 Azure Container Instances 時，預設會停用驗證。

若要控制驗證，請在建立或更新部署時使用 `auth_enabled` 參數。

如果啟用驗證，則可以使用 `get_keys` 方法擷取主要和次要驗證金鑰：

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 如果您需要重新產生金鑰，請使用 [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py&preserve-view=true) 。

#### <a name="authentication-with-tokens"></a>使用權杖進行驗證

當您針對 web 服務啟用權杖驗證時，使用者必須提供 Azure Machine Learning JWT 權杖給 web 服務，才能存取它。 

* 當您部署至 Azure Kubernetes Service 時，預設會停用權杖驗證。
* 當您部署至 Azure 容器實例時，不支援權杖驗證。

若要控制權杖驗證，請 `token_auth_enabled` 在建立或更新部署時使用參數。

如果已啟用權杖驗證，您可以使用 `get_token` 方法來取得持有人權杖和權杖到期時間：

```python
token, refresh_by = service.get_token()
print(token)
```

如果您有 [Azure CLI 和機器學習擴充](reference-azure-machine-learning-cli.md)功能，您可以使用下列命令來取得權杖：

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> 目前取得權杖的唯一方法是使用 Azure Machine Learning SDK 或 Azure CLI Machine Learning 擴充功能。

您將需要在權杖的時間之後要求新的權杖 `refresh_by` 。 

## <a name="request-data"></a>要求資料

REST API 預期是具有下列結構之 JSON 文件的要求主體：

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> 資料結構需要與服務中預期的評分指令碼和模型相符。 評分指令碼可能會在將資料傳遞至模型之前修改資料。

### <a name="binary-data"></a>二進位資料

如需如何在您的服務中啟用二進位資料支援的相關資訊，請參閱 [二進位資料](how-to-deploy-advanced-entry-script.md#binary-data)。

> [!TIP]
> 針對已部署模型所使用的 score.py 檔，啟用對二進位資料的支援。 從用戶端，使用您程式設計語言的 HTTP 功能。 例如，下列程式碼片段會將 JPG 檔案的內容傳送至 web 服務：
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>跨原始資源分享 (CORS) 

如需在您的服務中啟用 CORS 支援的相關資訊，請參閱 [跨原始資源分享](how-to-deploy-advanced-entry-script.md#cors)。

## <a name="call-the-service-c"></a>呼叫服務 (C#)

此範例示範如何使用 C# 呼叫從[在筆記本內訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/notebook_example.ipynb)範例建立的 Web 服務：

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

傳回的結果與下列 JSON 文件類似：

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>呼叫服務 (Go)

此範例示範如何使用 Go 來呼叫從[在筆記本內訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/notebook_example.ipynb)範例建立的 Web 服務：

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

傳回的結果與下列 JSON 文件類似：

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>呼叫服務 (Java)

此範例示範如何使用 Java 來呼叫從[在筆記本內訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/notebook_example.ipynb)範例建立的 Web 服務：

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

傳回的結果與下列 JSON 文件類似：

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>呼叫服務 (Python)

此範例示範如何使用 Python 來呼叫從[在筆記本內訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/notebook_example.ipynb)範例建立的 Web 服務：

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

傳回的結果與下列 JSON 文件類似：

```JSON
[217.67978776218715, 224.78937091757172]
```


## <a name="web-service-schema-openapi-specification"></a>Web 服務架構 (OpenAPI 規格) 

如果您在部署中使用自動產生架構，您可以使用 [swagger_uri 屬性](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#&preserve-view=trueswagger-uri)取得服務之 OpenAPI 規格的位址。 例如， (`print(service.swagger_uri)` 。 ) 使用 GET 要求或在瀏覽器中開啟 URI 以取得規格。

下列 JSON 檔是針對部署產生的架構 (OpenAPI 規格) 範例：

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

如需詳細資訊，請參閱 [OpenAPI 規格](https://swagger.io/specification/)。

如需可從規格建立用戶端程式庫的公用程式，請參閱 [swagger-codegen](https://github.com/swagger-api/swagger-codegen)。


> [!TIP]
> 您可以在部署服務之後，取得架構 JSON 檔。 從已部署的 web 服務使用 [swagger_uri 屬性](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#&preserve-view=trueswagger-uri) (例如， `service.swagger_uri`) 取得本機 Web 服務的 SWAGGER 檔案的 uri。

## <a name="consume-the-service-from-power-bi"></a>使用來自 Power BI 的服務

Power BI 支援 Azure Machine Learning web 服務的耗用量，以使用預測來豐富 Power BI 中的資料。 

若要產生 Power BI 的耗用量所支援的 web 服務，架構必須支援 Power BI 所需的格式。 [瞭解如何建立支援 Power BI 的架構](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#example-entry-script)。

一旦部署 web 服務之後，就可以從 Power BI 資料流程中取用它。 [瞭解如何使用 Power BI 的 Azure Machine Learning web 服務](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。

## <a name="next-steps"></a>接下來的步驟

若要查看適用于 Python 和深度學習模型的即時評分的參考架構，請移至 [Azure 架構中心](/azure/architecture/reference-architectures/ai/realtime-scoring-python)。
