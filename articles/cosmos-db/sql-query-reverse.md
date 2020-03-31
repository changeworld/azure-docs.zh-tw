---
title: 在 Azure 宇宙 DB 查詢語言中反轉
description: 在 Azure 宇宙 DB 中瞭解 SQL 系統函數 REVERSE。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a22e1c8a5f4350bd2f966ee48f96368c648a4a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302163"
---
# <a name="reverse-azure-cosmos-db"></a>反向（Azure 宇宙資料庫）
 傳回字串值的反轉順序。  
  
## <a name="syntax"></a>語法
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下面的示例演示如何`REVERSE`在查詢中使用。  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 以下為結果集。  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
