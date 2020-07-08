---
title: 針對活頁簿轉換選項 Azure 監視器視圖設計工具
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658705"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>針對活頁簿轉換選項 Azure 監視器視圖設計工具
「[視圖設計](view-designer.md)工具」是 Azure 監視器的功能，可讓您建立自訂的視圖，以協助您將 Log Analytics 工作區中的資料視覺化，其中包含圖表、清單和時間軸。 它們會被淘汰，並取代為提供額外功能的活頁簿。 本文會比較兩個和選項之間的基本概念，以便將 views 轉換成活頁簿。

## <a name="basic-workbook-designs"></a>基本活頁簿設計

View designer 具有固定的靜態表示樣式，而活頁簿則可自由地包含及修改資料的呈現方式。 下列影像描繪兩個範例，說明如何在轉換視圖時排列活頁簿。

[垂直活頁簿](view-designer-conversion-examples.md#vertical) 
 ![縱座標](media/view-designer-conversion-options/view-designer-vertical.png)

索引標籤[式活頁簿](view-designer-conversion-examples.md#tabbed) 
 ![資料類型散發索引標籤 ](media/view-designer-conversion-options/distribution-tab.png)
 ![ 資料類型隨時間索引標籤](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>磚轉換
視圖設計工具使用 [總覽] 磚功能來表示和匯總整體狀態。 這些會以七個磚表示，範圍從數位到圖表。 在活頁簿中，使用者可以建立類似的視覺效果，並將其釘選到類似于原始的總覽磚樣式。 

![主機庫](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>View 儀表板轉換
視圖設計工具磚通常包含兩個區段、一個視覺效果，以及一個符合視覺效果資料的清單，例如**環圈 & 清單**磚。

![環圈圖](media/view-designer-conversion-options/donut-example.png)

使用活頁簿，我們可以讓使用者選取查詢視圖的一個或兩個區段。 在活頁簿中編寫查詢是一個簡單的雙步驟程式。 首先，資料是從查詢產生，第二，資料會轉譯為視覺效果。  在活頁簿中重新建立此視圖的範例如下所示：

![轉換](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>後續步驟
- [存取活頁簿 & 許可權](view-designer-conversion-access.md)