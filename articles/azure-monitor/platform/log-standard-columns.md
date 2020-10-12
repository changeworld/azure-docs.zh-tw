---
title: Azure 監視器記錄檔記錄中的標準資料行 |Microsoft Docs
description: 描述 Azure 監視器記錄中多個資料類型通用的資料行。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: 01c0b6f280b8179760c6ecc55fd7feca3ddf2080
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90039005"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Azure 監視器記錄中的標準資料行
Azure 監視器記錄檔中的資料會 [儲存為 Log Analytics 工作區或 Application Insights 應用程式中的一組記錄](../log-query/logs-structure.md)，每個資料類型都有一組唯一的資料行。 許多資料類型會有多個類型之間常見的標準資料行。 本文描述這些資料行，並提供如何在查詢中使用它們的範例。

Application Insights 中的工作區應用程式會將其資料儲存在 Log Analytics 工作區中，並使用與工作區中其他其他資料表相同的標準資料行。 傳統應用程式會個別儲存其資料，並具有不同的標準資料行，如本文中所指定。

> [!NOTE]
> 某些標準資料行不會顯示在 Log Analytics 的架構視圖或 intellisense 中，除非您明確指定輸出中的資料行，否則這些資料行不會顯示在查詢結果中。

## <a name="timegenerated-and-timestamp"></a>TimeGenerated 和 timestamp
**TimeGenerated** (Log Analytics 工作區) 和**時間戳記** (Application Insights 應用程式) 資料行包含資料來源建立記錄的日期和時間。 如需詳細資訊，請參閱 [Azure 監視器中的記錄資料內嵌時間](data-ingestion-time.md) 。

**TimeGenerated** 和 **timestamp** 提供通用資料行，以用於篩選或依時間摘要。 當您在 Azure 入口網站中選取視圖或儀表板的時間範圍時，它會使用 TimeGenerated 或 timestamp 來篩選結果。 

### <a name="examples"></a>範例

下列查詢會傳回前一週中每天建立的錯誤事件數目。

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

下列查詢會傳回上一周的每一天建立的例外狀況數目。

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
** \_ TimeReceived**資料行包含 Azure 雲端中的 Azure 監視器內嵌點收到記錄的日期和時間。 這有助於識別資料來源與雲端之間的延遲問題。 其中一個範例是網路問題，導致從代理程式傳送資料時發生延遲。 如需詳細資訊，請參閱 [Azure 監視器中的記錄資料內嵌時間](data-ingestion-time.md) 。

下列查詢會針對代理程式中的事件記錄，提供平均延遲（以小時為單位）。 這包括從代理程式到雲端的時間，以及記錄查詢可用記錄的總時間。

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>類型和 itemType
 (Log Analytics 工作區) 和**itemType** (Application Insights 應用程式) 資料行的**類型**，會保留從中抓取記錄的資料表名稱，也可以視為記錄類型。 此資料行適用于結合多個資料表（例如使用運算子的記錄）的查詢， `search` 以區分不同類型的記錄。 **$table** 可用來取代某些位置中的**類型**。

### <a name="examples"></a>範例
下列查詢會依據類型傳回過去一小時所收集的記錄計數。

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
** \_ ItemId**資料行保存記錄的唯一識別碼。


## <a name="_resourceid"></a>\_ResourceId
** \_ ResourceId**資料行保存與記錄相關聯之資源的唯一識別碼。 如此一來，您就可以使用標準資料行，將查詢的範圍限定于特定資源的記錄，或在多個資料表之間聯結相關資料。

就 Azure 資源而言，**_ResourceId** 的值為 [Azure 資源識別碼 URL](../../azure-resource-manager/templates/template-functions-resource.md)。 此資料行目前僅限於 Azure 資源，但會延伸至 Azure 外部的資源，例如內部部署電腦。

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

下列查詢會剖析 **_ResourceId** ，並匯總每個 Azure 訂用帳戶的計費資料磁片區。

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

請謹慎使用這些 `union withsource = tt *` 查詢，因為執行跨資料類型掃描的費用相當高昂。

## <a name="_isbillable"></a>\_IsBillable
** \_ IsBillable**資料行會指定內嵌資料是否計費。 ** \_ IsBillable**等於的資料 `false` 會免費收集，且不會向您的 Azure 帳戶計費。

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
** \_ BilledSize**資料行會指定** \_ IsBillable**為 true 時，將向您的 Azure 帳戶收取的資料大小（以位元組為單位）。


### <a name="examples"></a>範例
若要查看每部電腦內嵌的可計費事件大小，請使用 `_BilledSize` 提供大小（以位元組為單位）的資料行：

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

若要查看每個訂用帳戶內嵌的可計費事件大小，請使用下列查詢：

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

若要查看特定電腦的可計費資料類型計數，請使用下列查詢：

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器記錄資料的儲存方式](../log-query/log-query-overview.md)。
- 參與[撰寫記錄查詢](../log-query/get-started-queries.md)的課程。
- 參與[在記錄查詢中聯結資料表](../log-query/joins.md)的課程。

