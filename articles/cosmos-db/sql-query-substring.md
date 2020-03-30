---
title: Azure 宇宙 DB 查詢語言中的 SUBSTRING
description: 瞭解 Azure 宇宙 DB 中的 SQL 系統函數 SUBSTRING。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303693"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING（Azure 宇宙資料庫）
 傳回字串運算式的部分，從指定字元以零為起始的位置開始，直到指定的長度，或直到字串的結尾。  
  
## <a name="syntax"></a>語法
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是字串運算式。
  
*num_expr1*  
   是表示起始字元的數位運算式。 值 0 是*str_expr*的第一個字元。
  
*num_expr2*  
   是表示要返回*的最大str_expr*字元數的數位運算式。 值 0 或更少會導致空字串。

## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回從 1 開始，且長度為 1 個字元的子字串 "abc"。  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 以下為結果集。  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>備註

如果起始位置為`0`，則此系統功能將受益于[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
