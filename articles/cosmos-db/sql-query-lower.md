---
title: Azure Cosmos DB 查詢語言較低
description: 瞭解 Azure Cosmos DB 中的較低 SQL 系統函數，以便在將大寫字元資料轉換成小寫之後傳回字串運算式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "78302265"
---
# <a name="lower-azure-cosmos-db"></a>較低的 (Azure Cosmos DB) 
 傳回將大寫字元資料轉換成小寫之後的字串運算式。  

較低的系統函數不使用索引。 如果您打算經常進行不區分大小寫的比較，則較低的系統函數可能會耗用大量的 RU。 如果是這種情況，而不是使用較低的系統函數，在每次進行比較時將資料標準化，您可以在插入時標準化大小寫。 然後查詢（例如 SELECT * FROM c，其中較低的 (c.name) = ' bob '）只會變成 SELECT * FROM c WHERE c.name = ' bob '。

## <a name="syntax"></a>語法
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例顯示如何 `LOWER` 在查詢中使用。  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 以下為結果集。  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>備註

這個系統函數將不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
