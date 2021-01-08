---
title: 快速入門：適用於 .NET 的表單辨識器用戶端程式庫
description: 使用適用於 .NET 的表單辨識器用戶端程式庫建立表單處理應用程式，從您的自訂文件中擷取索引鍵/值組和資料表資料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: a677623c030ff26567f14a4526f6d07bc6631a37
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/01/2021
ms.locfileid: "97853704"
---
> [!NOTE]
> 本指南使用 cURL 執行 REST API 呼叫。 [GitHub上還有範例程式碼](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/FormRecognizer/rest)，說明如何使用 Python 呼叫 REST API。

## <a name="prerequisites"></a>必要條件

* 已安裝 [cURL](https://curl.haxx.se/windows/)。
* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 包含一組訓練資料的 Azure 儲存體 Blob。 請參閱[為自訂模型建置訓練資料集](../../build-training-data-set.md) (機器翻譯)，以獲得產生訓練資料集的提示和選項。 在本快速入門中，您可使用 [範例資料集](https://go.microsoft.com/fwlink/?linkid=2090451)的 **Train** 資料夾底下的檔案 (下載 *sample_data.zip* 並將其解壓縮)。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="建立表單辨識器資源"  target="_blank">建立表單辨識器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至表單辨識器 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
* 收據影像的 URL。 您可以使用本快速入門的[範例影像](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg)。
* 名片影像的 URL。 您可以使用本快速入門的[範例影像](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg)。
* 發票影像的 URL。 您可以使用本快速入門的[範例文件](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf)。


## <a name="recognize-form-content"></a>辨識表單內容

您可以使用表單辨識器來辨識並擷取文件中的資料表、行和字組，而不需要訓練模型。 執行命令之前，請進行下列變更：

1. 將 `{Endpoint}` 取代為您使用表單辨識器訂用帳戶取得的端點。
1. 將 `{subscription key}` 取代為您在先前的步驟中複製的訂用帳戶金鑰。
1. 將要求主體中的 URL 取代為其中一個範例 URL。

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v -X POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{\"source\": \"http://example.com/test.jpg\"}" 
```

# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)    

```bash
curl -v -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyze"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{\"source\": \"http://example.com/test.jpg\"}" 
```
---

您會收到 `202 (Success)` 回應，其中包含 **Operation-Location** 標頭。 此標頭的值會包含可用來查詢非同步作業狀態並取得結果的作業識別碼。 在下列範例中，`analyzeResults/` 之後的字串就是作業識別碼。

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>取得配置結果

呼叫 **[分析配置](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)** API 後，您可以呼叫 **[取得分析配置結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult)** API 來取得作業狀態並擷取資料。 執行命令之前，請進行下列變更：

1. 將 `{Endpoint}` 取代為您使用表單辨識器訂用帳戶取得的端點。
1. 將 `{subscription key}` 取代為您在先前的步驟中複製的訂用帳戶金鑰。
1. 將 `{resultId}` 取代為先前步驟中的作業識別碼。

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)  
```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

### <a name="examine-the-results"></a>檢查結果

您會收到 `200 (success)` 回應及 JSON 內容。

請參閱下列發票影像和其對應的 JSON 輸出。 為了簡單起見，我們已將輸出內容縮短。 `"readResults"` 節點包含每一行文字，以及各自的周框方塊在頁面上的位置。 `"selectionMarks"` 節點 (在 v2.1 預覽中) 會顯示每個選取標記 (核取方塊、選項標記)，以及其狀態是「已選取」還是「未選取」。 `"pageResults"` 欄位會顯示資料表內的每一段文字，且各自會有其資料列資料行座標。

:::image type="content" source="../../media/contoso-invoice.png" alt-text="具有資料表的 Contoso 專案陳述文件。":::

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)   
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                        ]
                    }
                ],
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
``` 

---

## <a name="recognize-receipts"></a>辨識收據

若要開始分析收據，請使用下列 cURL 命令呼叫 **[分析收據](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeReceiptAsync)** API。 執行命令之前，請進行下列變更：

1. 將 `{Endpoint}` 取代為您使用表單辨識器訂用帳戶取得的端點。
1. 將 `{your receipt URL}` 取代為收據影像的 URL 位址。
1. 將 `{subscription key>` 取代為您在先前的步驟中複製的訂用帳戶金鑰。

# <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \"{your receipt URL}\"}"
```

# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)    

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \"{your receipt URL}\"}"
```
---

您會收到 `202 (Success)` 回應，其中包含 **Operation-Location** 標頭。 此標頭的值會包含可用來查詢非同步作業狀態並取得結果的作業識別碼。 在下列範例中，`operations/` 之後的字串就是作業識別碼。

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>取得收據結果

呼叫 **分析收據** API 之後，您可以呼叫 **[取得分析收據結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeReceiptResult)** API 來取得作業狀態並擷取資料。 執行命令之前，請進行下列變更：

1. 將 `{Endpoint}` 取代為您使用表單辨識器訂用帳戶金鑰取得的端點。 您可以在表單辨識器的資源 [概觀] 索引標籤上找到此項目。
1. 將 `{operationId}` 取代為先前步驟中的作業識別碼。
1. 將 `{subscription key}` 取代為訂用帳戶金鑰。

# <a name="v20"></a>[v2.0](#tab/v2-0)  
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)  
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

### <a name="examine-the-response"></a>檢查回應

您會收到 `200 (Success)` 回應及 JSON 輸出。 第一個 `"status"` 欄位會指出作業的狀態。 如果作業已完成，`"readResults"` 欄位會包含擷取自收據的每一行文字，而 `"documentResults"` 欄位會包含收據中最相關部分的索引鍵/值資訊。 如果作業未完成，`"status"` 的值將會是 `"running"` 或 `"notStarted"`，此時您應該以手動方式或透過指令碼再次呼叫 API。 我們建議您在每個呼叫之前間隔一秒以上的時間。

請參閱下列收據影像和其對應的 JSON 輸出。 為了方便閱讀，我們已將輸出縮短。

![來自 Contoso 商店的收據](../../media/contoso-allinone.jpg)

`"readResults"` 節點包含所有已辨識的文字 (如果您將選用的 *includeTextDetails* 參數設定為 `true`)。 文字會依頁面彙整，然後依文字行，再依個別字組彙整。 `"documentResults"` 節點包含此模型所探索到的收據專屬值。 您可在這裡找到有用的索引鍵/值組，例如稅額、總計、商家位址等等。

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="recognize-business-cards"></a>辨識名片

# <a name="v20"></a>[v2.0](#tab/v2-0)  

> [!IMPORTANT]
> 選取的 API 版本不提供這項功能。

# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)  

若要開始分析名片，請使用下面的 cURL 命令呼叫 **[分析名片](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)** API。 執行命令之前，請進行下列變更：

1. 將 `{Endpoint}` 取代為您使用表單辨識器訂用帳戶取得的端點。
1. 將 `{your receipt URL}` 取代為收據影像的 URL 位址。
1. 將 `{subscription key}` 取代為您在先前的步驟中複製的訂用帳戶金鑰。

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \"{your receipt URL}\"}"
```

您會收到 `202 (Success)` 回應，其中包含 **Operation-Location** 標頭。 此標頭的值會包含可用來查詢非同步作業狀態並取得結果的作業識別碼。

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>取得名片結果

呼叫 **分析名片** API 之後，您可以呼叫 **[取得分析名片結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult)** API 來取得作業狀態並擷取資料。 執行命令之前，請進行下列變更：

1. 將 `{Endpoint}` 取代為您使用表單辨識器訂用帳戶金鑰取得的端點。 您可以在表單辨識器的資源 [概觀] 索引標籤上找到此項目。
1. 將 `{resultId}` 取代為先前步驟中的作業識別碼。
1. 將 `{subscription key}` 取代為訂用帳戶金鑰。

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>檢查回應

您會收到 `200 (Success)` 回應及 JSON 輸出。 `"readResults"` 節點包含所有已辨識的文字。 文字會依頁面彙整，然後依文字行，再依個別字組彙整。 `"documentResults"` 節點包含此模型所探索到的名片專屬值。 您可以在這裡找到有用的資訊，例如公司名稱、名字、姓氏、電話號碼等等。

![Contoso 公司的名片](../../media/business-card-english.jpg)

這個範例說明表單辨識器所傳回的 JSON 輸出。 為了方便閱讀，此值已截斷。

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

指令碼會將回應輸出到主控台，直到 **分析名片** 作業完成。

---

## <a name="recognize-invoices"></a>辨識發票

# <a name="version-20"></a>[版本 2.0](#tab/v2-0)

> [!IMPORTANT]
> 選取的 API 版本不提供這項功能。

# <a name="version-21-preview"></a>[版本 2.1 預覽](#tab/v2-1)

若要開始分析發票，請使用下列 cURL 命令呼叫 **[分析發票](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)** API。 執行命令之前，請進行下列變更：

1. 將 `{Endpoint}` 取代為您使用表單辨識器訂用帳戶取得的端點。
1. 將 `{your invoice URL}` 取代為發票文件 的 URL 位址。
1. 將 `{subscription key}` 取代為訂用帳戶金鑰。

```bash
curl -v -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{ \"source\": \"{your invoice URL}\"}"
```

您會收到 `202 (Success)` 回應，其中包含 **Operation-Location** 標頭。 此標頭的值會包含可用來查詢非同步作業狀態並取得結果的作業識別碼。

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>取得發票結果

呼叫 **分析發票** API 之後，您可以呼叫 **[取得分析發票結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83)** API 來取得作業狀態並擷取資料。 執行命令之前，請進行下列變更：

1. 將 `{Endpoint}` 取代為您使用表單辨識器訂用帳戶金鑰取得的端點。 您可以在表單辨識器的資源 [概觀] 索引標籤上找到此項目。
1. 將 `{resultId}` 取代為先前步驟中的作業識別碼。
1. 將 `{subscription key}` 取代為訂用帳戶金鑰。

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>檢查回應

您會收到 `200 (Success)` 回應及 JSON 輸出。 `"readResults"` 欄位會包含擷取自發票的每一行文字，`"pageResults"` 欄位會包含擷取自發票的資料表和選取標記，而 `"documentResults"` 會包含發票中最相關部分的索引鍵/值資訊。

請參閱下列發票文件和其對應的 JSON 輸出。 為了方便閱讀，我們已將 JSON 內容縮短。

* [發票範例](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...                
            }
        }]
    }
}
```

---

## <a name="train-a-custom-model"></a>定型自訂模型

若要將自訂模型定型，您需要一組 Azure 儲存體 Blob 中的定型資料。 您至少要有五個類型/結構相同的已填妥表單 (PDF 文件和/或影像) 作為主要輸入資料。 或者，您可以使用單一空白表單和兩個已填寫的表單。 空白表單的檔案名稱必須包含「空白」一詞。 請參閱[為自訂模型建置訓練資料集](../../build-training-data-set.md)，以獲得如何將訓練資料放在一起的祕訣和選項。

> [!NOTE]
> 您可以使用標記資料功能，事先手動為部分或所有定型資料加上標籤。 這是更複雜的程序，但可產生較佳的定型模型。 若要深入了解此功能，請參閱概觀的[以標籤定型](../../overview.md#train-with-labels)一節。

若要使用 Azure Blob 容器中的文件來定型表單辨識器模型，請執行下列 cURL 命令以呼叫 **[定型自訂模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API。 執行命令之前，請進行下列變更：

1. 將 `{Endpoint}` 取代為您使用表單辨識器訂用帳戶取得的端點。
1. 將 `{subscription key}` 取代為您在先前的步驟中複製的訂用帳戶金鑰。
1. 將 `{SAS URL}` 取代為 Azure Blob 儲存體容器的共用存取簽章 (SAS) URL。 [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="擷取 SAS URL":::

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \""{SAS URL}"\"}"
```
# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)    
```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \""{SAS URL}"\"}"
```

