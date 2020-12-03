---
title: Azure Cosmos DB 查詢語言的最小值
description: 深入瞭解 Azure Cosmos DB 中最小的 (MIN) SQL 系統函數。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 679814822cca5a72bd261d3c8944863715e31f70
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552364"
---
# <a name="min-azure-cosmos-db"></a>最小 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

此彙總函式會傳回運算式中值的最小值。
  
## <a name="syntax"></a>語法
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*scalar_expr*  
   這是純量運算式。 
  
## <a name="return-types"></a>傳回類型
  
傳回純量運算式。  
  
## <a name="examples"></a>範例
  
下列範例會傳回的最小值 `propertyA` ：
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>備註

此系統函數將受益於[範圍索引](index-policy.md#includeexclude-strategy)。 中的引數 `MIN` 可以是數位、字串、布林值或 null。 將忽略任何未定義的值。

比較不同類型的資料時，會使用下列優先權順序 (以遞增順序) ：

- null
- boolean
- 數目
- 字串

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 中的數學函數](sql-query-mathematical-functions.md)
- [Azure Cosmos DB 中的系統函數](sql-query-system-functions.md)
- [Azure Cosmos DB 中的彙總函式](sql-query-aggregate-functions.md)