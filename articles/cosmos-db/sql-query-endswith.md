---
title: Azure Cosmos DB 查詢語言中的 ENDSWITH
description: 瞭解 Azure Cosmos DB 中的 ENDSWITH SQL 系統函數，以傳回布林值，指出第一個字串運算式的結尾是否為第二個
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c5a8b3c44c5ac46b837e4d851d22f85aeaf39c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299443"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH （Azure Cosmos DB）
 傳回布林值，表示第一個字串運算式是否以第二個字串運算式結尾。  
  
## <a name="syntax"></a>語法
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr1*  
   這是字串運算式。  
  
*str_expr2*  
   這是要與*str_expr1*結尾比較的字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回以 "b" 或 "bc" 結尾的 "abc"。  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 以下為結果集。  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>備註

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
