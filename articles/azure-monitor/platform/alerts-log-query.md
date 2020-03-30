---
title: Azure 監視器中的記錄警示查詢 | Microsoft Docs
description: 對於在 Azure 監視器更新中撰寫記錄警示的有效查詢以及轉換現有查詢的程序提供建議。
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: fdf492b8f103e725046b9b1cbbd079c4d249664a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667783"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Azure 監視器中的記錄警示查詢
[以 Azure 監視器記錄為基礎的警示規則](alerts-unified-log.md)會定期執行，因此您應該確定已撰寫規則將額外負荷和延遲降至最低。 這篇文章對於撰寫記錄警示的有效查詢以及轉換現有查詢的程序提供建議。 

## <a name="types-of-log-queries"></a>記錄查詢類型
[Azure 監視器中的日誌查詢](../log-query/log-query-overview.md)從表或[搜索](/azure/kusto/query/searchoperator)或[聯合](/azure/kusto/query/unionoperator)運算子開始。

例如，下列查詢只限於 _SecurityEvent_ 資料表，而且搜尋特定事件識別碼。 這是查詢必須處理的唯一資料表。

``` Kusto
SecurityEvent | where EventID == 4624 
```

開頭為 `search` 或 `union` 的查詢可供您搜尋同一個資料表中的多個資料行，或甚至搜尋多個資料表。 下列範例會顯示搜尋字詞 _Memory_ 的多個方法：

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

雖然在資料探索期間可使用 `search` 和 `union` 搜尋詞彙的整個資料模型，但是效果不如使用資料表，因為這些必須掃描多個資料表。 由於警示規則的查詢會定期執行，因此可能會導致過多的額外負荷，而導致警示出現延遲。 由於這個額外負荷，對於 Azure 中的記錄警示規則進行的查詢開頭一律是資料表，這會定義明確的範圍，藉以改善查詢效能和結果相關性。

## <a name="unsupported-queries"></a>不支援的查詢
從 2019 年 1 月 11 日開始，Azure 入口網站不支援建立或修改使用 `search` 或 `union` 運算子的記錄警示規則。 在警示規則中使用這些運算子會傳回錯誤訊息。 這項變更不影響現有的警示規則，以及使用 Log Analytics API 建立和編輯的警示規則。 此外，您仍應考慮變更使用這幾種查詢的任何警示規則，以便改善其效率。  

使用[跨資源查詢](../log-query/cross-workspace-query.md)的記錄警示規則不受此變更所影響，因為跨資源查詢使用 `union`，這會將查詢範圍限制於特定資源。 這並不等於 `union *`，這已經無法再使用。  下列範例在記錄警示規則中有效：

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>新的 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 支援記錄警示中的[跨資源查詢](../log-query/cross-workspace-query.md)。 根據預設，Azure 監視器會使用[舊版 Log Analytics 警示 API](api-alerts.md) 從 Azure 入口網站建立新的記錄警示規則，除非您從[舊版記錄警示 API](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) 切換。 切換之後，新的 API 將成為 Azure 入口網站中新警示規則的預設值，並允許您建立跨資源查詢記錄警示規則。 您可以使用[適用於 scheduledQueryRules API 的 ARM 範本](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template)建立[跨資源查詢](../log-query/cross-workspace-query.md)記錄警示規則而無需進行切換 – 但此警示規則可透過 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 進行管理，而不是 Azure 入口網站。

## <a name="examples"></a>範例
下列範例包含使用 `search` 和 `union` 的記錄檔查詢，並提供您可用來修改這些查詢以便搭配警示規則使用的步驟。

### <a name="example-1"></a>範例 1
您想要使用下列查詢建立記錄警示規則，以便使用 `search` 擷取效能資訊： 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

若要修改此查詢，請先使用下列查詢識別屬性隸屬的資料表：

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

此查詢的結果會顯示 _CounterName_ 屬性來自於 _Perf_ 資料表。 

您可以使用此結果來建立警示規則，以便用於下列查詢：

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>範例 2
您想要使用下列查詢建立記錄警示規則，以便使用 `search` 擷取效能資訊： 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

若要修改此查詢，請先使用下列查詢識別屬性隸屬的資料表：

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

此查詢的結果會顯示 _ObjectName_ 和 _CounterName_ 屬性來自於 _Perf_ 資料表。 

您可以使用此結果來建立警示規則，以便用於下列查詢：

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>範例 3

您想要使用下列查詢建立記錄警示規則，以便使用 `search` 和 `union` 擷取效能資訊： 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

若要修改此查詢，請先使用下列查詢，在查詢的第一個部分中識別屬性隸屬的資料表： 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

此查詢的結果會顯示所有這些屬性來自於 _Perf_ 資料表。 

現在，使用 `union` 與 `withsource` 命令，識別哪一個來源資料表已提供每個資料列。

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

此查詢的結果會顯示這些屬性也來自於 _Perf_ 資料表。 

您可以使用這些結果來建立警示規則，以便用於下列查詢： 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
``` 

### <a name="example-4"></a>範例 4
您想要使用下列查詢建立記錄警示規則，以便聯結兩個 `search` 查詢的結果：

```Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by Computer, Hour = bin(TimeGenerated, 1h) 
| join kind = leftouter ( 
    search in (Heartbeat) OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
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

 
結果指出聯結的右邊出現的屬性屬於 Heartbeat 資料表。 

您可以使用這些結果來建立警示規則，以便用於下列查詢： 


``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat  
    | where OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```

## <a name="next-steps"></a>後續步驟
- 了解 [Azure 監視器中的記錄警示](alerts-log.md)。
- 了解[記錄查詢](../log-query/log-query-overview.md)。

