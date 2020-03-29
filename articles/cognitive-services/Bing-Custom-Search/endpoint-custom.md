---
title: Bing 自訂搜尋端點
titleSuffix: Azure Cognitive Services
description: 為您關注的主題創建量身定制的搜索體驗。 使用者會看到根據他們關心的內容量身定制的搜尋結果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 03ec22caedd4e317b9e1fe781dc3d983febc7a6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072795"
---
# <a name="custom-search"></a>自訂搜尋
Bing 自訂搜尋可讓您針對感興趣的主題，建立量身訂做的搜尋經驗。 您的使用者會看到針對他們感興趣的內容量身訂做的搜尋結果，而不需要逐頁查看包含不相關內容的搜尋結果。

## <a name="custom-search-endpoint"></a>自訂搜尋端點
若要使用 Bing 自訂搜尋 API 取得結果，請將 `GET` 要求傳送至下列端點。 使用標頭和 URL 參數以進一步定義規格。

端點：隨著與 `?q=""` 所定義的使用者輸入相關的 JSON 結果傳回搜尋建議。
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

如需如何設定自訂搜尋來源的範例，請參閱[教學課程](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)。 如需關於標頭、參數、市場代碼、回應物件、錯誤的詳細資料，請參閱 [Bing 自訂搜尋 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) 參考資訊。

## <a name="custom-search-response-json"></a>自訂搜尋回應 JSON
自訂搜尋要求會傳回 JSON 物件形式的結果，請參閱[回應物件](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects)。 

## <a name="custom-autosuggest"></a>自訂自動建議
自訂自動建議 API 可讓您將部分搜尋查詢字詞傳送到 Bing，並取得您可以設定的建議查詢清單。 透過自訂自動建議，您可以新增 API 所傳回的建議，並選擇性地指定是否要包含 Bing 所產生的建議。

## <a name="custom-autosuggest-endpoint"></a>自訂自動建議端點
若要要求自訂查詢建議，請將 GET 要求傳送至：

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

如需定義自訂建議的相關資訊，請參閱[定義自訂搜尋建議](define-custom-suggestions.md)。

## <a name="custom-image-search"></a>自訂影像搜尋
自訂影像搜尋 API 可讓您將搜尋查詢傳送至 Bing，並從自訂搜尋執行個體取得相關影像清單。

## <a name="custom-image-search-endpoint"></a>自訂影像搜尋端點
若要從您的自訂搜尋執行個體要求影像，請將 GET 要求傳送至下列 URL：

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

如需設定自訂搜尋執行個體的相關資訊，請參閱[設定您的自訂搜尋體驗](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view)。

## <a name="next-steps"></a>後續步驟
**Bing** API 支援根據類型傳回結果的搜尋動作。所有搜尋端點會傳回作為 JSON 回應物件的結果。 所有端點均可支援依照經度、緯度和搜尋半徑傳回特定語言及/或位置的查詢。

如需每個端點支援的參數完整相關資訊，請參閱每種類型的參考頁面。
如需透過範例了解使用自訂搜尋 API 的基本要求，請參閱[自訂搜尋快速入門](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
