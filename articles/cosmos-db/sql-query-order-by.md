---
title: Azure Cosmos DB 中的 ORDER BY 子句
description: 瞭解 Azure Cosmos DB 的 SQL ORDER BY 子句。 使用 SQL 做為 Azure Cosmos DB 的 JSON 查詢語言。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: tisande
ms.openlocfilehash: 887dc13eb5e351688718d2a221e69499557b23e5
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338287"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 ORDER BY 子句
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

選擇性 `ORDER BY` 子句會指定查詢所傳回之結果的排序次序。

## <a name="syntax"></a>語法
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>引數
  
- `<sort_specification>`  
  
   指定要排列查詢結果集的屬性或運算式。 排序資料行可以指定為名稱或屬性別名。  
  
   您可以指定多個屬性。 屬性名稱必須是唯一的。 子句中的 sort 屬性順序會 `ORDER BY` 定義已排序結果集的組織。 也就是說，結果集是依第一個屬性排序，而該排序清單會依次要屬性進行排序，以此類推。  
  
   子句中參考的屬性名稱 `ORDER BY` 必須對應到選取清單中的屬性，或是對應至子句中所指定之集合中所定義的屬性（ `FROM` 不含任何多義性）。  
  
- `<sort_expression>`  
  
   指定要排序查詢結果集的一或多個屬性或運算式。  
  
- `<scalar_expression>`  
  
   請參閱[純量運算式](sql-query-scalar-expressions.md)一節以取得詳細資料。  
  
- `ASC | DESC`  
  
   指定指定之資料行的值應該以遞增或遞減順序排序。 `ASC` 從最低值到最高值進行排序。 `DESC` 從最高值到最低值進行排序。 `ASC` 這是預設的排序次序。 Null 值會當做最低的可能值來處理。  
  
## <a name="remarks"></a>備註  
  
   `ORDER BY`子句要求索引編制原則包含要排序之欄位的索引。 Azure Cosmos DB 的查詢執行時間支援針對屬性名稱進行排序，而不支援針對計算的屬性進行排序。 Azure Cosmos DB 支援多個 `ORDER BY` 屬性。 為了執行具有多個 ORDER BY 屬性的查詢，您應該在要排序的欄位上定義 [複合索引](index-policy.md#composite-indexes) 。

> [!Note]
> 如果某些檔的排序屬性可能未定義，而您想要在 ORDER BY 查詢中取得這些屬性，您必須在索引中明確包含此路徑。 預設的索引編制原則不允許抓取未定義 sort 屬性的檔。 [在含有某些遺漏欄位的檔上檢查範例查詢](#documents-with-missing-fields)。

## <a name="examples"></a>範例

例如，以下是以常駐城市名稱的遞增順序來抓取系列的查詢：

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

結果為：

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

下列查詢會依 `id` 專案的建立日期順序來抓取系列。 專案 `creationDate` 是代表 *epoch 時間* 的數位，或從1970年1月1日起經過的時間（以秒為單位）。

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

結果為：

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

此外，您可以依多個屬性排序。 依多個屬性排序的查詢需要 [複合索引](index-policy.md#composite-indexes)。 請考慮以下查詢：

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

此查詢會依 `id` 城市名稱的遞增順序來抓取系列。 如果多個專案具有相同的城市名稱，則查詢會依 `creationDate` 遞減順序排序。

## <a name="documents-with-missing-fields"></a>遺漏欄位的檔

`ORDER BY`針對具有預設索引編制原則之容器執行的查詢，將不會傳回 [排序] 屬性未定義的檔。 如果您想要包含 [排序] 屬性未定義的檔，您應該在編制索引原則中明確包含這個屬性。

例如，以下是具有索引編制原則的容器，但不會明確包含任何路徑 `"/*"` ：

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

如果您執行包含 `lastName` 在子句中的查詢 `Order By` ，結果只會包含已 `lastName` 定義屬性的檔。 我們尚未定義明確包含的路徑， `lastName` 因此沒有的任何檔 `lastName` 都不會出現在查詢結果中。

以下是依 `lastName` 兩份檔排序的查詢，其中一個檔沒有 `lastName` 已定義的：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

結果只包含已定義的檔 `lastName` ：

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

如果我們更新容器的編制索引原則，以明確地包含的路徑 `lastName` ，我們會在查詢結果中包含具有未定義之排序屬性的檔。 您必須明確地定義路徑以使此純量值 (，而不是在它) 之外。 您應該 `?` 在索引編制原則中使用路徑定義中的字元，以確保您明確地為屬性編制索引 `lastName` ，而不是任何其他的嵌套路徑。 如果您的 `Order By` 查詢使用 [複合索引](index-policy.md#composite-indexes)，則結果一律會在查詢結果中包含具有未定義之排序屬性的檔。

以下是範例編制索引原則，可讓您讓具有未定義的檔 `lastName` 出現在查詢結果中：

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

如果您再次執行相同的查詢，則遺漏的檔 `lastName` 會先出現在查詢結果中：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

結果為：

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

如果您將排序次序修改為 `DESC` ，遺漏 `lastName` 的檔會出現在查詢結果中的最後一個：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

結果為：

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

> [!Note]
> 只有 .NET SDK 版本3.4.0 或更新版本支援搭配混合類型的 ORDER BY。 因此，如果您想要依未定義和已定義值的組合進行排序，則應該使用這個版本 (或更新版本的) 。

您無法控制不同類型出現在結果中的順序。 在上述範例中，我們示範了如何在字串值之前排序未定義的值。 比方說，如果您想要更充分掌控未定義值的排序次序，您可以為字串值 "aaaaaaaaa" 或 "zzzzzzzz" 指派任何未定義的屬性，以確保它們是第一個或最後一個。

## <a name="next-steps"></a>後續步驟

- [快速入門](sql-query-getting-started.md)
- [Azure Cosmos DB 中的索引編製原則](index-policy.md)
- [OFFSET LIMIT 子句](sql-query-offset-limit.md)
