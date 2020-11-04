---
title: Azure Cosmos DB 的 SQL 子查詢
description: 深入瞭解 SQL 子查詢，以及它們在 Azure Cosmos DB 中的常見使用案例和不同類型的子查詢
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: f5f209229d17a2587258d21ee90e7560e629d082
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340850"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Azure Cosmos DB 的 SQL 子查詢範例
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

子查詢是在另一個查詢中嵌套的查詢。 子查詢也稱為內部查詢或內部 select。 包含子查詢的語句通常稱為外部查詢。

本文描述 SQL 子查詢及其在 Azure Cosmos DB 中的常見使用案例。 本檔中的所有範例查詢都可以針對預先載入 [Azure Cosmos DB 查詢遊樂場](https://www.documentdb.com/sql/demo)上的營養資料集執行。

## <a name="types-of-subqueries"></a>子查詢的類型

子查詢有兩種主要類型：

* 相互 **關聯** ：參考外部查詢值的子查詢。 針對外部查詢處理的每個資料列，都會評估子查詢一次。
* **非相互關聯** ：與外部查詢無關的子查詢。 您可以自行執行它，而不需要依賴外部查詢。

> [!NOTE]
> Azure Cosmos DB 僅支援相互關聯的子查詢。

您可以根據傳回的資料列和資料行數目，進一步分類子查詢。 有三種類型：
* **Table** ：傳回多個資料列和多個資料行。
* **多重值** ：傳回多個資料列和單一資料行。
* 純 **量：傳回** 單一資料列和單一資料行。

Azure Cosmos DB 中的 SQL 查詢一律會傳回單一資料行 (簡單值或複雜檔) 。 因此，Azure Cosmos DB 中只適用多重值與純量子查詢。 您只能在 FROM 子句中將多重值子查詢用作關聯式運算式。 您可以在 SELECT 或 WHERE 子句中使用純量子查詢做為純量運算式，或在 FROM 子句中做為關聯式運算式使用。

## <a name="multi-value-subqueries"></a>多重值子查詢

多重值子查詢會傳回一組檔，而且一律會在 FROM 子句內使用。 它們是用於：

* 優化聯結運算式。 
* 評估昂貴的運算式一次，並多次參考。

## <a name="optimize-join-expressions"></a>優化聯結運算式

多重值子查詢可以將述詞推送到每個 select-many 運算式之後，而不是在 WHERE 子句中的所有交叉聯結之後，藉以優化聯結運算式。

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

針對此查詢，索引會比對具有名稱 "嬰兒 formula" 之標記的任何檔。 它是一個 nutrient 專案，其值介於0和10之間，而服務專案的數量大於1。 此處的聯結運算式將在套用任何篩選之前，針對每個相符的檔執行標記、養分和 servings 陣列的所有專案的交叉乘積。 

然後 WHERE 子句會將篩選述詞套用到每個 <c、t、n、s> 元組上。 比方說，如果相符的檔在三個數組中的每一個都有10個專案，則會展開為 1 x 10 x 10 x 10 (也就是 1000) 的元組。 在此使用子查詢有助於篩選出聯結的陣列專案，然後再與下一個運算式聯結。

此查詢相當於上述其中一項，但是使用的是子查詢：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

假設標記陣列中只有一個專案符合篩選準則，且養分和 servings 陣列都有五個專案。 聯結運算式會接著展開至 1 x 1 x 5 x 5 = 25 個專案，而不是第一個查詢中的1000個專案。

## <a name="evaluate-once-and-reference-many-times"></a>評估一次並參考多次

子查詢可協助優化具有昂貴運算式的查詢，例如使用者定義函數 (Udf) 、複雜字串或算術運算式。 您可以使用子查詢以及聯結運算式來評估運算式一次，但多次參考。

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
> 請記住聯結運算式的交叉乘積行為。 如果 UDF 運算式可評估為未定義，您應該確定聯結運算式一律會產生單一資料列，方法是從子查詢傳回物件，而不是直接傳回值。
>

以下是傳回物件而非值的類似範例：

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

此方法不限於 Udf。 它適用于任何可能昂貴的運算式。 例如，您可以採用與數學函數相同的方法 `avg` ：

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>使用外部參考資料模仿聯結

您可能經常需要參考很少變更的靜態資料，例如度量單位或國家/地區代碼。 最好不要複製每個檔的這類資料。 避免這種重複的情況會節省儲存體，並藉由讓檔案大小保持較小，來改善寫入效能。 您可以使用子查詢，利用參考資料的集合來模擬內部聯結的語義。

例如，請考慮這組參考資料：

| **單位** | **名稱**            | **乘數** | **基礎單位** |
| -------- | ------------------- | -------------- | ------------- |
| 議員       | Nanogram            | 1.00 e-09       | 克          |
| µg       | 微克           | 1.00 e-06       | 克          |
| mg       | 毫克           | 1.00 e-03       | 克          |
| g        | 克                | 1.00 e + 00       | 克          |
| 公斤       | 公斤            | 1.00 e + 03       | 克          |
| 鎂       | Megagram            | 1.00 e + 06       | 克          |
| Gg       | Gigagram            | 1.00 e + 09       | 克          |
| 新澤西州       | Nanojoule           | 1.00 e-09       | 焦耳         |
| µJ       | Microjoule          | 1.00 e-06       | 焦耳         |
| Mj       | Millijoule          | 1.00 e-03       | 焦耳         |
| J        | 焦耳               | 1.00 e + 00       | 焦耳         |
| Kj       | Kilojoule           | 1.00 e + 03       | 焦耳         |
| Mj       | Megajoule           | 1.00 e + 06       | 焦耳         |
| GJ       | Gigajoule           | 1.00 e + 09       | 焦耳         |
| 卡爾      | 熱量             | 1.00 e + 00       | 熱量       |
| 大卡     | 熱量             | 1.00 e + 03       | 熱量       |
| IU       | 國際單位 |                |               |


下列查詢會模擬與此資料的聯結，以便您將單位的名稱加入至輸出：

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

純量查詢運算式是評估為單一值的子查詢。 純量查詢運算式的值是子查詢的投射 (SELECT 子句) 的值。  您可以在多個純量運算式有效的地方使用純量子查詢運算式。 例如，您可以在 SELECT 和 WHERE 子句的任何運算式中使用純量子查詢。

不過，使用純量子查詢並不一定有助於優化。 例如，將純量子查詢當作引數傳遞至系統或使用者定義函式，在資源單位 (RU) 耗用量或延遲中沒有任何好處。

您可以進一步將純量查詢分類為：
* 簡單運算式純量查詢
* 匯總純量查詢

## <a name="simple-expression-scalar-subqueries"></a>簡單運算式純量查詢

簡單運算式純量子查詢是一種相互關聯的子查詢，其中具有不包含任何匯總運算式的 SELECT 子句。 這些子查詢不提供優化優點，因為編譯器會將它們轉換成一個較大的簡單運算式。 內部和外部查詢之間沒有相互關聯的內容。

以下提供一些範例：

**範例 1**

```sql
SELECT 1 AS a, 2 AS b
```

您可以使用簡單的運算式純量子查詢，將此查詢重寫為：

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

您可以使用簡單的運算式純量子查詢，將此查詢重寫為：

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

您可以使用簡單的運算式純量子查詢，將此查詢重寫為：

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

匯總純量子查詢是在其投影或篩選準則中，具有匯總函數的子查詢會評估為單一值。

**範例 1：**

以下是在投射中具有單一彙總函式運算式的子查詢：

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

以下是投射和篩選器中具有匯總子查詢的查詢：

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

撰寫這個查詢的最佳方式是聯結子查詢，並參考 SELECT 和 WHERE 子句中的子查詢別名。 這項查詢較有效率，因為您只需要在 join 語句內執行子查詢，而不是在投射和篩選準則中執行。

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXISTS 運算式

Azure Cosmos DB 支援存在的運算式。 這是內建于 Azure Cosmos DB SQL API 中的匯總純量子查詢。 EXISTS 是採用子查詢運算式的布林運算式，如果子查詢傳回任何資料列，則會傳回 true。 否則會傳回 false。

因為 Azure Cosmos DB SQL API 不會區分布林運算式和任何其他純量運算式，所以您可以在 SELECT 和 WHERE 子句中使用 EXISTS。 這與 T-sql 不同，其中的布林運算式 (例如，EXISTS、BETWEEN 和 IN) 會限制為篩選準則。

如果 EXISTS 子查詢傳回未定義的單一值，EXISTS 將會評估為 false。 例如，請考慮下列評估為 false 的查詢：
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


如果省略上述子查詢中的 VALUE 關鍵字，查詢將會評估為 true：
```sql
SELECT EXISTS (SELECT undefined) 
```

子查詢會將所選取清單中的值清單包含在物件中。 如果選取的清單沒有任何值，子查詢會傳回單一值 ' {} '。 此值已定義，因此 EXISTS 會評估為 true。

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>範例：重寫 ARRAY_CONTAINS 並加入 EXISTS

ARRAY_CONTAINS 的常見使用案例是依陣列中的專案是否存在來篩選檔。 在此情況下，我們要檢查標記陣列是否包含名為 "橙色" 的專案。

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

您可以重新撰寫相同的查詢來使用存在：

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

針對集合中的每個檔，會以其陣列元素來執行交叉乘積。 這種聯結作業可以篩選陣列中的屬性。 不過，此查詢的 RU 耗用量將會很重要。 比方說，如果1000檔的每個陣列中都有100個專案，則會擴充至 1000 x 100 (也就是 100000) 元組。

使用 EXISTS 有助於避免這個昂貴的跨產品：

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

在此情況下，您會在 EXISTS 子查詢內篩選陣列元素。 如果陣列元素符合篩選準則，則您可以將它投射在一起，然後評估為 true。

您也可以存在別名，並在投影中參考它：

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

您可以使用陣列運算式，將查詢的結果投影為數組。 您只能在查詢的 SELECT 子句內使用此運算式。

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

如同其他子查詢，可以使用陣列運算式進行篩選。

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
