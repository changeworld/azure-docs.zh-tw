---
title: 為部署為 Web 服務的模型創建用戶端
titleSuffix: Azure Machine Learning
description: 了解如何取用在使用 Azure Machine Learning 模型部署模型時所產生的 Web 服務。 此 Web 服務會公開 REST API。 使用您選擇的程式設計語言來建立此 API 的用戶端。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: a86b8ddb59719db9bdaffea44aecd5428ad16834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282659"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>使用部署為 Web 服務的 Azure Machine Learning 模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

將 Azure Machine Learning 模型部署為 Web 服務可建立 REST API。 您可以將資料傳送至此 API 並接收模型傳回的預測。 在本文件中，了解如何使用 C#、Go、Java 和 Python 為Web 服務建立用戶端。

將映射部署到 Azure 容器實例、Azure 庫伯奈斯服務或現場可程式設計閘陣列 （FPGA） 時，可以創建 Web 服務。 您會從已註冊的模型及評分檔案建立映像。 您可以使用 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 擷取用來存取 Web 服務的 URI。 如果啟用了身份驗證，也可以使用 SDK 獲取身份驗證金鑰或權杖。

建立使用機器學習 Web 服務的用戶端所適用的一般工作流程是：

1. 使用 SDK 取得連線資訊。
1. 判斷模型所使用之要求資料的類型。
1. 建立一個呼叫 Web 服務的應用程式。

> [!TIP]
> 本文檔中的示例是在不使用 OpenAPI （Swagger） 規範的情況下手動創建的。 如果已為部署啟用了 OpenAPI 規範，則可以使用[諸如"wagger-codegen"](https://github.com/swagger-api/swagger-codegen)等工具為服務創建用戶端庫。

## <a name="connection-information"></a>連線資訊

> [!NOTE]
> 使用 Azure Machine Learning SDK 來取得 Web 服務資訊。 這是 Python SDK。 您可以使用任何語言來建立服務的用戶端。

[azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) 類別可提供建立用戶端所需的資訊。 建立用戶端應用程式時，下列 `Webservice` 屬性很有用：

* `auth_enabled`- 如果啟用了金鑰身份驗證`True`，將和否則， `False`.
* `token_auth_enabled`- 如果啟用了權杖身份驗證`True`，將 和否則， `False`.
* `scoring_uri` - REST API 的位址。
* `swagger_uri`- OpenAPI 規範的位址。 如果啟用了自動架構生成，則此 URI 可用。 有關詳細資訊，請參閱使用[Azure 機器學習部署模型](how-to-deploy-and-where.md)。

有三種方法可以針對已部署的 Web 服務擷取這項資訊：

* 部署模型時，將會傳回 `Webservice` 物件，其中包含服務的相關資訊：

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* 您可以使用 `Webservice.list` 擷取工作區中已針對模型部署的 Web 服務的清單。 您可以新增篩選來縮小傳回的資訊清單。 如需可篩選項目的詳細資訊，請參閱 [Webservice.list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) 參考文件。

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

### <a name="secured-web-service"></a>安全 Web 服務

如果使用 TLS/SSL 憑證保護已部署的 Web 服務，則可以使用[HTTPS](https://en.wikipedia.org/wiki/HTTPS)使用評分或揮霍 URI 連接到服務。 HTTPS 通過加密兩者之間的通信，説明保護用戶端和 Web 服務之間的通信。 加密使用[傳輸層安全 （TLS）。](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS 有時仍稱為*安全通訊端層*（SSL），它是 TLS 的前身。

> [!IMPORTANT]
> Azure 機器學習部署的 Web 服務僅支援 TLS 版本 1.2。 創建用戶端應用程式時，請確保它支援此版本。

有關詳細資訊，請參閱使用[TLS 通過 Azure 機器學習來保護 Web 服務](how-to-secure-web-service.md)。

### <a name="authentication-for-services"></a>服務的身份驗證

Azure 機器學習提供了兩種方法來控制對 Web 服務的訪問。

|驗證方法|Aci|AKS|
|---|---|---|
|Key|預設為停用| 預設已啟用|
|Token| 無法使用| 預設為停用 |

將要求傳送到使用金鑰或權杖保護的服務時，請使用__授權__標頭傳遞金鑰或權杖。 金鑰或權杖必須格式化為`Bearer <key-or-token>`，其中鍵或`<key-or-token>`權杖值。

#### <a name="authentication-with-keys"></a>使用金鑰進行身份驗證

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
> 如果需要重新生成金鑰，請使用[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)。

#### <a name="authentication-with-tokens"></a>使用權杖進行身份驗證

為 Web 服務啟用權杖身份驗證時，使用者必須向 Web 服務提供 Azure 機器學習 JWT 權杖才能訪問它。 

* 預設情況下，當您部署到 Azure 庫伯奈斯服務時，權杖身份驗證將禁用。
* 部署到 Azure 容器實例時不支援權杖身份驗證。

要控制權杖身份驗證，`token_auth_enabled`在創建或更新部署時使用 參數。

如果啟用了權杖身份驗證，則可以使用`get_token`方法檢索無記名權杖，並且權杖的過期時間：

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 您需要在權杖`refresh_by`之後請求新權杖。 

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

例如，[在筆記本內訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)中的模型預期 10 個數字的陣列。 此範例的評分指令碼要求建立 Numpy 陣列，並將其傳遞至模型。 下列範例會示範這項服務所預期的資料：

```json
{
    "data": 
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
            ]
        ]
}
```

Web 服務可以在單一要求中接受多個資料集。 它會傳回一個 JSON 文件，其中包含回應的陣列。

### <a name="binary-data"></a>二進位資料

有關如何啟用對服務中的二進位資料的支援的資訊，請參閱[二進位資料](how-to-deploy-and-where.md#binary)。

### <a name="cross-origin-resource-sharing-cors"></a>跨源資源分享 （CORS）

有關在服務中啟用 CORS 支援的資訊，請參閱[跨源資源分享](how-to-deploy-and-where.md#cors)。

## <a name="call-the-service-c"></a>呼叫服務 (C#)

此範例示範如何使用 C# 呼叫從[在筆記本內訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)範例建立的 Web 服務：

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

此範例示範如何使用 Go 來呼叫從[在筆記本內訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)範例建立的 Web 服務：

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

此範例示範如何使用 Java 來呼叫從[在筆記本內訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)範例建立的 Web 服務：

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

此範例示範如何使用 Python 來呼叫從[在筆記本內訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)範例建立的 Web 服務：

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

## <a name="consume-the-service-from-power-bi"></a>使用來自 Power BI 的服務

Power BI 支援使用 Azure 機器學習 Web 服務，以通過預測來豐富 Power BI 中的資料。 

要生成 Power BI 中支援的 Web 服務，架構必須支援 Power BI 所需的格式。 [瞭解如何創建 Power BI 支援的架構](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#example-entry-script)。

部署 Web 服務後，它可消耗來自 Power BI 資料流程。 [瞭解如何使用 Power BI 的 Azure 機器學習 Web 服務](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。

## <a name="next-steps"></a>後續步驟

要查看用於即時評分 Python 和深度學習模型的參考體系結構，請訪問[Azure 體系結構中心](/azure/architecture/reference-architectures/ai/realtime-scoring-python)。
