---
title: 從您的自訂檢視取得影像 - Bing 自訂搜尋
titleSuffix: Azure Cognitive Services
description: 使用 Bing 自訂搜尋從您的 Web 自訂檢視取得影像的概觀。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: c29e83f7851bc9be1688f3828373942c0e31f4ef
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367459"
---
# <a name="get-images-from-your-custom-view"></a>從您的自訂檢視取得影像

> [!WARNING]
> Bing 搜尋 Api 會從認知服務移至 Bing 搜尋服務。 從 **2020 年10月 30** 日開始，任何新的 Bing 搜尋實例都必須依照 [此處](https://aka.ms/cogsvcs/bingmove)所述的程式進行布建。
> 接下來的三年或 Enterprise 合約結束之前，將支援使用認知服務布建的 Bing 搜尋 Api （以先發生者為准）。
> 如需遷移指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

Bing 自訂影像搜尋可讓您的自訂影像搜尋體驗更豐富。 與 Web 結果類似，自訂搜尋支援搜尋執行個體之網站清單中的影像。 您可以使用 Bing 的「自訂影像搜尋 API」或透過「託管的 UI」功能來取得影像。 「託管的 UI」功能較容易使用且建議您使用，因為它只要需要一些時間就能讓您的搜尋體驗開始運作。  如需設定託管的 UI 以包含影像的相關資訊，請參閱[設定託管的 UI 體驗](hosted-ui.md)。

如果您想要進一步控制搜尋結果的顯示，您可以使用 Bing 的自訂影像搜尋 API。 因為呼叫該 API 就類似呼叫 Bing 影像搜尋 API，如需呼叫 API 的範例，請參閱 [Bing 影像搜尋](../Bing-Image-Search/overview.md)。 但在您這樣做之前，建議您先了解[自訂影像搜尋 API 參考](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) \(英文\) 內容。 主要的差異在於支援的查詢參數 (您必須包含 customConfig 查詢參數) 以及接收您傳送之要求的端點。

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->