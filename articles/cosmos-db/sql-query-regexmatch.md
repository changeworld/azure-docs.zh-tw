---
title: Azure Cosmos DB 查詢語言中的 RegexMatch
description: 瞭解 Azure Cosmos DB 中的 RegexMatch SQL 系統函數
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 7f5b2831f45e902b312636e4133557a16ee7ec95
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341632"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

提供正則運算式功能。 正則運算式是精確且彈性的標記法，用來尋找文字的模式。 Azure Cosmos DB 使用 [與 PERL 相容的正則運算式 (PCRE) ](http://www.pcre.org/)。 

## <a name="syntax"></a>語法
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>引數
  
*str_expr1*  
   這是要搜尋的字串運算式。  
  
*str_expr2*  
   這是正則運算式。

*str_expr3*  
   這是要搭配正則運算式使用的選定修飾詞字串。 這個字串值是選擇性的。 如果您想要執行不含修飾詞的 RegexMatch，您可以新增空字串或完全省略。 

您可以 [在 Perl 中瞭解用來建立正則運算式的語法](https://perldoc.perl.org/perlre)。 

Azure Cosmos DB 支援下列四個修飾詞：

| 修飾詞 | 描述 |
| ------ | ----------- |
| `m` | 將要搜尋的字串運算式視為多行。 如果沒有這個選項，"^" 和 "$" 將會比對字串的開頭或結尾，而不是每個個別行。 |
| `s` | 允許 "." 符合任何字元，包括分行符號。 | 
| `i` | 當模式比對時忽略大小寫。 |
| `x` | 忽略所有空白字元。 |

## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。 如果要搜尋的字串運算式、正則運算式或選取的修飾詞無效，則傳回 undefined。
  
## <a name="examples"></a>範例
  
下列簡單的 RegexMatch 範例會使用幾個不同的修飾詞來檢查字串 "abcd" 是否符合正則運算式比對。
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 以下為結果集。  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

使用 RegexMatch 時，您可以使用元字元來執行更複雜的字串搜尋，而不可能使用 StartsWith、EndsWith、Contains 或 StringEquals 系統函數。 以下是一些額外的範例，您可以使用透過 [Azure Cosmos DB 查詢遊樂場](https://www.documentdb.com/sql/demo)提供的營養資料集來執行這些範例。 

> [!NOTE] 
> 如果您需要在正則運算式中使用中繼條件，而不想讓它具有特殊意義，則應該使用來將中繼的轉義 `\` 。

**檢查有描述包含 "salt" 一字的專案：**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**檢查包含0到99之間之描述的專案：**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**檢查具有描述的專案，其中包含以 "S" 或 "s" 開頭的四個字母字：**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>備註

如果正則運算式可細分為 StartsWith、EndsWith、Contains 或 StringEquals 系統函數，則這個系統函數將受益于 [範圍索引](index-policy.md#includeexclude-strategy) 。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
