---
title: Azure Cosmos DB 查詢語言中的子字串
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數子字串。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 17888ccd8fc51ed96f7fc92a0f9275d2c8cb56f8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340820"
---
# <a name="substring-azure-cosmos-db"></a>子字串 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 傳回字串運算式的部分，從指定字元以零為起始的位置開始，直到指定的長度，或直到字串的結尾。  
  
## <a name="syntax"></a>語法
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是字串運算式。
  
*num_expr1*  
   這是表示起始字元的數值運算式。 0值是 *str_expr* 的第一個字元。
  
*num_expr2*  
   這是數值運算式，表示要傳回之 *str_expr* 的最大字元數。 0或較小的值會產生空字串。

## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回從 1 開始，且長度為 1 個字元的子字串 "abc"。  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 以下為結果集。  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>備註

如果開始位置為，這個系統函數將受益于 [範圍索引](index-policy.md#includeexclude-strategy) `0` 。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
