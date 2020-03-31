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
ms.date: 03/09/2020
ms.author: aahi
ms.openlocfilehash: b3c112876bfd2578e6ebaa95c6902aa9b8f832d9
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79203452"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>如何：使用文字分析 API 來偵測情感

文字分析 API 的情感分析功能會評估文字，並傳回每個句子的情感分數和標籤。 此功能可用來偵測社交媒體、客戶評價及討論論壇中的正面和負面情感。 API 所使用的 AI 模型由服務所提供，您只需要傳送要分析的內容即可。

> [!TIP]
> 文字分析也會提供可用來偵測語言的 Linux 型 Docker 容器映像，好讓您可以在接近資料的位置[安裝和執行文字分析容器](text-analytics-how-to-install-containers.md)。

情感分析支援多種語言，且預覽中支援的語言更多。 如需詳細資訊，請參閱[支援的語言](../text-analytics-supported-languages.md)。

## <a name="concepts"></a>概念

文字分析 API 會使用機器學習分類演算法來產生介於 0 和 1 之間的情感分數。 接近 1 的分數表示正面情感，而接近 0 的分數則表示負面情感。 情感分析會對整份文件執行，而不是文字中的個別實體。 這表示情感分數會以文件或句子層級傳回。 

使用的模型是以大量文字和情感關聯預先定型的。 此模型會使用數種技術的組合進行分析，包括文字處理、詞性分析、文字位置和文字關聯。 如需有關演算法的詳細資訊，請參閱[文字分析簡介](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/) \(英文\)。 目前，您並無法提供自己的定型資料。 

當文件包含較少句子而非大型文字區塊時，會較容易提升評分的準確度。 在客觀評估階段，模型會判斷整份文件是客觀的，還是包含情感的。 主要為客觀的文件不會進展到情感偵測階段，因此分數為 0.50，而且不會進一步處理。 對於繼續在管線中處理的文件，下一個階段會產生高於或低於 0.50 的分數。 分數取決於在文件中偵測到的情感程度。

## <a name="sentiment-analysis-versions-and-features"></a>情感分析版本和功能

文字分析 API 提供兩種版本的情感分析 - v2 和 v3。 情感分析 v3 (公開預覽版) 大幅改善了 API 文字分類和評分的精確度和詳細資料。

> [!NOTE]
> * 情感分析 v3 的要求格式和[資料限制](../overview.md#data-limits)與前版相同。
> * 情感分析 v3 的適用區域如下：`Australia East`、`Central Canada`、`Central US`、`East Asia`、`East US`、`East US 2`、`North Europe`、`Southeast Asia`、`South Central US`、`UK South`、`West Europe` 和 `West US 2`。

| 功能                                   | 情感分析 v2 | 情感分析 v3 |
|-------------------------------------------|-----------------------|-----------------------|
| 單一和批次要求的方法    | X                     | X                     |
| 整份文件的情感分數  | X                     | X                     |
| 個別句子的情感分數 |                       | X                     |
| 情感標籤                        |                       | X                     |
| 模型版本設定                   |                       | X                     |

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

### <a name="sentiment-scoring"></a>情感評分

情感分析 v3 會使用情感標籤來分類文字 (說明如下)。 傳回的分數代表文字屬於正面、負面或中性的模型信賴度。 值愈高，信賴度就愈高。 

### <a name="sentiment-labeling"></a>情感標籤

情感分析 V3 會傳回句子和文件層級的分數和標籤。 分數和標籤為 `positive`、`negative` 和 `neutral`。 在文件層級也可傳回不含分數的 `mixed` 情感標籤。 文件情感的判定方式如下：

| 句子情感                                                                            | 傳回的文件標籤 |
|-----------------------------------------------------------------------------------------------|-------------------------|
| 文件中至少有一個 `positive` 句子。 其餘句子皆為 `neutral`。 | `positive`              |
| 文件中至少有一個 `negative` 句子。 其餘句子皆為 `neutral`。 | `negative`              |
| 文件中至少有一個 `negative` 句子和至少一個 `positive` 句子。    | `mixed`                 |
| 文件中的所有句子皆為 `neutral`。                                                  | `neutral`               |

### <a name="model-versioning"></a>模型版本設定

> [!NOTE]
> 情感分析的模型版本設定是從版本 `v3.0-preview.1` 開始提供的。

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>範例 C# 程式碼

您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs) 上找到呼叫此情感分析版本的範例 C# 應用程式。


#### <a name="version-21"></a>[2.1 版](#tab/version-2)

### <a name="sentiment-scoring"></a>情感評分

