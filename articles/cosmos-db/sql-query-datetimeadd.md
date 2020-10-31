---
title: Azure Cosmos DB 查詢語言中的 DateTimeAdd
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 DateTimeAdd。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: f6177426ce62f3369092403c52344fa764bcf2e6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095844"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

傳回將指定的數值 (為帶正負號的整數) 至指定的日期時間字串所產生的日期時間字串值  
  
## <a name="syntax"></a>語法
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>引數
  
*DateTimePart*  
   DateTimeAdd 加入整數的日期部分。 下表列出所有有效的 DateTimePart 引數：

| DateTimePart | 縮寫        |
| ------------ | -------------------- |
| Year         | 「year」、「yyyy」、「yy」 |
| 月        | "month"、"mm"、"m"   |
| 天          | "day"、"dd"、"d"     |
| 小時         | "hour"、"hh"         |
| Minute       | "minute"、"mi"、"n"  |
| Second       | "second"、"ss"、"s"  |
| Millisecond  | "毫秒"、"ms"  |
| 微秒  | "微秒"、"mcs" |
| 納 秒   | "納秒"、"ns"   |

*numeric_expr*  
   這是一個帶正負號的整數值，將會加入至指定之日期時間的 DateTimePart。

*DateTime*  
   UTC 日期和時間 ISO 8601 字串值，格式為 `YYYY-MM-DDThh:mm:ss.fffffffZ` ：
  
  |格式|描述|
  |-|-|
  |YYYY|四位數年份|
  |MM|兩位數的月份 (01 = 一月，依此類推 ) |
  |DD|月中的兩位數天 (01 到 31) |
  |T|時間元素開始的 signifier|
  |hh|兩位數的小時 (00 到 23) |
  |mm|兩位數的分鐘 (00 到 59) |
  |ss|兩位數的秒 (00 到 59) |
  |. fffffff|七位數的小數秒數|
  |Z|UTC (國際標準時間) 指示項||
  
  如需 ISO 8601 格式的詳細資訊，請參閱 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="return-types"></a>傳回類型

以下列格式傳回 UTC 日期和時間 ISO 8601 字串值 `YYYY-MM-DDThh:mm:ss.fffffffZ` ：
  
  |格式|描述|
  |-|-|
  |YYYY|四位數年份|
  |MM|兩位數的月份 (01 = 一月，依此類推 ) |
  |DD|月中的兩位數天 (01 到 31) |
  |T|時間元素開始的 signifier|
  |hh|兩位數的小時 (00 到 23) |
  |mm|兩位數的分鐘 (00 到 59) |
  |ss|兩位數的秒 (00 到 59) |
  |. fffffff|七位數的小數秒數|
  |Z|UTC (國際標準時間) 指示項||

## <a name="remarks"></a>備註

DateTimeAdd 將會傳回， `undefined` 原因如下：

- 指定的 DateTimePart 值無效
- 指定的 numeric_expr 不是有效的整數
- 引數或結果中的日期時間不是有效的 ISO 8601 日期時間。

## <a name="examples"></a>範例
  
下列範例會將1個月新增至 DateTime： `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

下列範例會從日期時間減去2小時： `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>下一步

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
