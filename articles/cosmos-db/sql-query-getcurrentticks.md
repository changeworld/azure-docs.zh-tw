---
title: Azure Cosmos DB 查詢語言中的 GetCurrentTicks
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 GetCurrentTicks。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 6b3cd5ab5849c33172e4a629c79fb792b82f1255
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227072"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB) 

傳回目前的日期和時間（以刻度為單位）。
  
## <a name="syntax"></a>語法
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>傳回類型

傳回正整數值。

## <a name="remarks"></a>備註

這個系統函數不會使用索引。

## <a name="examples"></a>範例

以下範例會傳回目前的時間（以刻度為單位）：

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
