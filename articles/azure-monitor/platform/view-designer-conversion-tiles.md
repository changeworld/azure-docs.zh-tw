---
title: Azure 監視器視圖設計器到活頁簿磁貼轉換
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658621"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure 監視器視圖設計器磁貼轉換
[視圖設計器](view-designer.md)是 Azure 監視器的一項功能，允許您創建自訂視圖，以説明您在日誌分析工作區中視覺化資料，包括圖表、清單和時間表。 它們正在逐步淘汰，代之以提供額外功能的活頁簿。 本文提供了將不同磁貼轉換為活頁簿的詳細資訊。

## <a name="donut--list-tile"></a>圓環&清單磁貼

![甜甜圈清單](media/view-designer-conversion-tiles/donut-list.png)

在活頁簿中重新創建圓環&清單磁貼涉及兩個單獨的視覺化效果。 對於圓環部分，有兩個選項。
對於這兩個開始選擇**添加查詢**，並將從視圖設計器中的原始查詢粘貼到儲存格中。

**選項 1：** 從**視覺化下**拉功能表中選擇**圓形圖**：![圓形圖視覺化功能表](media/view-designer-conversion-tiles/pie-chart.png)

**選項 2：** 從**視覺化**下拉清單中選擇**按查詢設置**並添加到`| render piechart`查詢：

 ![視覺化功能表](media/view-designer-conversion-tiles/set-by-query.png)

**範例**

原始查詢
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

更新查詢
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

有關創建清單和啟用 Sparklines，請參閱有關[常見任務](view-designer-conversion-tasks.md)的文章。

下面是如何在活頁簿中重新解釋圓環&清單磁貼的示例：

![甜甜圈清單活頁簿](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>折線圖&清單磁貼
![折線圖清單](media/view-designer-conversion-tiles/line-list.png) 

要重新創建折線圖部分，請按如下方式更新查詢：

原始查詢
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

更新查詢
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

有兩個選項用於視覺化折線圖

**選項 1：** 從**視覺化**下拉清單中選擇**折線圖**：
 
 ![折線圖功能表](media/view-designer-conversion-tiles/line-visualization.png)

**選項 2：** 從**視覺化**下拉清單中選擇**按查詢設置**並添加到`| render linechart`查詢：

 ![視覺化功能表](media/view-designer-conversion-tiles/set-by-query.png)

**範例**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

有關創建清單和啟用 Sparklines，請參閱有關[常見任務](view-designer-conversion-tasks.md)的文章。

以下是如何在活頁簿中重新解釋折線圖&清單磁貼的示例：

![折線圖清單活頁簿](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>編號&清單磁貼

 ![磁貼清單](media/view-designer-conversion-tiles/tile-list-example.png)

對於數位磁貼，請按如下方式更新查詢：

原始查詢
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

更新查詢
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

將"視覺化"下拉式功能表更改為**磁貼**，然後選擇 **"磁貼設置**"。
 ![磁貼視覺化](media/view-designer-conversion-tiles/tile-visualization.png)

將 **"標題"** 部分留空，然後選擇 **"左側**"。 將 **"使用"列的值更改為****計數**，將**列渲染器**更改為 **"大數**" ：

![磁貼設置](media/view-designer-conversion-tiles/tile-settings.png)

 
有關創建清單和啟用 Sparklines，請參閱有關[常見任務](view-designer-conversion-tasks.md)的文章。

下面是如何在活頁簿中重新解釋數位&清單磁貼的示例：

![編號清單活頁簿](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>時間表和清單

 ![時間表清單](media/view-designer-conversion-tiles/time-list.png)

對於時間表，請按如下方式更新查詢：

原始查詢
```KQL
search * 
| sort by TimeGenerated desc
```

更新查詢
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

有兩個選項用於將查詢視覺化為橫條圖：

**選項 1：** 從**視覺化下**拉清單中選擇![**橫條圖**：橫條圖視覺化](media/view-designer-conversion-tiles/bar-visualization.png)
 
**選項 2：** 從**視覺化**下拉清單中選擇**按查詢設置**並添加到`| render barchart`查詢：

 ![視覺化功能表](media/view-designer-conversion-tiles/set-by-query.png)

 
有關創建清單和啟用 Sparklines，請參閱有關[常見任務](view-designer-conversion-tasks.md)的文章。

以下是如何在活頁簿中重新解釋時間表&清單磁貼的示例：

![時間軸清單活頁簿](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>後續步驟

- [視圖設計器到活頁簿轉換的概述](view-designer-conversion-overview.md)
