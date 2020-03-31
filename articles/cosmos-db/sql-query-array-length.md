---
title: Azure Cosmos DB 查詢語言中的ARRAY_LENGTH
description: 瞭解 Azure Cosmos DB 中的陣列長度 SQL 系統功能如何返回指定陣列運算式的元素數
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3596ce4bc702d5e54225d8c90db2f9563feab670
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303982"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH（Azure 宇宙資料庫）
 傳回指定陣列運算式的元素數目。  
  
## <a name="syntax"></a>語法
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*arr_expr*  
   是陣列運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下面的示例如何使用 獲取陣列的長度`ARRAY_LENGTH`。  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 以下為結果集。  
  
```json
[{"len": 3}]  
```  
  
## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [陣列函數 Azure 宇宙 DB](sql-query-array-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
