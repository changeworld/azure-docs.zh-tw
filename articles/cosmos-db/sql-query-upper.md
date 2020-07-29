---
title: Azure Cosmos DB 查詢語言的上限
description: 深入瞭解 SQL 系統函數上限 Azure Cosmos DB。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303965"
---
# <a name="upper-azure-cosmos-db"></a>上限（Azure Cosmos DB）
 傳回將小寫字元資料轉換成大寫之後的字串運算式。  

UPPER 系統函數不會使用索引。 如果您打算進行不區分大小寫的比較，則大寫系統函數可能會耗用大量的 RU。 如果是這種情況，您可以在插入時將大小寫正規化，而不是在每次進行比較時使用上層系統函數來將資料標準化。 然後查詢（例如 SELECT * FROM c，其中 UPPER （c. name） = ' BOB '）只會變成 SELECT * FROM c，其中 c.name = ' BOB '。

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

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
