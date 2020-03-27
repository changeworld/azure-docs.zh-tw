---
title: 什麼是 Bing 影像搜尋 API？
titleSuffix: Azure Cognitive Services
description: Bing 影像搜尋 API 可讓您在應用程式中使用 Bing 的認知影像搜尋功能。 使用 API 傳送使用者搜尋查詢，您即可取得並顯示類似於 Bing 影像的相關高品質影像。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 6bd8c472aa659bec31440292e20328865b1cb531
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79134522"
---
# <a name="what-is-the-bing-image-search-api"></a>什麼是 Bing 影像搜尋 API？

Bing 影像搜尋 API 可讓您在應用程式中使用 Bing 的影像搜尋功能。 將搜尋查詢傳回至 API，您即可取得並顯示類似於 [bing.com/影像](https://www.bing.com/images)的相關高品質影像。

雖然 Bing 影像搜尋 API 僅提供影像搜尋結果，但您可以結合或使用其他可用的 [Bing 搜尋 API](../bing-web-search/bing-api-comparison.md)，在 Web 上尋找許多類型的內容。

## <a name="bing-image-search-features"></a>Bing 影像搜尋功能

| 功能                                                                                                                                                                                 | 描述                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [即時建議搜尋字詞](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | 使用 [Bing 自動建議 API](../bing-autosuggest/get-suggested-search-terms.md) 隨著使用者的輸入顯示建議的搜尋字詞，以改善您的應用程式體驗。 |
| [篩選及限制影像結果](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | 藉由編輯查詢參數來篩選 Bing 所傳回的影像。                                                                                                       |
| [裁剪縮圖及調整其大小，並加以顯示](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/resize-and-crop-thumbnails)                                                | 為 Bing 影像搜尋所傳回的影像編輯及顯示縮圖預覽。                                                                                      |
| [樞紐和擴充使用者搜尋查詢](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | 在查詢中納入及顯示 Bing 建議的搜尋字詞，以擴充您的搜尋功能。                                                                    |
| [取得趨勢影像](trending-images.md)                                                                     | 自訂搜尋以尋找全球的趨勢影像。                                                                                                          |

## <a name="workflow"></a>工作流程

Bing 影像搜尋 API 是一種 RESTful Web 服務，因此可輕易地從任何可發出 HTTP 要求及剖析 JSON 的程式設計語言呼叫。 您可以透過 [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) 或 [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) 來使用此服務。

1. 建立具備 Bing 搜尋 API 存取權的[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果您沒有 Azure 訂用帳戶，可以[建立免費帳戶](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。
2. 使用有效的[搜尋查詢](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)，將要求傳送至 API。
3. 剖析傳回的 JSON 訊息以處理 API 回應。

## <a name="next-steps"></a>後續步驟

首先，觀看 Bing 影像搜尋 API 的[互動式示範](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)。
此示範將說明如何快速地自訂搜尋查詢，並翻找出 Web 上的影像。

當您準備好要呼叫 API 時，請建立[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果您沒有 Azure 訂用帳戶，可以[建立免費帳戶](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。

若要快速開始使用您的第一個 API 要求，您應了解如何：

* 使用 REST API [將搜尋查詢傳送至 Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp)，或
* 使用 SDK [要求和篩選](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) Bing 傳回的影像。

## <a name="see-also"></a>另請參閱

* Bing 搜尋 API 的[定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。 

* [Bing 影像搜尋 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) 參考章節包含 API 的端點、標頭、API 回應和查詢參數的相關資訊。

* [Bing 使用和顯示需求](./useanddisplayrequirements.md)指定了透過 Bing 搜尋 API 取得的內容和資訊可行的用法。

* [使用 Bing 影像搜尋 API 從 Web 取得影像](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)一文說明如何從 Web 搜尋及取得影像。

* [傳送並使用搜尋查詢](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)一文說明如何建立、自訂搜尋查詢及進行其樞紐分析。

* 請瀏覽 [Bing 搜尋 API 中樞頁面](../bing-web-search/search-the-web.md)以探索其他可用的 API。
