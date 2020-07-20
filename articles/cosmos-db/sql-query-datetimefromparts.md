---
title: Azure Cosmos DB 查詢語言中的 DateTimeFromParts
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 DateTimeFromParts。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aec567c642f8eb3cb421ef5a119fe58f3a5fe05a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261821"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB) 

傳回從輸入值所結構化的字串日期時間值。
  
## <a name="syntax"></a>語法
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>引數
  
*numberYear*年份的整數值，格式為`YYYY`

*numberMonth*  
   月份的整數值，格式為`MM`

*numberDay*  
   日期的整數值，格式為`DD`

*numberHour* (選用的) 整數值，格式為小時`hh`

*numberMinute* (選用的) 整數值（格式為分鐘）`mm`

*numberSecond* (選擇性) 第二個格式的整數值`ss`

*numberOfFractionsOfSecond* (選用的) 整數值，其格式為秒的小數`.fffffff`

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
  
 如需 ISO 8601 格式的詳細資訊，請參閱[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>備註

如果指定的整數會建立不正確日期時間，則 DateTimeFromParts 會傳回 `undefined` 。

如果未指定選擇性引數，其值會是0。

## <a name="examples"></a>範例

以下範例只包含用來建立日期時間的必要引數：

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

以下是另一個範例，它也會使用一些選擇性引數來建立日期時間：

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

以下是另一個範例，它也會使用所有的選擇性引數來建立日期時間：

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>後續步驟

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
