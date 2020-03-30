---
title: 在 Azure Cosmos DB 查詢語言中INDEX_OF
description: 瞭解 Azure Cosmos DB 中INDEX_OF的 SQL 系統函數。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50e489fdf57398d486b07944782ecbb3fd1d6a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71350997"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF（Azure 宇宙資料庫）
 傳回第一個指定的字串運算式中，第二個字串運算式第一次出現的開始位置，或者如果找不到字串，則為 -1。  
  
## <a name="syntax"></a>語法
  
```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  
  
## <a name="arguments"></a>引數
  
*str_expr1*  
   這是要搜尋的字串運算式。  
  
*str_expr2*  
   是要搜索的字串運算式。  

*numeric_expr*可選數位運算式，用於設置搜索將啟動的位置。 *str_expr1*中的第一個位置為 0。 
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回 "abc" 內各種子字串的索引。  
  
```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 以下為結果集。  
  
```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
