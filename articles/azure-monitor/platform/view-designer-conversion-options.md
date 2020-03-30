---
title: Azure 監視器視圖設計器到活頁簿轉換選項
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658705"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure 監視器視圖設計器到活頁簿轉換選項
[視圖設計器](view-designer.md)是 Azure 監視器的一項功能，允許您創建自訂視圖，以説明您在日誌分析工作區中視覺化資料，包括圖表、清單和時間表。 它們正在逐步淘汰，代之以提供額外功能的活頁簿。 本文比較了兩者之間的基本概念和將視圖轉換為活頁簿的選項。

## <a name="basic-workbook-designs"></a>基本活頁簿設計

視圖設計器具有固定的靜態表示風格，而活頁簿允許自由地包括和修改資料的表示方式。 下圖描述了在轉換視圖時如何排列活頁簿的兩個示例。

[垂直活頁簿](view-designer-conversion-examples.md#vertical)
![垂直](media/view-designer-conversion-options/view-designer-vertical.png)

[選項卡式活頁簿](view-designer-conversion-examples.md#tabbed)
![資料類型分發](media/view-designer-conversion-options/distribution-tab.png)
![選項卡 隨時間的資料類型選項卡](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>磁貼轉換
視圖設計器使用概覽磁貼功能表示和匯總總體狀態。 這些表示在七個磁貼中，從數位到圖表不等。 在活頁簿中，使用者可以創建類似的視覺化效果，並將其固定為類似于概述磁貼的原始樣式。 

![主機庫](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>查看儀表板轉換
查看設計器磁貼通常由兩個部分組成：視覺化效果和與視覺化資料匹配的清單，例如 **"圓環&清單"** 磁貼。

![環圈圖](media/view-designer-conversion-options/donut-example.png)

使用活頁簿，我們允許使用者選取查詢視圖的一個或兩個部分。 在活頁簿中編寫查詢是一個簡單的兩步過程。 首先，從查詢生成資料，其次，資料呈現為視覺化效果。  如何在活頁簿中重新創建此視圖的示例如下：

![轉換](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>後續步驟
- [訪問活頁簿&許可權](view-designer-conversion-access.md)