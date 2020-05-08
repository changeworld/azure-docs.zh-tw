---
title: 優化 Azure 監視器中的記錄查詢
description: 在 Azure 監視器中優化記錄查詢的最佳作法。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 9ae0aec6b87a746ed1f141dcf98f599acd20ab3a
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864244"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>優化 Azure 監視器中的記錄查詢
Azure 監視器記錄會使用[Azure 資料總管（ADX）](/azure/data-explorer/)來儲存記錄資料，並執行查詢來分析該資料。 它會為您建立、管理及維護 ADX 叢集，並針對您的記錄分析工作負載將它們優化。 當您執行查詢時，它會進行優化，並路由傳送至適當的 ADX 叢集，以儲存工作區資料。 Azure 監視器記錄和 Azure 資料總管都使用許多自動查詢優化機制。 雖然自動優化提供顯著的提升，但在某些情況下，您可以大幅提升查詢效能。 這篇文章說明效能考慮，以及解決這些問題的數種技術。

大部分的技術都是直接在 Azure 資料總管和 Azure 監視器記錄檔上執行的查詢常見，不過這裡討論的是幾個獨特的 Azure 監視器記錄考慮。 如需更多 Azure 資料總管優化秘訣，請參閱[查詢最佳做法](/azure/kusto/query/best-practices)。

優化的查詢將會：

- 執行的速度更快，縮短查詢執行的整體持續時間。
- 較小的可能會受到節流或拒絕。

您應該特別注意用於週期性和暴增使用量的查詢，例如儀表板、警示、Logic Apps 和 Power BI。 在這些情況下，無效查詢的影響相當重要。

## <a name="query-performance-pane"></a>查詢效能窗格
在 Log Analytics 中執行查詢之後，請按一下查詢結果上方的向下箭號，以查看 [查詢效能] 窗格，其中顯示查詢的數個效能指標的結果。 下一節將說明這些效能指標。

