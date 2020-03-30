---
title: 在 Azure 宇宙 DB 查詢語言中的上部
description: 在 Azure 宇宙 DB 中瞭解 SQL 系統函數 UPPER。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303965"
---
# <a name="upper-azure-cosmos-db"></a>上部（Azure 宇宙資料庫）
 傳回將小寫字元資料轉換成大寫之後的字串運算式。  

UPPER 系統功能不使用索引。 如果您計畫頻繁進行不區分大小寫的比較，則 UPPER 系統功能可能會消耗大量 RU。 如果是這種情況，而不是使用 UPPER 系統函數來對每次比較的資料進行正常化，您可以在插入時正常化套管。 然後，查詢，如選擇 = 從 c WHERE 上（c.name） = "BOB" 只是成為 SELECT = c where c.name = "BOB"。

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
  
  下面的示例演示如何`UPPER`在查詢中使用  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 以下為結果集。  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
