---
title: Azure Cosmos DB 查詢語言中的 IS_STRING
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 IS_STRING。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a717f343b0f46522a3ce2bb56c32e3f15998d777
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "78303761"
---
# <a name="is_string-azure-cosmos-db"></a>IS_STRING (Azure Cosmos DB) 
 傳回布林值，表示指定之運算式的類型為字串。  
  
## <a name="syntax"></a>語法
  
```sql
IS_STRING(<expr>)  
```  
  
## <a name="arguments"></a>引數
  
*expr*  
   這是任何運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會使用函式，檢查 JSON 布林值的物件、數位、字串、null、物件、陣列以及未定義的類型 `IS_STRING` 。  
  
```sql
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 以下為結果集。  
  
```json
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a name="remarks"></a>備註

此系統函數將受益於[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [類型檢查函式 Azure Cosmos DB](sql-query-type-checking-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
