---
title: 優化 Azure 監視器中的記錄查詢
description: Azure 監視器中優化記錄查詢的最佳做法。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 31b1ff3324c610c385ad793f124735be30cab9f9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327709"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>優化 Azure 監視器中的記錄查詢
Azure 監視器記錄使用 [Azure 資料總管 (ADX) ](/azure/data-explorer/) 儲存記錄資料，並執行查詢來分析該資料。 它會為您建立、管理和維護 ADX 叢集，並針對您的記錄分析工作負載進行優化。 當您執行查詢時，它會進行優化，並路由傳送至儲存工作區資料的適當 ADX 叢集。 Azure 監視器記錄和 Azure 資料總管都會使用許多自動查詢優化機制。 雖然自動優化提供大幅提升，但在某些情況下，您可以大幅改善查詢效能。 本文說明效能考慮，以及用來修正這些問題的幾項技術。

大部分的技術都是直接在 Azure 資料總管上執行的查詢，以及 Azure 監視器記錄檔的常見方法，不過這裡討論的有幾個獨特的 Azure 監視器記錄考慮。 如需更多 Azure 資料總管優化秘訣，請參閱 [查詢最佳做法](/azure/kusto/query/best-practices)。

優化的查詢將會：

- 執行速度更快，減少查詢執行的整體持續時間。
- 有較小的機會可以進行節流或拒絕。

您應該特別注意用於週期性和暴增使用量的查詢，例如儀表板、警示、Logic Apps 和 Power BI。 在這些情況下，無效查詢的影響相當重要。

## <a name="query-performance-pane"></a>查詢效能窗格
在 Log Analytics 中執行查詢之後，按一下查詢結果上方的向下箭號來查看 [查詢效能] 窗格，以顯示查詢的數個效能指標的結果。 下一節會說明這些效能指標。

