---
title: Azure Cosmos DB 查詢語言中的 DateTimeDiff
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 DateTimeDiff。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: b90b45072128252e8abc22d3422c84c813808119
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446382"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff （Azure Cosmos DB）

傳回跨越指定的開始和*結束**日期*之間所指定 DateTimePart 界限的計數（以帶正負號的整數值）。
  
## <a name="syntax"></a>語法
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
```

## <a name="arguments"></a>引數
  
*DateTimePart*  
   DateTimeAdd 加上整數的日期部分。 下表列出所有有效的 DateTimePart 引數：

| DateTimePart | 縮寫        |
| ------------ | -------------------- |
| 年         | "year"、"yyyy"、"yy" |
| Month        | "month"、"mm"、"m"   |
| 天          | "day"、"dd"、"d"     |
| 小時         | "hour"、"hh"         |
| Minute       | "minute"、"mi"、"n"  |
| Second       | "second"、"ss"、"s"  |
| Millisecond  | "毫秒"、"ms"  |
| 微秒  | 「微秒」、「mcs」 |
| 100奈秒   | "納秒"、"ns"   |

*開始*  
    UTC 日期和時間 ISO 8601 字串值，格式為 `YYYY-MM-DDThh:mm:ss.fffffffZ` ：
  
  |[格式]|描述|
  |-|-|
  |YYYY|四位數的年份|
  |MM|兩位數的月份（01 = 一月等等）|
  |DD|兩位數的月份日期（01到31）|
  |T|時間元素開頭的 signifier|
  |hh|兩位數的小時（00到23）|
  |mm|兩位數的分鐘數（00到59）|
  |ss|兩位數的秒數（00到59）|
  |. fffffff|七位數的小數秒數|
  |Z|UTC （國際標準時間）指示項||
  
  如需 ISO 8601 格式的詳細資訊，請參閱[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

*終止*  
   格式的 UTC 日期和時間 ISO 8601 字串值`YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>傳回類型

傳回帶正負號的整數值。

## <a name="remarks"></a>備註

DateTimeDiff 會 `undefined` 因為下列原因而傳回：

- 指定的 DateTimePart 值無效
- 開始或結束日期不是有效的 ISO 8601 DateTime

DateTimeDiff 一律會傳回帶正負號的整數值，而且是跨越的 DateTimePart 界限數測量，而不是測量時間間隔。

## <a name="examples"></a>範例
  
下列範例會計算跨越到的天數界限 `2020-01-01T01:02:03.1234527Z` `2020-01-03T01:02:03.1234567Z` 。

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

下列範例會計算跨越和之間的年界限數 `2028-01-01T01:02:03.1234527Z` `2020-01-03T01:02:03.1234567Z` 。

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

下列範例會計算跨越和之間的小時界限數 `2020-01-01T01:00:00.1234527Z` `2020-01-01T01:59:59.1234567Z` 。 雖然這些日期時間值相距超過0.99 小時，仍會傳回 `DateTimeDiff` 0，因為未超過任何小時界限。

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
