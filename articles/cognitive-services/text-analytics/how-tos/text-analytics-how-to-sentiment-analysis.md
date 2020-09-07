---
title: 使用文字分析 REST API 執行情感分析
titleSuffix: Azure Cognitive Services
description: 本文將說明如何使用 Azure 認知服務文字分析 REST API 來偵測文字中的情感。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 08/25/2020
ms.author: aahi
ms.openlocfilehash: a0557c3ccf6510ab3ee2ae29cbef1fc754473345
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933013"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>如何：使用文字分析 API 來偵測情感

文字分析 API 的情感分析功能會評估文字，並傳回每個句子的情感分數和標籤。 此功能可用來偵測社交媒體、客戶評價及討論論壇中的正面和負面情感。 API 所使用的 AI 模型由服務所提供，您只需要傳送要分析的內容即可。

傳送情感分析要求之後，API 會傳回情感標籤 (例如「負面」、「中性」和「正面」)，以及句子和文件層級的信賴分數。

情感分析支援多種語言，且預覽中支援的語言更多。 如需詳細資訊，請參閱[支援的語言](../text-analytics-supported-languages.md)。

## <a name="sentiment-analysis-versions-and-features"></a>情感分析版本和功能

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| 功能                                   | 情感分析 v3 | 情感分析 v3.1 (預覽) |
|-------------------------------------------|-----------------------|-----------------------------------|
| 單一和批次要求的方法    | X                     | X                                 |
| 情感分數和標籤             | X                     | X                                 |
| 以 Linux 為基礎的 [Docker 容器](text-analytics-how-to-install-containers.md) | X  |  |
| 意見挖掘                            |                       | X                                 |

### <a name="sentiment-scoring-and-labeling"></a>情感評分和標籤

v3 中的情感分析會將情感標籤套用至文字 (在句子和文件層級傳回)，每個都有信賴分數。 

標籤分別為「正面」、「負面」和「中立」。 在文件層級也可能傳回「混合」的情感標籤。 文件情感的判定方式如下：

| 句子情感                                                                            | 傳回的文件標籤 |
|-----------------------------------------------------------------------------------------------|-------------------------|
| 文件中至少有一個 `positive` 句子。 其餘句子皆為 `neutral`。 | `positive`              |
| 文件中至少有一個 `negative` 句子。 其餘句子皆為 `neutral`。 | `negative`              |
| 文件中至少有一個 `negative` 句子和至少一個 `positive` 句子。    | `mixed`                 |
| 文件中的所有句子皆為 `neutral`。                                                  | `neutral`               |

信賴分數的範圍是從 1 到 0。 接近 1 的分數表示標籤分類的信賴度更高，而較低的分數則表示較低的信賴度。 針對每個檔案或每個句子，與標籤 (正面、負面和中性) 相關聯的預測分數最多會加到 1 為止。

### <a name="opinion-mining"></a>意見挖掘

意見挖掘是情感分析的功能，從版本 3.1-preview.1 開始。 這項功能也稱為自然語言處理 (NLP) 中的層面型情感分析，可提供文字中與各層面相關意見的詳細資訊 (例如產品或服務的屬性)。

例如，如果客戶留下關於飯店的意見反應，例如「房間很棒，但員工很不友善」，則意見挖掘將會找出文字中的各個層面，以及相關聯的意見和情緒：

| 層面 | 意見    | 情感 |
|--------|------------|-----------|
| 房間   | 很棒      | 正面  |
| staff  | 不友善 | 負面  |

若要在結果中進行意見挖掘，您必須在情感分析的要求中包含 `opinionMining=true` 旗標。 意見挖掘結果會包含在情感分析回應中。

## <a name="sending-a-rest-api-request"></a>傳送 REST API 要求 

### <a name="preparation"></a>準備

情感分析會在您為其提供較少量的文字進行處理時，產生較高品質的結果。 這與關鍵片語擷取相反，後者在較大型文字區塊上的表現會更好。 若要從這兩個作業中取得最佳結果，請考慮據以重建輸入。

您必須具有此格式的 JSON 文件：識別碼、文字和語言。

每份文件的大小必須低於 5,120 個字元。 每個集合最多可以有 1,000 個項目 (識別碼)。 集合會在要求本文中提交。

## <a name="structure-the-request"></a>建立要求結構

