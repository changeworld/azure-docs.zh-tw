---
title: Bing 深入解析的範例 - Bing 圖像式搜尋
titleSuffix: Azure Cognitive Services
description: 本文包含必應視覺化搜索如何使用和顯示Bing.com的圖像見解的示例。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: df66dbeebb04209921ff91c4b99a14580f026718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111647"
---
# <a name="examples-of-bing-insights-usage"></a>Bing 深入解析使用方式的範例

本文包含 Bing 可如何在 Bing.com 上使用和顯示影像深入解析的範例。

## <a name="pagesincluding-insight-example"></a>PagesIncluding 深入解析範例

下面顯示指向第一個網頁的連結，並允許使用者展開和折疊包含圖像的其他網頁的清單：

![展開的頁面包括](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>ShoppingSources 深入解析範例

下面顯示了必應如何顯示圖片中看到的產品的購物源：

![購物來源](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>VisualSearch 深入解析範例

下面顯示了必應如何顯示視覺上相似的圖像（請參閱示例中**的相關圖像**）：

![在視覺效果上類似的影像](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Recipes 深入解析範例

以下顯示 Bing 對於影像顯示的食物如何顯示食譜。 該示例讓使用者知道有可用的配方：

![食譜和頁面包括](./media/recipes-pages-including.PNG)

 並在使用者展開清單時提供指向配方的連結：

![展開的食譜頁面包括](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelatedSearches 深入解析範例

以下顯示 Bing 如何顯示其他人對於影像進行的相關搜尋。 如果使用者按一下影像，使用者會進入該相關查詢的 Bing.com/images 搜尋結果頁面。

![影像的相關搜尋](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Entity 深入解析範例

以下顯示 Bing 如何顯示影像顯示的實體 (人、地、事) 相關的資訊。 如果使用者按一下實體連結，則使用者將被帶到實體的Bing.com搜尋結果頁：

![影像中顯示的實體](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>顯示使用者可以探索的其他深入解析

以下顯示 Bing 如何顯示使用者可以探索的影像有關的其他資訊。

![探索影像的其他深入解析](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>週框方塊和作用點

非預設標記包含識別標籤套用的影像之中關切區域的週框方塊。 如果週框方塊並未識別整個影像，可使用週框方塊在影像上建立作用點。 使用者可以按一下作用點取得在作用點 (或矩形) 之下找到的內容有關的資訊。 例如，如果圖像是高時尚圖像，則結果可能包含圖像中顯示的附件的標籤（和邊界框），例如錢包、珠寶、圍巾等。 下面的示例顯示了圖像中顯示的太陽鏡的熱點矩形：

![週框方塊和作用點](./media/click-to-search.PNG)

## <a name="next-steps"></a>後續步驟

要開始使用第一個請求，請參閱快速入門[：C#](quickstarts/csharp.md) | [JAVA](quickstarts/java.md) | [節點.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





