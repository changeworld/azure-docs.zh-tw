---
title: 反向 Azure Cosmos DB 查詢語言
description: 深入瞭解 SQL 系統函數在 Azure Cosmos DB 中的反向。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b25b720b0919038ca024f7c8b11712d78fd44d52
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082839"
---
# <a name="reverse-azure-cosmos-db"></a>反向 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
  下列範例顯示如何 `REVERSE` 在查詢中使用。  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 以下為結果集。  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>備註

這個系統函數將不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