建立 POST 要求。 您可以使用下列參考連結中的 [Postman](text-analytics-how-to-call-api.md) 或 **API 測試主控台**，以快速建構及傳送要求。 

#### <a name="version-30"></a>[3.0 版](#tab/version-3)

[情感分析 v3 參考](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

#### <a name="version-31-preview1"></a>[版本 3.1-preview.1](#tab/version-3-1)

[情感分析 v3.1 參考](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Sentiment)

---

### <a name="request-endpoints"></a>要求端點

使用 Azure 文字分析資源或具現化的[文字分析容器](text-analytics-how-to-install-containers.md)，設定可用來分析情感的 HTTPS 端點。 您必須針對您要使用的版本加上正確的 URL。 例如：

> [!NOTE]
> 您可以在 Azure 入口網站上找到適用於文字分析資源的金鑰和端點。 您可以在 [資源管理] 下的資源 [快速啟動] 頁面中找到。 

#### <a name="version-30"></a>[3.0 版](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

#### <a name="version-31-preview1"></a>[版本 3.1-preview.1](#tab/version-3-1)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment`

若要取得意見挖掘結果，您必須包含 `opinionMining=true` 參數。 例如：

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment?opinionMining=true`

根據預設，此參數設定為 `false`。 

---

設定要求標頭以包含您的文字分析 API 金鑰。 在要求主體中，提供您準備用於此分析的 JSON 文件集合。

### <a name="example-sentiment-analysis-request"></a>範例情感分析要求 

以下是您可能提交來進行情感分析的內容範例。 這兩個版本的要求格式是相同的。
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>張貼要求

分析會在接收要求時執行。 如需您每分鐘和每秒鐘可以傳送的要求大小和數量資訊，請參閱概觀中的[資料限制](../overview.md#data-limits)一節。

文字分析 API 是無狀態的。 您的帳戶中不會儲存任何資料，且結果會在回應中立即傳回。


### <a name="view-the-results"></a>View the results

情感分析會傳回整份文件的情感標籤和信賴分數，以及其中的每個句子。 接近 1 的分數表示標籤分類的信賴度更高，而較低的分數則表示較低的信賴度。 文件可以有多個句子，而每個文件或句子中的信賴分數增加最多至 1。

輸出會立即傳回。 您可以將結果串流至接受 JSON 的應用程式，或將輸出儲存到本機系統上的檔案。 然後，將輸出匯入應用程式，以便用來排序、搜尋和操作資料。 由於多語系和表情符號的支援，回應可能會包含文字位移。 如需詳細資訊，請參閱[如何處理位移](../concepts/text-offsets.md)。

#### <a name="version-30"></a>[3.0 版](#tab/version-3)

### <a name="sentiment-analysis-v30-example-response"></a>情感分析 v3.0 的回應範例

情感分析 v3 的回應會包含每個分析的句子和文件的情感標籤和分數。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-31-preview1"></a>[版本 3.1-preview.1](#tab/version-3-1)

### <a name="sentiment-analysis-v31-example-response"></a>情感分析 v3.1 的回應範例

情感分析 v3.1 除了 [版本 3.0] 索引標籤中的回應物件之外，還會提供意見挖掘。在下列回應中，「餐廳的餐點很棒，我們的服務生很友善」有兩個層面：「餐點」和「服務生」。 每個層面的 `relations` 屬性都包含 `ref` 值，其中具有相關聯 `documents`、`sentences` 和 `opinions` 物件的 URI 參考。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
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

在本文中，您已了解使用文字分析 API 進行情感分析的概念和工作流程。 摘要說明：

+ 情感分析僅針對特定語言提供。
+ 要求本文中的 JSON 文件包含識別碼、文字和語言代碼。
+ 使用對您訂用帳戶有效的個人化[存取金鑰和端點](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)，將要求 POST 到 `/sentiment` 端點。
+ 回應輸出 (其包含針對每個文件識別碼的情感分數) 可以串流處理到任何可接受 JSON 的應用程式。 例如，Excel 和 Power BI。

## <a name="see-also"></a>另請參閱

* [文字分析概觀](../overview.md)
* [使用文字分析用戶端程式庫](../quickstarts/text-analytics-sdk.md)
* [新功能](../whats-new.md)
