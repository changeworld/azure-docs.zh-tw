---
title: Azure Cosmos DB 查詢語言中的 DateTimeToTicks
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 DateTimeToTicks。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a6fcff691ee5278ed0a9e1c132f8ecebbee9431c
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227049"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB) 

將指定的日期時間轉換為刻度。 單一滴答代表100毫微秒或 1 10-百萬分之一秒的秒數。
  
## <a name="syntax"></a>語法
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>引數
  
*DateTime*  
   格式的 UTC 日期和時間 ISO 8601 字串值 `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>傳回類型

傳回正整數值。

## <a name="remarks"></a>備註

`undefined`如果 datetime 不是有效的 ISO 8601 日期時間，則 DateTimeDateTimeToTicks 會傳回

這個系統函數不會使用索引。

## <a name="examples"></a>範例

以下範例會傳回刻度的數目：

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

以下範例會傳回滴答數，但不指定小數秒數：

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>後續步驟

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
