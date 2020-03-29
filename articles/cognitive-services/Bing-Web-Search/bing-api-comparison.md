---
title: Bing 搜尋 API 是什麼？
titleSuffix: Azure Cognitive Services
description: 您可以使用本文來了解 Bing 搜尋 API，以及如何啟用應用程式及服務的認知網際網路搜尋。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 82b1f13562a49284059c25bcbd39a33daf949dcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74775535"
---
# <a name="what-are-the-bing-search-apis"></a>Bing 搜尋 API 是什麼？

Bing 搜尋 API 可讓您建置網路連線的應用程式和服務來尋找網頁、影像、新聞、位置等等，完全沒有廣告。 藉由使用 Bing 搜尋 REST API 或 SDK 傳送搜尋要求，您可以取得網路搜尋的相關資訊和內容。 使用本文瞭解不同的必應搜索 API 以及如何將認知搜索集成到應用程式和服務中。 API 之間可能有不同的定價與速率限制。

## <a name="the-bing-web-search-api"></a>Bing Web 搜尋 API

[Bing Web 搜尋 API](../Bing-Web-Search/overview.md) 會傳回網頁、影像、影片、新聞等等。 您可以篩選發送到此 API 的搜索查詢，以包括或排除某些內容類型。

請考慮在可能需要搜尋各種相關網路內容的應用程式中使用 Bing Web 搜尋 API。 如果您的應用程式搜尋特定類型的線上內容，請考慮下列其中一個搜尋 API：

## <a name="content-specific-bing-search-apis"></a>特定內容的 Bing 搜尋 API

以下必應搜索 API 從 Web 返回特定內容，如圖像、新聞、本地企業和視頻。

| Bing API | 描述 |
| -- | -- |
| [實體搜索](../Bing-Entities-Search/overview.md) | Bing 實體搜尋 API 會傳回內含人員、地點或事物等等實體的搜尋結果。 根據查詢，API 將返回滿足搜索查詢的一個或多個實體。 搜索查詢可以包括值得注意的個人、本地企業、地標、目的地等。 |
| [影像搜尋](../Bing-Image-Search/overview.md) | 必應圖像搜索 API 允許您搜索和查找類似于[Bing.com/images](https://www.Bing.com/images)的高品質靜態和動畫圖像。 您可以精簡搜尋來依屬性包含或排除影像，包括大小、色彩、授權和有效期限。 您也可以搜尋趨勢影像、上傳影像以深入了解影像，以及顯示縮圖預覽。 |
| [新聞搜索](../Bing-News-Search/search-the-web.md) | Bing 新聞搜索 API 允許您查找類似于[Bing.com/news](https://www.Bing.com/news)的新聞故事。 此 API 會傳回多個來源或特定網域的新聞文章。 您可以跨類別搜尋，以取得趨勢文章、熱門文章和頭條新聞。 |
| [視頻搜索](../Bing-Video-Search/overview.md) | 必應視頻搜索 API 可讓您在 Web 上查找視頻。 取得發燒影片、相關內容和縮圖預覽。 |
| [圖像式搜尋](../Bing-visual-search/overview.md) | 上傳影像，或使用網址取得相關的深入資訊，例如看起來類似的產品、影像和相關搜尋。 |
 [本地商家搜尋](../bing-local-business-search/overview.md) | 必應本地業務搜索 API 允許您的應用程式根據搜索查詢查找有關本地企業的連絡人和位置資訊。 |

## <a name="the-bing-custom-search-api"></a>Bing 自訂搜尋 API

通過[Bing 自訂搜索](../Bing-Custom-Search/overview.md)API 創建自訂搜索實例，您可以創建僅關注您關心的內容和主題的搜索體驗。 例如，在指定必應將搜索的域、網站和特定網頁後，必應自訂搜索將根據該特定內容定制結果。 您可以整合 Bing 自訂自動建議、影像和視訊搜尋 API，進一步自訂您的搜尋體驗。

## <a name="additional-bing-search-apis"></a>其他 Bing 搜尋 API

以下必應搜索 API 允許您通過將搜索體驗與其他必應搜索 API 相結合來改善搜索體驗。

| API | 描述 |
| -- | -- |
| [Bing 自動建議](../Bing-Autosuggest/get-suggested-search-terms.md) | 通過即時返回建議的搜索，使用必應自動建議 API 改善應用程式的搜索體驗。  |
| [Bing 統計資料](bing-web-stats.md) | Bing 統計資料可為應用程式使用的 Bing 搜尋 API 提供分析。 可用的分析包括呼叫量、熱門查詢字串及地理分佈。 |

## <a name="next-steps"></a>後續步驟

* Bing 搜尋 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* [Bing 使用和顯示需求](./use-display-requirements.md)指定了透過 Bing 搜尋 API 取得的內容和資訊可行的用法。
