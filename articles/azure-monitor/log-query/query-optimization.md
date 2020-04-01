---
title: 最佳化 Azure 監視器中的紀錄查詢
description: 優化 Azure 監視器中的日誌查詢的最佳做法。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 29d5213b8eecd94ed8c8ce565972c9f98872a362
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411438"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>最佳化 Azure 監視器中的紀錄查詢
Azure 監視器日誌使用[Azure 資料資源管理員 (ADX)](/azure/data-explorer/)來儲存日誌數據並運行用於分析該資料的查詢。 它為您創建、管理和維護 ADX 群集,並針對日誌分析工作負荷對其進行優化。 運行查詢時,查詢已優化,並路由到存儲工作區數據的相應 ADX 群集。 Azure 監視器日誌和 Azure 資料資源管理員都使用許多自動查詢優化機制。 雖然自動優化可以顯著提升,但在某些情況下,您可以顯著提高查詢性能。 本文介紹了性能注意事項和修復它們的幾個技術。

大多數技術對於直接在 Azure 資料資源管理器和 Azure 監視器日誌上運行的查詢是通用的,但此處將討論幾個唯一的 Azure 監視器日誌注意事項。 有關更多 Azure 資料資源管理員優化提示,請參閱[查詢最佳做法](/azure/kusto/query/best-practices)。

最佳化查詢:

- 運行得更快,減少查詢執行的總體持續時間。
- 被限制或拒絕的可能性較小。

您應該特別注意用於重複和突發性使用的查詢,如儀錶板、警報、邏輯應用和 Power BI。 在這些情況下,無效查詢的影響是巨大的。

## <a name="query-performance-pane"></a>查詢效能窗格
在 Log Analytics 中執行查詢後,按一下查詢結果上方的向下箭頭以查看顯示查詢多個性能指標結果的查詢效能窗格。 下一節將分別介紹這些績效指標。

