---
title: Azure Cosmos DB 查詢語言中的 DateTimeAdd
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 DateTimeAdd。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 0b2741a3d2b013ba7bd97038eb4ba4512f36af11
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261826"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB) 

傳回日期時間字串值，這是因為將指定的數位值 (做為帶正負號的整數) 加入指定的日期時間字串所產生  
  
## <a name="syntax"></a>語法
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>引數
  
*DateTimePart*  
   DateTimeAdd 加上整數的日期部分。 下表列出所有有效的 DateTimePart 引數：

| DateTimePart | 縮寫        |
| ------------ | -------------------- |
| 年         | "year"、"yyyy"、"yy" |
| 月        | "month"、"mm"、"m"   |
| 天          | "day"、"dd"、"d"     |
| 小時         | "hour"、"hh"         |
| Minute       | "minute"、"mi"、"n"  |
| Second       | "second"、"ss"、"s"  |
| Millisecond  | "毫秒"、"ms"  |
| 微秒  | 「微秒」、「mcs」 |
| 100奈秒   | "納秒"、"ns"   |

*numeric_expr*  
   這是一個帶正負號的整數值，將會加入至指定之日期時間的 DateTimePart

*DateTime*  
   UTC 日期和時間 ISO 8601 字串值，格式為 `YYYY-MM-DDThh:mm:ss.fffffffZ` ：
  
  |[格式]|描述|
  |-|-|
  |YYYY|四位數的年份|
  |MM|兩位數的月份 (01 = 一月等等 ) |
  |DD|兩位數的當月日期 (01 到 31) |
  |T|時間元素開頭的 signifier|
  |hh|兩位數的小時 (00 到 23) |
  |mm|兩位數的分鐘 (00 到 59) |
  |ss|兩位數的秒 (00 到 59) |
  |. fffffff|七位數的小數秒數|
  |Z|UTC (國際標準時間) 指示項||
  
  如需 ISO 8601 格式的詳細資訊，請參閱[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="return-types"></a>傳回類型

傳回格式為的 UTC 日期和時間 ISO 8601 字串值， `YYYY-MM-DDThh:mm:ss.fffffffZ` 其中：
  
  |[格式]|描述|
  |-|-|
  |YYYY|四位數的年份|
  |MM|兩位數的月份 (01 = 一月等等 ) |
  |DD|兩位數的當月日期 (01 到 31) |
  |T|時間元素開頭的 signifier|
  |hh|兩位數的小時 (00 到 23) |
  |mm|兩位數的分鐘 (00 到 59) |
  |ss|兩位數的秒 (00 到 59) |
  |. fffffff|七位數的小數秒數|
  |Z|UTC (國際標準時間) 指示項||

## <a name="remarks"></a>備註

DateTimeAdd 會 `undefined` 因為下列原因而傳回：

- 指定的 DateTimePart 值無效
- 指定的 numeric_expr 不是有效的整數
- 引數或結果中的日期時間不是有效的 ISO 8601 日期時間。

## <a name="examples"></a>範例
  
下列範例會在日期時間加上1個月：`2020-07-09T23:20:13.4575530Z`

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

下列範例會從日期時間減去2小時：`2020-07-09T23:20:13.4575530Z`

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

## <a name="next-steps"></a>後續步驟

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
