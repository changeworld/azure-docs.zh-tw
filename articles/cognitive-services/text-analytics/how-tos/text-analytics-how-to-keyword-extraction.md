---
title: 使用文本分析 REST API 提取關鍵短語
titleSuffix: Azure Cognitive Services
description: 如何使用 Azure 認知服務的文本分析 REST API 提取關鍵短語。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/29/2019
ms.author: raymondl
ms.openlocfilehash: ec5ff756d7e732430675676868bc754627a2a4a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "72429019"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>示例：如何使用文本分析提取關鍵短語

[關鍵片語擷取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) \(英文\) 會評估非結構化的文字，並針對每份 JSON 文件，傳回關鍵片語的清單。

此功能在您需要快速識別文件集合中的要點時相當有用。 例如，假設輸入文字為 "The food was delicious and there were wonderful staff"，服務即會傳回主要討論要點："food" 和 "wonderful staff"。

有關詳細資訊，請參閱[支援的語言](../text-analytics-supported-languages.md)。

> [!TIP]
> 文字分析也會提供可用來擷取關鍵片語的 Linux 型 Docker 容器映像，好讓您可以在接近資料的位置[安裝和執行文字分析容器](text-analytics-how-to-install-containers.md)。

## <a name="preparation"></a>準備

關鍵片語擷取在處理較大量文字時的效果最佳。 這與情感分析相反，後者較適合用來處理較少量的文字。 若要從這兩個作業中取得最佳結果，請考慮據以重建輸入。

您必須具有此格式的 JSON 文檔：ID、文本、語言

每個文檔的文檔大小必須為 5，120 個字元或更少，並且每個集合最多隻能有 1，000 個項 （ID）。 集合會在要求本文中提交。 下列範例是您可能會針對關鍵片語擷取所提交內容的說明。

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

## <a name="step-1-structure-the-request"></a>步驟 1：建立要求結構

有關請求定義的資訊，請參閱[如何調用文本分析 API](text-analytics-how-to-call-api.md)。 為了方便起見，我們將重申下列各點：

+ 創建**POST 請求**。 查看此請求的 API 文檔：[關鍵短語 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)。

+ 使用 Azure 上的文本分析資源或具現化[的文本分析容器](text-analytics-how-to-install-containers.md)，設置用於關鍵短語提取的 HTTP 終結點。 您必須在 URL 中納入 `/text/analytics/v2.1/keyPhrases`。 例如：`https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v2.1/keyPhrases`。

+ 設定要求標頭以包含文字分析作業的[存取金鑰](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。

+ 在要求主體中，提供您準備用於此分析的 JSON 文件集合。

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或開啟[文件](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) \(英文\) 中的 **API 測試主控台**來建立要求結構，並將它 POST 到服務。

## <a name="step-2-post-the-request"></a>步驟 2：張貼要求

分析會在接收要求時執行。 有關每分鐘或每秒可以發送的請求的大小和數量的資訊，請參閱概述中[的資料限制](../overview.md#data-limits)部分。

請記得，服務是無狀態的。 您的帳戶中並不會儲存任何資料。 結果會在回應中立即傳回。

## <a name="step-3-view-results"></a>步驟 3：檢視結果

所有的 POST 要求都會傳回 JSON 格式的回應，具有識別碼和偵測到的屬性。 返回的關鍵短語的順序由模型在內部確定。

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

如前所述，分析器查找並丟棄非必需的單詞，並且它保留看似句子的主題或物件的單個術語或短語。

## <a name="summary"></a>總結

在本文中，您通過在認知服務中使用文本分析，學習了關鍵短語提取的概念和工作流。 摘要說明：

+ [關鍵片語擷取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) \(英文\) 僅針對特定語言提供。
+ 要求本文中的 JSON 文件包含識別碼、文字和語言代碼。
+ 使用對您訂用帳戶有效的個人化[存取金鑰和端點](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)，將要求 POST 到 `/keyphrases` 端點。
+ 回應輸出由每個文件識別碼 的關鍵字和短語組成，可以資料流到接受 JSON 的任何應用，包括 Microsoft Office Excel 和 Power BI，僅舉幾例。

## <a name="see-also"></a>另請參閱

 [文字分析概觀](../overview.md) [常見問題集 (FAQ)](../text-analytics-resource-faq.md)</br>
 [文字分析產品頁面](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文本分析 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
