---
title: Azure Cosmos DB 查詢語言中的 EndsWith
description: 了解 Azure Cosmos DB 中的 ENDSWITH SQL 系統函式如何傳回布林值，指出第一個字串運算式是否以第二個字串運算式結尾
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3d37786c7364b07228d1d8d6540e7b6d8a174eb5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84322681"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)

傳回布林值，表示第一個字串運算式是否以第二個字串運算式結尾。  
  
## <a name="syntax"></a>語法
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>引數
  
*str_expr1*  
   是字串運算式。  
  
*str_expr2*  
   是要與 *str_expr1*的結尾進行比較的字串運算式。

*bool_expr* 忽略大小寫的選擇性值。 設定為 true 時，ENDSWITH 會執行不區分大小寫的搜尋。 若未指定，則此值為 false。
  
## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。  
  
## <a name="examples"></a>範例
  
下列範例會檢查字串 "abc" 是否以 "b" 和 "bC" 結尾。  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 以下為結果集。  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>備註

此系統函式將受益於[範圍索引](index-policy.md#includeexclude-strategy)。

EndsWith 的 RU 耗用量會隨著系統函式中屬性的基數增加而增加。 換句話說，如果您要檢查屬性值是否以特定字串結尾，則查詢的 RU 費用將取決於該屬性的可能值數目。

例如，假設有兩個屬性：城鎮和國家/地區。 城鎮的基數為 5,000，而國家/地區的基數為 200。 以下是兩個查詢範例：

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

第一個查詢使用的 RU 可能會比第二個查詢多，因為城鎮的基數高於國家/地區。

如果某些檔的 EndsWith 屬性大小大於 1 KB，則查詢引擎將需要載入這些檔。 在此情況下，查詢引擎將無法使用索引來完全評估 EndsWith。 如果您有大量的屬性大小大於 1 KB 的檔，EndsWith 的 RU 費用將會很高。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
