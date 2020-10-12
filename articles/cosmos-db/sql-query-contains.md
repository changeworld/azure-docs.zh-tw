---
title: Azure Cosmos DB 查詢語言中的包含函式
description: 了解 Azure Cosmos DB 中的 CONTAINS SQL 系統函數如何傳回布林值，指出第一個字串運算式是否包含第二個字串運算式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4877272fc2db521977a4111317118380399d27c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84322698"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)

傳回布林值，表示第一個字串運算式是否包含第二個字串運算式。  
  
## <a name="syntax"></a>語法
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>引數
  
*str_expr1*  
   這是要搜尋的字串運算式。  
  
*str_expr2*  
   這是要尋找的字串運算式。  

*bool_expr* 忽略大小寫的選擇性值 設定為 true 時，CONTAINS 會執行不區分大小寫的搜尋。 若未指定，則此值為 false。
  
## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會檢查 "abc" 是否包含 "ab" 及 "abc" 是否包含 "A"。  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
```  
  
 以下為結果集。  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>備註

此系統函數將受益於[範圍索引](index-policy.md#includeexclude-strategy)。

「包含」的 RU 耗用量會隨著系統函數中屬性的基數增加而增加。 換句話說，如果您要檢查屬性值是否包含特定字串，則查詢的 RU 費用將取決於該屬性的可能值數目。

例如，假設有兩個屬性：城鎮和國家/地區。 城鎮的基數為 5,000，而國家/地區的基數為 200。 以下是兩個查詢範例：

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

第一個查詢使用的 RU 可能會比第二個查詢多，因為城鎮的基數高於國家/地區。

如果某些檔的屬性大小在 Contains 中大於 1 KB，則查詢引擎將需要載入這些檔。 在此情況下，查詢引擎將無法以索引完整評估 Contains。 如果您有大量檔的屬性大小超過 1 KB，則 [包含] 的 RU 費用會很高。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
