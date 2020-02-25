---
title: Azure 監視器視圖設計工具到活頁簿磚的轉換
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: b1cce6d1aafabda62b71ecd38ff5ac7df1033b26
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561981"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure 監視器視圖設計工具磚轉換
「[視圖設計](view-designer.md)工具」是 Azure 監視器的功能，可讓您建立自訂的視圖，以協助您將 Log Analytics 工作區中的資料視覺化，其中包含圖表、清單和時間軸。 它們會被淘汰，並取代為提供額外功能的活頁簿。 本文提供將不同磚轉換成活頁簿的詳細資訊。

## <a name="donut--list-tile"></a>環圈 & 清單磚

![環圈清單](media/view-designer-conversion-tiles/donut-list.png)

在活頁簿中重新建立環圈 & 清單磚牽涉到兩個不同的視覺效果。 環圈圖部分有兩個選項。
從 [開始] 選取 [**加入查詢**]，並將原始查詢從 view designer 貼入資料格中。

**選項1：** 從 [**視覺效果**] 下拉式清單中選取 [**圓形圖**]： ![圓形圖視覺效果 功能表](media/view-designer-conversion-tiles/pie-chart.png)

**選項2：** 從 [**視覺效果**] 下拉式清單中選取 [**由查詢設定**]，並將 `| render piechart` 新增至查詢：

 ![視覺效果功能表](media/view-designer-conversion-tiles/set-by-query.png)

**範例**

原始查詢
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

已更新查詢
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

如需建立清單和啟用走勢圖，請參閱關於[一般](view-designer-conversion-tasks.md)工作的文章。

以下是如何在活頁簿中向量環圈圖 & 清單磚的範例：

![環圈清單活頁簿](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>折線圖 & 清單磚
![折線圖清單](media/view-designer-conversion-tiles/line-list.png) 

若要重新建立折線圖部分，請更新查詢，如下所示：

原始查詢
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

已更新查詢
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

有兩個選項可讓您將折線圖視覺化

**選項1：** 從 [**視覺效果**] 下拉式清單中選取 [**折線圖**]：
 
 ![折線圖功能表](media/view-designer-conversion-tiles/line-visualization.png)

**選項2：** 從 [**視覺效果**] 下拉式清單中選取 [**由查詢設定**]，並將 `| render linechart` 新增至查詢：

 ![視覺效果功能表](media/view-designer-conversion-tiles/set-by-query.png)

**範例**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

如需建立清單和啟用走勢圖，請參閱關於[一般](view-designer-conversion-tasks.md)工作的文章。

以下是如何在活頁簿中向量折線圖 & 清單磚的範例：

![折線圖清單活頁簿](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>數位 & 清單磚

 ![磚清單](media/view-designer-conversion-tiles/tile-list-example.png)

針對 [數位] 磚，更新查詢，如下所示：

原始查詢
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

已更新查詢
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

將 [視覺效果] 下拉式清單變更為 [**磚**]，然後選取 [**磚設定**]。
 ![磚視覺效果](media/view-designer-conversion-tiles/tile-visualization.png)

將 [**標題**] 區段保留空白，然後選取 [**左方**]。 將 [使用資料**行：** **計數**] 和 [資料**行**轉譯器] 的值變更為 [**大數**]：

![磚設定](media/view-designer-conversion-tiles/tile-settings.png)

 
如需建立清單和啟用走勢圖，請參閱關於[一般](view-designer-conversion-tasks.md)工作的文章。

以下是如何在活頁簿中向量數位 & 清單磚的範例：

![數位清單活頁簿](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>時間表和清單

 ![時程表清單](media/view-designer-conversion-tiles/time-list.png)

針對時間軸，請更新您的查詢，如下所示：

原始查詢
```KQL
search * 
| sort by TimeGenerated desc
```

已更新查詢
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

有兩個選項可將查詢視覺化為橫條圖：

**選項1：** 從 [**視覺效果**] 下拉式清單中選取 [**橫條圖**]： ![Barchart 視覺效果](media/view-designer-conversion-tiles/bar-visualization.png)
 
**選項2：** 從 [**視覺效果**] 下拉式清單中選取 [**由查詢設定**]，並將 `| render barchart` 新增至查詢：

 ![視覺效果功能表](media/view-designer-conversion-tiles/set-by-query.png)

 
如需建立清單和啟用走勢圖，請參閱關於[一般](view-designer-conversion-tasks.md)工作的文章。

以下是如何在活頁簿中向量時間軸 & 清單磚的範例：

![時程表清單活頁簿](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>後續步驟

- [瀏覽器與活頁簿轉換的總覽](view-designer-conversion-overview.md)
