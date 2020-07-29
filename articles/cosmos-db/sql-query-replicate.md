---
title: 以 Azure Cosmos DB 查詢語言進行複寫
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數複寫。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302180"
---
# <a name="replicate-azure-cosmos-db"></a>複寫（Azure Cosmos DB）
 將字串值重複指定的次數。
  
## <a name="syntax"></a>語法
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是字串運算式。
  
*num_expr*  
   為數值運算式。 如果*num_expr*為負數或非有限，則結果為未定義。
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。
  
## <a name="remarks"></a>備註
  結果的最大長度是10000個字元，亦即（長度（*str_expr*） * *num_expr*） <= 10000。

## <a name="examples"></a>範例
  
  下列範例顯示如何 `REPLICATE` 在查詢中使用。
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 以下為結果集。
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>備註

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
