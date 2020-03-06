---
title: Azure Cosmos DB 查詢語言中的 IS_OBJECT
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 IS_OBJECT。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43d8237d859249317d7b581e25ed9a2b34d39d00
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303795"
---
# <a name="is_object-azure-cosmos-db"></a>IS_OBJECT （Azure Cosmos DB）
 傳回布林值，表示指定之運算式的類型為 JSON 物件。  
  
## <a name="syntax"></a>語法
  
```sql
IS_OBJECT(<expr>)  
```  
  
## <a name="arguments"></a>引數
  
*expr*  
   這是任何運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會使用 `IS_OBJECT` 函式，檢查 JSON 布林值的物件、數位、字串、null、物件、陣列以及未定義的類型。  
  
```sql
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 以下為結果集。  
  
```json
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  

## <a name="remarks"></a>備註

這個系統函數將受益于[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [型別檢查函數 Azure Cosmos DB](sql-query-type-checking-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
