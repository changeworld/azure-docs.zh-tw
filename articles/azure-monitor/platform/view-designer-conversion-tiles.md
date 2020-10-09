---
title: Azure 監視器視圖設計工具到活頁簿磚的轉換
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77658621"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure 監視器 view designer 磚轉換
[View designer](view-designer.md) 是 Azure 監視器的功能，可讓您建立自訂視圖，以協助您在 Log Analytics 工作區中以圖表、清單和時程表來視覺化資料。 它們會被淘汰並取代為提供額外功能的活頁簿。 本文提供將不同磚轉換成活頁簿的詳細資料。

## <a name="donut--list-tile"></a>環圈圖 & 清單磚

![環圈清單](media/view-designer-conversion-tiles/donut-list.png)

在活頁簿中重新建立環圈圖 & 清單磚，需要兩個不同的視覺效果。 環圈部分有兩個選項。
從開始，選取 [ **加入查詢** ]，然後從 view designer 將原始查詢貼到儲存格。

**選項1：** 從**視覺效果**下拉式清單中選取**圓形圖**： ![ 圓形圖視覺效果功能表](media/view-designer-conversion-tiles/pie-chart.png)

**選項2：** 從 [**視覺效果**] 下拉式清單選取 [**依查詢設定**]，並加入 `| render piechart` 至查詢：

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

如需建立清單和啟用走勢圖，請參閱關於 [一般](view-designer-conversion-tasks.md)工作的文章。

下列範例說明如何在活頁簿中重新解譯環圈圖 & 清單磚：

![環圈圖清單活頁簿](media/view-designer-conversion-tiles/donut-workbooks.png)

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

有兩個選項可將折線圖視覺化

**選項1：** 從 [**視覺效果**] 下拉式清單中選取**折線圖**：
 
 ![折線圖功能表](media/view-designer-conversion-tiles/line-visualization.png)

**選項2：** 從 [**視覺效果**] 下拉式清單選取 [**依查詢設定**]，並加入 `| render linechart` 至查詢：

 ![視覺效果功能表](media/view-designer-conversion-tiles/set-by-query.png)

**範例**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

如需建立清單和啟用走勢圖，請參閱關於 [一般](view-designer-conversion-tasks.md)工作的文章。

下列範例說明如何在活頁簿中重新解譯折線圖 & 清單磚：

![折線圖清單活頁簿](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>數位 & 清單磚

 ![磚清單](media/view-designer-conversion-tiles/tile-list-example.png)

在 [數位] 磚中，更新查詢，如下所示：

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

將 [視覺效果] 下拉式清單變更為 [ **磚** ]，然後選取 [ **磚設定**]。
 ![磚視覺效果](media/view-designer-conversion-tiles/tile-visualization.png)

將 [ **標題** ] 區段保留空白，然後選取 [ **左方**]。 將 [使用資料 **行：** **計數**] 和 [資料 **行** 轉譯器] 的值變更為 [ **大數值**]：

![磚設定](media/view-designer-conversion-tiles/tile-settings.png)

 
如需建立清單和啟用走勢圖，請參閱關於 [一般](view-designer-conversion-tasks.md)工作的文章。

下列範例示範如何在活頁簿中重新解譯數位 & 清單磚：

![編號清單活頁簿](media/view-designer-conversion-tiles/number-workbooks.png)

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

**選項1：** 從 [**視覺效果**] 下拉式清單選取**橫條圖**： ![ Barchart 視覺效果](media/view-designer-conversion-tiles/bar-visualization.png)
 
**選項2：** 從 [**視覺效果**] 下拉式清單選取 [**依查詢設定**]，並加入 `| render barchart` 至查詢：

 ![視覺效果功能表](media/view-designer-conversion-tiles/set-by-query.png)

 
如需建立清單和啟用走勢圖，請參閱關於 [一般](view-designer-conversion-tasks.md)工作的文章。

下列範例說明如何在活頁簿中重新解譯時間軸 & 清單磚：

![時程表清單活頁簿](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>後續步驟

- [活頁簿轉換的視圖設計工具總覽](view-designer-conversion-overview.md)
