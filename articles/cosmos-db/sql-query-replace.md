---
title: 在 Azure 宇宙 DB 查詢語言中替換
description: 瞭解 Azure 宇宙 DB 中的 SQL 系統函數 REPLACE。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302197"
---
# <a name="replace-azure-cosmos-db"></a>替換（Azure 宇宙資料庫）
 將指定字串值的所有相符項目取代成另一個字串值。  
  
## <a name="syntax"></a>語法
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr1*  
   這是要搜尋的字串運算式。  
  
*str_expr2*  
   是要找到的字串運算式。  
  
*str_expr3*  
   是字串運算式，用於替換*str_expr1*中的*str_expr2*事件。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下面的示例演示如何`REPLACE`在查詢中使用。  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 以下為結果集。  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
