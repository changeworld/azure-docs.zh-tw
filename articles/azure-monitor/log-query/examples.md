---
title: Azure 監視器記錄查詢範例 | Microsoft Docs
description: Azure 監視器中使用 Kusto 查詢語言進行的記錄查詢範例。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/01/2019
ms.openlocfilehash: 9bfadf55e4f68bb7188b27e4ef5bc03e3955f375
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662043"
---
# <a name="azure-monitor-log-query-examples"></a>Azure 監視器記錄查詢範例
本文包含多個使用 [Kusto 查詢語言](log-query-overview.md)的[查詢](/azure/kusto/query/)範例，以從 Azure 監視器擷取不同的記錄資料類型。 您可以透過方法來合併和分析資料，以使用這些範例識別用於您的要求的不同策略。  

請參閱 [Kusto 語言參考](https://docs.microsoft.com/azure/kusto/query/)了解在這些範例中使用不同關鍵字的詳細資料。 如果您不熟悉 Azure 監視器，請逐步查看[建立查詢的課程](get-started-queries.md)。

## <a name="events"></a>事件

### <a name="search-application-level-events-described-as-cryptographic"></a>搜尋說明為「密碼編譯」的應用程式層級事件
此範例會搜尋 **Events** 資料表，找出 **EventLog** 是 _Application_ 且 **RenderedDescription** 包含 _cryptographic_ 的記錄。 包含過去 24 小時的記錄。

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>搜尋解除封送處理相關的事件
搜尋 **Event** 和 **SecurityEvents** 資料表，找出提到 _unmashaling_ 的記錄。

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>活動訊號

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>列出逐週傳送資料的電腦數目圖表

下列範例圖表列出了每週傳送活動訊號的相異電腦數目。

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>尋找過時的電腦

下列範例會尋找在前一天使用中但在前一個小時未傳送活動訊號的電腦。

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>取得每個電腦 IP 最新的活動訊號記錄

此範例會傳回每個電腦 IP 的最新活動訊號記錄。
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>比對受保護的狀態記錄與活動訊號記錄

此範例會比對電腦和時間，尋找相關的保護狀態記錄和活動訊號記錄。
請注意，[時間] 欄位會四捨五入到最近的分鐘數。 我們使用執行階段 bin 計算以達成目的：`round_time=bin(TimeGenerated, 1m)`。

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>伺服器可用率

根據活動訊號記錄計算伺服器可用率。 可用性定義為「每小時至少 1 個活動訊號」。
因此，如果伺服器在 100 個小時中有 98 個小時可用，則可用率為 98%。

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>多種資料類型

### <a name="chart-the-record-count-per-table"></a>列出每個資料表的記錄計數圖表
以下範例會收集過去五小時內的所有資料表的所有記錄，並計算每個資料表中的記錄數目。 這些結果如時間圖所示。

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>按照型別計算過去一個小時中收集的所有記錄。
下列範例會搜尋過去一個小時中的報告內容，並按照**類型**計算每個資料表的記錄數目。 結果會顯示在橫條圖中。

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>計算每個類別的 Azure 診斷記錄
此範例會計算每個唯一類別的所有 Azure 診斷記錄。

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>取得每個唯一類別的隨機記錄
此範例會取得每個唯一類別的單一隨機 Azure 診斷記錄。

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>取得每個類別的最新記錄
此範例會取得每個唯一類別的最新 Azure 診斷記錄。

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>網路監視

### <a name="computers-with-unhealthy-latency"></a>狀況不良延遲的電腦
此範例會建立一份具有狀況不良延遲的相異電腦清單。

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>效能

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>加入電腦效能記錄以將記憶體和 CPU 建立關聯
此範例會為特定電腦的**效能**記錄建立關聯，並建立兩個時間圖、平均 CPU 和最大記憶體。

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>每部電腦的效能 CPU 使用率圖形
此範例會計算開頭為 _Contoso_ 的電腦 CPU 使用率，並製作成圖表。

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>保護狀態

### <a name="computers-with-non-reporting-protection-status-duration"></a>電腦未回報的保護狀態持續時間
此範例會列出具有 _Not Reporting_ 保護狀態的電腦，和它們處於這個狀態的持續時間。

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>比對受保護的狀態記錄與活動訊號記錄
此範例會比對電腦和時間，尋找相關的保護狀態記錄和活動訊號記錄。
[時間] 欄位會使用 **bin** 四捨五入到最近的分鐘數。

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>安全性記錄

### <a name="count-security-events-by-activity-id"></a>使用活動識別碼計算安全性事件的數目


此範例仰賴**活動**資料行的固定結構：\<識別碼\>-\<名稱\>。
它會將**活動**的值剖析為兩個新的資料行，並計算每個**活動識別碼**的發生次數。

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>計算與權限相關的安全性事件數目
此範例會顯示 **securityEvent** 記錄的數目，其中 **Activity** 資料行包含完整詞彙 _Permissions_。 查詢適用於過去 30 分鐘內建立的記錄。

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>尋找無法從電腦安全性偵測登入的帳戶
此範例會尋找並計算無法從我們識別為安全性偵測的電腦登入的帳戶。

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>我的安全性資料是否可用？
啟動資料探索通常由資料可用性檢查開始。 此範例會顯示過去 30 分鐘內 **SecurityEvent** 記錄的數目。

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>剖析活動名稱和識別碼
下列兩個範例仰賴**活動**資料行的固定結構：\<識別碼\>-\<名稱\>。 第一個範例使用**剖析**運算子來將值指派給兩個新的資料行：**activityID** 和 **activityDesc**。

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

此範例會使用**分割**運算子來建立不同的值的陣列
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>明確的認證程序
下列範例會顯示過去一週使用明確認證程序的圓形圖

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>熱門執行處理序

下列範例會顯示過去三天中，五個最常見處理序的時間線。

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>透過不同 IP 的相同帳戶，尋找重複的失敗登入嘗試。

下列範例會從過去六小時內具備五個以上不同 IP 中的相同帳戶尋找失敗的登入嘗試。

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>尋找無法登入的使用者帳戶 
下列範例會識別在過去一天內登入失敗超過五次的使用者帳戶，以及帳戶最後嘗試登入的時間。

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

如果相同的可疑帳戶在稍後可以成功登入，請使用 **join** 和**我們可以查檢查的**陳述式。

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>使用方式

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>計算每部電腦的效能使用量報告的平均大小

此範例會計算過去 3 小時內每部電腦的效能使用量報告的平均大小。
結果會以橫條圖顯示。
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>時間圖表延遲百分位數為 50 和 95

此範例會計算過去 24 小時內，報告每小時的 **avgLatency** 第 50 個和第 95 個百分位數，並製作成圖表。

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>特定電腦今天的使用量
此範例會從前一天包含字串 **ContosoFile** 的電腦名稱擷取_使用量_資料。 結果的排序依據為 **TimeGenerated**。

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>更新

### <a name="computers-still-missing-updates"></a>遺漏更新的電腦
此範例會示範在過去幾天內遺漏一或多個重大更新的電腦清單，以及仍遺漏更新的電腦清單。

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>後續步驟

- 請參閱 [Kusto 語言參考](/azure/kusto/query)了解語言詳細資訊。
- 進行[在 Azure 監視器中撰寫記錄查詢](get-started-queries.md)課程。
