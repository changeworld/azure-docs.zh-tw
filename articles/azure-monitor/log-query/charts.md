---
title: 從 Azure 監視器記錄查詢建立圖表 | Microsoft Docs
description: 說明 Azure 監視器中能以不同方式顯示您記錄資料的不同視覺效果。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77670316"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>從 Azure 監視器記錄查詢建立圖表

> [!NOTE]
> 您應該先完成 [Azure 監視器記錄查詢中的進階彙總](advanced-aggregations.md)，再完成此課程。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

本文說明 Azure 監視器中能以不同方式顯示您記錄資料的不同視覺效果。

## <a name="charting-the-results"></a>製作結果圖表
從檢閱過去一小時內每個作業系統類型中有多少部電腦開始：

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

根據預設，結果會顯示成資料表︰

![Table](media/charts/table-display.png)

若要取的更好的檢視，請選取 [圖表]****，然後選擇 [圓形圖]**** 選項以將結果視覺化：

![圓形圖](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>時間表
顯示 1 小時間隔中的處理器時間平均值、第 50 個百分位數與第 95 個百分位數。 查詢會產生多個查詢，而且您可以接著選取要在時間表上顯示哪些數列：

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

選取 **折線圖** 顯示選項：

![折線圖](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>參考線

參考線可協助您輕鬆地識別計量是否超過特定閾值。 若要將線條加到圖表中，請以常數欄延伸資料集：

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![參考線](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>多維度
`summarize` 之 `by` 子句中的多個運算式會在結果中建立多列，每個值組合都各有一列。

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

當您以圖表方式檢視結果時，它會使用來自 `by` 子句的第一欄。 下列範例顯示使用 _EventID_ 顯示堆疊直條圖。 維度類型必須是 `string`，因此此範例中的 _EventID_ 會被轉換為字串。 

![橫條圖 EventID](media/charts/charts-and-diagrams-multiDimension1.png)

您可以選取欄名下拉式清單以進行切換。 

![橫條圖 AccountType](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>後續步驟
若要了解如何搭配 Azure 監視器記錄資料使用 [Kusto 查詢語言](/azure/kusto/query/)，請參閱其他課程：

- [字串作業](string-operations.md)
- [日期和時間作業](datetime-operations.md)
- [彙總函式](aggregations.md)
- [進階彙總](advanced-aggregations.md)
- [JSON 與資料結構](json-data-structures.md)
- [進階查詢撰寫](advanced-query-writing.md)
- [聯結](joins.md)