![查詢效能窗格](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>查詢效能指標

執行的每個查詢都可以使用以下查詢效能指標:

- [總 CPU:](#total-cpu)用於處理所有計算節點查詢的總體計算。 它表示用於計算、分析和數據提取的時間。 

- [處理查詢的資料](#data-used-for-processed-query):用於處理查詢的總體資料。 受目標表大小、使用的時間跨度、應用的篩選器和引用的列數的影響。

- [已處理查詢的時間跨度](#time-span-of-the-processed-query):用於處理查詢的最新數據與最舊資料之間的差距。 受為查詢指定的顯式時間範圍的影響。

- [處理數據的年齡](#age-of-processed-data):現在與用於處理查詢的最早數據之間的差距。 它對數據提取的效率有很大的影響。

- [工作區數](#number-of-workspaces):由於隱式或顯式選擇,在查詢處理期間訪問了多少工作區。

- [區域數](#number-of-regions):由於默示或顯式選擇工作區,在查詢處理期間訪問的區域數。 多區域查詢的效率要低得多,績效指標具有部分覆蓋率。

- [並行性](#parallelism):指示系統能夠在多個節點上執行此查詢的多少。 僅與 CPU 消耗高的查詢相關。 受特定功能和運算符使用的影響。


## <a name="total-cpu"></a>總 CPU
用於跨所有查詢處理節點處理此查詢的實際計算 CPU。 由於大多數查詢是在大量節點上執行的,因此這通常比查詢實際執行的持續時間大得多。 

查詢處理時間花在:
- 數據檢索 – 與檢索最新數據相比,檢索舊數據將消耗更多時間。
- 數據處理 – 資料的邏輯和評估。 

除了在查詢處理節點中花費的時間之外,Azure 監視器日誌還花費額外的時間:對使用者進行身份驗證,並驗證是否允許他們訪問此數據、查找數據存儲、分析查詢和分配查詢處理節點。 此時間不包括在查詢總 CPU 時間中。

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>在使用高 CPU 功能之前提前篩選記錄

某些查詢命令和函數的 CPU 消耗量很大。 對於解析 JSON 和 XML 或提取複雜正則表達式的命令尤其如此。 此類解析可以通過[parse_json()](/azure/kusto/query/parsejsonfunction)或[parse_xml()](/azure/kusto/query/parse-xmlfunction)函數顯式進行,也可以在引用動態列時隱式進行。

這些函數消耗 CPU 與其處理的行數成比例。 最有效的優化是在查詢的早期添加可在執行 CPU 密集型函數之前篩選出盡可能多的記錄的條件。

例如,以下查詢產生的結果完全相同,但第二個查詢是[最有效的,因為](/azure/kusto/query/whereoperator)解析之前的條件排除了許多記錄:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>避免在子句的位置使用評估

[包含計算](/azure/kusto/query/whereoperator)列上子句而不是數據集中實際存在的列子句的查詢會提高效率。 在處理大型數據集時,對計算的列進行篩選會阻止某些系統優化。
例如,以下查詢產生的結果完全相同,但第二個查詢的效率更高,因為[條件](/azure/kusto/query/whereoperator)引用內置列的位置

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count() by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count() by Computer
```

### <a name="use-effective-aggregation-commands-and-dimmentions-in-summarize-and-join"></a>在匯總和聯接中使用有效的聚合命令和暗調

雖然某些聚合命令(如 max()、sum()、count() 和[avg()](/azure/kusto/query/avg-aggfunction)由於其邏輯而具有較低的 CPU 影響,但其他命令則更為複雜,包括啟發式和估計,[max()](/azure/kusto/query/max-aggfunction)[sum()](/azure/kusto/query/sum-aggfunction)[count()](/azure/kusto/query/count-aggfunction)以便有效地執行它們。 例如[,dcount()](/azure/kusto/query/dcount-aggfunction)使用 HyperLog 演演演算法提供與大型數據集的不同計數的緊密估計,而無需實際計算每個值;百分位數函數使用最近的百分位數演算法執行類似的近似值。 其中一些命令包括可選參數,以減少其影響。 例如[,makeset()](/azure/kusto/query/makeset-aggfunction)函數具有用於定義最大集大小的可選參數,這顯著影響 CPU 和記憶體。

[聯接](/azure/kusto/query/joinoperator?pivots=azuremonitor)和[匯總](/azure/kusto/query/summarizeoperator)命令在處理大量數據時可能會導致高 CPU 利用率。 它們的複雜性與用作`by`匯總或聯接屬性的列的可能值的數量(稱為*基數*)直接相關。 有關聯接和匯總的說明和優化,請參閱其文檔文章和優化提示。

例如,以下查詢產生的結果完全相同,因為**反路徑**始終以一對一方式映射到**反名稱**和**物件名稱**。 第二個越高效,因為聚合維度較小:

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

CPU 消耗也可能受到需要密集計算的條件或擴展列的影響。 所有瑣碎的字串比較(如[等號 )](/azure/kusto/query/datatypes-string-operators)和[開頭](/azure/kusto/query/datatypes-string-operators)的字串都具有大致相同的 CPU 影響,而高級文本匹配的影響更大。 具體來說[,has](/azure/kusto/query/datatypes-string-operators)運算子的效率比[包含](/azure/kusto/query/datatypes-string-operators)運算符的效率更高。 由於字串處理技術,查找比短字串長的字串效率更高。

例如,根據計算機命名策略,以下查詢會產生類似的結果,但第二個查詢的效率更高:

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
> 此指示器僅顯示來自直接群集的 CPU。 在多區域查詢中,它將僅表示其中一個區域。 在多工作區查詢中,它可能不包括所有工作區。

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>避免在字串解析時完全 XML 和 JSON 解析
完全解析 XML 或 JSON 物件可能會消耗高 CPU 和記憶體資源。 在許多情況下,當只需要一個或兩個參數,而XML或JSON物件很簡單時,使用[解析運算符](/azure/kusto/query/parseoperator)或其他[文本解析技術](/azure/azure-monitor/log-query/parse-text)將它們解析為字串更容易。 隨著 XML 或 JSON 物件中記錄數量的增加,性能提升將更為顯著。 當記錄數達到數千萬時,這一點至關重要。

例如,以下查詢將返回與上述查詢完全相同的結果,而不執行完整的 XML 分析。 請注意,它對 XML 檔結構進行了一些假設,例如該檔路徑元素在 FileHash 之後出現,並且它們都沒有屬性。 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>處理查詢資料

處理查詢的一個關鍵因素是掃描和使用用於查詢處理的數據量。 與其他數據平臺相比,Azure 數據資源管理器使用積極的優化可顯著減少數據量。 不過,查詢中仍有一些關鍵因素會影響所使用的數據量。

在 Azure 監視器紀錄中,**時間生成**列用作對資料進行索引的一種方式。 通過將**Time生成**值限制為盡可能縮小範圍,將顯著限制必須處理的數據量,從而顯著提高查詢性能。

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>避免不必要地使用搜尋和聯合運算子

增加正在處理的數據的另一個因素是使用大量表。 這通常發生在使用`search *`和`union *`命令時。 這些命令強制系統評估和掃描工作區中所有表的數據。 在某些情況下,工作區中可能有數百個表。 盡量避免使用「搜索+」或任何搜索,而不將其範圍界定到特定表。

例如,以下查詢產生的結果完全相同,但最後一個查詢是迄今為止最有效的結果:

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

### <a name="add-early-filters-to-the-query"></a>新增早期篩選器

減少資料量的另一種方法是在查詢的早期有[條件](/azure/kusto/query/whereoperator)。 Azure 資料資源管理員平臺包含一個緩存,用於告知它知道哪些分區包含與特定條件相關的數據。 例如,如果查詢包含`where EventID == 4624`,則它將僅將查詢分發到處理具有匹配事件的分區的節點。

以下範例查詢產生完全相同的結果,但第二個查詢的效率更高:

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

### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>減少檢索的欄數

由於 Azure 資料資源管理器是列數據存儲,因此檢索每個列獨立於其他列。 檢索的列數直接影響整個數據量。 應僅通過[匯總](/azure/kusto/query/summarizeoperator)結果或[投影](/azure/kusto/query/projectoperator)特定列來在輸出中包含所需的列。 Azure 資料資源管理器具有多個優化,以減少檢索列的數量。 如果它確定不需要列,例如,如果在[匯總](/azure/kusto/query/summarizeoperator)命令中未引用該列,則它不會檢索該列。

例如,第二個查詢可能處理三倍以上的數據,因為它不需要獲取一列,而是需要獲取三個列:

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

## <a name="time-span-of-the-processed-query"></a>已處理查詢的時間跨度

Azure 監視器日誌中的所有日誌都根據**時間生成**列進行分區。 訪問的分區數與時間跨度直接相關。 縮短時間範圍是保證快速查詢執行的最有效方法。

時間範圍可以使用日誌分析螢幕中的時間範圍選擇器進行設置,如[Azure 監視器日誌分析中的日誌查詢範圍和時間範圍中](scope.md#time-range)所述。 這是推薦的方法,因為所選時間範圍使用查詢元數據傳遞到後端。 

另一種方法是顯式在查詢中包含**Time生成**上的[where](/azure/kusto/query/whereoperator)條件。 應使用此方法,因為它可確保時間跨度是固定的,即使查詢是從其他介面使用的。
應確保查詢的所有部分都有**時間生成**篩選器。 當查詢具有從各種表或同一表提取數據的子查詢時,每個查詢必須包括其自己的[位置](/azure/kusto/query/whereoperator)條件。

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>確保所有子查詢都有時間產生篩選器

例如,在以下查詢中,雖然**Perf**表將僅掃描最後一天,但將掃描**檢測訊號**表的所有歷史記錄,這些歷史記錄可能長達兩年:

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

發生此類錯誤的常見情況是,當[arg_max()](/azure/kusto/query/arg-max-aggfunction)用於查找最新發生情況時。 例如：

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

透過在內部查詢中新增時間篩選器,可以輕鬆更正此問題:

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

此故障的另一個範例是在多個表上的[聯合](/azure/kusto/query/unionoperator?pivots=azuremonitor)之後執行時間範圍篩選時。 執行聯合時,應限定每個子查詢的範圍。 您可以使用[let](/azure/kusto/query/letstatement)語句來確保範圍的一致性。

例如,以下查詢將掃描*檢測訊號*表和*Perf*表中的所有資料,而不僅僅是最後 1 天:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

此查詢應按如下方式修復:

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

### <a name="time-span-measurement-limitations"></a>時間跨度測量限制

測量始終大於指定的實際時間。 例如,如果查詢上的篩選器為 7 天,則系統可能會掃描 7.5 天或 8.1 天。 這是因為系統將數據劃分為可變大小的區塊。 為了確保掃描所有相關記錄,它會掃描整個分區,該分區可能涵蓋幾個小時甚至超過一天。

在以下幾種情況下,系統無法提供時間範圍的精確測量。 在大多數情況下,查詢的跨度小於一天或在多工作區查詢中發生這種情況。


> [!IMPORTANT]
> 此指標僅顯示在直接群集中處理的數據。 在多區域查詢中,它將僅表示其中一個區域。 在多工作區查詢中,它可能不包括所有工作區。

## <a name="age-of-processed-data"></a>處理資料的年齡
Azure 資料資源管理器使用多個儲存層:記憶體中、本地 SSD 磁碟和速度慢得多的 Azure Blob。 數據越新,就越有可能存儲在具有較小延遲的更具性能的層中,從而縮短查詢持續時間和 CPU。 除了數據本身之外,系統還具有元數據緩存。 數據越舊,其元數據在緩存中的可能性就越小。

雖然某些查詢需要使用舊數據,但在某些情況下,舊數據被錯誤地使用。 當執行查詢時,未在其元數據中提供時間範圍,並且並非所有表引用都包含**Time生成**列上的篩選器,則會發生這種情況。 在這些情況下,系統將掃描存儲在該表中的所有數據。 當數據保留時間長時,它可以覆蓋很長的時間段,因此數據與數據保留期一樣舊。

例如,這種情況可以:

- 未使用不限制的子查詢在日誌分析中設置時間範圍。 請參閱上面的範例。
- 使用無時間範圍可選參數的 API。
- 使用不強制時間範圍(如 Power BI 連接器)的用戶端。

請參閱"可滲透"部分中的範例和註釋,因為它們與本例中也相關。

## <a name="number-of-regions"></a>區域數
在以下幾種情況下,可以在不同區域執行單個查詢:

- 顯式列出多個工作區,並且它們位於不同的區域中時。
- 當資源範圍的查詢正在提取數據,並且數據存儲在位於不同區域的多個工作區中時。

跨區域查詢執行要求系統在後端序列化和傳輸通常比查詢最終結果大得多的大塊中間數據。 它還限制了系統執行優化、啟發式和利用緩存的能力。
如果沒有實際理由掃描所有這些區域,則應調整範圍,使其覆蓋較少的區域。 如果資源範圍最小化,但仍使用許多區域,則可能是由於配置錯誤。 例如,審核日誌和診斷設置將發送到不同區域的不同工作區,或者有多個診斷設置配置。 

> [!IMPORTANT]
> 在多個區域上運行查詢時,CPU 和數據測量將不準確,並且僅表示其中一個區域的度量值。

## <a name="number-of-workspaces"></a>工作區數
工作區是用於隔離和管理日誌數據的邏輯容器。 後端優化所選區域中物理群集上的工作區位置。

多個工作區的使用可能源於: 

- 顯式列出多個工作區的位置。
- 當資源範圍的查詢正在提取數據,並且數據存儲在多個工作區中時。
 
跨區域和跨群集執行查詢要求系統在通常比查詢最終結果大得多的後端大塊中間數據中序列化和傳輸。 它還限制了系統執行優化、啟發式和利用緩存的能力。

> [!IMPORTANT]
> 在某些多工作區方案中,CPU 和數據測量將不準確,並且僅表示對少數工作區的度量。

## <a name="parallelism"></a>平行處理原則
Azure 監視器日誌正在使用 Azure 資料資源管理器的大型叢集來運行查詢,這些群集的規模各不相同,可能最多獲得數十個計算節點。 系統根據工作區放置邏輯和容量自動縮放群集。

為了有效地執行查詢,它根據處理所需的數據將其分區並分發到計算節點。 在某些情況下,系統無法有效地執行此操作。 這可能導致查詢的持續時間長。 

可以減少並行性的查詢行為包括:

- 使用序列化和視窗函數,如[序列化運算子](/azure/kusto/query/serializeoperator)、[下一個()](/azure/kusto/query/nextfunction)[和](/azure/kusto/query/prevfunction)[行](/azure/kusto/query/rowcumsumfunction)函數。 在某些情況下,可以使用時間序列和使用者分析功能。 如果查詢末尾不使用以下運算符,則也可能發生低效序列化:[範圍](/azure/kusto/query/rangeoperator)、[排序](/azure/kusto/query/sortoperator)、[順序](/azure/kusto/query/orderoperator)、[頂部](/azure/kusto/query/topoperator)[、頂部、getschema。](/azure/kusto/query/tophittersoperator) [getschema](/azure/kusto/query/getschemaoperator)
-    [dcount()](/azure/kusto/query/dcount-aggfunction)聚合函數的使用強制系統具有不同值的中央副本。 當資料量級較高時,請考慮使用 dcount 函數可選參數來降低準確性。
-    在許多情況下,[聯接](/azure/kusto/query/joinoperator?pivots=azuremonitor)運算符降低整體並行性。 當性能有問題時,檢查隨機連接作為替代方案。
-    在資源範圍查詢中,在執行前 RBAC 檢查可能會在 RBAC 分配數量非常多的情況下繼續。 這可能導致較長的檢查,從而導致較低的並行性。 例如,在訂閱上執行查詢,其中有數千個資源,並且每個資源在資源級別中具有許多角色分配,而不是在訂閱或資源組上。
-    如果查詢正在處理小塊數據,則其並行性將很低,因為系統不會將其分散到許多計算節點上。



## <a name="next-steps"></a>後續步驟

- [Kusto 查詢語言的參考文件](/azure/kusto/query/)。
