---
title: Bing 影像搜尋 API 的端點
titleSuffix: Azure Cognitive Services
description: 影像搜尋 API 包含三個端點。 端點1會從 web 傳回影像。 端點 2 會傳回 ImageInsights。 端點 3 會傳回發燒影像。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 67fec77136f5d593279be2846e63c51b60e16bb4
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593513"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Bing 影像搜尋 API 的端點

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

**影像搜尋 API** 包含三個端點。  端點 1 會根據查詢從 Web 傳回影像。 端點 2 會傳回 [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse)。  端點 3 會傳回發燒影像。

## <a name="endpoints"></a>端點

若要使用 Bing API 取得影像結果，請將要求傳送至下列其中一個端點。 使用標頭和 URL 參數以進一步定義規格。

**端點 1：** 傳回 `?q=""` 所定義使用者搜尋查詢的相關影像。
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**端點 2：** 使用 `GET` 或 `POST`，傳回影像的相關見解。
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
GET 要求會傳回影像相關見解，例如包含影像的網頁。 透過 `GET` 要求包含 [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) 參數。

或者，您可以在 `POST` 要求的本文中包含二進位影像，並將 [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) 參數設定為 `RecognizedEntities`。 這會傳回 [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) 作為後續 `GET` 要求中的參數，以傳回影像中人員的相關資訊。  將 `modules` 設定為 `All` 可取得所有見解 (`POST` 結果中的 `RecognizedEntities` 除外)，而不需要使用 `insightsToken` 進行其他呼叫。


**端點 3：** 傳回影像，這些影像是以他人所提出的搜尋要求為基礎的發燒影像。 影像可分成不同類別，例如，根據值得注意的人員或事件。
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

如需支援發燒影像的市場清單，請參閱[發燒影像](./trending-images.md)。

如需關於標頭、參數、市場代碼、回應物件、錯誤的詳細資料，請參閱 [Bing 影像搜尋 API v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) 參考。
## <a name="response-json"></a>回應 JSON
影像搜尋要求的回應包含作為 JSON 物件的結果。 如需結果剖析的範例，請參閱[教學課程](./tutorial-bing-image-search-single-page-app.md)和[原始程式碼](./tutorial-bing-image-search-single-page-app.md)。

## <a name="next-steps"></a>後續步驟
**Bing** API 支援根據類型傳回結果的搜尋動作。 所有搜尋端點會傳回作為 JSON 回應物件的結果。  所有端點均可支援依照經度、緯度和搜尋半徑傳回特定語言及/或位置的查詢。

如需每個端點支援的參數完整相關資訊，請參閱每種類型的參考頁面。
如需透過範例了解使用影像搜尋 API 的基本要求，請參閱[影像搜尋快速入門](./overview.md)。