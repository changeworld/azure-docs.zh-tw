---
title: Azure Cosmos DB 查詢語言中的 CONCAT
description: 瞭解 Azure Cosmos DB 中的 CONCAT SQL 系統函數如何傳回串連兩個或更多字串值的結果字串
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302605"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT （Azure Cosmos DB）
 傳回字串，該字串是串連兩個或多個字串值的結果。  
  
## <a name="syntax"></a>語法
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   這是要串連至其他值的字串運算式。 `CONCAT`函數需要至少兩個*str_expr*引數。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回指定值的串連字串。  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 以下為結果集。  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>備註

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
