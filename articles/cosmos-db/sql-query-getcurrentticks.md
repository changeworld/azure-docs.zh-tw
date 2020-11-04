---
title: Azure Cosmos DB 查詢語言中的 GetCurrentTicks
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 GetCurrentTicks。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 610c545bf25822d27e0a641a1b2631f899502420
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340153"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

傳回自00:00:00 星期四（1月1970）以來經過的 100-毫微秒滴答數。
  
## <a name="syntax"></a>語法
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>傳回類型

傳回已簽署的數值，這是自 Unix epoch 以來經過的目前 100-毫微秒滴答數。 換句話說，GetCurrentTicks 會傳回自00:00:00 星期四（1月1970）以來經過的100毫微秒滴答數。

## <a name="remarks"></a>備註

GetCurrentTicks ( # A1 是不具決定性的函數。 傳回的結果為 UTC (國際標準時間) 。

這個系統函數將不會使用索引。

## <a name="examples"></a>範例

以下範例會傳回目前的時間，以刻度測量：

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>後續步驟

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
