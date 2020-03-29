---
title: 在 Azure 宇宙 DB 查詢語言中的正確
description: 在 Azure 宇宙 DB 中瞭解 SQL 系統函數 RIGHT。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247616d2ac4f2a5799a5896d679f6e6d5917d5a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302146"
---
# <a name="right-azure-cosmos-db"></a>右（Azure 宇宙資料庫）
 傳回具有指定字元數目的字串右側部分。  
  
## <a name="syntax"></a>語法
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是從中提取字元的字串運算式。  
  
*num_expr*  
   是指定字元數的數位運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回 "abc"右側部分的各種長度值。  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 以下為結果集。  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
