---
title: Azure Cosmos DB 查詢語言中的 RTRIM
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 RTRIM。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b740d14315f6d9ba2f1788c56d6b1fcd8945c83e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302078"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM （Azure Cosmos DB）
 傳回移除結尾空白之後的字串運算式。  
  
## <a name="syntax"></a>語法
  
```sql
RTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   為任何有效的字運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例顯示如何 `RTRIM` 在查詢內使用。  
  
```sql
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 以下為結果集。  
  
```json
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  

## <a name="remarks"></a>備註

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
