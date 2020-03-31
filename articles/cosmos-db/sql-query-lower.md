---
title: 在 Azure 宇宙 DB 查詢語言中降低
description: 瞭解 Azure Cosmos DB 中的 LOWER SQL 系統函數，在將大寫字元資料轉換為小寫資料後返回字串運算式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302265"
---
# <a name="lower-azure-cosmos-db"></a>低（Azure 宇宙資料庫）
 傳回將大寫字元資料轉換成小寫之後的字串運算式。  

LOWER 系統功能不使用索引。 如果您計畫頻繁進行不區分大小寫的比較，LOWER 系統功能可能會消耗大量 RU。 如果是這種情況，而不是使用 LOWER 系統函數來使資料每次都正常化進行比較，您可以在插入時正常化套管。 然後，查詢，如選擇 + 從 c WHERE 低 （c. 名稱） = "bob" 只是成為選擇 = 從 c 位置c.name = "bob"。

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
  
  下面的示例演示如何`LOWER`在查詢中使用。  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 以下為結果集。  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