---


您會收到含有 **位置** 標頭的 `201 (Success)` 回應。 此標頭的值是要定型之新模型的識別碼。

### <a name="get-training-results"></a>取得定型結果

哀使進行定型作業後，您可以使用新的作業 ( **[取得自訂模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** ) 來檢查定型狀態。 請將模型識別碼傳入此 API 呼叫以檢查定型狀態：

1. 將 `{Endpoint}` 取代為您使用表單辨識器訂用帳戶金鑰取得的端點。
1. 將 `{subscription key}` 取代為訂用帳戶金鑰
1. 將 `{model ID}` 取代為您在先前的步驟中取得的模型識別碼


# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
    
---

您將會收到 `200 (Success)` 回應，內含下列格式的 JSON 本文。 請留意 `"status"` 欄位。 定型完成後，其值將是 `"ready"`。 如果模型未完成定型，您必須重新執行命令，以重新查詢服務。 我們建議您在每個呼叫之前間隔一秒以上的時間。

`"modelId"` 欄位包含您要定型之模型的識別碼。 下一個步驟將需要這項資料。

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>使用自訂模型分析表單

接下來，您會使用新定型的模型來分析文件，並從中擷取索引鍵/值組和資料表。 執行下列 cURL 命令，以呼叫 **[分析表單](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** API。 執行命令之前，請進行下列變更：

1. 將 `{Endpoint}` 取代為您從表單辨識器訂用帳戶金鑰中取得的端點。 您可以在表單辨識器的資源 [概觀]  索引標籤上找到此項目。
1. 將 `{model ID}` 取代為您在上一節中取得的模型識別碼。
1. 將 `{SAS URL}` 取代為您在 Azure 儲存體中的檔案的 SAS URL。 請依照「訓練」一節中的步驟操作，但不要取得整個 Blob 容器的 SAS URL，而是取得您要分析之特定檔案的 URL。
1. 將 `{subscription key}` 取代為訂用帳戶金鑰。

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ \"source\": \""{SAS URL}"\" } "
```

# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)    
```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ \"source\": \""{SAS URL}"\" } "
```
    
---



您會收到 `202 (Success)` 回應，其中包含 **Operation-Location** 標頭。 此標頭的值會包含您用來追蹤分析作業結果的結果識別碼。 請儲存此結果識別碼以供下一個步驟使用。

### <a name="get-the-analyze-results"></a>取得分析結果

使用下列 API 來查詢分析作業的結果。

1. 將 `{Endpoint}` 取代為您從表單辨識器訂用帳戶金鑰中取得的端點。 您可以在表單辨識器的資源 [概觀]  索引標籤上找到此項目。
1. 將 `{result ID}` 取代為您在上一節中取得的識別碼。
1. 將 `{subscription key}` 取代為訂用帳戶金鑰。

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

您將會收到 `200 (Success)` 回應，內含下列格式的 JSON 本文。 為了簡單起見，我們已將輸出內容縮短。 請留意底部附近的 `"status"` 欄位。 當分析作業完成時，其值將是 `"succeeded"`。 如果分析作業未完成，您必須重新執行命令，以重新查詢服務。 我們建議您在每個呼叫之前間隔一秒以上的時間。

主要索引鍵/值組的關聯和資料表位於 `"pageResults"` 節點中。 如果您也透過 *includeTextDetails* URL 參數指定了純文字擷取，則 `"readResults"` 節點會顯示文件中所有文字的內容和位置。

為了簡單起見，此範例 JSON 輸出已縮短。

# <a name="v20"></a>[v2.0](#tab/v2-0)
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          }, 
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```    
# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)    
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          }, 
          ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
``` 
---

### <a name="improve-results"></a>改善結果

[!INCLUDE [improve results](../improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>管理自訂模型

### <a name="get-a-list-of-custom-models"></a>取得自訂模型的清單

使用下列命令傳回屬於您訂用帳戶之所有自訂模型的清單。

1. 將 `{Endpoint}` 取代為您使用表單辨識器訂用帳戶取得的端點。
1. 將 `{subscription key}` 取代為您在先前的步驟中複製的訂用帳戶金鑰。

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

您會收到 `200` 成功回應，其中包含如下所示的 JSON 資料。 `"modelList"` 元素包含所有已建立的模型及相關資訊。

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>取得特定模型

若要取出特定自訂模型的詳細資訊，請使用下列命令。

1. 將 `{Endpoint}` 取代為您使用表單辨識器訂用帳戶取得的端點。
1. 將 `{subscription key}` 取代為您在先前的步驟中複製的訂用帳戶金鑰。
1. 以想要查閱的自訂模型識別碼取代 `{modelId}`。

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{body}" 
```

# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{modelId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{body}" 
```
---

您會收到 `200` 成功回應，其中包含如下所示的 JSON 資料。

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您使用了表單辨識器 REST API，以不同方式將模型定型並分析表單。 接下來，請參閱參考文件來深入探索表單辨識器 API。

> [!div class="nextstepaction"]
> [REST API 參考文件](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)

* [什麼是表單辨識器？](../../overview.md)
