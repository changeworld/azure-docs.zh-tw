---
title: Azure Cosmos DB 索引編製原則
description: 瞭解如何在 Azure Cosmos DB 中設定及變更自動編制索引的預設索引編制原則和更高的效能。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: tisande
ms.openlocfilehash: b525f3299420f81670c0aea9872ac5fdef00be97
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277800"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引編製原則

在 Azure Cosmos DB 中，每個容器都有索引編製原則，以指示容器項目應該如何編制索引。 新建立的容器所套用之每個項目的每個屬性之預設索引編製原則，會對任何字串或數字強制執行範圍索引。 這可讓您獲得高查詢效能，而不需要事先考慮索引編製和索引管理。

在某些情況下，您可以覆寫此自動行為，使其更符合您的需求。 您可以藉由設定 *索引編制模式*來自訂容器的編制索引原則，以及包含或排除 *屬性路徑*。

> [!NOTE]
> 本文章中所述的更新編制索引原則的方法只適用于 Azure Cosmos DB 的 SQL (Core) API。 瞭解如何在[Azure Cosmos DB 的 MONGODB API](mongodb-indexing.md)中編制索引

## <a name="indexing-mode"></a>編制索引模式

Azure Cosmos DB 支援兩種索引編制模式：

- **一致**：當您建立、更新或刪除專案時，會以同步方式更新索引。 這表示您的讀取查詢一致性將會是 [為帳戶設定的一致性](consistency-levels.md)。
- **無**：容器上的索引已停用。 當容器作為純索引鍵-值存放區，而不需要次要索引時，通常會使用這種方式。 它也可以用來改善大量作業的效能。 完成大量作業之後，索引模式可以設定為一致，然後使用 [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk) 進行監視，直到完成為止。

> [!NOTE]
> Azure Cosmos DB 也支援延遲編制索引模式。 當引擎並未執行任何其他工作時，延遲索引會以較低的優先順序層級對更新執行索引。 這可能會導致**不一致或不完整的**查詢結果。 如果您打算查詢 Cosmos 容器，則不應該選取延遲索引。 在2020年6月，我們引進了變更，不再允許將新的容器設定為延遲編制索引模式。 如果您的 Azure Cosmos DB 帳戶已經至少包含一個具有延遲索引編制的容器，此帳戶會自動豁免變更。 您也可以藉由聯絡 [Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) (來要求豁免，除非您在 [無伺服器](serverless.md) 模式下使用 Azure Cosmos 帳戶，但不支援延遲索引) 。

依預設，編制索引原則是設定為 `automatic` 。 將 `automatic` 索引編制原則中的屬性設定為，即可達成此目的 `true` 。 將此屬性設定為可 `true` 讓 Azure CosmosDB 在檔寫入時自動編制索引。

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a> 包含和排除屬性路徑

