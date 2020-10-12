---
title: Azure Cosmos DB 查詢語言中的 DateTimePart
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 DateTimePart。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 87663c18ddaa5da6740a0f54aa5f2812cbb06af8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227079"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart (Azure Cosmos DB) 

傳回指定之日期時間之間指定之 DateTimePart 的值。
  
## <a name="syntax"></a>語法
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>引數
  
*DateTimePart*  
   DateTimePart 將傳回值的日期部分。 下表列出所有有效的 DateTimePart 引數：

| DateTimePart | 縮寫        |
| ------------ | -------------------- |
| Year         | 「year」、「yyyy」、「yy」 |
| Month        | "month"、"mm"、"m"   |
| 天          | "day"、"dd"、"d"     |
| 小時         | "hour"、"hh"         |
| Minute       | "minute"、"mi"、"n"  |
| Second       | "second"、"ss"、"s"  |
| Millisecond  | "毫秒"、"ms"  |
| 微秒  | "微秒"、"mcs" |
| 納 秒   | "納秒"、"ns"   |

*DateTime*  
   格式的 UTC 日期和時間 ISO 8601 字串值 `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>傳回類型

傳回正整數值。

## <a name="remarks"></a>備註

DateTimePart 將會傳回， `undefined` 原因如下：

- 指定的 DateTimePart 值無效
- DateTime 不是有效的 ISO 8601 DateTime

這個系統函數將不會使用索引。

## <a name="examples"></a>範例

以下範例會傳回月份的整數值：

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

以下是傳回微秒數的範例：

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>後續步驟

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
