---
title: Azure Cosmos DB 查詢語言中的 DateTimeDiff
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 DateTimeDiff。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 09b801c48bf2998a2d8926009cae76287c1ac9b6
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342261"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
傳回 (為帶正負號的整數值) ，並在指定的開始日期和結束 *日期* 之間 *EndDate* 跨越指定的 DateTimePart 界限。
  
## <a name="syntax"></a>語法
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
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

*起始*  
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

*日期*  
   格式的 UTC 日期和時間 ISO 8601 字串值 `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>傳回類型

傳回帶正負號的整數值。

## <a name="remarks"></a>備註

DateTimeDiff 將會傳回， `undefined` 原因如下：

- 指定的 DateTimePart 值無效
- 開始日期或結束項不是有效的 ISO 8601 日期時間

DateTimeDiff 一律會傳回帶正負號的整數值，而且會測量超過的 DateTimePart 界限數目，而不是時間間隔的度量。

## <a name="examples"></a>範例
  
下列範例會計算與之間跨越的天數界限 `2020-01-01T01:02:03.1234527Z` `2020-01-03T01:02:03.1234567Z` 。

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

下列範例會計算和之間跨越的年份界限數目 `2028-01-01T01:02:03.1234527Z` `2020-01-03T01:02:03.1234567Z` 。

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

下列範例會計算與之間跨越的小時界限數目 `2020-01-01T01:00:00.1234527Z` `2020-01-01T01:59:59.1234567Z` 。 即使這些日期時間值的間隔超過0.99 小時，也會傳回 `DateTimeDiff` 0，因為未超過任何小時界限。

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>後續步驟

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
