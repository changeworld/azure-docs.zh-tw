---
title: 開始使用 Azure 監視器中的記錄查詢 | Microsoft Docs
description: 本文提供教學課程來說明如何在 Azure 監視器中開始撰寫記錄查詢。
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: a949c9b34e299e0dc4eccbb62f4b4ebb38d6ccb9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186706"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>開始使用 Azure 監視器中的查詢

> [!NOTE]
> 如果您要從至少一台虛擬機器收集資料，就可以在自己的環境中進行這項練習。 若非如此，請使用我們的[示範環境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)，內有許多範例資料。  如果您已經知道如何在 KQL 中查詢，但只需要根據資源類型快速建立有用的查詢，請參閱[儲存的範例查詢窗格](./example-queries.md)。

在本教學課程中，您會了解如何在 Azure 監視器中撰寫記錄查詢。 它會告訴您如何：

- 了解查詢結構
- 排序查詢結果
- 篩選查詢結果
- 指定時間範圍
- 選取要包含在結果中的欄位
- 定義和使用自訂欄位
- 彙總和群組結果

如需如何在 Azure 入口網站中使用 Log Analytics 的教學課程，請參閱[開始使用 Azure 監視器 Log Analytics](./log-analytics-tutorial.md)。<br>
如需 Azure 監視器之中記錄查詢的詳細資訊，請參閱 [Azure 監視器中的記錄查詢總覽](log-query-overview.md)。

請按照以下本教學課程的影片版本進行：

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>撰寫新的查詢

查詢可以透過資料表名稱或 *search* 命令來開始。 請從資料表名稱開始，原因是它會定義清楚的查詢範圍，並改善查詢效能和結果的相關性。

> [!NOTE]
> Azure 監視器所使用的 Kusto 查詢語言會區分大小寫。 語言關鍵字通常會以小寫來撰寫。 當查詢中使用到資料表或資料行的名稱時，請務必使用正確的大小寫，如結構描述窗格所示。

### <a name="table-based-queries"></a>以資料表為基礎的查詢

Azure 監視器會將記錄資料組織到資料表中，每個資料表都包含多個資料行。 所有資料表和資料行都會顯示在 Analytics 入口網站中 Log Analytics 中的 [結構描述] 窗格。 請找出您感興趣的資料表，然後看看其中的資料：

```Kusto
SecurityEvent
| take 10
```

上述查詢會傳回「SecurityEvent」資料表中的 10 個結果 (沒有特定順序)。 這是瀏覽資料表並了解其結構和內容的常見方式。 讓我們檢查其建置方式：

* 此查詢會從「SecurityEvent」資料表名稱來開始，這部分會定義查詢的範圍。
* 垂直線 (|) 字元會分隔命令，下列命令輸入中第一個項目的輸出也是如此。 您可以新增任意數目的垂直線元素。
* 垂直線後面是 **take** 命令，會從資料表傳回特定數目的任意記錄。

即使未新增 `| take 10`，我們實際上仍可執行查詢，查詢仍會有效，但它可能會傳回高達 10,000 個結果。

### <a name="search-queries"></a>搜尋查詢

搜尋查詢的結構較鬆散，通常更適合用於尋找其資料行中包含特定值的記錄：

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

此查詢會搜尋「SecurityEvent」資料表，看看其中是否有記錄包含「Cryptographic」片語。 系統會傳回並顯示這些記錄的其中 10 筆。 如果我們省略了 `in (SecurityEvent)` 部分，只執行 `search "Cryptographic"`，則搜尋會找遍「所有」資料表，因此需要較長時間，且較沒效率。

> [!WARNING]
> 搜尋查詢的速度通常會比資料表式的查詢慢，因為所需處理的資料更多。 

## <a name="sort-and-top"></a>Sort 和 top
雖然 **take** 適合用來取得一些記錄，但所選取和顯示的結果並沒有依特定順序來排列。 若要取得已排序的檢視，您可以依所慣用的資料行來 **排序**：

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

不過，這麼做會傳回太多結果，而且也可能需要一些時間。 上述查詢會依 TimeGenerated 資料行來排序「整個」SecurityEvent 資料表。 Analytics 入口網站接著會將顯示限制為只顯示 10,000 筆記錄。 這當然不是最佳方法。

若要只取得最近 10 筆記錄，最佳方法是使用 **top**，其會在伺服器端排序整個資料表，然後傳回排序在前的記錄：

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

預設的排序順序是遞減，因此，我們通常會忽略 **desc** 引數。輸出看起來會像這樣：

