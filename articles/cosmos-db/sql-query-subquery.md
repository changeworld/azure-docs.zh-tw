---
title: Azure Cosmos DB 的 SQL 子查詢
description: 瞭解 SQL 子查詢及其常見使用案例和不同類型的子查詢 Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74870559"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Azure Cosmos DB 的 SQL 子查詢範例

子查詢是在另一個查詢中嵌套的查詢。 子查詢也稱為內部查詢或內部 select。 包含子查詢的語句通常稱為外部查詢。

本文描述 SQL 子查詢及其在 Azure Cosmos DB 中的常見使用案例。 本檔中的所有範例查詢都可以針對[Azure Cosmos DB 查詢遊樂場](https://www.documentdb.com/sql/demo)預先載入的營養資料集來執行。

## <a name="types-of-subqueries"></a>子查詢的類型

子查詢有兩種主要類型：

* 相互**關聯**：參考來自外部查詢之值的子查詢。 查詢會針對外部查詢所處理的每個資料列評估一次。
* **非相互關聯**：與外部查詢無關的子查詢。 它可以自行執行，而不需依賴外部查詢。

> [!NOTE]
> Azure Cosmos DB 只支援相互關聯的子查詢。

子查詢可以根據其傳回的資料列和資料行數目進一步分類。 有三種類型：
* **Table**：傳回多個資料列和多個資料行。
* **多重值**：傳回多個資料列和一個資料行。
* 純**量：傳回**單一資料列和單一資料行。

Azure Cosmos DB 中的 SQL 查詢一律會傳回單一資料行（簡單的值或複雜的檔）。 因此，只有多重值和純量查詢適用于 Azure Cosmos DB。 您只能在 FROM 子句中使用多重值子查詢作為關聯式運算式。 您可以在 SELECT 或 WHERE 子句中使用純量子子查詢當做純量運算式，或在 FROM 子句中當做關聯式運算式。

## <a name="multi-value-subqueries"></a>多重值子查詢

多重值子查詢會傳回一組檔，而且一律會在 FROM 子句中使用。 它們用於：

* 優化聯結運算式。 
* 評估昂貴的運算式一次並參考多次。

## <a name="optimize-join-expressions"></a>優化聯結運算式

多重值子查詢可以優化聯結運算式，方法是在每個 select-many 運算式之後推送述詞，而不是在 WHERE 子句中的所有交叉聯結之後。

請考慮下列查詢：

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

在此查詢中，索引會比對具有名稱 "嬰兒 formula" 之標記的任何檔。 這是一個 nutrient 專案，其值介於0到10之間，而服務專案的數量大於1。 此處的聯結運算式會在套用任何篩選之前，針對每個相符的檔，執行標記、nutrients 和 servings 陣列之所有專案的交叉乘積。 

然後，WHERE 子句會將篩選述詞套用到每個 <c，t，n，s> 元組。 比方說，如果相符的檔在三個數組的每一個都有10個專案，它會擴展為 1 x 10 x 10 x 10 （也就是1000）元組。 在這裡使用子查詢可以協助篩選出聯結的陣列專案，然後再與下一個運算式聯結。

此查詢相當於上述其中一項，但是使用的是子查詢：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

假設標記陣列中只有一個專案符合篩選準則，而且 nutrients 和 servings 陣列都有五個專案。 然後，聯結運算式會展開至 1 x 1 x 5 x 5 = 25 個專案，而不是第一個查詢中的1000個專案。

## <a name="evaluate-once-and-reference-many-times"></a>評估一次並參考多次

子查詢可協助您使用昂貴的運算式（例如使用者定義函數（Udf）、複雜字串或算術運算式）來優化查詢。 您可以使用子查詢以及聯結運算式來評估運算式一次，但參考多次。

下列查詢會執行 UDF `GetMaxNutritionValue` 兩次：

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

以下是只執行 UDF 一次的對等查詢：

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> 請記住聯結運算式的交叉乘積行為。 如果 UDF 運算式可以評估為未定義，您應該確定聯結運算式一律會藉由從子查詢傳回物件，而不是直接從值來產生單一資料列。
>

以下是傳回物件的類似範例，而不是值：

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

此方法不限於 Udf。 它適用于任何可能耗費資源的運算式。 例如，您可以使用數學函數來採取相同的方法 `avg` ：

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>使用外部參考資料模仿聯結

您可能經常需要參考極少變更的靜態資料，例如測量單位或國家/地區代碼。 最好不要複製每份檔的這類資料。 避免這項重複會節省儲存空間，並藉由讓檔案大小保持較小來改善寫入效能。 您可以使用子查詢，以參考資料的集合來模擬內部聯結的語義。

例如，請考慮這組參考資料：

| **單位** | **名稱**            | **乘數** | **基礎單位** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogram            | 1.00 e-09       | 語法          |
| µg       | Microgram           | 1.00 e-06       | 語法          |
| mg       | Milligram           | 1.00 e-03       | 語法          |
| g        | 語法                | 1.00 e + 00       | 語法          |
| 公斤       | Kilogram            | 1.00 e + 03       | 語法          |
| Mg       | Megagram            | 1.00 e + 06       | 語法          |
| Gg       | Gigagram            | 1.00 e + 09       | 語法          |
| 新澤西       | Nanojoule           | 1.00 e-09       | Joule         |
| µJ       | Microjoule          | 1.00 e-06       | Joule         |
| mJ       | Millijoule          | 1.00 e-03       | Joule         |
| J        | Joule               | 1.00 e + 00       | Joule         |
| kJ       | Kilojoule           | 1.00 e + 03       | Joule         |
| MJ       | Megajoule           | 1.00 e + 06       | Joule         |
| GJ       | Gigajoule           | 1.00 e + 09       | Joule         |
| cal      | Calorie             | 1.00 e + 00       | calorie       |
| 卡路里     | Calorie             | 1.00 e + 03       | calorie       |
| IU       | 國際單位 |                |               |


下列查詢會模擬與此資料的聯結，讓您將單位的名稱新增至輸出：

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

純量子查詢運算式是評估為單一值的子查詢。 純量子查詢運算式的值是子查詢之投射（SELECT 子句）的值。  在純量運算式有效的許多地方，您都可以使用純量子查詢運算式。 例如，您可以在 SELECT 和 WHERE 子句中的任何運算式中使用純量子查詢。

不過，使用純量子查詢不一定有助於優化。 例如，將純量子子查詢當做引數傳遞至系統或使用者定義的函式，不會對資源單位（RU）耗用量或延遲提供任何好處。

純量子查詢可以進一步分類為：
* 簡單運算式純量查詢
* 匯總純量查詢

## <a name="simple-expression-scalar-subqueries"></a>簡單運算式純量查詢

簡單運算式純量查詢是具有 SELECT 子句的相互關聯子查詢，其中不包含任何匯總運算式。 這些子查詢不提供優化的優點，因為編譯器會將它們轉換成一個較大的簡單運算式。 內部和外部查詢之間沒有相互關聯的內容。

以下提供一些範例：

**範例 1**

```sql
SELECT 1 AS a, 2 AS b
```

您可以使用簡單運算式純量子查詢來重寫此查詢，以執行下列動作：

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

這兩個查詢都會產生下列輸出：

```json
[
  { "a": 1, "b": 2 }
]
```

**範例 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

您可以使用簡單運算式純量子查詢來重寫此查詢，以執行下列動作：

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

**範例 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

您可以使用簡單運算式純量子查詢來重寫此查詢，以執行下列動作：

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

### <a name="aggregate-scalar-subqueries"></a>匯總純量查詢

匯總純量子查詢是在其投射或篩選準則中有彙總函式的子查詢，會評估為單一值。

**範例 1：**

以下是在其投影中具有單一彙總函式運算式的子查詢：

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

**範例 2**

以下是具有多個彙總函式運算式的子查詢：

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

**範例 3**

以下是在投影和篩選中具有匯總子查詢的查詢：

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

撰寫此查詢的最佳方式是聯結子查詢，並參考 SELECT 和 WHERE 子句中的子查詢別名。 此查詢較有效率，因為您只需要在聯結語句內執行子查詢，而不是在投影和篩選中。

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXISTS 運算式

Azure Cosmos DB 支援 EXISTS 運算式。 這是內建于 Azure Cosmos DB SQL API 中的匯總純量子查詢。 EXISTS 是使用子查詢運算式的布林運算式，如果子查詢傳回任何資料列，則傳回 true。 否則會傳回 false。

因為 Azure Cosmos DB SQL API 不會區分布林運算式和任何其他純量運算式，所以您可以在 SELECT 和 WHERE 子句中使用 EXISTS。 這不同于 T-sql，其中布林運算式（例如 EXISTS、BETWEEN 和 IN）會限制為篩選準則。

如果 EXISTS 子查詢傳回未定義的單一值，EXISTS 會評估為 false。 例如，請考慮下列評估為 false 的查詢：
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


如果省略前述子查詢中的 VALUE 關鍵字，則查詢會評估為 true：
```sql
SELECT EXISTS (SELECT undefined) 
```

子查詢會將所選清單中的值清單括在物件中。 如果選取的清單沒有任何值，則子查詢會傳回單一值 ' {} '。 已定義此值，因此 EXISTS 會評估為 true。

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>範例：重寫 ARRAY_CONTAINS 並加入為 EXISTS

ARRAY_CONTAINS 的常見使用案例是根據陣列中的專案是否存在來篩選檔。 在此情況下，我們要檢查標記陣列是否包含名為 "橙色" 的專案。

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

此外，ARRAY_CONTAINS 只能檢查某個值是否等於陣列中的任何元素。 如果您需要更複雜的陣列屬性篩選，請使用聯結。

請考慮下列根據陣列中的單位和屬性進行篩選的查詢 `nutritionValue` ： 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

針對集合中的每個檔，會使用其陣列元素來執行交叉乘積。 這種聯結作業可以篩選陣列內的屬性。 不過，此查詢的 RU 耗用量會很重要。 比方說，如果1000檔的每個陣列中有100個專案，它會擴展到 1000 x 100 （也就是100000）元組。

使用 EXISTS 有助於避免這種昂貴的跨產品：

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

在此情況下，您會篩選 EXISTS 子查詢內的陣列元素。 如果陣列元素符合篩選準則，則您會將它投影，而 EXISTS 會評估為 true。

您也可以將別名存在，並在投影中參考它：

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

## <a name="array-expression"></a>陣列運算式

您可以使用陣列運算式，將查詢的結果投影為數組。 您只能在查詢的 SELECT 子句中使用此運算式。

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

就像其他子查詢一樣，可以使用陣列運算式來進行篩選。

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

陣列運算式也可以出現在子查詢的 FROM 子句之後。

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