![查詢效能窗格](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>查詢效能指標

下列查詢效能指標適用于每個執行的查詢：

- [總 CPU](#total-cpu)：用來處理所有計算節點上查詢的整體計算。 它代表用於計算、剖析和資料提取的時間。 

- [用於處理查詢的資料](#data-used-for-processed-query)：已存取來處理查詢的整體資料。 受目標資料表的大小、使用的時間範圍、套用的篩選，以及所參考的資料行數目所影響。

- 已[處理之查詢的時間範圍](#time-span-of-the-processed-query)：最新和最舊的資料之間的間距，這是為了處理查詢所存取。 受到針對查詢所指定之明確時間範圍的影響。

- 已[處理資料的存留期](#age-of-processed-data)：現在與用來處理查詢的最舊資料之間的差距。 它會高度影響資料提取的效率。

- [工作區數目](#number-of-workspaces)：因隱含或明確選取而在查詢處理期間存取多少個工作區。

- [區域數目](#number-of-regions)：根據隱含或明確選取的工作區，在查詢處理期間所存取的區域數量。 多重區域查詢的效率較低，效能指標則呈現部分涵蓋範圍。

- [平行](#parallelism)處理原則：指出系統能夠在多個節點上執行此查詢的程度。 僅與具有高 CPU 耗用量的查詢有關。 會受到特定函式和運算子的使用影響。


## <a name="total-cpu"></a>CPU 總計
實際計算 CPU，這是為了處理所有查詢處理節點的此查詢所投入的。 因為大部分的查詢都是在大量的節點上執行，所以通常會比查詢實際執行的持續時間大很多。 

查詢處理時間花費在：
- 資料抓取-舊資料的抓取會耗用比抓取最近資料更多的時間。
- 資料處理–資料的邏輯和評估。 

除了花在查詢處理節點的時間之外，Azure 監視器記錄還有額外的時間來進行：驗證使用者，並確認是否允許存取此資料、找出資料存放區、剖析查詢，以及配置查詢處理節點。 這段時間不會包含在查詢總 CPU 時間內。

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>使用高 CPU 函數之前的記錄早期篩選

某些查詢命令和函式在其 CPU 耗用量中很繁重。 這對於剖析 JSON 和 XML 或解壓縮複雜正則運算式的命令更是如此。 這種剖析可能會透過[parse_json （）](/azure/kusto/query/parsejsonfunction)或[parse_xml （）](/azure/kusto/query/parse-xmlfunction)函數明確地進行，或在參考動態資料行時隱含執行。

這些函式會耗用 CPU，與它們所處理的資料列數目成正比。 最有效率的優化是在查詢初期加入 where 條件，以便在執行大量 CPU 的函式之前，盡可能篩選出最多的記錄。

例如，下列查詢會產生完全相同的結果，但第二個則是最有效率的，[因為在剖析之前的條件會](/azure/kusto/query/whereoperator)排除許多記錄：

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

### <a name="avoid-using-evaluated-where-clauses"></a>避免使用評估的 where 子句

在已評估的資料行上包含[where](/azure/kusto/query/whereoperator)子句的查詢，而非實際出現在 dataset 中的資料行，則會失去效率。 在處理大型資料集時，篩選已評估的資料行可避免某些系統優化。
例如，下列查詢會產生完全相同的結果，但第二個會更有效率，因為[where](/azure/kusto/query/whereoperator)條件指的是內建資料行

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

### <a name="use-effective-aggregation-commands-and-dimensions-in-summarize-and-join"></a>在摘要和聯結中使用有效的匯總命令和維度

雖然某些匯總命令（例如[max （）](/azure/kusto/query/max-aggfunction)、 [sum （）](/azure/kusto/query/sum-aggfunction)、 [count （）](/azure/kusto/query/count-aggfunction)和[avg （））](/azure/kusto/query/avg-aggfunction)對其邏輯造成的 CPU 影響很低，但其他則較為複雜，包括啟發學習法和估計值，讓它們可以有效率地執行。 例如， [dcount （）](/azure/kusto/query/dcount-aggfunction)會使用 HyperLogLog 演算法，針對大型資料集的相異計數提供接近的估計，而不會實際計算每個值;百分位數函數使用最接近的排名百分位數演算法執行類似的近似值。 有數個命令包含可減少其影響的選擇性參數。 例如， [makeset （）](/azure/kusto/query/makeset-aggfunction)函式具有選擇性的參數，可定義最大的集合大小，這會大幅影響 CPU 和記憶體。

[聯結](/azure/kusto/query/joinoperator?pivots=azuremonitor)和[摘要](/azure/kusto/query/summarizeoperator)命令可能會在處理大型資料集時造成 CPU 使用率過高。 其複雜性與在摘要或當做聯結屬性`by`中使用之資料行的可能值數目（稱為*基數*）直接相關。 如需聯結和摘要的說明和優化，請參閱其檔文章和優化提示。

例如，下列查詢會產生完全相同的結果，因為**CounterPath**一定是一對一對應至**CounterName**和**ObjectName**。 第二個比較有效率，因為匯總維度較小：

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

需要大量計算的條件或擴充資料行可能也會影響 CPU 耗用量。 所有簡單的字串比較（例如[等號 = =](/azure/kusto/query/datatypes-string-operators)和[startswith](/azure/kusto/query/datatypes-string-operators) ）大約會影響相同的 CPU，而先進的文字比對會有更大的影響。 具體而言， [has 運算子會](/azure/kusto/query/datatypes-string-operators)更有效率地[包含](/azure/kusto/query/datatypes-string-operators)運算子。 由於字串處理技術的緣故，尋找長度超過四個字元的字串比短字串更有效率。

例如，下列查詢會產生類似的結果，視電腦命名原則而定，而第二個則更有效率：

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
> 此指標只會顯示來自立即叢集的 CPU。 在多重區域查詢中，它只會代表其中一個區域。 在多工作區查詢中，它可能不會包含所有工作區。

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>當字串剖析運作時，避免進行完整的 XML 和 JSON 剖析
完整剖析 XML 或 JSON 物件可能會耗用大量的 CPU 和記憶體資源。 在許多情況下，當只需要一個或兩個參數，而且 XML 或 JSON 物件很簡單時，使用[parse 運算子](/azure/kusto/query/parseoperator)或其他[文字剖析技術](/azure/azure-monitor/log-query/parse-text)，就能更輕鬆地將它們剖析為字串。 當 XML 或 JSON 物件中的記錄數目增加時，效能提升會更顯著。 當記錄數目達到數十百萬時，這是不可或缺的。

例如，下列查詢會傳回與上述查詢完全相同的結果，而不會執行完整的 XML 剖析。 請注意，它會對 XML 檔案結構進行一些假設，例如該 FilePath 元素會在 Get-filehash 之後，而且沒有任何屬性。 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>用於已處理查詢的資料

查詢處理的關鍵因素是掃描並用於查詢處理的資料量。 Azure 資料總管使用積極的優化，相較于其他資料平臺，大幅減少資料量。 儘管如此，查詢中也有重要的因素會影響所使用的資料量。

在 Azure 監視器記錄檔中，會使用**TimeGenerated**資料行做為資料的索引方式。 將**TimeGenerated**值限制為盡可能縮小範圍，可大幅限制必須處理的資料量，進而大幅改善查詢效能。

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>避免不必要地使用搜尋和聯集運算子

增加所處理資料的另一個因素是使用大量資料表。 當使用和命令`search *`時`union *` ，通常會發生這種情況。 這些命令會強制系統評估和掃描工作區中所有資料表的資料。 在某些情況下，工作區中可能會有數百個數據表。 請儘量避免使用「搜尋 *」或任何搜尋，而不將它的範圍設定為特定的資料表。

例如，下列查詢會產生完全相同的結果，但最後一個是最有效率的：

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

減少資料量的另一種方法[是在查詢初期有條件。](/azure/kusto/query/whereoperator) Azure 資料總管平臺包含快取，可讓 it 知道哪些分割區包含與特定 where 條件相關的資料。 例如，如果查詢包含`where EventID == 4624` ，則它只會將查詢散發給處理具有相符事件之分割區的節點。

下列範例查詢會產生完全相同的結果，但第二個會更有效率：

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

### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>減少抓取的資料行數目

因為 Azure 資料總管是單欄式資料存放區，所以每個資料行的抓取都與其他欄位無關。 直接抓取的資料行數目會影響整體資料量。 您應該只在輸出結果或[投影](/azure/kusto/query/projectoperator)特定資料行時， [summarizing](/azure/kusto/query/summarizeoperator)將所需的資料行包含在輸出中。 Azure 資料總管有數個優化功能可減少抓取的資料行數目。 如果它判斷不需要資料行（例如，在 [[摘要](/azure/kusto/query/summarizeoperator)] 命令中不會參考它），就不會將它取出。

例如，第二個查詢可能會處理三倍以上的資料，因為它需要提取不是一欄，但有三個：

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

Azure 監視器記錄中的所有記錄都會根據**TimeGenerated**資料行進行分割。 存取的資料分割數目與時間範圍直接相關。 縮短時間範圍是確保提示查詢執行的最有效率方式。

您可以使用 Log Analytics 畫面中的時間範圍選取器來設定時間範圍，如[Azure 監視器 Log analytics 中的記錄查詢範圍和時間範圍](scope.md#time-range)中所述。 這是建議的方法，因為選取的時間範圍會使用查詢中繼資料傳遞至後端。 

另一個方法是在查詢的**TimeGenerated**上明確包含[where](/azure/kusto/query/whereoperator)條件。 您應該使用這個方法，因為它會確保時間範圍是固定的，即使是從不同的介面使用查詢也是如此。
您應該確定查詢的所有部分都具有**TimeGenerated**篩選。 當查詢具有從各種資料表或相同資料表中提取資料的子查詢時，每個都必須包含它自己[的 where](/azure/kusto/query/whereoperator)條件。

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>請確定所有子查詢都具有 TimeGenerated 篩選

例如，在下列查詢中，雖然只會掃描最後一**天的效能資料表，** 但會掃描其所有歷程記錄的「**心跳**」資料表，最多可能會有兩年：

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

發生這種錯誤的常見情況是，當[arg_max （）](/azure/kusto/query/arg-max-aggfunction)用來尋找最近發生的情況時。 例如：

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

這可以藉由在內部查詢中新增時間篩選來輕鬆更正：

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

這項錯誤的另一個範例是在執行等位資料表的聯[集](/azure/kusto/query/unionoperator?pivots=azuremonitor)之後，進行時間範圍篩選。 執行聯集時，每個子查詢的範圍應為。 您可以使用[let](/azure/kusto/query/letstatement)語句來確保範圍的一致性。

例如，下列查詢會掃描 [*心跳* *] 和*[效能] 資料表中的所有資料，而不只是過去1天：

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

### <a name="time-span-measurement-limitations"></a>時間範圍測量限制

量測一定會大於指定的實際時間。 例如，如果查詢的篩選為7天，則系統可能會掃描7.5 或8.1 天。 這是因為系統會將資料分割成變數大小的區塊。 為了確保系統會掃描所有相關記錄，它會掃描可能涵蓋數小時甚至超過一天的整個分割區。

在幾種情況下，系統無法提供精確的時間範圍測量。 在大部分的情況下，如果查詢的範圍少於一天或在多個工作區查詢中，就會發生這種情況。


> [!IMPORTANT]
> 此指標只會顯示立即叢集中所處理的資料。 在多重區域查詢中，它只會代表其中一個區域。 在多工作區查詢中，它可能不會包含所有工作區。

## <a name="age-of-processed-data"></a>已處理資料的存留期
Azure 資料總管使用數個儲存層：記憶體內部、本機 SSD 磁片和較慢的 Azure Blob。 較新的資料，較高的機率是以較小的延遲儲存在更具效能的層級，減少查詢持續時間和 CPU。 除了資料本身之外，系統也具有中繼資料的快取。 資料越舊，其中繼資料的快取機率就越低。

雖然有些查詢需要使用舊資料，但在某些情況下，錯誤會使用舊資料。 當執行查詢時未在其中繼資料中提供時間範圍，而且並非所有資料表參考都包含**TimeGenerated**資料行的篩選準則時，就會發生這種情況。 在這些情況下，系統會掃描儲存在該資料表中的所有資料。 當資料保留時間很長時，它可以涵蓋較長的時間範圍，以及與資料保留週期一樣舊的資料。

例如，這類案例可能如下：

- 未在 Log Analytics 中使用不受限制的子查詢來設定時間範圍。 請參閱上面的範例。
- 使用沒有時間範圍選擇性參數的 API。
- 使用不會強制執行時間範圍的用戶端（例如 Power BI 連接器）。

請參閱舊一節中的範例和附注，因為在此情況下也會相關。

## <a name="number-of-regions"></a>區域數目
在幾種情況下，單一查詢可能會跨不同區域執行：

- 當有數個工作區明確列出，而且它們位於不同的區域時。
- 當資源範圍的查詢正在提取資料，且資料儲存在位於不同區域的多個工作區時。

跨區域查詢執行需要系統在後端大量的中繼資料區塊中進行序列化和傳輸，這通常會比查詢的最終結果大很多。 它也會限制系統執行優化、啟發學習法和使用快取的能力。
如果沒有真正的原因要掃描所有區域，您應該調整範圍，使其涵蓋較少的區域。 如果資源範圍已最小化，但仍有許多區域使用，可能是因為設定錯誤而發生。 例如，「audit 記錄」和「診斷」設定會傳送至不同區域中的不同工作區，或有多個診斷設定設定。 

> [!IMPORTANT]
> 跨數個區域執行查詢時，CPU 和資料量測將不會正確，而且只會代表其中一個區域的度量。

## <a name="number-of-workspaces"></a>工作區數目
工作區是用來隔離和記錄管理資料的邏輯容器。 後端會優化所選區域內實體叢集的工作區放置。

使用多個工作區的結果可能是： 

- 其中已明確列出數個工作區。
- 當資源範圍的查詢正在提取資料，且資料儲存在多個工作區時。
 
跨區域和跨叢集執行查詢需要系統在後端大量的中繼資料區塊中進行序列化和傳輸，通常會大於查詢的最終結果。 它也會限制系統執行優化、啟發學習法和利用快取的能力。

> [!IMPORTANT]
> 在某些多個工作區的情況下，CPU 和資料量測將不會正確，而且只會代表少數工作區的測量。

## <a name="parallelism"></a>平行處理原則
Azure 監視器記錄會使用 Azure 資料總管的大型叢集來執行查詢，而這些叢集的規模會有所不同，可能會取得多達數十個計算節點。 系統會根據工作區放置邏輯和容量，自動調整叢集規模。

為了有效率地執行查詢，它會進行分割，並根據其處理所需的資料散發至計算節點。 在某些情況下，系統無法有效率地執行此動作。 這可能會導致查詢的長時間。 

可以減少平行處理的查詢行為包括：

- 使用序列化和視窗函數，例如[序列化運算子](/azure/kusto/query/serializeoperator)、 [next （）](/azure/kusto/query/nextfunction)、[上月（）](/azure/kusto/query/prevfunction)和資料[列](/azure/kusto/query/rowcumsumfunction)函數。 在這些情況下，可以使用時間序列和使用者分析函數。 如果下列運算子不是用在查詢的結尾，也可能會發生沒有效率的序列化： [range](/azure/kusto/query/rangeoperator)、 [sort](/azure/kusto/query/sortoperator)、 [order](/azure/kusto/query/orderoperator)、 [top](/azure/kusto/query/topoperator)、 [top-hitters](/azure/kusto/query/tophittersoperator)、 [getschema](/azure/kusto/query/getschemaoperator)。
-    使用[dcount （）](/azure/kusto/query/dcount-aggfunction)彙總函式會強制系統擁有相異值的中央複本。 當資料的小數值偏高時，請考慮使用 dcount 函數的選擇性參數來降低精確度。
-    在許多情況下，[聯結](/azure/kusto/query/joinoperator?pivots=azuremonitor)運算子會降低整體平行處理原則。 當效能問題時，請檢查隨機聯結做為替代方案。
-    在資源範圍查詢中，預先執行的 RBAC 檢查可能會在有大量 RBAC 指派的情況下逗留。 這可能會導致較長的檢查，而導致平行處理原則。 例如，查詢是在有數千個資源的訂用帳戶上執行，而每個資源在資源層級中有許多角色指派，而不是在訂用帳戶或資源群組上。
-    如果查詢處理的資料量很小，則其平行處理會很低，因為系統不會將其分散到多個計算節點。



## <a name="next-steps"></a>後續步驟

- [Kusto 查詢語言的參考檔](/azure/kusto/query/)。
