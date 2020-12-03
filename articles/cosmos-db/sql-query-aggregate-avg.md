---
title: AVG in Azure Cosmos DB 查詢語言
description: 深入瞭解 Azure Cosmos DB 中的平均 (AVG) SQL 系統函數。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2cfbca798a7b404e4ee12b93396b2a5b08d7d5bb
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552368"
---
# <a name="avg-azure-cosmos-db"></a>平均 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

此彙總函式會傳回運算式中值的平均值。
  
## <a name="syntax"></a>語法
  
```sql
AVG(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
傳回數值運算式。  
  
## <a name="examples"></a>範例
  
下列範例會傳回的平均值 `propertyA` ：
  
```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>備註

此系統函數將受益於[範圍索引](index-policy.md#includeexclude-strategy)。 如果中的任何引數 `AVG` 是字串、布林值或 null，則整個匯總系統函數將會傳回 `undefined` 。 如果有任何引數有 `undefined` 值，就不會影響 `AVG` 計算。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 中的數學函數](sql-query-mathematical-functions.md)
- [Azure Cosmos DB 中的系統函數](sql-query-system-functions.md)
- [Azure Cosmos DB 中的彙總函式](sql-query-aggregate-functions.md)