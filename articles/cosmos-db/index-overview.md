---
title: Azure Cosmos DB 中的編製索引
description: 了解索引在 Azure Cosmos DB 中的運作方式、支援的不同索引類型 (例如，範圍、空間、複合式索引)。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: tisande
ms.openlocfilehash: 35d81d0076c4b29a8b1399331aa0254178720931
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277823"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>在 Azure Cosmos DB 中編製索引 - 概觀

Azure Cosmos DB 是一個無從驗證結構描述的資料庫，可讓您逐一查看應用程式，而不需處理結構描述或索引管理。 根據預設，Azure Cosmos DB 會自動為[容器](account-databases-containers-items.md#azure-cosmos-containers)中的所有項目編製索引，而不需要界定任何結構描述或設定次要索引。

本文的目標是要說明 Azure Cosmos DB 如何為資料編製索引，以及如何使用索引來改善查詢效能。 建議您先完成本節，再探索如何自訂[索引編製原則](index-policy.md)。

## <a name="from-items-to-trees"></a>從項目到樹狀結構

每次在容器時儲存項目時，其內容都會投射為 JSON 文件，然後轉換成樹狀結構標記法。 這表示該項目的每個屬性都會呈現為樹狀結構中的某個節點。 會建立虛擬根節點做為該項目中所有第一層屬性的父系。 分葉節點包含項目所攜帶的實際純量值。

以下列項目為例：

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

可由以下列樹狀結構呈現：

:::image type="content" source="./media/index-overview/item-as-tree.png" alt-text="以樹狀結構表示的上一個項目" border="false":::

請注意陣列在樹狀結構中的編碼方式：陣列中的每個項目都會取得中繼節點，並標示陣列內該項目的索引 (0、1 等)。

## <a name="from-trees-to-property-paths"></a>從樹狀結構到屬性路徑

Azure Cosmos DB 將項目轉換成樹狀結構的原因，在於允許這類樹狀結構內的路徑參考屬性。 若要取得屬性的路徑，可以將樹狀結構從根節點移至該屬性，並串連每個周遊節點的標籤。

以下是上述範例項目中每個屬性的路徑：

- /locations/0/country： "德國"
- /locations/0/city： "柏林"
- /locations/1/country： "法國"
- /locations/1/city： "巴黎"
- /headquarters/country： "比利時"
- /headquarters/employees：250
- /exports/0/city： "莫斯科"
- /exports/1/city： "雅典"

寫入項目時，Azure Cosmos DB 會有效地為每個屬性的路徑和其對應的值編制索引。

## <a name="index-kinds"></a>索引種類

Azure Cosmos DB 目前支援三種索引。

### <a name="range-index"></a>範圍索引

**範圍**索引是以排序好的樹狀結構型結構為基礎。 範圍索引種類用於：

- 相等查詢：

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   陣列元素的相等比對
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- 範圍查詢：

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (適用於 `>`、`<`、`>=`、`<=`、`!=`)

- 檢查屬性是否存在：

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- 字串系統函式：

   ```sql
   SELECT * FROM c WHERE CONTAINS(c.property, "value")
   ```

   ```sql
   SELECT * FROM c WHERE STRINGEQUALS(c.property, "value")
   ```

- `ORDER BY` 查詢：

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` 查詢：

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

範圍索引可以用於純量值 (字串或數字)。

### <a name="spatial-index"></a>空間索引

**空間**索引可有效率地查詢地理空間物件，例如點、線、多邊形和多多邊形。 這些查詢會使用 ST_DISTANCE、ST_WITHIN ST_INTERSECTS 關鍵字。 以下是一些使用空間索引的範例：

- 地理空間距離查詢：

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- 查詢中的地理空間：

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] })
   ```

- 地理空間交集查詢：

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

空間索引可用於格式正確的 [GeoJSON](geospatial.md) 物件。 目前支援點、LineString、多邊形和多多邊形。

### <a name="composite-indexes"></a>複合式索引

**複合式**索引可提高在多個欄位上執行作業時的效率。 複合式索引用於：

- 多種屬性的 `ORDER BY` 查詢：

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- 具有篩選條件和 `ORDER BY` 的查詢。 如果將篩選條件屬性加入至 `ORDER BY` 子句，這類查詢就可使用複合式索引。

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- 在兩個或多個屬性上具有篩選條件的查詢，其中至少有一個屬性是相等篩選條件

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

只要有一個篩選述詞使用其中一種索引，查詢引擎就會先對其進行評估，再掃描其餘部分。 舉例來說，如果您有 SQL 查詢 (例如 `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`)

* 上述查詢會先使用索引篩選出 firstName = "Andrew" 的項目。 接著會透過後續管道傳遞所有 firstName = "Andrew" 項目，以評估 CONTAINS 篩選器述詞。

* 藉由新增其他會使用索引的篩選器述詞，您可以加速查詢，並避免在使用未使用索引的函式時進行完整的容器掃描 (例如 CONTAINS)。 篩選子句的順序並不重要。 查詢引擎會找出哪些述詞篩選條件更嚴格，並據以執行查詢。

## <a name="querying-with-indexes"></a>使用索引進行查詢

編制資料索引時所解壓縮的路徑，可讓您在處理查詢時輕鬆地查閱索引。 將查詢的 `WHERE` 子句與已編制索引的路徑清單進行比對，就有可能非常快速地識別出符合查詢述詞的項目。

例如可試想下列查詢：`SELECT location FROM location IN company.locations WHERE location.country = 'France'`。 查詢述詞 (篩選有任何位置的國家/地區為「法國」的項目) 符合下列以紅色反白顯示的路徑：

:::image type="content" source="./media/index-overview/matching-path.png" alt-text="以樹狀結構表示的上一個項目" border="false":::

> [!NOTE]
> 依單一屬性排序的 `ORDER BY` 子句一律需要使用範圍索引，而且如果所參考的路徑不具範圍索引，就會失敗。 同理，按照多個屬性排序的 `ORDER BY` 查詢一律需要使用複合式索引。

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

- [編製索引原則](index-policy.md)
- [如何管理編製索引原則](how-to-manage-indexing-policy.md)
