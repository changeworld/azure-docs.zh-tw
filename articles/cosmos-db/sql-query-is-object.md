---
title: Azure Cosmos DB 查詢語言中的IS_OBJECT
description: 瞭解 Azure Cosmos DB 中IS_OBJECT的 SQL 系統函數。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43d8237d859249317d7b581e25ed9a2b34d39d00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303795"
---
# <a name="is_object-azure-cosmos-db"></a>IS_OBJECT（Azure 宇宙資料庫）
 傳回布林值，表示指定之運算式的類型為 JSON 物件。  
  
## <a name="syntax"></a>語法
  
```sql
IS_OBJECT(<expr>)  
```  
  
## <a name="arguments"></a>引數
  
*expr*  
   是任何運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。  
  
## <a name="examples"></a>範例
  
  下面的示例使用`IS_OBJECT`函數檢查 JSON 布林、數位、字串、空、物件、陣列和未定義類型的物件。  
  
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

該系統功能將受益于[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [類型檢查功能 Azure 宇宙資料庫](sql-query-type-checking-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
