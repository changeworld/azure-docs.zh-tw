---
title: 四捨五入 Azure Cosmos DB 查詢語言
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 ROUND。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 41b1372cd5165b3548a4e574e7eb037111188bac
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341547"
---
# <a name="round-azure-cosmos-db"></a>四捨五入 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 傳回數值，四捨五入到最接近的整數值。  
  
## <a name="syntax"></a>語法
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="remarks"></a>備註
  
  執行的舍入運算會依照中間點舍入遠離零。 如果輸入是剛好介於兩個整數之間的數值運算式，則結果將會是離零的最接近整數值。 此系統函數將受益於[範圍索引](index-policy.md#includeexclude-strategy)。
  
  |<numeric_expr>|圓形|
  |-|-|
  |-6.5000|-7|
  |-0。5|-1|
  |0.5|1|
  |6.5000|7||
  
## <a name="examples"></a>範例
  
  下列範例會將以下正數和負數數字四捨五入至最接近的整數。  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  以下為結果集。  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
