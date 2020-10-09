---
title: Azure 監視器視圖設計工具轉換為活頁簿轉換選項
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77658705"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure 監視器視圖設計工具轉換為活頁簿轉換選項
[View designer](view-designer.md) 是 Azure 監視器的功能，可讓您建立自訂視圖，以協助您在 Log Analytics 工作區中以圖表、清單和時程表來視覺化資料。 它們會被淘汰並取代為提供額外功能的活頁簿。 本文會比較兩個和選項之間的基本概念，將視圖轉換成活頁簿。

## <a name="basic-workbook-designs"></a>基本活頁簿設計

View designer 具有固定的靜態表示樣式，而活頁簿可讓您自由地包含和修改資料的呈現方式。 下列影像說明如何在轉換視圖時排列活頁簿的兩個範例。

[垂直活頁簿](view-designer-conversion-examples.md#vertical) 
 ![垂直](media/view-designer-conversion-options/view-designer-vertical.png)

索引標籤[式活頁簿](view-designer-conversion-examples.md#tabbed) 
 ![時間索引標籤上的資料類型散發索引標籤 ](media/view-designer-conversion-options/distribution-tab.png)
 ![ 資料類型](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>磚轉換
視圖設計工具會使用總覽磚功能來代表和摘要整體狀態。 這些會以七個磚表示，範圍從數位到圖表。 在活頁簿中，使用者可以建立類似的視覺效果，並將其釘選到類似于原始總覽磚樣式的視覺效果。 

![主機庫](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>查看儀表板轉換
視圖設計師圖格通常包含兩個區段、一個視覺效果，以及一個與視覺效果中的資料相符的清單，例如 **環圈 & 清單** 磚。

![環圈圖](media/view-designer-conversion-options/donut-example.png)

使用活頁簿時，可讓使用者選取查詢視圖的一或兩個區段。 在活頁簿中編寫查詢是一個簡單的雙步驟程式。 首先，資料是從查詢產生的，第二個則是將資料轉譯為視覺效果。  在活頁簿中重新建立此視圖的範例如下所示：

![轉換](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>後續步驟
- [& 許可權存取活頁簿](view-designer-conversion-access.md)