---
title: 什麼是 Bing 自訂搜尋 API？
titleSuffix: Azure Cognitive Services
description: Bing 自訂搜尋 API 可讓您針對感興趣的主題，建立量身訂做的搜尋經驗。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 010f2960693a06a50cd15ac3bac2fe35b6c0985f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "84434568"
---
# <a name="what-is-the-bing-custom-search-api"></a>什麼是 Bing 自訂搜尋 API？

Bing 自訂搜尋 API 可讓您針對感興趣的主題，建立量身訂做且無廣告的搜尋經驗。 您可以指定 Bing 要搜尋的網域和網頁，以及釘選、升級或降級特定內容，以建立自訂的 Web 檢視，並協助您的使用者快速找出相關的搜尋結果。 

## <a name="features"></a>特性

|功能  |描述  |
|---------|---------|
|[自訂即時搜尋建議](define-custom-suggestions.md)     | 提供使用者輸入時可顯示為下拉式清單的搜尋建議。       | 
|[自訂影像搜尋體驗](get-images-from-instance.md)     | 可讓使用者從自訂搜尋執行個體中指定的網域和網站搜尋影像。        |        
|[自訂影片搜尋體驗](get-videos-from-instance.md)     | 可讓使用者從自訂搜尋執行個體中指定的網域和網站搜尋影片。        |    
|[共用自訂搜尋執行個體](share-your-custom-search.md)     | 透過與小組成員分享來共同編輯並測試搜尋執行個體。        | 
|[設定應用程式和網站的 UI](hosted-ui.md)     | 提供主控的 UI，可讓您輕鬆地將其整合在網頁和 Web 應用程式中，做為 JavaScript 程式碼片段。        | 
## <a name="workflow"></a>工作流程

您可以使用 [Bing 自訂搜尋入口網站](https://customsearch.ai)建立自訂搜尋執行個體。 此入口網站可讓您建立自訂搜尋執行個體，指定您要 Bing 搜尋的網域、網站和網頁，以及不想要搜尋的網域、網站和網頁。 您也可以使用此入口網站：預覽搜尋體驗、調整 API 所提供的搜尋排名，以及選擇性地設定要在網站和應用程式中呈現的可搜尋使用者介面。

建立搜尋執行個體之後，您可以透過呼叫 Bing 自訂搜尋 API，將其 (以及使用者介面 (選擇性)) 整合至網站或應用程式：

![顯示您可以透過 API 連接到 Bing 自訂搜尋的影像](media/BCS-Overview.png "Bing 自訂搜尋的運作方式。")


## <a name="next-steps"></a>後續步驟

若要快速開始，請參閱[建立您的第一個 Bing 自訂搜尋執行個體](quick-start.md)。

如需有關自訂搜尋執行個體的詳細資，請參閱[定義自訂搜尋執行個體](define-your-custom-view.md)。

請務必先閱讀 [Bing 使用和顯示需求](./use-and-display-requirements.md)，以便在服務和應用程式中使用搜尋結果。

請瀏覽 [Bing 搜尋 API 中樞頁面](../bing-web-search/search-the-web.md)以探索其他可用的 API。

讓自己熟悉每個自訂搜尋端點的內容。 此參考包含端點、標頭，以及您用來要求搜尋結果的查詢參數。 它也包含回應物件的定義。

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [自訂搜尋 API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [自訂影像 API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [自訂影片 API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [自訂自動建議 API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)

