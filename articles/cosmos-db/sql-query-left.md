---
title: 在 Azure 宇宙 DB 查詢語言中左
description: 瞭解 Azure 宇宙 DB 中的 SQL 系統函數 LEFT。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0eac35a91e4d5158335d6797d49a09f8f6f391e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303744"
---
# <a name="left-azure-cosmos-db"></a>左（Azure 宇宙資料庫）
 傳回具有指定字元數目的字串左側部分。  
  
## <a name="syntax"></a>語法
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是從中提取字元的字串運算式。  
  
*num_expr*  
   是指定字元數的數位運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回 "abc"左側部分的各種長度值。  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 以下為結果集。  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>備註

該系統功能將受益于[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
