---
title: 什麼是 Bing 影片搜尋 API？
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Bing 影片搜尋 API 在 Web 上搜尋影片。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2c52f909cf3cc77b4f5e40ee9804d0c473e575c5
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85601933"
---
# <a name="what-is-the-bing-video-search-api"></a>什麼是 Bing 影片搜尋 API？

Bing 影片搜尋 API 可讓您輕鬆地將影片搜尋功能新增至服務和應用程式。 使用 API 傳送使用者搜尋查詢，您即可取得並顯示類似於 [Bing 影片](https://www.bing.com/video)的相關高品質影片。 針對僅包含影片的搜尋結果使用此 API。 [Bing Web 搜尋 API](../bing-web-search/search-the-web.md) 可以傳回其他類型的 Web 內容，包括網頁、影片、新聞和影像。

## <a name="bing-video-search-api-features"></a>Bing 影片搜尋 API 功能

| 功能                                                                                                                                                                                 | 描述                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [即時建議搜尋字詞](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | 使用 [Bing 自動建議 API](../bing-autosuggest/get-suggested-search-terms.md) 隨著使用者的輸入顯示建議的搜尋字詞，以改善您的應用程式體驗。 |
| [篩選及限制影片結果](concepts/get-videos.md#filtering-videos)                      | 藉由編輯查詢參數來篩選傳回的影片。                                                                                                       |
| [裁剪縮圖及調整其大小，並加以顯示](../bing-web-search/resize-and-crop-thumbnails.md)                                                | 為 Bing 影片搜尋 API 所傳回的影片編輯及顯示縮圖預覽。                                                                                      |
| [取得發燒影片](trending-videos.md) | 搜尋來自全球的發燒影片。                                                                                                          |
| [取得影片深入解析](video-insights.md) | 自訂搜尋以尋找全球的發燒影片。                                                                                                          |

## <a name="workflow"></a>工作流程

Bing 影片搜尋 API 是一種 RESTful Web 服務，因此可輕易地從任何可發出 HTTP 要求及剖析 JSON 的程式設計語言呼叫。 您可以透過 [REST API](csharp.md) 或 [SDK](video-search-sdk-quickstart.md) 來使用此服務。

1. 建立具備 Bing 搜尋 API 存取權的[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果您沒有 Azure 訂用帳戶，可以[建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。
2. 使用有效的搜尋查詢，將要求傳送至 API。
3. 剖析傳回的 JSON 訊息以處理 API 回應。


## <a name="next-steps"></a>後續步驟

Bing 影片搜尋 API 的[互動式示範](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)會說明如何自訂搜尋查詢，並搜尋 Web 上的影片。

使用[快速入門](csharp.md)來快速開始使用您的第一個 API 要求。

## <a name="see-also"></a>另請參閱

* [Bing 影片搜尋 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) 參考頁面包含端點、標頭，以及用來要求搜尋結果的查詢參數清單。

* [Bing 使用和顯示需求](./useanddisplayrequirements.md)指定了透過 Bing 搜尋 API 取得的內容和資訊可行的用法。

* 請瀏覽 [Bing 搜尋 API 中樞頁面](../bing-web-search/search-the-web.md)以探索其他可用的 API。