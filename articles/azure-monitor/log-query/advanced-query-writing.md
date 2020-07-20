---
title: Azure 監視器中的先進查詢 |Microsoft Docs
description: 本文提供教學課程來說明如何使用 Analytics 入口網站在 Azure 監視器中撰寫查詢。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 3d228c62cd2d1bcb7f4515cd698186e2ebcbe929
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77670282"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>在 Azure 監視器中撰寫進階查詢

> [!NOTE]
> 您應該先完成[開始使用 Azure 監視器 Log Analytics](get-started-portal.md)和[開始使用查詢](get-started-queries.md)，再完成此課程。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>透過 let 重複使用程式碼
使用 `let` 來指派結果給變數，並稍後在查詢中使用該變數：

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

您也可以將常數值指派給變數。 這支援一種方法，讓您針對每次執行查詢時都必須變更的欄位設定參數。 視需要修改那些參數。 例如，若要計算給定時段內的可用磁碟空間與可用記憶體 (以百分位數表示)：

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

這可讓您在下次執行查詢時輕鬆變更開始與結束時間。

### <a name="local-functions-and-parameters"></a>區域函式與參數
使用 `let` 陳述式來建立可在相同查詢中使用的函式。 例如，定義一個會取得日期時間欄位值 (國際標準時間 (UTC) 格式) 並將它轉換為標準美國格式的函式。 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="print"></a>列印
`print` 將會傳回具有單欄與單列的表格，顯示計算的結果。 當您需要簡單的計算時，通常會使用這種方式。 例如，尋找太平洋標準時間 (PST) 的目前時間並加上美國東部標準時間 (EST) 欄：

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable` 可讓您定義資料集合。 您可以提供結構描述與一組值，然後以管道方式將該表格傳送到另一個查詢元素。 例如，建立 RAM 使用狀況表並每小時計算其平均值：

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

建立查閱表時，Datatable 建構也很實用。 若要將表格資料 (例如來自 _SecurityEvent_ 表格的事件識別碼) 對應到任意處列出的事件型別，請使用 `datatable` 建立具有事件型別的查閱表，並將此 Datatable 與 _SecurityEvent_ 資料聯結：

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>後續步驟
若要了解如何搭配 Azure 監視器記錄資料使用 [Kusto 查詢語言](/azure/kusto/query/)，請參閱其他課程：

- [字串作業](string-operations.md)
- [日期和時間作業](datetime-operations.md)
- [彙總函式](aggregations.md)
- [進階彙總](advanced-aggregations.md)
- [JSON 與資料結構](json-data-structures.md)
- [聯結](joins.md)
- [圖表](charts.md)
