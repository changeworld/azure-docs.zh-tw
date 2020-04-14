---
title: Azure Cosmos DB 中的編製索引
description: 瞭解索引在 Azure Cosmos DB 中的工作方式,支援不同類型的索引,如範圍、空間索引、複合索引。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: thweiss
ms.openlocfilehash: 684799ee12715c789910accf80aa5b4afec763d4
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273234"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>在 Azure Cosmos DB 中編製索引 - 概觀

Azure Cosmos DB 是一個與架構無關的資料庫,允許您在應用程式上反覆運算,而無需處理架構或索引管理。 默認情況下,Azure Cosmos DB 會自動索引[容器](databases-containers-items.md#azure-cosmos-containers)中所有項的每個屬性,而無需定義任何架構或配置輔助索引。

本文的目標是要說明 Azure Cosmos DB 如何為資料編製索引，以及如何使用索引來改善查詢效能。 建議在探討如何自定義[索引策略](index-policy.md)之前,先流覽本節。

## <a name="from-items-to-trees"></a>從項目到樹木

每次將專案存儲在容器中時,其內容都會投影為 JSON 文檔,然後轉換為樹表示形式。 這意味著該專案的每個屬性都表示為樹中的節點。 偽根節點作為項的所有第一級屬性的父級創建。 葉節點包含項攜帶的實際標量值。

例如,請考慮以下專案:

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

它將由以下樹表示:

![表示為樹的上一項](./media/index-overview/item-as-tree.png)

請注意如何在樹中編碼陣列:陣列中的每個條目都會獲得一個中間節點,該節點在陣列(0、1 等)中標有該條目的索引。

## <a name="from-trees-to-property-paths"></a>從樹到屬性路徑

Azure Cosmos DB 將項轉換為樹的原因是因為它允許這些樹中的路徑引用屬性。 要獲取屬性的路徑,我們可以遍歷樹從根節點到該屬性,並串聯每個遍歷節點的標籤。

下面是上面描述的範例項目中每個屬性的路徑:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

編寫項時,Azure Cosmos DB 可有效地索引每個屬性的路徑及其相應的值。

## <a name="index-kinds"></a>索引型別

Azure 宇宙 DB 當前支援三種索引。

### <a name="range-index"></a>範圍索引

**範圍**索引基於有序的樹狀結構。 範圍索引型態用於:

- 相等查詢:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   陣列元素上的相等比對
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- 範圍查詢:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (`>`適用於`<``>=`、 `<=` `!=`、 、 )

- 檢查是否存在屬性:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- 字串前置字串(CONTAINS 關鍵字不會利用範圍索引):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY`查詢:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`查詢:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

範圍索引可用於標量值(字串或數位)。

### <a name="spatial-index"></a>空間索引

**空間**索引支援對地理空間物件(如 - 點、線、面和多面)進行有效的查詢。 這些查詢使用ST_DISTANCE、ST_WITHIN、ST_INTERSECTS關鍵字。 以下是一些使用空間索引類型的範例:

- 地理空間距離查詢:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- 查詢中的地理空間:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- 地理空間相交查詢:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

空間索引可用於格式正確的[GeoJSON](geospatial.md)物件。 當前支援點、線弦、多邊形和多面形。

### <a name="composite-indexes"></a>複合索引

當您對多個字段執行操作時,**複合**索引會提高效率。 複合索引型態用於:

- `ORDER BY`對多個屬性的查詢:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- 具有篩選器和`ORDER BY`的查詢。 如果將篩選器屬性添加到`ORDER BY`子句,則這些查詢可以利用複合索引。

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- 在兩個或多個屬性上具有篩選器的查詢,其中至少有一個屬性是相等篩選器

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

只要一個篩選器謂詞使用索引類型之一,查詢引擎將首先在掃描其餘內容之前對其進行計算。 例如,如果您有 SQL 查詢,例如`SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* 上述查詢將首先篩選名字 = 「Andrew」使用索引的條目。 然後,它通過後續管道傳遞所有名字 ="Andrew"條目,以評估 CONTAINS 篩選器謂詞。

* 通過使用不使用索引(例如 CONTAINS)的函數時,可以通過添加使用索引的其他篩選器謂詞來加快查詢並避免完整的容器掃描。 篩選器子句的順序並不重要。 查詢引擎將找出哪些謂詞更具選擇性,並相應地運行查詢。


## <a name="querying-with-indexes"></a>使用索引進行查詢

索引數據時提取的路徑便於在處理查詢時查找索引。 通過將查詢`WHERE`子句與索引路徑列表進行匹配,可以非常快速地標識與查詢謂詞匹配的項。

例如,請考慮以下查詢: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`。 查詢謂詞(篩選任何位置以"法國"作為其國家/地區的項)將與下面以紅色突出顯示的路徑匹配:

![符合樹中的特定路徑](./media/index-overview/matching-path.png)

> [!NOTE]
> 按`ORDER BY`單個屬性排序的子句*始終*需要範圍索引,如果引用的路徑沒有範圍索引,則該子句將失敗。 同樣,按`ORDER BY`多個屬性排序的查詢*始終*需要複合索引。

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

- [編製索引原則](index-policy.md)
- [如何管理索引原則](how-to-manage-indexing-policy.md)
