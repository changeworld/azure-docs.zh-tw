---
title: 使用文字分析 REST API 的關鍵字組解壓縮
titleSuffix: Azure Cognitive Services
description: 如何使用 Azure 認知服務的文字分析 REST API 來解壓縮主要片語。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: c1ca14b8471ef6257c0603e61d78e789e846f0ae
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142396"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>範例：如何使用文字分析解壓縮關鍵字組

[關鍵片語擷取 API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) \(英文\) 會評估非結構化的文字，並針對每份 JSON 文件，傳回關鍵片語的清單。

此功能在您需要快速識別文件集合中的要點時相當有用。 例如，假設輸入文字為 "The food was delicious and there were wonderful staff"，服務即會傳回主要討論要點："food" 和 "wonderful staff"。

如需詳細資訊，請參閱[支援的語言](../text-analytics-supported-languages.md)。

> [!TIP]
> 文字分析也會提供可用來擷取關鍵片語的 Linux 型 Docker 容器映像，好讓您可以在接近資料的位置[安裝和執行文字分析容器](text-analytics-how-to-install-containers.md)。

## <a name="preparation"></a>準備

關鍵片語擷取在處理較大量文字時的效果最佳。 這與情感分析相反，後者較適合用來處理較少量的文字。 若要從這兩個作業中取得最佳結果，請考慮據以重建輸入。

您必須具有此格式的 JSON 檔：識別碼、文字、語言

檔案大小必須是5120或每份檔較少的字元，而且每個集合最多可以有1000個專案（識別碼）。 集合會在要求本文中提交。 下列範例是您可能會針對關鍵片語擷取所提交內容的說明。

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>步驟 1:建立要求結構

如需要求定義的詳細資訊，請參閱[如何呼叫文字分析 API](text-analytics-how-to-call-api.md)。 為了方便起見，我們將重申下列各點：

+ 建立**POST**要求。 請參閱此要求的 API 檔：[關鍵字組 api](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases)。

+ 使用 Azure 上的文字分析資源或具現化的[文字分析容器](text-analytics-how-to-install-containers.md)，設定關鍵字組解壓縮的 HTTP 端點。 您必須在 URL 中納入 `/text/analytics/v3.0/keyPhrases`。 例如： `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases` 。

+ 設定要求標頭以包含文字分析作業的[存取金鑰](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。

+ 在要求主體中，提供您準備用於此分析的 JSON 文件集合。

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或開啟[文件](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) \(英文\) 中的 **API 測試主控台**來建立要求結構，並將它 POST 到服務。

## <a name="step-2-post-the-request"></a>步驟 2：張貼要求

分析會在接收要求時執行。 如需每分鐘或每秒可傳送之要求的大小和數目的詳細資訊，請參閱總覽中的[資料限制](../overview.md#data-limits)一節。

請記得，服務是無狀態的。 您的帳戶中並不會儲存任何資料。 結果會在回應中立即傳回。

## <a name="step-3-view-results"></a>步驟 3：檢視結果

所有的 POST 要求都會傳回 JSON 格式的回應，具有識別碼和偵測到的屬性。 傳回之主要片語的順序是由模型在內部決定。

輸出會立即傳回。 您可以將結果串流處理到可接受 JSON 的應用程式，或將輸出儲存到本機系統上的檔案，然後將它匯入能讓您排序、搜尋和操作資料的應用程式。

以下顯示關鍵片語擷取的輸出範例：

```json
    {
        "documents": [
            {
                "keyPhrases": [
                    "year",
                    "trail",
                    "trip",
                    "views"
                ],
                "id": "1"
            },
            {
                "keyPhrases": [
                    "marked trails",
                    "Worst hike",
                    "goners"
                ],
                "id": "2"
            },
            {
                "keyPhrases": [
                    "trail",
                    "small children",
                    "family"
                ],
                "id": "3"
            },
            {
                "keyPhrases": [
                    "spectacular views",
                    "trail",
                    "area"
                ],
                "id": "4"
            },
            {
                "keyPhrases": [
                    "places",
                    "beautiful views",
                    "favorite trail"
                ],
                "id": "5"
            }
        ],
        "errors": []
    }
```

如先前所述，分析器會尋找並捨棄不必要的單字，而且會保留看似句子主旨或物件的單一詞彙或片語。

## <a name="summary"></a>摘要

在本文中，您已瞭解使用認知服務中的文字分析來進行關鍵字組解壓縮的概念和工作流程。 摘要說明：

+ [關鍵片語擷取 API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) \(英文\) 僅針對特定語言提供。
+ 要求本文中的 JSON 文件包含識別碼、文字和語言代碼。
+ 使用對您訂用帳戶有效的個人化[存取金鑰和端點](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)，將要求 POST 到 `/keyphrases` 端點。
+ 回應輸出（由每個檔識別碼的關鍵字和片語組成）可以串流處理到任何可接受 JSON 的應用程式，包括 Microsoft Office Excel 和 Power BI 等。

## <a name="see-also"></a>另請參閱

 [文字分析概觀](../overview.md) [常見問題集 (FAQ)](../text-analytics-resource-faq.md)</br>
 [文字分析產品頁面](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>後續步驟

* [文字分析概觀](../overview.md)
* [使用文字分析用戶端程式庫](../quickstarts/text-analytics-sdk.md)
* [新功能](../whats-new.md)
