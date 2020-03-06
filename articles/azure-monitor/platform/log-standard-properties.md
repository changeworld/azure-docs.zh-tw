---
title: Azure 監視器記錄中的標準屬性 | Microsoft Docs
description: 說明「Azure 監視器」記錄中多種資料類型通用的屬性。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 252ddeb372744986df0b8ba9b742d0462a4e8202
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372277"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Azure 監視器記錄中的標準屬性
Azure 監視器記錄檔中的資料會[儲存為 Log Analytics 工作區或 Application Insights 應用程式中的一組記錄](../log-query/logs-structure.md)，每一個都具有具有一組唯一屬性的特定資料類型。 有許多資料類型都具有多種類型之間通用的標準屬性。 本文將說明這些屬性，並提供在查詢中加以使用的範例。

> [!NOTE]
> 部分標準屬性不會顯示在 Log Analytics 中的架構視圖或 intellisense 中，除非您在輸出中明確指定屬性，否則不會顯示在查詢結果中。

## <a name="timegenerated-and-timestamp"></a>TimeGenerated 和時間戳記
**TimeGenerated** （Log Analytics 工作區）和**時間戳記**（Application Insights 應用程式）屬性包含資料來源建立記錄的日期和時間。 如需詳細資訊，請參閱[Azure 監視器中的記錄資料內建時間](data-ingestion-time.md)。

**TimeGenerated**和**timestamp**提供通用的屬性，可用於依時間篩選或摘要。 當您在 Azure 入口網站中選取視圖或儀表板的時間範圍時，它會使用 TimeGenerated 或 timestamp 來篩選結果。 

### <a name="examples"></a>範例

下列查詢會傳回前一週中每天建立的錯誤事件數目。

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

下列查詢會傳回上一周中每天所建立的例外狀況數目。

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
**\_TimeReceived**屬性包含 Azure 雲端中 Azure 監視器內建點收到記錄的日期和時間。 這有助於識別資料來源與雲端之間的延遲問題。 其中一個範例是網路問題，會造成從代理程式傳送資料的延遲。 如需詳細資訊，請參閱[Azure 監視器中的記錄資料內建時間](data-ingestion-time.md)。

下列查詢提供來自代理程式之事件記錄的平均延遲（以小時為單位）。 這包括從代理程式到雲端的時間，以及記錄查詢可使用的總時間。

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>類型和 itemType
[**類型**（Log Analytics 工作區）] 和 [ **itemType** （Application Insights 應用程式）] 屬性會保存從中抓取記錄之資料表的名稱，也可以將其視為記錄類型。 在結合多份資料表中記錄的查詢中 (例如使用 `search` 運算子的那些查詢)，此屬性十分適合用來區分不同類型的記錄。 **$table** 可用來取代某些位置中的**類型**。

### <a name="examples"></a>範例
下列查詢會依據類型傳回過去一小時所收集的記錄計數。

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
**\_ItemId**屬性會保留記錄的唯一識別碼。


## <a name="_resourceid"></a>\_ResourceId
**\_ResourceId** 屬性會保存與記錄相關聯的資源唯一識別碼。 這可讓您有標準屬性可用來將查詢範圍限定於來自特定資源的記錄，或跨多個資料表聯結相關的資料。

就 Azure 資源而言， **_ResourceId** 的值為 [Azure 資源識別碼 URL](../../azure-resource-manager/templates/template-functions-resource.md)。 此屬性目前僅適用於 Azure 資源，但未來將擴充至 Azure 以外的資源，例如內部部署電腦。

> [!NOTE]
> 某些資料類型的欄位已包含 Azure 資源識別碼，或是至少屬於其一部份的訂用帳戶識別碼等。 雖然這些欄位會保留回溯相容性，但還是建議您使用 _ResourceId 執行交叉相互關聯，這樣將會更一致。

### <a name="examples"></a>範例
下列是將每一部電腦的效能和事件資料相聯結的查詢。 其中顯示識別碼為 _101_ 且處理器使用率超過 50% 的所有事件。

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

下列是將 _AzureActivity_ 記錄與 _SecurityEvent_ 記錄相聯結的查詢。 其中顯示已登入這些機器的使用者所產生的所有活動作業。

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

下列查詢會剖析 **_ResourceId** ，並匯總每個 Azure 訂用帳戶的計費資料量。

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

請謹慎使用這些 `union withsource = tt *` 查詢，因為執行跨資料類型掃描的費用相當高昂。

## <a name="_isbillable"></a>\_IsBillable
**\_IsBillable** 屬性會指定擷取的資料是否可計費。 **\_IsBillable** 等於 _false_ 的資料會免費收集，且費用不會計入您的 Azure 帳戶。

### <a name="examples"></a>範例
若要取得傳送計費資料類型的電腦清單，請使用下列查詢：

> [!NOTE]
> 請謹慎使用這些查詢搭配 `union withsource = tt *`，因為執行跨資料類型掃描相當昂貴。 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

加以延伸，即可傳回每小時傳送計費資料類型的電腦計數：

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
如果 **\_IsBillable** 為 true， **\_BilledSize** 屬性可指定將計入 Azure 帳戶的資料大小 (以位元組為單位)。


### <a name="examples"></a>範例
若要查看每部電腦擷取的可計費事件的大小，請使用大小以位元組計算的 `_BilledSize` 屬性：

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

若要查看每個訂用帳戶所內嵌的可計費事件大小，請使用下列查詢：

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

若要查看每個資源群組內嵌的可計費事件大小，請使用下列查詢：

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


若要查看每部電腦所擷取的事件計數，請使用下列查詢：

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

若要查看每部電腦所擷取的可計費事件計數，請使用下列查詢： 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

若要從特定電腦查看可計費資料類型的計數，請使用下列查詢：

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器記錄資料的儲存方式](../log-query/log-query-overview.md)。
- 參與[撰寫記錄查詢](../../azure-monitor/log-query/get-started-queries.md)的課程。
- 參與[在記錄查詢中聯結資料表](../../azure-monitor/log-query/joins.md)的課程。