![前 10 個](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Where︰針對條件進行篩選
顧名思義，篩選會根據特定條件來篩選資料。 這種方式最常用來限制查詢結果，以便只顯示相關資訊。

若要對查詢新增篩選，請使用 **where** 運算子，後面加上一或多個條件。 例如，下列查詢只會傳回 Level 等於 8 的 SecurityEvent 記錄：

```Kusto
SecurityEvent
| where Level == 8
```

在撰寫篩選條件時，您可以使用下列運算式：

| 運算是 | 描述 | 範例 |
|:---|:---|:---|
| == | 檢查是否相等<br>(區分大小寫) | `Level == 8` |
| =~ | 檢查是否相等<br>(不區分大小寫) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=、<> | 檢查是否不相等<br>(這兩個運算式同義) | `Level != 4` |
| and、or | 條件之間必須有此項目| `Level == 16 or CommandLine != ""` |

若要依多個條件進行篩選，您可以使用 **and**：

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

也可以透過垂直線將多個 **where** 元素一個接一個串連在一起：

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> 值可以有不同類型，因此，您可能需要轉換值才能對正確類型進行比較。 例如，SecurityEvent 的 *Level* 資料行是字串類型，因此，您必須先將其轉換為數值類型 (例如，int 或 long)，才能對其使用數值運算子：`SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>指定時間範圍

### <a name="time-picker"></a>時間選擇器

時間選擇器位於 [執行] 按鈕旁邊，會指出我們只查詢過去 24 小時內的記錄。 這是適用於所有查詢的預設時間範圍。 若只要取得過去 1 小時的記錄，請選取 [過去 1 小時]，然後再次執行查詢。

![時間選擇器](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>查詢中的時間篩選

您也可以對查詢新增時間篩選，藉以定義您自己的時間範圍。 時間篩選最好直接放在資料表名稱之後： 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

在上述時間篩選中，`ago(30m)` 表示「30 分鐘前」，因此，這個查詢只會傳回過去 30 分鐘的記錄。 其他時間單位包括天 (2d)、分鐘 (25m) 和秒 (10s)。


## <a name="project-and-extend-select-and-compute-columns"></a>Project 和 Extend：選取和計算資料行

使用 **project** 可選取要包含在結果中的特定資料行：

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

上述範例會產生以下輸出：

![查詢專案結果](media/get-started-queries/project.png)

您也可以使用 **project** 將資料行重新命名並定義新的資料行。 下列範例會使用 project 來執行下列作業：

* 僅選取 Computer 和 TimeGenerated 原始資料行。
* 將 Activity 資料行顯示為 EventDetails。
* 建立名為 EventCode 的新資料行。 **substring()** 函式可用來僅取得 [活動] 欄位中的前四個字元。


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**extend** 會在結果集內保留所有原始資料行，並定義其他資料行。 下列查詢會使用 [extend] 來新增 [EventCode] 資料行。 請注意，此資料行可能不會顯示在資料表的結尾，因此需要展開記錄的詳細資料才能查看。

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Summarize：彙總資料列群組
使用 **summarize** 可識別記錄群組 (根據一或多個資料行)，並對其套用彙總。 **summarize** 最常見的用法是 *count*，其會傳回每個群組中的結果數目。

下列查詢會檢閱過去 1 小時的所有 Perf 記錄、依 ObjectName 將這些記錄分組，並計算每個群組中的記錄數目： 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

依多個維度來定義群組有時很合理。 這些值的每個唯一組合可定義出不同的群組：

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

另一個常見用途是針對每個群組進行數學或統計計算。 例如，下列運算式會計算每部電腦的 CounterValue 平均值：

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

不幸的是，此查詢的結果並無意義，原因是我們混搭使用不同的效能計數器。 若要讓結果更有意義，我們應該針對 CounterName 和 Computer 的每個組合分別計算平均值：

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>依時間資料行彙總
群組結果也可以根據時間資料行或其他連續值來進行。 不過，只彙總 `by TimeGenerated` 會針對時間範圍內的每一毫秒建立群組，原因是這些是唯一值。 

若要根據連續值建立群組，最好是使用 **bin** 將範圍分成可管理的單位。 下列查詢會分析 Perf 記錄，這些記錄會測量特定電腦上的可用記憶體 (可用的 MB 數)。 這會計算過去 7 天內每 1 小時期間的平均值：

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

若要讓輸出更加清楚，您可以選取將它顯示為時間圖表，以顯示一段時間內的可用記憶體：

![經過一段時間的查詢記憶體](media/get-started-queries/chart.png)



## <a name="next-steps"></a>後續步驟

- 深入了解如何依照[在 Azure 監視器記錄查詢中使用字串](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)的指示，在記錄查詢中使用字串資料。
- 深入了解如何依照 [Azure 監視器記錄查詢中的進階彙總](/azure/data-explorer/write-queries#advanced-aggregations)的指示，在記錄查詢中彙總資料。
- 了解如何依照 [Azure 監視器記錄查詢中的聯結](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins) 的指示，從多個資料表聯結資料。
- 在 [KQL 語言參考](/azure/kusto/query/)中取得整個 Kusto 查詢語言的相關文件。