---
title: 搭配文字分析 API 使用實體辨識
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用文字分析 REST API，找出並區分在文字中找到的實體身分識別。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 12/15/2020
ms.author: aahi
ms.openlocfilehash: 9b90f177432de11f8281d03021b38bae647dadf2
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562526"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>如何在文字分析中使用命名實體辨識

文字分析 API 可讓您採用非結構化的文字，並傳回一份清楚的實體清單，其中包含網路上詳細資訊的連結。 API 支援多個實體類別的命名實體辨識 (NER) ，以及實體連結。

## <a name="entity-linking"></a>實體連結

實體連結是識別和區分在文字中找到之實體身分識別的能力 (例如，判斷是否出現 "Mars" 一字是指地球，或 war) 的羅馬上帝。 此程式需要以適當的語言存在知識庫，以連結文字中可辨識的實體。 實體連結會使用 [維琪百科](https://www.wikipedia.org/) 作為此知識庫。

## <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)

命名實體辨識 (NER) 能夠識別文字中的不同實體，並將它們分類成預先定義的類別或類型，例如： person、location、event、product 和組織。  

## <a name="personally-identifiable-information-pii"></a>個人識別資訊 (PII)

PII 功能是 NER 的一部分，而且可以識別和修訂與個別人員相關聯之文字中的敏感實體，例如電話號碼、電子郵件地址、郵寄地址、passport 號碼。

## <a name="named-entity-recognition-features-and-versions"></a>命名實體辨識功能和版本

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| 功能                                                         | NER v3。0 | NER 3.1-preview. 3 |
|-----------------------------------------------------------------|--------|----------|
| 單一和批次要求的方法                          | X      | X        |
| 跨多個類別展開實體辨識           | X      | X        |
| 用於傳送實體連結和 NER 要求的不同端點。 | X      | X        |
| 辨識個人 (`PII`) 和健全狀況 (`PHI`) 資訊實體        |        | X        |
| 修訂 `PII`        |        | X        |

請參閱 [語言支援](../language-support.md) 以取得資訊。

命名實體辨識 v3 提供跨多個類型的擴充偵測。 目前，NER v3.0 可以辨識 [一般實體類別](../named-entity-types.md)中的實體。

命名實體辨識 3.1-preview。3包括 v3.0 的偵測功能，以及： 
* 使用端點來偵測個人資訊 (`PII`) 的能力 `v3.1-preview.3/entities/recognition/pii` 。 
* 用 `domain=phi` 來偵測機密健康情況資訊 () 的選擇性參數 `PHI` 。
* 使用端點的[非同步作業](text-analytics-how-to-call-api.md) `/analyze` 。

如需詳細資訊，請參閱下面的「 [實體類別](../named-entity-types.md) 」一文和「 [要求端點](#request-endpoints) 」一節。 

## <a name="sending-a-rest-api-request"></a>傳送 REST API 要求

### <a name="preparation"></a>準備

您必須具有下列格式的 JSON 檔：識別碼、文字、語言。

每份檔都必須使用5120個字元，而且每個集合可以有多達1000個專案 (識別碼) 。 集合會在要求本文中提交。

### <a name="structure-the-request"></a>建立要求結構

建立 POST 要求。 您可以 [使用](text-analytics-how-to-call-api.md) 下列連結中的 Postman 或 **API 測試主控台** ，快速地結構和傳送一個。 

> [!NOTE]
> 您可以在 Azure 入口網站上找到適用於文字分析資源的金鑰和端點。 您可以在 [資源管理] 下的資源 [快速啟動] 頁面中找到。 


### <a name="request-endpoints"></a>要求端點

#### <a name="version-31-preview3"></a>[版本 3.1-preview 3](#tab/version-3-preview)

命名實體辨識 `v3.1-preview.3` 針對 NER、PII 和實體連結要求使用不同的端點。 根據您的要求使用下列 URL 格式。

**實體連結**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/linking`

[命名實體辨識版本 3.1-預覽參考 `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesLinking)

**具名實體辨識**
* 一般實體- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/general`

[命名實體辨識版本 3.1-預覽參考 `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionGeneral)

**個人識別資訊 (PII)**
* 個人 (`PII`) 資訊- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii`

您也可以使用選擇性 `domain=phi` 參數來偵測 `PHI` 文字中) 資訊 (健康情況。 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi`

