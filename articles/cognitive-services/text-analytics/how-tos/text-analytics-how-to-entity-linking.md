---
title: 搭配文字分析 API 使用實體辨識
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用文字分析 REST API 識別並區分文字中找到的實體識別。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 243086ddaae47eba20eea6877fe6d7f8f9889290
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "79203486"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>如何在文字分析中使用已命名的實體識別

文字分析 API 可讓您採用非結構化文字，並傳回可區分的實體清單，並提供網路上詳細資訊的連結。 API 同時支援命名實體識別（NER）和實體連結。

### <a name="entity-linking"></a>實體連結

實體連結能夠識別並區分在文字中找到之實體的身分識別（例如，判斷某個單字`Mars`是否參考到地球，或 war 的上帝）。 此程式需要以適當的語言呈現知識庫，以連結已辨識的實體文字。 實體連結會使用[維琪百科](https://www.wikipedia.org/)做為此知識庫。


### <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)

命名實體辨識（NER）能夠識別文字中的不同實體，並將其分類為預先定義的類別或類型，例如： person、location、event、product 和組織。  

從第3版開始，文字分析 API 的這項功能也可以識別個人和機密資訊類型，例如：電話號碼、社會保險號碼、電子郵件地址和銀行帳戶號碼。  識別這些實體有助於分類機密檔，並校訂個人資訊。

## <a name="named-entity-recognition-versions-and-features"></a>命名實體辨識版本和功能

文字分析 API 提供兩種版本的命名實體辨識-v2 和 v3。 第3版（公開預覽）在可偵測和分類的實體中提供更多詳細資料。

| 功能                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| 單一和批次要求的方法                          | X      | X      |
| 跨數個類別的基底實體識別              | X      | X      |
| 已辨識實體的擴充分類                 |        | X      |
| 用於傳送實體連結和 NER 要求的個別端點。 |        | X      |
| 模型版本設定                                                |        | X      |

如需相關資訊，請參閱[語言支援](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition)。


#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

### <a name="entity-types"></a>實體類型

命名實體辨識 v3 提供跨多個類型的展開偵測。 目前，NER v3 可以辨識下列類別的實體：

* 一般
* 個人資訊 

如需支援的實體和語言的詳細清單，請參閱[NER v3 支援的實體類型](../named-entity-types.md)一文。

### <a name="request-endpoints"></a>要求端點

命名實體辨識 v3 會針對 NER 和實體連結要求使用不同的端點。 根據您的要求使用下列 URL 格式：

NER
* 一般實體-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 個人資訊-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

實體連結
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>模型版本設定

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

### <a name="entity-types"></a>實體類型

> [!NOTE]
> 命名實體識別（NER）第2版僅支援下列實體。 NER v3 處於公開預覽狀態，可大幅擴充文字中辨識之實體的數目和深度。   

| 類型  | 子類型 | 範例 |
|:-----------   |:------------- |:---------|
| 個人        | N/A\*         | "Jeff"、"Bill Gates"     |
| Location      | N/A\*         | "Redmond, Washington"、"Paris"  |
| 組織  | N/A\*         | "Microsoft"   |
| 數量      | 數字        | "6"、"six"     |
| 數量      | 百分比    | "50%"、"fifty percent"|
| 數量      | Ordinal       | "2nd"、"second"     |
| 數量      | Age           | "90 day old"、"30 years old"    |
| 數量      | 貨幣      | "$10.99"     |
| 數量      | 維度     | "10 miles"、"40 cm"     |
| 數量      | 溫度   | "32 degrees"    |
| Datetime      | N/A\*         | "6:30PM February 4, 2012"      |
| Datetime      | Date          | "May 2nd, 2017"、"05/02/2017"   |
| Datetime      | 時間          | "8am"、"8:00"  |
| Datetime      | 日期範圍     | "May 2nd to May 5th"    |
| Datetime      | 時間範圍     | "6pm to 7pm"     |
| Datetime      | Duration      | "1 minute and 45 seconds"   |
| Datetime      | 設定           | "every Tuesday"     |
| URL           | N/A\*         | "HTTPs：\//www.bing.com"    |
| 電子郵件         | N/A\*         | "support@contoso.com" |
| 美國電話號碼  | N/A\*         | （僅限美國電話號碼）"（312） 555-0176" |
| IP 位址    | N/A\*         | 10.0.0.100 |

\* 依輸入和擷取的實體而定，某些實體可能會省略 `SubType`。  列出的所有支援實體類型僅適用于英文、簡體中文、法文、德文和西班牙文等語言。

### <a name="request-endpoints"></a>要求端點

命名實體辨識 v2 會針對 NER 和實體連結要求使用單一端點：

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>傳送 REST API 要求

### <a name="preparation"></a>準備

您必須具有此格式的 JSON 檔：識別碼、文字、語言。

每份檔都必須是5120個字元，而且每個集合最多可以有1000個專案（Id）。 集合會在要求本文中提交。

### <a name="structure-the-request"></a>建立要求結構

建立 POST 要求。 您可以[使用](text-analytics-how-to-call-api.md)下列連結中的 Postman 或**API 測試主控台**，快速地結構並傳送一個。 

> [!NOTE]
> 您可以在 Azure 入口網站上找到適用於文字分析資源的金鑰和端點。 您可以在 [資源管理]  下的資源 [快速啟動]  頁面中找到。 

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

[命名實體辨識 v3 參考](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

第3版針對 NER 和實體連結要求使用不同的端點。 根據您的要求使用下列 URL 格式：

NER
* 一般實體-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 個人資訊實體-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

實體連結
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

[命名實體識別（NER） v2 參考](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

第2版會針對實體連結和 NER 要求使用下列端點： 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

設定要求標頭以包含您的文字分析 API 金鑰。 在 [要求本文] 中，提供您準備的 JSON 檔。

### <a name="example-ner-request"></a>範例 NER 要求 

以下是您可能會傳送至 API 的內容範例。 這兩個 API 版本的要求格式是相同的。

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

所有 POST 要求都會傳回 JSON 格式的回應，其中包含識別碼和偵測到的實體屬性。

輸出會立即傳回。 您可以將結果串流處理到可接受 JSON 的應用程式，或將輸出儲存到本機系統上的檔案，然後將它匯入能讓您排序、搜尋和操作資料的應用程式。 由於多語系和表情符號的支援，回應可能會包含文字位移。 如需詳細資訊，請參閱[如何處理文字位移](../concepts/text-offsets.md)。

#### <a name="version-30-preview"></a>[版本 3.0-preview）](#tab/version-3)

### <a name="example-v3-responses"></a>範例 v3 回應

第3版為 NER 和實體連結提供個別的端點。 這兩項作業的回應如下所示。 

#### <a name="example-ner-response"></a>範例 NER 回應

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

#### <a name="example-entity-linking-response"></a>範例實體連結回應

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

### <a name="example-ner-v2-response"></a>範例 NER v2 回應
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

## <a name="summary"></a>[摘要]

在本文中，您已了解在認知服務中使用文字分析的實體連結概念和工作流程。 摘要說明：

* 在兩個版本中，已針對選取的語言提供命名實體辨識。
* 要求本文中的 JSON 文件包含識別碼、文字和語言代碼。
* POST 要求會傳送至一或多個端點，並使用個人化[存取金鑰和](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)適用于您訂用帳戶的端點。
* 由連結實體 (包含每個文件識別碼的信賴分數、位移和網頁連結) 組成的回應輸出可用於任何應用程式

## <a name="next-steps"></a>後續步驟

* [文字分析概觀](../overview.md)
* [使用文字分析用戶端程式庫](../quickstarts/text-analytics-sdk.md)
* [新功能](../whats-new.md)
