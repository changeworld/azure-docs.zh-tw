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
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 457be5ac014fda6b4984ed7af3dcc89780b16379
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141612"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>如何在文字分析中使用已命名的實體識別

文字分析 API 可讓您採用非結構化文字，並傳回可區分的實體清單，並提供網路上詳細資訊的連結。 API 同時支援命名實體識別（NER）和實體連結。

### <a name="entity-linking"></a>實體連結

實體連結能夠識別並區分在文字中找到之實體的身分識別（例如，判斷 "Mars" 一詞是指的是地球的出現，還是上帝的 war）。 此程式需要以適當的語言呈現知識庫，以連結已辨識的實體文字。 實體連結會使用[維琪百科](https://www.wikipedia.org/)做為此知識庫。


### <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)

命名實體辨識（NER）能夠識別文字中的不同實體，並將其分類為預先定義的類別或類型，例如： person、location、event、product 和組織。  

## <a name="named-entity-recognition-versions-and-features"></a>命名實體辨識版本和功能

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| 功能                                                         | NER v3。0 | NER 3.1-preview. 1 |
|-----------------------------------------------------------------|--------|----------|
| 單一和批次要求的方法                          | X      | X        |
| 跨數個類別的擴充實體識別           | X      | X        |
| 用於傳送實體連結和 NER 要求的個別端點。 | X      | X        |
| 個人（ `PII` ）和健全狀況（ `PHI` ）資訊實體的辨識        |        | X        |

如需相關資訊，請參閱[語言支援](../language-support.md)。

### <a name="entity-types"></a>實體類型

命名實體辨識 v3 提供跨多個類型的展開偵測。 目前，NER v3.0 可以辨識[一般實體類別](../named-entity-types.md)中的實體。

名為 Entity 辨識 3.1-preview。1包含 v3.0 的偵測功能，以及使用端點偵測個人資訊（）的功能。 `PII` `v3.1-preview.1/entities/recognition/pii` 您可以使用選擇性 `domain=phi` 參數來偵測機密的健全狀況資訊（ `PHI` ）。 如需詳細資訊，請參閱[實體分類](../named-entity-types.md)一文和[要求端點](#request-endpoints)一節。


## <a name="sending-a-rest-api-request"></a>傳送 REST API 要求

### <a name="preparation"></a>準備

您必須具有此格式的 JSON 檔：識別碼、文字、語言。

每份檔都必須是5120個字元，而且每個集合最多可以有1000個專案（Id）。 集合會在要求本文中提交。

### <a name="structure-the-request"></a>建立要求結構

建立 POST 要求。 您可以[使用](text-analytics-how-to-call-api.md)下列連結中的 Postman 或**API 測試主控台**，快速地結構並傳送一個。 

> [!NOTE]
> 您可以在 Azure 入口網站上找到適用於文字分析資源的金鑰和端點。 您可以在 [資源管理]  下的資源 [快速啟動]  頁面中找到。 


### <a name="request-endpoints"></a>要求端點

#### <a name="version-30"></a>[3.0 版](#tab/version-3)

命名實體辨識 v3 會針對 NER 和實體連結要求使用不同的端點。 根據您的要求使用下列 URL 格式：

實體連結
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

#### <a name="version-31-preview1"></a>[版本 3.1-preview 1](#tab/version-3-preview)

命名實體識別會 `v3.1-preview.1` 針對 NER 和實體連結要求使用不同的端點。 根據您的要求使用下列 URL 格式：

實體連結
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/linking`

NER
* 一般實體-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/general`

* Personal （ `PII` ）資訊-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii`

您也可以使用選擇性 `domain=phi` 參數來偵測文字中的健全狀況（ `PHI` ）資訊。 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi`

---

設定要求標頭以包含您的文字分析 API 金鑰。 在 [要求本文] 中，提供您準備的 JSON 檔。

### <a name="example-ner-request"></a>範例 NER 要求 

以下是您可能會傳送至 API 的內容範例。 這兩個 API 版本的要求格式是相同的。

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

## <a name="post-the-request"></a>張貼要求

分析會在接收要求時執行。 請參閱概觀中的[資料限制](../overview.md#data-limits)一節，以取得您每分鐘和每秒鐘可以傳送的要求大小和數量資訊。

文字分析 API 是無狀態的。 您的帳戶中不會儲存任何資料，且結果會在回應中立即傳回。

## <a name="view-results"></a>檢視結果

所有 POST 要求都會傳回 JSON 格式的回應，其中包含識別碼和偵測到的實體屬性。

輸出會立即傳回。 您可以將結果串流處理到可接受 JSON 的應用程式，或將輸出儲存到本機系統上的檔案，然後將它匯入能讓您排序、搜尋和操作資料的應用程式。 由於多語系和表情符號的支援，回應可能會包含文字位移。 如需詳細資訊，請參閱[如何處理文字位移](../concepts/text-offsets.md)。

### <a name="example-v3-responses"></a>範例 v3 回應

第3版為 NER 和實體連結提供個別的端點。 這兩項作業的回應如下所示。 

#### <a name="example-ner-response"></a>範例 NER 回應

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


#### <a name="example-entity-linking-response"></a>範例實體連結回應

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
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


## <a name="summary"></a>摘要

在本文中，您已了解在認知服務中使用文字分析的實體連結概念和工作流程。 摘要說明：

* 要求本文中的 JSON 文件包含識別碼、文字和語言代碼。
* POST 要求會傳送至一或多個端點，並使用個人化[存取金鑰和](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)適用于您訂用帳戶的端點。
* 由連結實體 (包含每個文件識別碼的信賴分數、位移和網頁連結) 組成的回應輸出可用於任何應用程式

## <a name="next-steps"></a>後續步驟

* [文字分析概觀](../overview.md)
* [使用文字分析用戶端程式庫](../quickstarts/text-analytics-sdk.md)
* [新功能](../whats-new.md)