情感分析器會將文字明確地分類為正面或負面。 並指派範圍從 0 到 1 的分數。 接近 0.5 的值是中立或不確定的。 0\.5 的分數表示中立。 當無法針對情感分析字串，或字串沒有任何情感時，分數一律為 0.5。 例如，如果您使用英文語言代碼傳入西班牙字串，分數將會是 0.5。

---

## <a name="sending-a-rest-api-request"></a>傳送 REST API 要求 

### <a name="preparation"></a>準備

情感分析會在您為其提供較少量的文字進行處理時，產生較高品質的結果。 這與關鍵片語擷取相反，後者在較大型文字區塊上的表現會更好。 若要從這兩個作業中取得最佳結果，請考慮據以重建輸入。

您必須具有此格式的 JSON 文件：識別碼、文字和語言。

每份文件的大小必須低於 5,120 個字元。 每個集合最多可以有 1,000 個項目 (識別碼)。 集合會在要求本文中提交。

## <a name="structure-the-request"></a>建立要求結構

建立 POST 要求。 您可以使用下列參考連結中的 [Postman](text-analytics-how-to-call-api.md) 或 **API 測試主控台**，以快速建構及傳送要求。 

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

[情感分析 v3 參考](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

[情感分析 v2 參考](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

使用 Azure 文字分析資源或具現化的[文字分析容器](text-analytics-how-to-install-containers.md)，設定可用來分析情感的 HTTPS 端點。 您必須針對您要使用的版本加上正確的 URL。 例如：

> [!NOTE]
> 您可以在 Azure 入口網站上找到適用於文字分析資源的金鑰和端點。 您可以在 [資源管理]  下的資源 [快速啟動]  頁面中找到。 

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

設定要求標頭以包含您的文字分析 API 金鑰。 在要求主體中，提供您準備用於此分析的 JSON 文件集合。

### <a name="example-sentiment-analysis-request"></a>範例情感分析要求 

以下是您可能提交來進行情感分析的內容範例。 這兩個 API 版本的要求格式是相同的。
    
```json
{
    "documents": [
    {
        "language": "en",
        "id": "1",
        "text": "Hello world. This is some input text that I love."
    },
    {
        "language": "en",
        "id": "2",
        "text": "It's incredibly sunny outside! I'm so happy."
    }
    ],
}
```

### <a name="post-the-request"></a>張貼要求

分析會在接收要求時執行。 如需您每分鐘和每秒鐘可以傳送的要求大小和數量資訊，請參閱概觀中的[資料限制](../overview.md#data-limits)一節。

文字分析 API 是無狀態的。 您的帳戶中不會儲存任何資料，且結果會在回應中立即傳回。


### <a name="view-the-results"></a>View the results

情感分析器會將文字明確地分類為正面或負面。 並指派範圍從 0 到 1 的分數。 接近 0.5 的值是中立或不確定的。 0\.5 的分數表示中立。 當無法針對情感分析字串，或字串沒有任何情感時，分數一律為 0.5。 例如，如果您使用英文語言代碼傳入西班牙字串，分數將會是 0.5。

輸出會立即傳回。 您可以將結果串流至接受 JSON 的應用程式，或將輸出儲存到本機系統上的檔案。 然後，將輸出匯入應用程式，以便用來排序、搜尋和操作資料。 由於多語系和表情符號的支援，回應可能會包含文字位移。 如需詳細資訊，請參閱[如何處理位移](../concepts/text-offsets.md)。

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>情感分析 V3 的回應範例

情感分析 v3 的回應會包含每個分析的句子和文件的情感標籤和分數。 如果文件情感標籤為 `mixed`，則不會傳回 `documentScores`。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>情感分析 v2 範例回應

情感分析 v2 的回應會包含每個已傳送文件的情感分數。

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

---

## <a name="summary"></a>摘要

在本文中，您已了解使用文字分析 API 進行情感分析的概念和工作流程。 摘要說明：

+ 情感分析僅適用於兩個版本中的選定語言。
+ 要求本文中的 JSON 文件包含識別碼、文字和語言代碼。
+ 使用對您訂用帳戶有效的個人化[存取金鑰和端點](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)，將要求 POST 到 `/sentiment` 端點。
+ 回應輸出 (其包含針對每個文件識別碼的情感分數) 可以串流處理到任何可接受 JSON 的應用程式。 例如，Excel 和 Power BI。

## <a name="see-also"></a>另請參閱

* [文字分析概觀](../overview.md)
* [使用文字分析用戶端程式庫](../quickstarts/text-analytics-sdk.md)
* [新功能](../whats-new.md)
