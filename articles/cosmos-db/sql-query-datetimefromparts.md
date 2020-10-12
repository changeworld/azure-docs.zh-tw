---
title: Azure Cosmos DB 查詢語言中的 DateTimeFromParts
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 DateTimeFromParts。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aec567c642f8eb3cb421ef5a119fe58f3a5fe05a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261821"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB) 

傳回從輸入值所構成的字串日期時間值。
  
## <a name="syntax"></a>語法
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>引數
  
*numberYear* 年份的整數值，格式為 `YYYY`

*numberMonth*  
   月份的整數值，格式為 `MM`

*numberDay*  
   日期的整數值，格式為 `DD`

*numberHour* (選用) 整數值（以格式表示） `hh`

*numberMinute* (選用的分鐘) 整數值（格式為 `mm`

*numberSecond* (選用的) 整數值（以格式為秒） `ss`

*numberOfFractionsOfSecond* (選用的) 整數值（以格式為秒） `.fffffff`

## <a name="return-types"></a>傳回類型

以下列格式傳回 UTC 日期和時間 ISO 8601 字串值 `YYYY-MM-DDThh:mm:ss.fffffffZ` ：
  
  |[格式]|描述|
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

## <a name="remarks"></a>備註

如果指定的整數會建立不正確日期時間，DateTimeFromParts 會傳回 `undefined` 。

如果未指定選擇性引數，其值將會是0。

## <a name="examples"></a>範例

以下是一個範例，其中只包含用來建立日期時間的必要引數：

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

以下是另一個範例，也會使用一些選擇性的引數來建立 DateTime：

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

以下是另一個範例，也會使用所有選擇性引數來建立 DateTime：

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

## <a name="next-steps"></a>接下來的步驟

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
