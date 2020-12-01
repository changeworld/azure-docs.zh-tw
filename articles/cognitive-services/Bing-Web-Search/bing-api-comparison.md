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
ms.openlocfilehash: 72b4755c6f01a10851e79cf274842f1599bc2c55
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349532"
---
# <a name="what-are-the-bing-search-apis"></a>Bing 搜尋 API 是什麼？

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

Bing 搜尋 API 可讓您建置網路連線的應用程式和服務來尋找網頁、影像、新聞、位置等等，完全沒有廣告。 藉由使用 Bing 搜尋 REST API 或 SDK 傳送搜尋要求，您可以取得網路搜尋的相關資訊和內容。 您可以使用本文來瞭解不同的 Bing 搜尋 Api，以及如何將認知搜尋整合到您的應用程式和服務中。 API 之間可能有不同的定價與速率限制。

## <a name="the-bing-web-search-api"></a>Bing Web 搜尋 API

[Bing Web 搜尋 API](../Bing-Web-Search/overview.md) 會傳回網頁、影像、影片、新聞等等。 您可以篩選傳送至此 API 的搜尋查詢，以包含或排除特定內容類型。

請考慮在可能需要搜尋各種相關網路內容的應用程式中使用 Bing Web 搜尋 API。 如果您的應用程式搜尋特定類型的線上內容，請考慮下列其中一個搜尋 API：

## <a name="content-specific-bing-search-apis"></a>特定內容的 Bing 搜尋 API

下列 Bing 搜尋 Api 會從 web 傳回特定內容，例如影像、新聞、當地商家和影片。

| Bing API | 說明 |
| -- | -- |
| [實體搜尋](../Bing-Entities-Search/overview.md) | Bing 實體搜尋 API 會傳回內含人員、地點或事物等等實體的搜尋結果。 視查詢而定，API 會傳回一或多個滿足搜尋查詢的實體。 搜尋查詢可能包含值得注意的個人、當地商家、地標、目的地等等。 |
| [影像搜尋](../Bing-Image-Search/overview.md) | Bing 影像搜尋 API 可讓您搜尋和尋找高品質的靜態和動畫影像，類似于 [Bing.com/images](https://www.Bing.com/images)。 您可以精簡搜尋來依屬性包含或排除影像，包括大小、色彩、授權和有效期限。 您也可以搜尋趨勢影像、上傳影像以深入了解影像，以及顯示縮圖預覽。 |
| [新聞搜尋](../Bing-News-Search/search-the-web.md) | Bing 新聞搜尋 API 可讓您尋找類似 [Bing.com/news](https://www.Bing.com/news)的新聞故事。 此 API 會傳回多個來源或特定網域的新聞文章。 您可以跨類別搜尋，以取得趨勢文章、熱門文章和頭條新聞。 |
| [影片搜尋](../Bing-Video-Search/overview.md) | Bing 影片搜尋 API 可讓您在網路上尋找影片。 取得發燒影片、相關內容和縮圖預覽。 |
| [圖像式搜尋](../Bing-visual-search/overview.md) | 上傳影像，或使用網址取得相關的深入資訊，例如看起來類似的產品、影像和相關搜尋。 |
 [本地商家搜尋](../bing-local-business-search/overview.md) | Bing 當地企業搜尋 API 可讓您的應用程式根據搜尋查詢來尋找當地商家的連絡人和位置資訊。 |

## <a name="the-bing-custom-search-api"></a>Bing 自訂搜尋 API

使用 [Bing 自訂搜尋](../Bing-Custom-Search/overview.md) API 建立自訂搜尋實例，可讓您建立僅專注于您感興趣之內容和主題的搜尋體驗。 例如，在您指定 Bing 將搜尋的網域、網站和特定網頁之後，Bing 自訂搜尋會將結果量身打造至該特定內容。 您可以整合 Bing 自訂自動建議、影像和視訊搜尋 API，進一步自訂您的搜尋體驗。

## <a name="additional-bing-search-apis"></a>其他 Bing 搜尋 API

下列 Bing 搜尋 Api 可讓您藉由結合其他 Bing 搜尋 Api 來改善搜尋體驗。

| API | 說明 |
| -- | -- |
| [Bing 自動建議](../Bing-Autosuggest/get-suggested-search-terms.md) | 藉由即時傳回建議的搜尋，來改善應用程式的 Bing 自動建議 API 搜尋體驗。  |
| [Bing 統計資料](bing-web-stats.md) | Bing 統計資料可為應用程式使用的 Bing 搜尋 API 提供分析。 可用的分析包括呼叫量、熱門查詢字串及地理分佈。 |

## <a name="next-steps"></a>後續步驟

* Bing 搜尋 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* [Bing 使用和顯示需求](./use-display-requirements.md)指定了透過 Bing 搜尋 API 取得的內容和資訊可行的用法。