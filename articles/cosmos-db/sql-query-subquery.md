---
title: Azure 宇宙資料庫的 SQL 子查詢
description: 瞭解 Azure Cosmos DB 中的 SQL 子查詢及其常見用例和不同類型的子查詢
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870559"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Azure 宇宙資料庫的 SQL 子查詢示例

子查詢是嵌套在另一個查詢中的查詢。 子查詢也稱為內部查詢或內部選擇。 包含子查詢的語句通常稱為外部查詢。

本文介紹了 SQL 子查詢及其在 Azure Cosmos DB 中的常見用例。 此文檔中的所有依例查詢都可以針對在[Azure Cosmos DB 查詢運動場](https://www.documentdb.com/sql/demo)上預載入的營養資料集運行。

## <a name="types-of-subqueries"></a>子查詢的類型

子查詢有兩種主要類型：

* **相關**： 引用外部查詢中的值的子查詢。 對於外部查詢處理的每一行，將計算子查詢一次。
* **非相關**：獨立于外部查詢的子查詢。 它可以自行運行，而無需依賴外部查詢。

> [!NOTE]
> Azure 宇宙資料庫僅支援相關的子查詢。

子查詢可以根據子查詢返回的行和列數進一步分類。 有三種類型：
* **表**：返回多行和多列。
* **多值**：返回多行和單個列。
* **Scalar**：返回一行和一列。

Azure Cosmos DB 中的 SQL 查詢始終返回單個列（簡單值或複雜文檔）。 因此，Azure Cosmos DB 中僅適用多值和標量子查詢。 只能在 FROM 子句中使用多值子查詢作為關聯運算式。 您可以在 SELECT 或 WHERE 子句中使用標量子查詢作為標量運算式，或在 FROM 子句中用作關聯運算式。

## <a name="multi-value-subqueries"></a>多值子查詢

多值子查詢返回一組文檔，並且始終在 FROM 子句中使用。 它們用於：

* 優化 JOIN 運算式。 
* 評估一次昂貴的運算式並多次引用。

## <a name="optimize-join-expressions"></a>優化 JOIN 運算式

多值子查詢可以通過在每個選擇多運算式之後推送謂詞來優化 JOIN 運算式，而不是在 WHERE 子句中的所有交叉聯接之後。

請考慮以下查詢：

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

對於此查詢，索引將匹配具有標記的任何文檔的名稱為"嬰兒配方奶粉"。 它是值介於 0 和 10 之間的營養品，以及金額大於 1 的份品。 此處的 JOIN 運算式將在應用任何篩選器之前，對每個匹配文檔執行標記、營養和服務陣列的所有專案的交叉產品。 

然後，WHERE 子句將在每個<c、t、n>元組上應用篩選器謂詞。 例如，如果匹配的文檔在三個數組中每個陣列中有 10 個專案，它將擴展到 1 x 10 x 10 x 10（即 1，000 個）tup。 在此處使用子查詢有助於在加入下一個運算式之前篩選出聯接的陣列項。

此查詢等效于前一個查詢，但使用子查詢：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

假設標記陣列中只有一個專案與篩選器匹配，並且營養和服務陣列有五個專案。 然後，JOIN 運算式將擴展到 1 x 1 x 5 x 5 = 25 項，而第一個查詢中的 1，000 項將展開。

## <a name="evaluate-once-and-reference-many-times"></a>評估一次，多次參考

子查詢可以説明使用昂貴的運算式（如使用者定義的函數 （UF）、複雜字串或算術運算式）優化查詢。 可以使用子查詢和 JOIN 運算式一次計算運算式，但多次引用它。

以下查詢運行 UDF`GetMaxNutritionValue`兩次：

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

下面是只運行 UDF 一次的等效查詢：

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> 請記住 JOIN 運算式的跨產品行為。 如果 UDF 運算式可以計算為未定義，則應確保 JOIN 運算式始終通過從子查詢返回物件而不是直接傳回值來生成單個行。
>

下面是返回物件而不是值的類似示例：

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

該方法不僅限於 UdF。 它適用于任何可能昂貴的運算式。 例如，您可以採用與數學函數`avg`相同的方法：

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>使用外部參考資料類比聯接

您可能需要引用很少更改的靜態資料，例如度量單位或國家/地區代碼。 最好不要為每個文檔複製此類資料。 避免這種重複將節省存儲，並通過減小文檔大小來提高寫入性能。 可以使用子查詢使用參考資料收集來類比內部聯接語義。

例如，請考慮這組參考資料：

| **單位** | **名稱**            | **乘數** | **基本單位** |
| -------- | ------------------- | -------------- | ------------- |
| 議員       | 納米克            | 1.00E-09       | 克          |
| μg       | 微克           | 1.00E-06       | 克          |
| mg       | 毫克           | 1.00E-03       | 克          |
| g        | 克                | 1.00E+00       | 克          |
| 公斤       | 公斤            | 1.00E+03       | 克          |
| 鎂       | 兆克            | 1.00E+06       | 克          |
| Gg       | 千兆格拉姆            | 1.00E+09       | 克          |
| 新澤西州       | 納米焦耳           | 1.00E-09       | 焦耳         |
| μJ       | 微焦耳          | 1.00E-06       | 焦耳         |
| Mj       | 米利朱萊          | 1.00E-03       | 焦耳         |
| J        | 焦耳               | 1.00E+00       | 焦耳         |
| Kj       | 基洛焦勒           | 1.00E+03       | 焦耳         |
| Mj       | 梅加朱勒           | 1.00E+06       | 焦耳         |
| GJ       | 吉加焦勒           | 1.00E+09       | 焦耳         |
| cal      | 熱量             | 1.00E+00       | 熱量       |
| 大卡     | 熱量             | 1.00E+03       | 熱量       |
| IU       | 國際單位 |                |               |


以下查詢類比使用此資料聯接，以便將設備的名稱添加到輸出中：

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>純量子查詢

標量子查詢運算式是計算為單個值的子查詢。 標量子查詢運算式的值是子查詢的投影 （SELECT 子句）的值。  您可以在標量運算式有效的許多地方使用標量子查詢運算式。 例如，您可以在 SELECT 和 WHERE 子句中的任何運算式中使用標量子查詢。

但是，使用標量子查詢並不總是有助於優化。 例如，將標量子查詢作為參數傳遞給系統或使用者定義的函數，對資源單元 （RU） 消耗或延遲沒有任何好處。

Scalar 子查詢可以進一步分類為：
* 簡單運算式標量子查詢
* 聚合標量子查詢

## <a name="simple-expression-scalar-subqueries"></a>簡單運算式標量子查詢

簡單運算式標量子查詢是具有不包含任何聚合運算式的 SELECT 子查詢的相關子查詢。 這些子查詢不提供優化優勢，因為編譯器將它們轉換為一個更大的簡單運算式。 內部和外部查詢之間沒有相關的上下文。

以下提供一些範例：

**示例 1**

```sql
SELECT 1 AS a, 2 AS b
```

您可以使用簡單運算式標量子查詢重寫此查詢，以便：

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

兩個查詢都會產生此輸出：

```json
[
  { "a": 1, "b": 2 }
]
```

**示例 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

您可以使用簡單運算式標量子查詢重寫此查詢，以便：

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

查詢輸出：

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**示例 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

您可以使用簡單運算式標量子查詢重寫此查詢，以便：

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

查詢輸出：

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>聚合標量子查詢

聚合標量子查詢是在其投影或篩選器中具有彙總函式的子查詢，該函數計算為單個值。

**範例 1：**

下面是一個子查詢，其投影中包含單個彙總函式運算式：

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

查詢輸出：

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**示例 2**

下面是具有多個彙總函式運算式的子查詢：

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

查詢輸出：

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**示例 3**

下面是投影和篩選器中包含聚合子查詢的查詢：

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

查詢輸出：

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

編寫此查詢的更最佳方法是在子查詢上聯接，並在 SELECT 和 WHERE 子句中引用子查詢別名。 此查詢效率更高，因為您只需在 join 語句中執行子查詢，而不需要在投影和篩選器中執行子查詢。

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>已存在運算式

Azure 宇宙 DB 支援 EXISTS 運算式。 這是內置於 Azure Cosmos DB SQL API 中的聚合標量子查詢。 EXISTS 是一個布林運算式，它採用子查詢運算式，並在子查詢返回任何行時返回 true。 否則會傳回 false。

由於 Azure Cosmos DB SQL API 不會區分布林運算式和任何其他標量運算式，因此可以在 SELECT 和 WHERE 子句中使用 EXISTS。 這與 T-SQL 不同，T-SQL 表示布林運算式（例如，EXISTS、IN 和 IN）僅限於篩選器。

如果 EXISTS 子查詢返回未定義的單個值，則 EXISTS 將計算為 false。 例如，請考慮以下計算為 false 的查詢：
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


如果省略了前面的子查詢中的 VALUE 關鍵字，則查詢將計算為 true：
```sql
SELECT EXISTS (SELECT undefined) 
```

子查詢將包含物件中所選清單中的值清單。 如果所選清單沒有值，則子查詢將返回單個值 ''。{} 此值已定義，因此 EXISTS 計算為 true。

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>示例：重寫ARRAY_CONTAINS和 JOIN 作為 EXISTS

ARRAY_CONTAINS的常見用例是按陣列中是否存在項來篩選文檔。 在這種情況下，我們將檢查標記陣列是否包含名為"橙色"的專案。

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

您可以重寫相同的查詢以使用 EXISTS：

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

此外，ARRAY_CONTAINS只能檢查值是否等於陣列中的任何元素。 如果需要陣列屬性上更複雜的篩選器，請使用 JOIN。

請考慮根據陣列中的單位和`nutritionValue`屬性篩選的以下查詢： 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

對於集合中的每個文檔，使用其陣列元素執行交叉產品。 通過此 JOIN 操作，可以篩選陣列中的屬性。 但是，此查詢的 RU 消耗量將很大。 例如，如果 1，000 個文檔在每個陣列中包含 100 個專案，它將擴展到 1，000 x 100（即 100，000）tup。

使用 EXISTS 有助於避免這種昂貴的交叉產品：

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

在這種情況下，您可以篩選 EXISTS 子查詢中的陣列元素。 如果陣列元素與篩選器匹配，則投影它，並"存在"計算為 true。

您還可以別名 EXISTS 並在投影中引用它：

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

查詢輸出：

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>ARRAY 運算式

可以使用 ARRAY 運算式將查詢的結果投影為數組。 只能在查詢的 SELECT 子句中使用此運算式。

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

查詢輸出：

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

與其他子查詢一樣，可以使用 ARRAY 運算式進行篩選器。

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

查詢輸出：

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

陣列運算式也可以位於子查詢中的 FROM 子句之後。

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

查詢輸出：

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [模型文件資料](modeling-data.md)
