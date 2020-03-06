---
title: 反向 Azure Cosmos DB 查詢語言
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數反向。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a22e1c8a5f4350bd2f966ee48f96368c648a4a1e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302163"
---
# <a name="reverse-azure-cosmos-db"></a>反轉（Azure Cosmos DB）
 傳回字串值的反轉順序。  
  
## <a name="syntax"></a>語法
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   這是字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例顯示如何在查詢中使用 `REVERSE`。  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 以下為結果集。  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>備註

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
