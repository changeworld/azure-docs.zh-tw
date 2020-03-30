---
title: azure 宇宙 DB 查詢語言中的IS_ARRAY
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數IS_ARRAY。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f5867850db6eb3d6552bc129cca3708ef7747072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303880"
---
# <a name="is_array-azure-cosmos-db"></a>IS_ARRAY（Azure 宇宙資料庫）
 傳回布林值，表示指定之運算式的類型為陣列。  
  
## <a name="syntax"></a>語法
  
```sql
IS_ARRAY(<expr>)  
```  
  
## <a name="arguments"></a>引數
  
*expr*  
   是任何運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。  
  
## <a name="examples"></a>範例
  
  下面的示例使用`IS_ARRAY`函數檢查 JSON 布林、數位、字串、空、物件、陣列和未定義類型的物件。  
  
```sql
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 以下為結果集。  
  
```json
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  

## <a name="remarks"></a>備註

該系統功能將受益于[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [類型檢查功能 Azure 宇宙資料庫](sql-query-type-checking-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
