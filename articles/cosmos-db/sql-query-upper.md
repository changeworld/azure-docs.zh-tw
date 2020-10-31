---
title: Azure Cosmos DB 查詢語言的上限
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fbec4033d093d83925d1682eb7882587e0dc6ca5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93093634"
---
# <a name="upper-azure-cosmos-db"></a>高 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 傳回將小寫字元資料轉換成大寫之後的字串運算式。  

UPPER 系統函數不使用索引。 如果您打算經常進行不區分大小寫的比較，則上層系統函數可能會耗用大量的 RU。 如果是這種情況，而不是使用大寫系統函式，每次進行比較時都將資料正規化，您可以在插入時將大小寫標準化。 然後查詢（例如 SELECT * FROM c，其中 UPPER (c.name) = ' BOB '）只會變成 SELECT * FROM c WHERE c.name = ' BOB '。

## <a name="syntax"></a>語法
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例顯示如何 `UPPER` 在查詢中使用  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 以下為結果集。  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>備註

這個系統函數將不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
