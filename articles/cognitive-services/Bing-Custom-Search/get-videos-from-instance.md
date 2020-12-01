---
title: 從您的自訂檢視取得影片 - Bing 自訂搜尋
titleSuffix: Azure Cognitive Services
description: 使用 Bing 自訂搜尋從您的 Web 自訂檢視取得影片的概觀。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 4e2bb14519f8ed4e3e4a0e9ac8800957b30229a9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338413"
---
# <a name="get-videos-from-your-custom-view"></a>從您的自訂檢視取得影片

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

Bing 自訂影片搜尋可讓您的自訂影片搜尋體驗更豐富。 與 Web 結果類似，自訂搜尋支援搜尋執行個體之網站清單中的影片。 您可以使用 Bing 的「自訂影片搜尋 API」，或透過「託管的 UI」功能來取得影片。 「託管的 UI」功能較容易使用且建議您使用，因為它只要需要一些時間就能讓您的搜尋體驗開始運作。 如需設定託管的 UI 以包含影片的相關資訊，請參閱[設定託管的 UI 體驗](hosted-ui.md)。

如果您想要進一步控制搜尋結果的顯示，您可以使用 Bing 的自訂影片搜尋 API。 因為呼叫該 API 就類似呼叫 Bing 影片搜尋 API，如需呼叫 API 的範例，請參閱 [Bing 影片搜尋](../bing-video-search/overview.md)。 但在您這樣做之前，建議您先了解[自訂影片搜尋 API 參考](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) \(英文\) 內容。 主要的差異在於支援的查詢參數 (您必須包含 customConfig 查詢參數) 以及接收您傳送之要求的端點。

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->