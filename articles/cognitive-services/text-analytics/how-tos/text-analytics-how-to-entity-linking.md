---
title: 搭配文字分析 API 使用實體辨識
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用文本分析 REST API 識別和消除文本中找到的實體的標識。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 243086ddaae47eba20eea6877fe6d7f8f9889290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79203486"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>如何在文本分析中使用命名實體識別

文本分析 API 允許您獲取非結構化文本，並返回消除歧義的實體清單，並連結到 Web 上的詳細資訊。 API 支援命名實體識別 （NER） 和實體連結。

### <a name="entity-linking"></a>實體連結

實體連結是識別和消除文本中找到實體身份的能力（例如，確定單詞`Mars`的出現是指地球還是羅馬戰爭之神）。 此過程要求以適當語言存在知識庫，以在文本中連結已識別的實體。 實體連結使用[維琪百科](https://www.wikipedia.org/)作為這個知識庫。


### <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)

命名實體識別 （NER） 是識別文本中不同實體並將其分類為預定義的類或類型（如：人員、位置、事件、產品和組織）的能力。  

從第 3 版開始，文本分析 API 的此功能還可以識別個人和敏感資訊類型，例如：電話號碼、社會保險號、電子郵件地址和銀行帳號。  識別這些實體有助於對敏感文檔進行分類，並編輯個人資訊。

## <a name="named-entity-recognition-versions-and-features"></a>命名實體識別版本和功能

文本分析 API 提供兩個版本的命名實體識別 - v2 和 v3。 版本 3（公共預覽版）提供了可檢測和分類的實體中增加的詳細資訊。

| 功能                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| 單一和批次要求的方法                          | X      | X      |
| 跨多個類別的基底實體識別              | X      | X      |
| 已確認實體的擴展分類                 |        | X      |
| 用於發送實體連結和 NER 請求的單獨終結點。 |        | X      |
| 模型版本設定                                                |        | X      |

有關資訊，請參閱[語言支援](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition)。


#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

### <a name="entity-types"></a>實體類型

命名實體識別 v3 提供跨多種類型的擴展檢測。 目前，NER v3 可以識別以下類別的實體：

* 一般
* 個人資訊 

有關支援實體和語言的詳細清單，請參閱 NER [v3 支援的實體類型](../named-entity-types.md)文章。

### <a name="request-endpoints"></a>請求終結點

命名實體識別 v3 對 NER 和實體連結請求使用單獨的終結點。 根據您的請求使用以下 URL 格式：

內爾
* 一般實體 -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 個人資訊 -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

實體連結
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>模型版本設定

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

### <a name="entity-types"></a>實體類型

> [!NOTE]
> 命名實體識別 （NER） 版本 2 僅支援以下實體。 NER v3 處於公共預覽版中，並極大地擴展了文本中識別的實體的數量和深度。   

| 類型  | 子類型 | 範例 |
|:-----------   |:------------- |:---------|
| 個人        | N/A\*         | "Jeff"、"Bill Gates"     |
| Location      | N/A\*         | "Redmond, Washington"、"Paris"  |
| 組織  | N/A\*         | "Microsoft"   |
| 數量      | Number        | "6"、"six"     |
| 數量      | 百分比    | "50%"、"fifty percent"|
| 數量      | Ordinal       | "2nd"、"second"     |
| 數量      | Age           | "90 day old"、"30 years old"    |
| 數量      | 貨幣      | "$10.99"     |
| 數量      | 維度     | "10 miles"、"40 cm"     |
| 數量      | 溫度   | "32 degrees"    |
| Datetime      | N/A\*         | "6:30PM February 4, 2012"      |
| Datetime      | Date          | "May 2nd, 2017"、"05/02/2017"   |
| Datetime      | Time          | "8am"、"8:00"  |
| Datetime      | 日期範圍     | "May 2nd to May 5th"    |
| Datetime      | 時間範圍     | "6pm to 7pm"     |
| Datetime      | Duration      | "1 minute and 45 seconds"   |
| Datetime      | 設定           | "every Tuesday"     |
| URL           | N/A\*         | "HTTPs：\//www.bing.com"    |
| 電子郵件         | N/A\*         | "support@contoso.com" |
| 美國電話號碼  | N/A\*         | （僅限美國電話號碼）"(312) 555-0176" |
| IP 位址    | N/A\*         | "10.0.0.100" |

\* 依輸入和擷取的實體而定，某些實體可能會省略 `SubType`。  列出的所有支援的實體類型僅適用于英語、中文、法語、德語和西班牙文。

### <a name="request-endpoints"></a>請求終結點

命名實體識別 v2 使用 NER 和實體連結請求的單個終結點：

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>傳送 REST API 要求

### <a name="preparation"></a>準備

您必須具有此格式的 JSON 文檔：ID、文本、語言。

每個文檔必須低於 5，120 個字元，並且每個集合最多可以有 1，000 個項 （ID）。 集合會在要求本文中提交。

### <a name="structure-the-request"></a>建立要求結構

建立 POST 要求。 您可以在以下連結中使用[Postman](text-analytics-how-to-call-api.md)或**API 測試主控台**來快速構建和發送一個。 

> [!NOTE]
> 您可以在 Azure 入口網站上找到適用於文字分析資源的金鑰和端點。 您可以在 [資源管理]**** 下的資源 [快速啟動]**** 頁面中找到。 

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

[命名實體識別 v3 引用](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

版本 3 對 NER 和實體連結請求使用單獨的終結點。 根據您的請求使用以下 URL 格式：

內爾
* 一般實體 -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 個人資訊實體 -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

實體連結
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

[命名實體識別 （NER） v2 引用](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

版本 2 使用以下終結點進行實體連結和 NER 請求： 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

設定要求標頭以包含您的文字分析 API 金鑰。 在請求正文中，提供您準備的 JSON 文檔。

### <a name="example-ner-request"></a>示例 NER 請求 

下面是可能發送到 API 的內容的示例。 這兩個 API 版本的要求格式是相同的。

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>張貼要求

分析會在接收要求時執行。 請參閱概觀中的[資料限制](../overview.md#data-limits)一節，以取得您每分鐘和每秒鐘可以傳送的要求大小和數量資訊。

文字分析 API 是無狀態的。 您的帳戶中不會儲存任何資料，且結果會在回應中立即傳回。

## <a name="view-results"></a>檢視結果

所有 POST 請求都返回帶有 DID 和檢測到的實體屬性的 JSON 格式化的回應。

輸出會立即傳回。 您可以將結果串流處理到可接受 JSON 的應用程式，或將輸出儲存到本機系統上的檔案，然後將它匯入能讓您排序、搜尋和操作資料的應用程式。 由於多語言和表情符號支援，回應可能包含文本偏移。 [有關詳細資訊，請參閱如何處理文本偏移](../concepts/text-offsets.md)。

#### <a name="version-30-preview"></a>[版本 3.0 預覽版）](#tab/version-3)

### <a name="example-v3-responses"></a>示例 v3 回應

版本 3 為 NER 和實體連結提供了單獨的終結點。 下面列出了兩個操作的回應。 

#### <a name="example-ner-response"></a>NER 回應示例

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>連結回應的示例實體

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

### <a name="example-ner-v2-response"></a>示例 NER v2 回應
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

---

## <a name="summary"></a>總結

在本文中，您已了解在認知服務中使用文字分析的實體連結概念和工作流程。 摘要說明：

* 命名實體識別可用於兩個版本的選定語言。
* 要求本文中的 JSON 文件包含識別碼、文字和語言代碼。
* POST 請求使用個人化[訪問金鑰和](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)對訂閱有效的終結點發送到一個或多個終結點。
* 由連結實體 (包含每個文件識別碼的信賴分數、位移和網頁連結) 組成的回應輸出可用於任何應用程式

## <a name="next-steps"></a>後續步驟

* [文字分析概觀](../overview.md)
* [使用文字分析用戶端程式庫](../quickstarts/text-analytics-sdk.md)
* [新功能](../whats-new.md)
