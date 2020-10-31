---
title: Azure Cosmos DB 查詢語言中的 DateTimeToTicks
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 DateTimeToTicks。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 47bf8a3a2ffe66e295fcb9d8a2a02891812c6813
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095776"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

將指定的日期時間轉換為刻度。 單一刻度代表100毫微秒或 1 10-百萬分之一秒秒。 

## <a name="syntax"></a>語法
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>引數
  
*DateTime*  
   格式的 UTC 日期和時間 ISO 8601 字串值 `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>傳回類型

傳回已簽署的數值，這是自 Unix epoch 以來經過的目前 100-毫微秒滴答數。 換句話說，DateTimeToTicks 會傳回自00:00:00 星期四（1月1970）以來經過的 100-毫微秒滴答數。

## <a name="remarks"></a>備註

`undefined`如果日期時間不是有效的 ISO 8601 日期時間，DateTimeDateTimeToTicks 會傳回

這個系統函數將不會使用索引。

## <a name="examples"></a>範例

以下是傳回刻度數目的範例：

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

以下範例會傳回刻度數目，而不指定小數秒數：

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

## <a name="next-steps"></a>下一步

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