![查詢效能窗格](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>查詢效能指標

下列查詢效能指標適用于每個執行的查詢：

- [CPU 總計](#total-cpu)：用於跨所有計算節點處理查詢的整體計算。 它代表用於計算、剖析和資料提取的時間。 

- [用於處理查詢的資料](#data-used-for-processed-query)：已存取用來處理查詢的整體資料。 受目標資料表大小、使用的時間範圍、套用的篩選，以及所參考資料行數目的影響。

- 已[處理查詢的時間範圍](#time-span-of-the-processed-query)：最新的和最舊的資料之間的差距，可處理查詢。 受查詢指定的明確時間範圍影響。

- 已[處理資料的存留期](#age-of-processed-data)：目前與存取用來處理查詢的最舊資料之間的差距。 它會高度影響資料提取的效率。

- [工作區數目](#number-of-workspaces)：由於隱含或明確選取，在查詢處理期間存取了多少工作區。

- [區域數目](#number-of-regions)：根據隱含或明確選取的工作區，在查詢處理期間存取的區域數量。 多區域查詢的效率較低，且效能指標呈現部分涵蓋範圍。

- [平行](#parallelism)處理原則：指出系統能夠在多個節點上執行此查詢的程度。 僅與具有高 CPU 耗用量的查詢相關。 使用特定函數和運算子的影響。


## <a name="total-cpu"></a>CPU 總計
在所有查詢處理節點上，為了處理此查詢而投資的實際計算 CPU。 因為大部分的查詢都是在多個節點上執行，所以這通常會比查詢實際執行所花的時間大得多。 

使用超過100秒 CPU 的查詢會被視為取用過多資源的查詢。 使用超過1000秒 CPU 的查詢被視為濫用查詢，可能會進行節流。

查詢處理時間花費在：
- 資料抓取：舊資料的抓取將耗用比抓取最近資料更多的時間。
- 資料處理–資料的邏輯和評估。 

除了在查詢處理節點所花費的時間之外，Azure 監視器記錄還需要花費額外的時間來進行驗證：驗證使用者，並確認它們可以存取此資料、找出資料存放區、剖析查詢，以及配置查詢處理節點。 此時間不包含在查詢的總 CPU 時間內。

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>在使用高 CPU 功能之前先行篩選記錄

有些查詢命令和函式的 CPU 耗用量很繁重。 這特別適用于剖析 JSON 和 XML 或將複雜的正則運算式解壓縮的命令。 這類剖析可能會在參考動態資料行時，透過 [parse_json ( # B1 ](/azure/kusto/query/parsejsonfunction) 或 [Parse_xml ( # B3 ](/azure/kusto/query/parse-xmlfunction) 函數或隱含方式進行。

這些函式耗用 CPU 的比例與它們正在處理的資料列數目。 最有效率的優化是在查詢的早期加入 where 條件，以便在執行 CPU 密集函式之前，盡可能篩選出盡可能多的記錄。

例如，下列查詢會產生完全相同的結果，但第二個查詢最有效率，因為剖析之前的 [條件會](/azure/kusto/query/whereoperator) 排除許多記錄：

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
| summarize count() by FileHash, FilePath
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // exact removal of results. Early filter is not accurate enough
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>避免使用評估的 where 子句

在評估的資料行上包含 [where](/azure/kusto/query/whereoperator) 子句的查詢，而不是實際存在於資料集中的資料行，則會失去效率。 當處理大型資料集時，篩選評估資料行可避免某些系統優化。
例如，下列查詢會產生完全相同的結果，但第二個查詢會比較有效率，因為 [where](/azure/kusto/query/whereoperator) 條件是指內建的資料行。

```Kusto
//less efficient
Syslog
| extend Msg = strcat("Syslog: ",SyslogMessage)
| where  Msg  has "Error"
| count 
```
```Kusto
//more efficient
Syslog
| where  SyslogMessage  has "Error"
| count 
```

在某些情況下，查詢處理 enine 會隱含地建立評估的資料行，因為篩選只會在欄位上執行：
```Kusto
//less efficient
SecurityEvent
| where tolower(Process) == "conhost.exe"
| count 
```
```Kusto
//more efficient
SecurityEvent
| where Process =~ "conhost.exe"
| count 
```




### <a name="use-effective-aggregation-commands-and-dimensions-in-summarize-and-join"></a>在摘要和聯結中使用有效的匯總命令和維度

雖然 [ ( # B1 ](/azure/kusto/query/max-aggfunction)、 [Sum ( # B3 ](/azure/kusto/query/sum-aggfunction)、 [count ( # B5 ](/azure/kusto/query/count-aggfunction)和 [avg ( # B7 ](/azure/kusto/query/avg-aggfunction) 等的一些匯總命令都有低 CPU 的影響，但其他比較複雜，而且包含啟發學習法和估計值，可讓它們有效率地執行。 例如， [dcount ( # B1 ](/azure/kusto/query/dcount-aggfunction) 會使用 HyperLogLog 演算法來提供接近大量資料集的相異計數，而不會實際計算每個值;百分位數函數使用最接近的排名百分位數演算法來執行類似的近似值。 有幾個命令包含可減少其影響的選擇性參數。 例如， [makeset ( # B1 ](/azure/kusto/query/makeset-aggfunction) 函式有一個選擇性參數，可用來定義最大設定大小，這會大幅影響 CPU 和記憶體。

[加入](/azure/kusto/query/joinoperator?pivots=azuremonitor) 和 [摘要](/azure/kusto/query/summarizeoperator) 命令可能會在處理大型資料集時造成高 CPU 使用率。 它們的複雜性與使用做為*cardinality* `by` 摘要或聯結屬性之資料行的可能值數目（稱為基數）直接相關。 如需聯結和摘要的說明和優化，請參閱其檔文章和優化提示。

例如，下列查詢會產生完全相同的結果，因為 **CounterPath** 一律是一對一對應到 **CounterName** 和 **ObjectName**。 第二個比較有效率，因為匯總維度較小：

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

CPU 耗用量可能也會受到需要大量運算的條件或擴充資料行所影響。 所有簡單的字串比較（例如 [等於 = =](/azure/kusto/query/datatypes-string-operators) 和 [startswith](/azure/kusto/query/datatypes-string-operators) ），都有大約相同的 CPU 影響，而先進的文字比對會有更大的影響。 具體而言， [contains](/azure/kusto/query/datatypes-string-operators) [運算子更](/azure/kusto/query/datatypes-string-operators)有效率。 由於字串處理技術的緣故，尋找長度超過四個字元的字串比短字串更有效率。

例如，下列查詢會根據電腦命名原則產生類似的結果，但第二個查詢則更有效率：

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> 此指標只會顯示來自立即叢集的 CPU。 在多重區域查詢中，它只會代表其中一個區域。 在多個工作區查詢中，它可能不會包含所有工作區。

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>避免在字串剖析運作時進行完整的 XML 和 JSON 剖析
XML 或 JSON 物件的完整剖析可能會耗用高 CPU 和記憶體資源。 在許多情況下，當只需要一個或兩個參數，且 XML 或 JSON 物件很簡單時，使用 [parse 運算子](/azure/kusto/query/parseoperator) 或其他 [文字剖析技術](./parse-text.md)可以更輕鬆地將它們剖析為字串。 當 XML 或 JSON 物件中的記錄數目增加時，效能提升會更顯著。 當記錄數目達到數十百萬個時，這是不可或缺的。

例如，下列查詢會傳回與上述查詢完全相同的結果，而不會執行完整的 XML 剖析。 請注意，它會在 XML 檔案結構上進行一些假設，例如，FilePath 元素在 Get-filehash 之後，而且沒有屬性。 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>用於處理查詢的資料

處理查詢的關鍵因素是掃描並用於查詢處理的資料量。 Azure 資料總管使用積極的優化，相較于其他資料平臺，大幅減少資料量。 但是查詢中有很重要的因素可能會影響所使用的資料量。

處理超過2個000KB 資料的查詢會被視為取用過多資源的查詢。 如果查詢的處理超過20個，000KB 的資料會被視為濫用查詢，可能會進行節流。

在 Azure 監視器記錄檔中， **TimeGenerated** 資料行是用來為數據編制索引的一種方式。 將 **TimeGenerated** 的值限制為盡可能縮小範圍，會大幅限制必須處理的資料量，以大幅改善查詢效能。

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>避免不必要的搜尋和聯集運算子使用

增加處理資料的另一個因素是使用大量資料表。 當 `search *` 使用和命令時，通常會發生這種情況 `union *` 。 這些命令會強制系統評估和掃描工作區中所有資料表的資料。 在某些情況下，工作區中可能會有數百個數據表。 請儘量避免使用 "search *" 或任何搜尋，而不將其範圍設定為特定的資料表。

例如，下列查詢會產生完全相同的結果，但最後一個則是最有效率的結果：

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>將早期篩選新增至查詢

減少資料量的另一種方法[是在查詢初期有條件。](/azure/kusto/query/whereoperator) Azure 資料總管平臺包含快取，可讓它知道哪些分割區包含與特定 where 條件相關的資料。 例如，如果查詢包含，則 `where EventID == 4624` 它只會將查詢散發給處理具有相符事件之分割區的節點。

下列範例查詢會產生完全相同的結果，但第二個查詢會更有效率：

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="avoid-multiple-scans-of-same-source-data-using-conditional-aggregation-functions-and-materialize-function"></a>使用條件式彙總函式和具體化函式避免多次掃描相同的來源資料
當查詢有數個使用聯結或 union 運算子合併的子查詢時，每個子查詢都會個別掃描整個來源，然後合併結果。 這會將資料掃描的次數乘以非常大型資料集中的關鍵因素。

避免這種情況的技巧是使用條件式彙總函式。 摘要運算子中使用的大部分 [彙總函式](/azure/data-explorer/kusto/query/summarizeoperator#list-of-aggregation-functions) 都有條件式版本，可讓您搭配多個條件使用單一摘要運算子。 

例如，下列查詢會顯示登入事件的數目，以及每個帳戶的進程執行事件數目。 它們會傳回相同的結果，但第一次掃描資料兩次，第二次掃描一次：

```Kusto
//Scans the SecurityEvent table twice and perform expensive join
SecurityEvent
| where EventID == 4624 //Login event
| summarize LoginCount = count() by Account
| join 
(
    SecurityEvent
    | where EventID == 4688 //Process execution event
    | summarize ExecutionCount = count(), ExecutedProcesses = make_set(Process) by Account
) on Account
```

```Kusto
//Scan only once with no join
SecurityEvent
| where EventID == 4624 or EventID == 4688 //early filter
| summarize LoginCount = countif(EventID == 4624), ExecutionCount = countif(EventID == 4688), ExecutedProcesses = make_set_if(Process,EventID == 4688)  by Account
```

不需要子查詢的另一種情況是預先篩選 [parse 運算子](/azure/data-explorer/kusto/query/parseoperator?pivots=azuremonitor) ，以確保它只會處理符合特定模式的記錄。 這是不必要的，因為剖析運算子和其他類似的運算子會在模式不相符時傳回空的結果。 以下兩個查詢會傳回完全相同的結果，而第二個查詢只會掃描一次資料。 在第二個查詢中，每個 parse 命令都與其事件相關。 之後的擴充運算子會顯示如何參考空白資料的情況。

```Kusto
//Scan SecurityEvent table twice
union(
SecurityEvent
| where EventID == 8002 
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| distinct FilePath
),(
SecurityEvent
| where EventID == 4799
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" * 
| distinct CallerProcessName1
)
```

```Kusto
//Single scan of the SecurityEvent table
SecurityEvent
| where EventID == 8002 or EventID == 4799
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" * //Relevant only for event 8002
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" *  //Relevant only for event 4799
| extend FilePath = iif(isempty(CallerProcessName1),FilePath,"")
| distinct FilePath, CallerProcessName1
```

當上述專案不允許避免使用子查詢時，另一項技術是使用具體化 ( # A1 函式來提示查詢引擎，其中每一個來源資料都使用 [具體化 # A1 函數](/azure/data-explorer/kusto/query/materializefunction?pivots=azuremonitor)。 當來源資料來自查詢中使用數次的函式時，這非常有用。 當子查詢的輸出遠小於輸入時，具體化就有效。 查詢引擎會快取並重複使用所有出現的輸出。



### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>減少取出的資料行數目

由於 Azure 資料總管是單欄式資料存放區，因此，每個資料行的抓取都與其他資料行無關。 直接取出的資料行數目會影響整體資料量。 您應該只將資料行包含在 [摘要](/azure/kusto/query/summarizeoperator) 中所需的輸出，或 [投射](/azure/kusto/query/projectoperator) 特定的資料行。 Azure 資料總管有數個優化，可減少抓取的資料行數目。 如果它判斷不需要資料行，例如，在 [ [摘要](/azure/kusto/query/summarizeoperator) ] 命令中未參考該資料行，則不會將它取出。

例如，第二個查詢可能會處理三倍以上的資料，因為它不需要提取一個資料行，而是三個數據行：

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>已處理查詢的時間範圍

Azure 監視器記錄檔中的所有記錄都會根據 **TimeGenerated** 資料行進行分割。 存取的資料分割數目與時間範圍直接相關。 縮短時間範圍是確保提示查詢執行最有效率的方式。

時間範圍超過15天的查詢會被視為耗用過多資源的查詢。 時間範圍超過90天的查詢被視為濫用查詢，可能會進行節流。

您可以使用 Log Analytics 畫面中的時間範圍選取器來設定時間範圍，如 [Azure 監視器 Log analytics 中的記錄查詢範圍和時間範圍](scope.md#time-range)所述。 這是建議的方法，因為選取的時間範圍會使用查詢中繼資料傳遞至後端。 

替代方法是在查詢的**TimeGenerated**上明確包含[where](/azure/kusto/query/whereoperator)條件。 您應該使用這個方法，因為它會確保時間範圍是固定的，即使是從不同的介面使用查詢也是一樣。
您應確定查詢的所有部分都有 **TimeGenerated** 篩選。 當查詢有從不同資料表或相同資料表中提取資料的子查詢時，每個查詢都必須包含其本身 [的 where](/azure/kusto/query/whereoperator) 條件。

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>請確定所有子查詢都有 TimeGenerated 篩選

例如，在下列查詢中，雖然 **效能資料表只會在最後** 一天掃描，但會掃描其所有歷程記錄的 **心跳** 表，最多可能會有兩年：

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

發生這種錯誤的常見案例是，使用 [arg_max ( # B1 ](/azure/kusto/query/arg-max-aggfunction) 來尋找最新的發生情況。 例如：

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

您可以藉由在內部查詢中新增時間篩選來輕鬆修正此情況：

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

此錯誤的另一個範例是，在多個資料表的聯 [集](/azure/kusto/query/unionoperator?pivots=azuremonitor) 之後執行時間範圍篩選。 執行聯集時，每個子查詢的範圍應為。 您可以使用 [let](/azure/kusto/query/letstatement) 語句來確保範圍一致性。

例如，下列查詢將掃描 *心跳* 和效能資料表中的所有資料， *而不* 只是過去1天：

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

此查詢應固定如下：

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>時間範圍度量限制

量測一律大於指定的實際時間。 例如，如果查詢的篩選是7天，系統可能會掃描7.5 或8.1 天。 這是因為系統會將資料分割成變數大小的區塊。 為了確保所有相關記錄都已掃描，它會掃描可能涵蓋數小時甚至一天以上的整個資料分割。

在幾個情況下，系統無法提供時間範圍的精確度量。 當查詢的範圍小於一天或多個工作區的查詢時，就會發生這種情況。


> [!IMPORTANT]
> 此指標只會顯示立即叢集中處理的資料。 在多重區域查詢中，它只會代表其中一個區域。 在多個工作區查詢中，它可能不會包含所有工作區。

## <a name="age-of-processed-data"></a>已處理資料的存留期
Azure 資料總管會使用數個儲存層：記憶體內部、本機 SSD 磁片，以及更慢的 Azure Blob。 較新的資料，越高，就有可能以較小的延遲將其儲存在更高效能的層級中，以減少查詢持續時間和 CPU。 除了資料本身之外，系統也有用於中繼資料的快取。 資料越舊，其中繼資料的機率就愈低。

如果查詢處理的資料超過14天，則會被視為耗用過多資源的查詢。


雖然有些查詢需要使用舊的資料，但在某些情況下，錯誤會使用舊的資料。 當查詢執行時未在其中繼資料中提供時間範圍，而且並非所有資料表參考都包含 **TimeGenerated** 資料行的篩選準則時，就會發生這種情況。 在這些情況下，系統會掃描儲存在該資料表中的所有資料。 當資料保留時間很長時，就可以涵蓋長時間範圍，以及與資料保留期間一樣舊的資料。

例如，這類案例可以是：

- 請勿使用不受限制的子查詢來設定 Log Analytics 中的時間範圍。 請參閱上面的範例。
- 使用不含時間範圍選擇性參數的 API。
- 使用不會強制時間範圍（例如 Power BI 連接器）的用戶端。

請參閱舊一節中的範例和附注，因為它們在此案例中也相關。

## <a name="number-of-regions"></a>區域數目
在許多情況下，單一查詢可能會跨不同區域執行：

- 當有數個工作區明確列出，而且它們位於不同的區域時。
- 當資源範圍查詢正在提取資料，而且資料儲存在位於不同區域的多個工作區中時。

跨區域查詢執行需要在後端大量的中繼資料區塊中序列化和傳輸通常比查詢最終結果更大的區塊。 它也會限制系統執行優化、啟發學習法和使用快取的能力。
如果沒有真正的原因要掃描所有區域，您應該調整範圍，使其涵蓋較少的區域。 如果資源範圍最小化，但使用的區域仍有許多，可能是因為設定不正確所致。 例如，會將 audit 記錄和診斷設定傳送到不同區域中的不同工作區，或有多個診斷設定。 

跨越3個以上區域的查詢會被視為取用過多資源的查詢。 橫跨6個以上區域的查詢被視為濫用查詢，可能會進行節流。

> [!IMPORTANT]
> 跨多個區域執行查詢時，CPU 和資料量測將不正確，而且只會代表其中一個區域的測量。

## <a name="number-of-workspaces"></a>工作區數目
工作區是用來隔離和記錄管理資料的邏輯容器。 後端會在所選區域內的實體叢集上優化工作區放置。

多個工作區的使用方式可能會導致： 

- 其中會明確列出數個工作區。
- 當資源範圍查詢正在提取資料，並將資料儲存在多個工作區時。
 
跨區域和跨叢集執行查詢時，系統必須在後端大型的中繼資料區塊中序列化和傳輸，通常比查詢最終結果大很多。 它也會限制系統執行優化、啟發學習法及利用快取的能力。

跨越超過5個工作區的查詢會被視為取用過多資源的查詢。 查詢不能跨越超過100個工作區。

> [!IMPORTANT]
> 在某些多個工作區的案例中，CPU 和資料量測將不正確，而且只會代表數個工作區的測量。

## <a name="parallelism"></a>平行處理原則
Azure 監視器記錄會使用 Azure 資料總管的大型叢集來執行查詢，而這些叢集會大規模變化，可能會有多達數十個計算節點。 系統會根據工作區放置邏輯和容量，自動調整叢集規模。

為了有效率地執行查詢，它會根據其處理所需的資料分割並散發至計算節點。 在某些情況下，系統無法有效地完成此作業。 這可能會導致長時間的查詢。 

可降低平行處理原則的查詢行為包括：

- 使用序列化和視窗函式，例如 [序列化運算子](/azure/kusto/query/serializeoperator)、 [下一個 ( # B1 ](/azure/kusto/query/nextfunction)、 [上一個 ( # B3 ](/azure/kusto/query/prevfunction)和 [row](/azure/kusto/query/rowcumsumfunction) 函數。 時間序列和使用者分析函數可用於某些情況下。 如果不在查詢的結尾處使用下列運算子，也可能會發生效率不佳的序列化： [range](/azure/kusto/query/rangeoperator)、 [sort](/azure/kusto/query/sortoperator)、 [order](/azure/kusto/query/orderoperator)、 [top](/azure/kusto/query/topoperator)、 [top-hitters](/azure/kusto/query/tophittersoperator)、 [getschema](/azure/kusto/query/getschemaoperator)。
-    使用 [dcount ( # B1 ](/azure/kusto/query/dcount-aggfunction) 彙總函式會強制系統擁有相異值的中央複本。 當資料的小數位數很高時，請考慮使用 dcount 函數的選擇性參數來降低精確度。
-    在許多情況下， [聯結](/azure/kusto/query/joinoperator?pivots=azuremonitor) 運算子會降低整體的平行處理原則。 當效能有問題時，請檢查「隨機聯結」作為替代方案。
-    在資源範圍查詢中，預先執行的 RBAC 檢查可能會在有大量 Azure 角色指派的情況下逗留。 這可能會導致較長的檢查，進而導致較低的平行處理原則。 例如，查詢會在有上千個資源的訂用帳戶上執行，而且每個資源在資源層級中有許多角色指派，而不是在訂用帳戶或資源群組上。
-    如果查詢處理的資料區塊較小，其平行處理原則將會很低，因為系統不會將其分散到多個計算節點。



## <a name="next-steps"></a>後續步驟

- [Kusto 查詢語言的參考檔](/azure/kusto/query/)。