自訂編制索引原則可以指定明確包含或排除在索引編制中的屬性路徑。 藉由優化已編制索引的路徑數目，您可以大幅降低寫入作業的延遲和 RU 費用。 這些路徑會依照「 [索引編制總覽」一節中所述的方法](index-overview.md#from-trees-to-property-paths) 定義，其中包含下列新增專案：

- 開頭為純量值的路徑 (字串或數位，) 結尾為 `/?`
- 陣列中的專案會透過 `/[]` 標記法（ (而非等等 `/0` ）來定址 `/1` 。 ) 
- `/*`萬用字元可以用來比對節點下的任何元素

再次採用相同的範例：

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- 的 `headquarters` `employees` 路徑是 `/headquarters/employees/?`

- `locations`' `country` 路徑為`/locations/[]/country/?`

- 下方任何的路徑 `headquarters` 都是 `/headquarters/*`

例如，我們可以包含 `/headquarters/employees/?` 路徑。 此路徑可確保我們為 employee 屬性編制索引，但不會在這個屬性中編制額外的嵌套 JSON 索引。

## <a name="includeexclude-strategy"></a>包含/排除策略

任何編制索引原則都必須將根路徑包含 `/*` 為包含或排除的路徑。

- 包含可選擇性排除不需要編制索引之路徑的根路徑。 這是建議的方法，因為它可讓 Azure Cosmos DB 主動為可能新增至模型的新屬性編制索引。
- 排除根路徑以選擇性地包含需要編制索引的路徑。

- 針對具有一般字元的路徑，其中包含：英數位元和 _ (底線) ，您不需要以雙引號括住路徑字串 (例如 "/path/？") 。 針對其他特殊字元的路徑，您必須以雙引號括住路徑字串 (例如 "/ \" path-abc \" /？") 。 如果您想要在路徑中有特殊字元，您可以將每個路徑都換成安全。 在功能上，如果您要將每個路徑與具有特殊字元的路徑進行 escape，則不會產生任何差異。

- `_etag`預設會排除系統屬性，除非將 etag 新增至包含的路徑以進行索引編制。

- 如果索引模式設定為 [ **一致**]，系統 `id` 就會自動為系統屬性 `_ts` 編制索引。

包含和排除路徑時，您可能會遇到下列屬性：

- `kind` 可以是 `range` 或 `hash` 。 雜湊索引支援僅限於相等篩選。 範圍索引功能提供雜湊索引的所有功能，以及有效率的排序、範圍篩選和系統函數。 我們一律建議使用範圍索引。

- `precision` 是在包含路徑的索引層級定義的數位。 的值 `-1` 表示最大有效位數。 建議您一律將此值設定為 `-1` 。

- `dataType` 可以是 `String` 或 `Number` 。 這表示將會編制索引的 JSON 屬性類型。

如果未指定，這些屬性將會有下列預設值：

| **屬性名稱**     | **預設值** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` 和 `Number` |

請參閱 [這一節](how-to-manage-indexing-policy.md#indexing-policy-examples) ，以取得包含和排除路徑的編制索引原則範例。

## <a name="includeexclude-precedence"></a>包含/排除優先順序

如果包含的路徑和排除的路徑發生衝突，則會優先採用更精確的路徑。

以下是範例：

**包含的路徑**： `/food/ingredients/nutrition/*`

**排除的路徑**： `/food/ingredients/*`

在此情況下，包含的路徑會優先于排除的路徑，因為它較為精確。 根據這些路徑，路徑中或嵌套的任何資料 `food/ingredients` 都會從索引中排除。 例外狀況是包含的路徑中的資料： `/food/ingredients/nutrition/*` ，它會進行索引。

以下是 Azure Cosmos DB 中包含和排除的路徑優先順序的一些規則：

- 更深入的路徑比較窄的路徑更精確。 例如： `/a/b/?` 比更精確 `/a/?` 。

- `/?`比更精確 `/*` 。 例如 `/a/?` ，比起 `/a/*` `/a/?` 優先使用更精確。

- 此路徑 `/*` 必須是包含的路徑或排除的路徑。

## <a name="spatial-indexes"></a>空間索引

當您在編制索引原則中定義空間路徑時，您應該定義 ```type``` 應套用至該路徑的索引。 空間索引的可能類型包括：

* Point

* Polygon

* MultiPolygon

* LineString

Azure Cosmos DB 預設不會建立任何空間索引。 如果您想要使用空間 SQL 內建函數，您應該在必要的屬性上建立空間索引。 請參閱 [這一節](sql-query-geospatial-index.md) ，以取得新增空間索引的索引編制原則範例。

## <a name="composite-indexes"></a>複合式索引

具有 `ORDER BY` 兩個或多個屬性之子句的查詢需要複合索引。 您也可以定義複合索引，以改善許多相等和範圍查詢的效能。 依預設，不會定義任何複合索引，因此您應該視需要 [加入複合索引](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) 。

不同于包含或排除的路徑，您無法使用萬用字元來建立路徑 `/*` 。 每個複合路徑在 `/?` 不需要指定的路徑結尾都有隱含的。 複合路徑會導致純量值，而這是複合索引中唯一包含的值。

定義複合索引時，您可以指定：

- 兩個或多個屬性路徑。 定義屬性路徑的順序很重要。

- 順序 (遞增或遞減) 。

> [!NOTE]
> 當您加入複合索引時，查詢會利用現有的範圍索引，直到新的複合索引加入完成為止。 因此，當您加入複合索引時，可能不會立即觀察到效能改進。 您可以 [使用其中一個 sdk](how-to-manage-indexing-policy.md)來追蹤索引轉換的進度。

### <a name="order-by-queries-on-multiple-properties"></a>多個屬性的 ORDER BY 查詢：

使用具有 `ORDER BY` 兩個或更多屬性的子句之查詢的複合索引時，會使用下列考慮：

- 如果複合索引路徑與子句中的屬性順序不相符 `ORDER BY` ，則複合索引無法支援查詢。

- 複合索引路徑 (遞增或遞減) 的順序也應該與 `order` 子句中的相符 `ORDER BY` 。

- 複合索引也支援 `ORDER BY` 在所有路徑上具有相反順序的子句。

請參考下列範例，其中會在屬性名稱、年齡和 _ts 上定義複合索引：

| **複合索引**     | **範例 `ORDER BY` 查詢**      | **由複合索引支援？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

您應該自訂索引編制原則，以便提供所有必要 `ORDER BY` 的查詢。

### <a name="queries-with-filters-on-multiple-properties"></a>具有多屬性篩選條件的查詢

如果查詢中有兩個或多個屬性的篩選，則建立這些屬性的複合索引可能會很有説明。

例如，請考慮下列兩個屬性具有相等篩選的查詢：

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

如果能夠利用 (name ASC，age ASC) 的複合索引，此查詢將會更有效率，縮短時間並取用較少的 RU。

具有範圍篩選的查詢也可以使用複合索引進行優化。 不過，查詢只能有單一範圍篩選準則。 範圍篩選準則包括 `>` 、 `<` 、 `<=` 、 `>=` 和 `!=` 。 範圍篩選應定義在複合索引中的最後一個。

請考慮使用相等和範圍篩選準則的下列查詢：

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

使用 (name ASC，age ASC) 的複合索引，此查詢將會更有效率。 不過，此查詢不會利用 (age ASC，name ASC) 上的複合索引，因為相等篩選準則必須先在複合索引中定義。

針對具有多個屬性篩選的查詢建立複合索引時，會使用下列考慮

- 查詢篩選中的屬性應該符合複合索引中的屬性。 如果屬性在複合索引中，但未包含在查詢中做為篩選準則，則查詢將不會使用複合索引。
- 如果查詢在未定義于複合索引中的篩選中有其他屬性，則會使用複合和範圍索引的組合來評估查詢。 這需要的 RU 數目會比僅使用範圍索引更少。
- 如果屬性具有範圍篩選 (、、 `>` 、 `<` `<=` `>=` 或 `!=`) ，則應該在複合索引中最後定義這個屬性。 如果查詢有一個以上的範圍篩選準則，則不會使用複合索引。
- 當您建立複合索引來優化具有多個篩選準則的查詢時， `ORDER` 複合索引的不會影響結果。 這是選用屬性。
- 如果您沒有針對具有多個屬性篩選的查詢定義複合索引，查詢仍然會成功。 不過，您可以使用複合索引來縮減查詢的 RU 成本。

請考慮下列範例，其中會在屬性名稱、年齡和時間戳記上定義複合索引：

| **複合索引**     | **範例查詢**      | **由複合索引支援？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>具有篩選條件和 ORDER BY 子句的查詢

如果查詢篩選一或多個屬性，並且在 ORDER BY 子句中有不同的屬性，將篩選準則加入至子句可能會很有説明 `ORDER BY` 。

例如，藉由將篩選中的屬性加入 ORDER BY 子句中，可以重寫下列查詢來利用複合索引：

使用範圍索引進行查詢：

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

使用複合索引進行查詢：

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

您可以針對具有多個相等篩選的查詢，將相同的模式和查詢優化一般化：

使用範圍索引進行查詢：

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

使用複合索引進行查詢：

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

使用篩選準則和子句來建立複合索引來優化查詢時，會使用下列考慮 `ORDER BY` ：

* 如果查詢對屬性進行篩選，則應該在子句中先包含這些屬性 `ORDER BY` 。
* 如果您未在具有某個屬性之篩選的查詢上定義複合索引，並使用不同的屬性來定義個別 `ORDER BY` 子句，則查詢仍然會成功。 不過，您可以使用複合索引來縮減查詢的 RU 成本，特別是當子句中的屬性 `ORDER BY` 具有高基數時。
* 針對 `ORDER BY` 具有多個屬性的查詢，以及具有多個屬性之篩選準則的查詢，建立複合索引的所有考慮仍適用。


| **複合索引**                      | **範例 `ORDER BY` 查詢**                                  | **由複合索引支援？** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>修改編製索引原則

您可以 [使用 Azure 入口網站或其中一個支援的 sdk](how-to-manage-indexing-policy.md)，隨時更新容器的編制索引原則。 索引編制原則的更新會將舊索引的轉換觸發至新的索引，這會在線上和就地 (執行，因此在作業) 期間不會耗用額外的儲存空間。 舊原則的索引會有效率地轉換成新的原則，而不會影響寫入可用性、讀取可用性或容器上布建的輸送量。 索引轉換是非同步作業，完成所需的時間取決於布建的輸送量、專案數和大小。

> [!IMPORTANT]
> 索引轉換是取用 [要求單位](request-units.md)的作業。 如果您使用 [無伺服器](serverless.md) 容器，則索引轉換所耗用的要求單位目前不計費。 當無伺服器正式推出後，這些要求單位將會收取費用。

> [!NOTE]
> 您可以 [使用其中一個 sdk](how-to-manage-indexing-policy.md)來追蹤索引轉換的進度。

在任何索引轉換期間都不會有寫入可用性的影響。 索引轉換會使用您布建的 ru，但優先順序高於 CRUD 作業或查詢。

新增新的索引時，不會影響讀取可用性。 只有在索引轉換完成之後，查詢才會使用新的索引。 在索引轉換期間，查詢引擎會繼續使用現有的索引，因此您會在索引轉換期間觀察到在起始索引變更之前所觀察到的讀取效能類似。 加入新的索引時，也不會有不完整或不一致的查詢結果的風險。

移除索引並立即執行篩選已卸載索引的查詢時，並不保證會有一致或完整的查詢結果。 如果您在單一索引編制原則變更中移除多個索引，則查詢引擎會在整個索引轉換中提供一致且完整的結果。 但是，如果您透過多個編制索引原則變更來移除索引，則在所有索引轉換完成之前，查詢引擎將不會提供一致或完整的結果。 大部分的開發人員都不會捨棄索引，然後立即嘗試執行利用這些索引的查詢，因此在實務上，這種情況不太可能發生。

> [!NOTE]
> 可能的話，您應該一律嘗試將多個編制索引變更群組為單一的索引編制原則修改

## <a name="indexing-policies-and-ttl"></a>編制索引原則和 TTL

使用生存 [時間 (TTL) 功能](time-to-live.md) 需要編制索引。 這表示：

- 在編制索引模式設定為 [無] 的容器上，不可能啟用 TTL。
- 在啟用 TTL 的容器上，無法將索引編制模式設定為 [無]。

對於不需要為屬性路徑編制索引，但需要 TTL 的情況，您可以搭配使用索引編制原則：

- 索引模式設定為 [一致]，而
- 沒有包含的路徑，以及
- `/*` 作為唯一的排除路徑。

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

- [編製索引概觀](index-overview.md)
- [如何管理編製索引原則](how-to-manage-indexing-policy.md)
