---
title: 在 Azure 宇宙 DB 中執行 BY 子句
description: 瞭解 Azure 宇宙 DB 的 SQL ORDER BY 子句。 使用 SQL 作為 Azure 宇宙 DB JSON 查詢語言。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.openlocfilehash: b88184be39a41ec42f8fb304a7511073f645f1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188740"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>在 Azure 宇宙 DB 中執行 BY 子句

可選的 ORDER BY 子句指定查詢返回的結果的排序次序。

## <a name="syntax"></a>語法
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>引數
  
- `<sort_specification>`  
  
   指定要排列查詢結果集的屬性或運算式。 排序列可以指定為名稱或屬性別名。  
  
   可以指定多個屬性。 屬性名稱必須是唯一的。 ORDER BY 子句中的排序屬性的順序定義排序結果集的組織。 也就是說，結果集是依第一個屬性排序，而該排序清單會依次要屬性進行排序，以此類推。  
  
   ORDER BY 子句中引用的屬性名稱必須對應于挑選清單中的屬性或 FROM 子句中指定的集合中定義的屬性，沒有任何模糊性。  
  
- `<sort_expression>`  
  
   指定一個或多個屬性或運算式，用於對查詢結果集進行排序。  
  
- `<scalar_expression>`  
  
   請參閱[純量運算式](sql-query-scalar-expressions.md)一節以取得詳細資料。  
  
- `ASC | DESC`  
  
   指定指定之資料行的值應該以遞增或遞減順序排序。 ASC 從最低值到最高值進行排序。 DESC 從最高值到最低值進行排序。 ASC 是預設排序次序。 Null 值會當做最低的可能值來處理。  
  
## <a name="remarks"></a>備註  
  
   子`ORDER BY`句要求索引策略包含要排序的欄位的索引。 Azure Cosmos DB 查詢運行時支援對屬性名稱進行排序，而不是對計算的屬性進行排序。 Azure 宇宙資料庫支援多個`ORDER BY`屬性。 為了運行具有多個 ORDER BY 屬性的查詢，應在要排序的欄位上定義[複合索引](index-policy.md#composite-indexes)。

> [!Note]
> 如果某些文檔的排序屬性可能未定義，並且希望在 ORDER BY 查詢中檢索它們，則必須在索引中顯式包含此路徑。 預設索引策略不允許檢索未定義排序屬性的文檔。 [查看對文檔中缺少一些欄位的依例查詢](#documents-with-missing-fields)。

## <a name="examples"></a>範例

例如，下面是按居民城市名稱的昇冪檢索家庭的查詢：

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

結果如下：

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

以下查詢按其專案創建`id`日期的順序檢索族。 專案`creationDate`是一個數位，表示*紀元時間*，或自1970年1月1日以來經過的時間，以秒為單位。

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

結果如下：

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

此外，您可以按多個屬性訂購。 按多個屬性排序的查詢需要[複合索引](index-policy.md#composite-indexes)。 請考慮以下查詢：

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

此查詢按城市名稱的`id`昇冪檢索族。 如果多個項具有相同的城市名稱，則查詢將`creationDate`按降冪排序。

## <a name="documents-with-missing-fields"></a>缺少欄位的文檔

具有預設索引`ORDER BY`策略的容器運行的查詢不會返回未定義排序屬性的文檔。 如果要包括未定義排序屬性的文檔，則應在索引策略中顯式包含此屬性。

例如，下面是一個包含索引策略的容器，該策略未顯式包含 除`"/*"`：

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

如果運行包含子句中的`lastName``Order By`查詢，則結果將僅包括定義`lastName`屬性的文檔。 我們尚未為`lastName`任何沒有 a 的文檔定義顯式包含的路徑`lastName`，因此查詢結果中不會顯示任何沒有 a 的文檔。

下面是一個查詢，它按`lastName`兩個文檔進行排序，其中一個文檔沒有`lastName`定義：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

結果僅包括具有定義的`lastName`文檔：

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

如果我們更新容器的索引策略以顯式包含 的`lastName`路徑，我們將在查詢結果中包括具有未定義排序屬性的文檔。 您必須顯式定義路徑才能導致此標量值（而不是超出該值）。 應在索引策略中使用`?`路徑定義中的字元，以確保顯式索引屬性，並且沒有超出該屬性`lastName`的其他嵌套路徑。

下面是一個示例索引策略，允許您在查詢結果中顯示未定義`lastName`的文檔：

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

如果再次運行同一查詢，則缺少`lastName`的文檔首先出現在查詢結果中：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

結果如下：

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

如果將排序次序修改為`DESC`，則缺少`lastName`的文檔將在最後一個查詢結果中顯示：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

結果如下：

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [Azure Cosmos DB 中的索引編製原則](index-policy.md)
- [OFFSET 限制子句](sql-query-offset-limit.md)
