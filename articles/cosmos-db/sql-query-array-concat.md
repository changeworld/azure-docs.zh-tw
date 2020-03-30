---
title: azure Cosmos DB 查詢語言中的ARRAY_CONCAT
description: 瞭解 Azure Cosmos DB 中的 Array Concat SQL 系統功能如何返回連接兩個或多個陣列值的結果的陣列
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295873"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT（Azure 宇宙資料庫）
 傳回串連兩個或多個陣列值之結果的陣列。  
  
## <a name="syntax"></a>語法
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>引數
  
*arr_expr*  
   是要串聯到其他值的陣列運算式。 該`ARRAY_CONCAT`函數至少需要兩*個arr_expr*參數。  
  
## <a name="return-types"></a>傳回類型
  
  傳回陣列運算式。  
  
## <a name="examples"></a>範例
  
  下列範例顯示如何串連兩個陣列。  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 以下為結果集。  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [陣列函數 Azure 宇宙 DB](sql-query-array-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
