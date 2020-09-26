---
title: 優化記錄警示查詢 |Microsoft Docs
description: 撰寫有效率警示查詢的建議
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: 7f03858b2427b2a2069ebe2c9d06425e7a741e2b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294354"
---
# <a name="optimizing-log-alert-queries"></a>優化記錄警示查詢
本文描述如何寫入和轉換 [記錄警示](alerts-unified-log.md) 查詢，以達到最佳效能。 優化查詢可減少延遲，以及經常執行的警示載入。

## <a name="how-to-start-writing-an-alert-log-query"></a>如何開始撰寫警示記錄查詢

警示查詢會開始 [查詢 Log Analytics 中](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) 指出問題的記錄資料。 您可以使用「 [警示查詢範例」主題](../log-query/saved-queries.md) 來瞭解您可以探索的內容。 您也可以 [開始撰寫自己的查詢](../log-query/get-started-portal.md)。 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>指出問題的查詢，而不是警示

警示流程的建立是為了將表示問題的結果轉換成警示。 例如，在類似以下的查詢案例中：

``` Kusto
SecurityEvent
| where EventID == 4624
```

如果使用者的意圖是警示，當發生此事件種類時，警示邏輯會附加 `count` 至查詢。 將執行的查詢將會是：

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

不需要將警示邏輯新增到查詢中，這樣做可能甚至會造成問題。 在上述範例中，如果您將包含 `count` 在查詢中，它一律會產生值1，因為警示服務將會完成 `count` `count` 。

### <a name="avoid-limit-and-take-operators"></a>避免 `limit` 和 `take` 運算子

`limit` `take` 在查詢中使用和會增加警示的延遲和載入，因為結果不會隨著時間而保持一致。 建議您只在需要時才使用它。

## <a name="log-query-constraints"></a>記錄查詢準則約束
[Azure 監視器中的記錄查詢](../log-query/log-query-overview.md) 是以資料表、 [`search`](/azure/kusto/query/searchoperator) 或運算子開頭 [`union`](/azure/kusto/query/unionoperator) 。

記錄警示規則的查詢應該一律以資料表開頭，以定義清楚的範圍，以改善查詢效能和結果相關性。 警示規則中的查詢經常執行，因此使用 `search` 和 `union` 可能會導致額外的額外負荷增加警示的延遲，因為它需要跨多個資料表進行掃描。 這些運算子也可以減少警示服務將查詢優化的能力。

我們不支援建立或修改使用或運算子的記錄警示規則 `search` `union` ，而這些規則預期會進行跨資源查詢。

例如，下列警示查詢的範圍是 _SecurityEvent_ 資料表，並且會搜尋特定的事件識別碼。 這是查詢必須處理的唯一資料表。

``` Kusto
SecurityEvent
| where EventID == 4624
```

使用 [跨資源查詢](../log-query/cross-workspace-query.md) 的記錄警示規則不會受到這項變更的影響，因為跨資源查詢會使用的類型 `union` ，這會將查詢範圍限制為特定的資源。 下列範例是有效的記錄警示查詢：

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> 新的[SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)支援[跨資源查詢](../log-query/cross-workspace-query.md)。 如果您仍使用 [舊版 Log Analytics 警示 API](api-alerts.md) 來建立記錄警示，您可以在 [這裡](alerts-log-api-switch.md)瞭解如何切換。

## <a name="examples"></a>範例
下列範例包含使用和的記錄查詢 `search` ， `union` 並提供可讓您用來修改這些查詢以用於警示規則的步驟。

### <a name="example-1"></a>範例 1
您想要使用下列查詢建立記錄警示規則，以使用下列程式抓取效能資訊 `search` ： 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

若要修改此查詢，請先使用下列查詢識別屬性隸屬的資料表：

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

此查詢的結果會顯示 _CounterName_ 屬性來自於 _Perf_ 資料表。

您可以使用此結果來建立下列您將用於警示規則的查詢：

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>範例 2
您想要使用下列查詢建立記錄警示規則，以使用下列程式抓取效能資訊 `search` ： 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

若要修改此查詢，請先使用下列查詢識別屬性隸屬的資料表：

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

此查詢的結果會顯示 _ObjectName_ 和 _CounterName_ 屬性來自於 _Perf_ 資料表。

您可以使用此結果來建立下列您將用於警示規則的查詢：

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>範例 3

您想要使用下列查詢建立記錄警示規則，以使用 `search` 和 `union` 來取出效能資訊： 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

若要修改此查詢，請先使用下列查詢，在查詢的第一個部分中識別屬性隸屬的資料表： 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

此查詢的結果會顯示所有這些屬性來自於 _Perf_ 資料表。

現在，使用 `union` 與 `withsource` 命令，識別哪一個來源資料表已提供每個資料列。

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

此查詢的結果會顯示這些屬性也來自於 _Perf_ 資料表。

您可以使用這些結果來建立下列您將用於警示規則的查詢： 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>範例 4
您想要使用下列查詢建立記錄警示規則，以聯結兩個查詢的結果 `search` ：

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

若要修改此查詢，請先使用下列查詢，識別聯結的左邊有這些屬性的資料表： 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

結果指出聯結的左邊出現的屬性屬於 _SecurityEvent_ 資料表。 

現在使用下列查詢，識別聯結的右邊有這些屬性的資料表： 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
結果會指出聯結右邊的屬性屬於 _心跳_ 表。

您可以使用這些結果來建立下列您將用於警示規則的查詢： 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>後續步驟
- 了解 [Azure 監視器中的記錄警示](alerts-log.md)。
- 了解[記錄查詢](../log-query/log-query-overview.md)。