從開始 `v3.1-preview.3` ，JSON 回應會包含 `redactedText` 屬性，其中包含已修改的輸入文字，其中會 `*` 為實體中的每個字元取代所偵測到的 PII 實體。

[命名實體辨識版本 3.1-預覽參考 `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionPii)

**非同步作業**

從開始 `v3.1-preview.3` ，您可以使用端點以非同步方式傳送 NER 要求 `/analyze` 。

* 非同步作業- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze`

如需有關傳送非同步要求的資訊，請參閱 [如何呼叫文字分析 API](text-analytics-how-to-call-api.md) 。

#### <a name="version-30"></a>[3.0 版](#tab/version-3)

命名實體辨識 v3 針對 NER 和實體連結要求使用不同的端點。 根據您的要求使用下列 URL 格式：

**實體連結**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[的命名實體辨識版本3.0 參考 `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

**具名實體辨識**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[的命名實體辨識版本3.0 參考 `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

設定要求標頭以包含您的文字分析 API 金鑰。 在要求主體中，提供您準備的 JSON 檔。

## <a name="example-requests"></a>範例要求

#### <a name="version-31-preview"></a>[3.1-preview 版](#tab/version-3-preview)

### <a name="example-synchronous-ner-request"></a>同步 NER 要求範例 

下列 JSON 是您可能傳送給 API 的內容範例。 這兩個 API 版本的要求格式是相同的。

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

### <a name="example-asynchronous-ner-request"></a>非同步 NER 要求範例

如果您使用 `/analyze` 端點進行 [非同步作業](text-analytics-how-to-call-api.md)，您將會收到包含您傳送給 API 之工作的回應。

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}
```

#### <a name="version-30"></a>[3.0 版](#tab/version-3)

### <a name="example-synchronous-ner-request"></a>同步 NER 要求範例 

版本3.0 只包含同步操作。 下列 JSON 是您可能傳送給 API 的內容範例。 這兩個 API 版本的要求格式是相同的。

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

---

## <a name="post-the-request"></a>張貼要求

分析會在接收要求時執行。 請參閱概觀中的[資料限制](../overview.md#data-limits)一節，以取得您每分鐘和每秒鐘可以傳送的要求大小和數量資訊。

文字分析 API 是無狀態的。 您的帳戶中不會儲存任何資料，且結果會在回應中立即傳回。

## <a name="view-results"></a>檢視結果

所有 POST 要求都會傳回 JSON 格式的回應，其中包含識別碼和偵測到的實體屬性。

輸出會立即傳回。 您可以將結果串流處理到可接受 JSON 的應用程式，或將輸出儲存到本機系統上的檔案，然後將它匯入能讓您排序、搜尋和操作資料的應用程式。 由於多語系和表情符號的支援，回應可能會包含文字位移。 如需詳細資訊，請參閱 [如何處理文字位移](../concepts/text-offsets.md)。

### <a name="example-responses"></a>範例回應

第3版提供適用于一般 NER、PII 和實體連結的個別端點。 版本 3.1-pareview 包含非同步分析模式。 這些作業的回應如下。 

#### <a name="version-31-preview"></a>[3.1-preview 版](#tab/version-3-preview)

### <a name="synchronous-example-results"></a>同步範例結果

一般 NER 回應的範例：

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

PII 回應的範例：

```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

實體連結回應的範例：

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```

### <a name="example-asynchronous-result"></a>非同步結果範例

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


#### <a name="version-30"></a>[3.0 版](#tab/version-3)

一般 NER 回應的範例：
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

---


## <a name="summary"></a>摘要

在本文中，您已了解在認知服務中使用文字分析的實體連結概念和工作流程。 摘要說明：

* 要求本文中的 JSON 文件包含識別碼、文字和語言代碼。
* POST 要求會傳送至一或多個端點，使用個人化 [存取金鑰和](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) 對您訂用帳戶有效的端點。
* 由連結實體 (包含每個文件識別碼的信賴分數、位移和網頁連結) 組成的回應輸出可用於任何應用程式

## <a name="next-steps"></a>後續步驟

* [文字分析概觀](../overview.md)
* [使用文字分析用戶端程式庫](../quickstarts/client-libraries-rest-api.md)
* [新功能](../whats-new.md)
