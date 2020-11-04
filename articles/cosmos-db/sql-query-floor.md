---
title: Azure Cosmos DB 查詢語言的樓層
description: 瞭解 Azure Cosmos DB 中的 FLOOR SQL 系統函數，以傳回小於或等於指定之數值運算式的最大整數
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4696b90531b63a01fd4bd9260b24b9af5c6bbd93
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335597"
---
# <a name="floor-azure-cosmos-db"></a>樓層 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 傳回小於或等於指定數值運算式的最大整數。  
  
## <a name="syntax"></a>語法
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會使用函數顯示正數值、負值和零值 `FLOOR` 。  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 以下為結果集。  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```

## <a name="remarks"></a>備註

此系統函數將受益於[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
