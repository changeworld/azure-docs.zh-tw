---
title: Log Analytics 智慧分析範例 | Microsoft Docs
description: 在 Log Analytics 中使用智慧分析功能執行使用者活動分析的範例。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/15/2019
ms.openlocfilehash: 51584ccf5f845be8a06b1e049cae11e636edef11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77659827"
---
# <a name="log-analytics-smart-analytics-examples"></a>Log Analytics 智慧分析範例
本文包含了在 Log Analytics 中使用智慧分析功能執行使用者活動分析的範例。 您可以使用這些範例來分析 Application Insights 監視的應用程式，或使用這些查詢中的概念對其他資料進行類似的分析。 

請參閱 [Kusto 語言參考](https://docs.microsoft.com/azure/kusto/query/)了解在這些範例中使用不同關鍵字的詳細資料。 如果您不熟悉 Log Analytics，請逐步查看[建立查詢的課程](get-started-queries.md)。

## <a name="cohorts-analytics"></a>世代分析

世代分析追蹤特定使用者群組 (稱為世代) 的活動。 它會嘗試藉由測量傳回使用者的比率來測量服務的吸引力。 使用者會依他們第一次使用服務的時間進行分組。 在分析世代時，我們預期在第一個追蹤期間發現活動減少。 每個世代都是以第一次觀察其成員的當週為標題。

下列範例會分析使用者在第一次使用該服務後的 5 週內執行的活動數目。

```Kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample we calculate distinct users but you can change
// this to any other metric that would measure the engagement of the cohort members.
| extend 
    r0 = DistinctUsers(startDate, startDate+7d),
    r1 = DistinctUsers(startDate, startDate+14d),
    r2 = DistinctUsers(startDate, startDate+21d),
    r3 = DistinctUsers(startDate, startDate+28d),
    r4 = DistinctUsers(startDate, startDate+35d)
| union (week | where Cohort == startDate + 7d 
| extend 
    r0 = DistinctUsers(startDate+7d, startDate+14d),
    r1 = DistinctUsers(startDate+7d, startDate+21d),
    r2 = DistinctUsers(startDate+7d, startDate+28d),
    r3 = DistinctUsers(startDate+7d, startDate+35d) )
| union (week | where Cohort == startDate + 14d 
| extend 
    r0 = DistinctUsers(startDate+14d, startDate+21d),
    r1 = DistinctUsers(startDate+14d, startDate+28d),
    r2 = DistinctUsers(startDate+14d, startDate+35d) )
| union (week | where Cohort == startDate + 21d 
| extend 
    r0 = DistinctUsers(startDate+21d, startDate+28d),
    r1 = DistinctUsers(startDate+21d, startDate+35d) ) 
| union (week | where Cohort == startDate + 28d 
| extend 
    r0 = DistinctUsers (startDate+28d, startDate+35d) )
// Calculate the retention percentage for each cohort by weeks
| project Cohort, r0, r1, r2, r3, r4,
          p0 = r0/r0*100,
          p1 = todouble(r1)/todouble (r0)*100,
          p2 = todouble(r2)/todouble(r0)*100,
          p3 = todouble(r3)/todouble(r0)*100,
          p4 = todouble(r4)/todouble(r0)*100 
| sort by Cohort asc
```
此範例會導致下列輸出。

![世代分析輸出](media/smart-analytics/cohorts.png)

## <a name="rolling-monthly-active-users-and-user-stickiness"></a>每月累積作用中使用者和使用者黏著度
下列範例會使用 [series_fir](/azure/kusto/query/series-firfunction) 函數進行時間序列分析，該函數允許您執行滑動視窗計算。 受監視的範例應用程式是線上商店，透過自訂的事件追蹤使用者的活動。 該查詢會追蹤兩種類型的使用者活動，_AddToCart_ 和 _Checkout_，並將_作用中使用者_定義為在指定日期內至少執行一次結帳的使用者。



```Kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they performed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked-out in our web site
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column containing a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day)
| mvexpand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// render as timechart
| render timechart
```

此範例會導致下列輸出。

![每月累積使用者輸出](media/smart-analytics/rolling-mau.png)

下列範例將上述查詢轉換成可重複使用的函數，並使用它來計算累積使用者黏著度。 此查詢中的作用中使用者僅定義為在指定日期至少執行一次結帳的使用者。

``` Kusto
let rollingDcount = (sliding_window_size: int, event_name:string)
{
    let endtime = endofday(datetime(2017-03-01T00:00:00Z));
    let window = 90d;
    let starttime = endtime-window;
    let interval = 1d;
    let moving_sum_filter = toscalar(range x from 1 to sliding_window_size step 1 | extend v=1| summarize makelist(v));    
    let min_activity = 1;
    customEvents
    | where timestamp > starttime
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    | where (name == event_name)
    | where user_AuthenticatedId <> ""
    | make-series UserClicks=count() default=0 on timestamp 
        in range(starttime, endtime-1s, interval) by user_AuthenticatedId
    | extend RollingUserClicks=fir(UserClicks, moving_sum_filter, false)
    | project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
    | mvexpand RollingUserClicksByDay
    | extend Timestamp=todatetime(RollingUserClicksByDay[0])
    | extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
    | summarize sum(RollingActiveUsersByDay) by Timestamp
    | where Timestamp > starttime + 28d
};
// Use the moving_sum_filter with bin size of 28 to count MAU.
rollingDcount(28, "Checkout")
| join
(
    // Use the moving_sum_filter with bin size of 1 to count DAU.
    rollingDcount(1, "Checkout")
)
on Timestamp
| project sum_RollingActiveUsersByDay1 *1.0 / sum_RollingActiveUsersByDay, Timestamp
| render timechart
```

此範例會導致下列輸出。

![使用者黏著度輸出](media/smart-analytics/user-stickiness.png)

## <a name="regression-analysis"></a>迴歸分析
此範例示範如何僅根據應用程式追蹤記錄，針對服務中斷建立自動化的偵測器。 偵測器會搜尋應用程式中相對少量錯誤和警告追蹤的異常突然增加。

兩種技術用來根據追蹤記錄資料評估服務狀態：

- 使用 [make-series](/azure/kusto/query/make-seriesoperator) 將半結構化文字追蹤記錄轉換為表示正追蹤行和負追蹤行之間比率的計量。
- 使用 [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) 和 [series_fit_line](/azure/kusto/query/series-fit-linefunction) 進行進階步驟跳躍偵測 (使用時間序列分析和 2 行線性迴歸)。

``` Kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease)
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```

## <a name="next-steps"></a>後續步驟

- 請參閱[資料總管語言參考](/azure/kusto/query)了解語言詳細資訊。
- 進行[在 Log Analytics 中撰寫查詢課程](get-started-queries.md)。
