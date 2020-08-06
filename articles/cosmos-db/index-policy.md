---
title: Azure Cosmos DB 索引編製原則
description: 瞭解如何在 Azure Cosmos DB 中設定和變更自動編制索引和更佳效能的預設索引編制原則。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: tisande
ms.openlocfilehash: e3981e828e7ffe401be3b72f68185c272ab11645
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760816"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引編製原則

在 Azure Cosmos DB 中，每個容器都有索引編制原則，以指示容器的專案應該如何編制索引。 新建立之容器的預設索引編制原則會編制每個專案的每個屬性的索引、強制執行任何字串或數位的範圍索引，以及類型為 Point 之任何 GeoJSON 物件的空間索引。 這可讓您取得高查詢效能，而不需要事先考慮索引編制和索引管理。

在某些情況下，您可以覆寫此自動行為，使其更符合您的需求。 您可以藉由設定其*索引模式*來自訂容器的索引編制原則，並包含或排除*屬性路徑*。

> [!NOTE]
> 本文所述的更新索引編制原則的方法僅適用于 Azure Cosmos DB 的 SQL (Core) API。 瞭解[Azure Cosmos DB 的 MONGODB API](mongodb-indexing.md)中編制索引

## <a name="indexing-mode"></a>索引模式

Azure Cosmos DB 支援兩種編制索引模式：

- **一致**：當您建立、更新或刪除專案時，會同步更新索引。 這表示讀取查詢的一致性會是[為帳戶設定的一致性](consistency-levels.md)。
- **無**：在容器上停用索引。 當容器當做單純的索引鍵-值存放區使用，而不需要次要索引時，通常會使用此參數。 它也可以用來改善大量作業的效能。 完成大量作業之後，可以將索引模式設定為一致，然後使用[IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk)進行監視，直到完成為止。

> [!NOTE]
> Azure Cosmos DB 也支援延遲索引編制模式。 當引擎不執行任何其他工作時，延遲索引會以較低的優先權層級執行索引的更新。 這可能會導致**不一致或不完整**的查詢結果。 如果您打算查詢 Cosmos 容器，則不應該選取 [延遲索引]。 我們在2020年6月引進了一項變更，不再允許將新的容器設定為延遲編制索引模式。 如果您的 Azure Cosmos DB 帳戶已經包含至少一個具有延遲編制索引的容器，此帳戶就會自動從變更中免除。 您也可以聯繫[Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)來要求豁免。

根據預設，索引編制原則會設定為 `automatic` 。 將 `automatic` 索引編制原則中的屬性設定為，即可達成此目的 `true` 。 將此屬性設定為 `true` ，可讓 Azure CosmosDB 在檔寫入時自動編制索引。

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>包含和排除屬性路徑

自訂索引編制原則可以指定從索引中明確包含或排除的屬性路徑。 藉由優化已編制索引的路徑數目，您可以大幅減少寫入作業的延遲和 RU 費用。 這些路徑會依照[[編制索引總覽] 區段中所述的方法](index-overview.md#from-trees-to-property-paths)定義，並具有下列新增專案：

- 以純量值開頭的路徑 (字串或數位) 結尾為`/?`
- 陣列中的元素會透過 `/[]` 表示 (法（而不是）來定址，而不是 `/0` `/1` 等，) 
- `/*`萬用字元可用來比對節點底下的任何元素

再次取得相同的範例：

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

- `headquarters`的 `employees` 路徑為`/headquarters/employees/?`

- `locations`' `country` 路徑為`/locations/[]/country/?`

- 下任何專案的路徑 `headquarters` 為`/headquarters/*`

例如，我們可以包含 `/headquarters/employees/?` 路徑。 此路徑可確保我們為 employee 屬性編制索引，但不會在此屬性中編制額外的嵌套 JSON 索引。

## <a name="includeexclude-strategy"></a>包含/排除策略

任何索引編制原則都必須包含根路徑 `/*` 作為包含或排除的路徑。

- 包含根路徑，以選擇性地排除不需要編制索引的路徑。 這是建議的方法，因為它可讓 Azure Cosmos DB 主動為任何可能新增至模型的新屬性編制索引。
- 排除根路徑，以選擇性地包含需要編制索引的路徑。

- 對於包含：英數位元和 _ (底線) 的一般字元路徑，您不需要以雙引號括住路徑字串 (例如 "/path/？") 。 針對具有其他特殊字元的路徑，您需要以雙引號括住路徑字串， (例如 "/ \" path-abc \" /？") 。 如果您的路徑中預期有特殊字元，您可以將每個路徑都換成安全。 在功能上，如果您要將每個路徑與包含特殊字元的路徑進行轉義，則不會有任何差異。

- `_etag`根據預設，除非將 etag 新增至所包含的索引路徑，否則系統屬性會從索引中排除。

- 如果索引模式設定為 [**一致**]，系統屬性 `id` 和 `_ts` 會自動編制索引。

包含和排除路徑時，您可能會遇到下列屬性：

- `kind`可以是 `range` 或 `hash` 。 範圍索引功能提供雜湊索引的所有功能，因此建議使用範圍索引。

- `precision`是在包含路徑的索引層級上定義的數位。 的值 `-1` 表示最大有效位數。 建議您一律將此值設定為 `-1` 。

- `dataType`可以是 `String` 或 `Number` 。 這表示將會編制索引的 JSON 屬性類型。

若未指定，這些屬性將會具有下列預設值：

| **屬性名稱**     | **預設值** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` 和 `Number` |

如需包含和排除路徑的索引編制原則範例，請參閱[這一節](how-to-manage-indexing-policy.md#indexing-policy-examples)。

## <a name="includeexclude-precedence"></a>包含/排除優先順序

如果包含的路徑和排除的路徑發生衝突，則會優先使用更精確的路徑。

以下為範例：

**包含的路徑**：`/food/ingredients/nutrition/*`

**排除的路徑**：`/food/ingredients/*`

在此情況下，包含的路徑會優先于排除的路徑，因為它比較精確。 根據這些路徑，路徑中或內建的任何資料 `food/ingredients` 都會從索引中排除。 例外狀況會是包含的路徑內的資料： `/food/ingredients/nutrition/*` ，這會編制索引。

以下是 Azure Cosmos DB 中包含和排除路徑優先順序的一些規則：

- 更深層的路徑比較窄的路徑更精確。 例如： `/a/b/?` 比更精確 `/a/?` 。

- `/?`比更精確 `/*` 。 例如， `/a/?` 比起 `/a/*` `/a/?` 優先使用更精確。

- 路徑 `/*` 必須是包含的路徑或排除的路徑。

## <a name="spatial-indexes"></a>空間索引

當您在索引編制原則中定義空間路徑時，您應該定義應該將哪一個索引套用 ```type``` 至該路徑。 空間索引的可能類型包括：

* Point

* 多邊形

* MultiPolygon

* LineString

Azure Cosmos DB 預設不會建立任何空間索引。 如果您想要使用空間 SQL 內建函數，您應該在必要的屬性上建立空間索引。 如需新增空間索引的編制索引原則範例，請參閱[這一節](sql-query-geospatial-index.md)。

## <a name="composite-indexes"></a>複合式索引

具有 `ORDER BY` 具有二或多個屬性之子句的查詢，需要複合索引。 您也可以定義複合索引，以改善許多相等和範圍查詢的效能。 根據預設，不會定義任何複合索引，因此您應該視需要[加入複合索引](how-to-manage-indexing-policy.md#composite-indexing-policy-examples)。

不同于包含或排除的路徑，您無法使用萬用字元建立路徑 `/*` 。 每個複合路徑 `/?` 在您不需要指定的路徑結尾都有隱含的。 複合路徑會導致純量值，這是包含在複合索引中的唯一值。

定義複合索引時，您可以指定：

- 兩個或多個屬性路徑。 定義屬性路徑的順序很重要。

- 順序 (遞增或遞減) 。

> [!NOTE]
> 當您加入複合索引時，查詢會利用現有的範圍索引，直到新的複合索引加入完成為止。 因此，當您新增複合索引時，可能不會立即觀察效能改進。 您可以[使用其中一個 sdk](how-to-manage-indexing-policy.md)來追蹤索引轉換的進度。

### <a name="order-by-queries-on-multiple-properties"></a>依查詢排序多個屬性：

針對具有 `ORDER BY` 兩個或多個屬性之子句的查詢使用複合索引時，會使用下列考慮：

- 如果複合索引路徑不符合子句中的屬性順序 `ORDER BY` ，則複合索引無法支援查詢。

- 複合索引路徑 (遞增或遞減) 的順序，也應該符合 `order` 子句中的 `ORDER BY` 。

- 複合索引也支援 `ORDER BY` 在所有路徑上具有相反順序的子句。

請考慮下列範例，其中的複合索引定義于屬性名稱、年齡和 _ts：

| **複合索引**     | **範例 `ORDER BY` 查詢**      | **支援複合索引嗎？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

您應該自訂索引編制原則，讓您可以服務所有必要 `ORDER BY` 的查詢。

### <a name="queries-with-filters-on-multiple-properties"></a>具有多屬性篩選條件的查詢

如果查詢有兩個或多個屬性的篩選，則建立這些屬性的複合索引可能會很有説明。

例如，請考慮下列在兩個屬性上具有相等篩選準則的查詢：

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

此查詢將會更有效率，如果能夠利用 (名稱 ASC，ASC) 的複合索引，則縮短時間並耗用較少的 RU。

具有範圍篩選的查詢也可以使用複合索引來優化。 不過，查詢只能有一個範圍篩選。 範圍篩選包括 `>` 、 `<` 、 `<=` 、 `>=` 和 `!=` 。 範圍篩選應定義于複合索引中的最後一個。

請考慮下列具有相等和範圍篩選的查詢：

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

此查詢將會更有效率地使用 (name ASC，age ASC) 的複合索引。 不過，查詢不會利用 (age ASC，name ASC) 的複合索引，因為在複合索引中必須先定義等號比較篩選準則。

針對具有多個屬性篩選的查詢來建立複合索引時，會使用下列考慮

- 查詢的篩選準則中的屬性應該符合複合索引中的屬性。 如果屬性位於複合索引中，但不包含在查詢中做為篩選準則，則查詢不會使用複合索引。
- 如果查詢的篩選準則中沒有定義于複合索引中的其他屬性，則會使用複合和範圍索引的組合來評估查詢。 這只需要較少的 RU，而不是使用範圍索引。
- 如果屬性具有範圍篩選 (`>` 、、、 `<` `<=` `>=` 或 `!=`) ，則此屬性應該定義于複合索引中的最後一個。 如果查詢有一個以上的範圍篩選，則不會使用複合索引。
- 當您建立複合索引以使用多個篩選準則來優化查詢時， `ORDER` 複合索引的不會影響結果。 這是選擇性屬性。
- 如果您未針對具有多個屬性之篩選的查詢定義複合索引，則查詢仍然會成功。 不過，使用複合索引可以減少查詢的 RU 成本。

請考慮下列在屬性名稱、年齡和時間戳記上定義複合索引的範例：

| **複合索引**     | **範例查詢**      | **支援複合索引嗎？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>具有篩選條件和 ORDER BY 子句的查詢

如果查詢篩選一或多個屬性，並在 ORDER BY 子句中有不同的屬性，將篩選準則中的屬性加入至子句可能會很有説明 `ORDER BY` 。

例如，藉由將篩選中的屬性加入至 ORDER BY 子句，可以重寫下列查詢以利用複合索引：

使用範圍索引的查詢：

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

使用複合索引進行查詢：

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

相同的模式和查詢優化可以針對具有多個相等篩選準則的查詢進行一般化：

使用範圍索引的查詢：

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

使用複合索引進行查詢：

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

建立複合索引以使用篩選準則和子句來優化查詢時，會用到下列考慮 `ORDER BY` ：

* 如果查詢會篩選屬性，則應該先在子句中包含這些內容 `ORDER BY` 。
* 如果您未在具有某個屬性之篩選的查詢和使用不同屬性的個別子句上定義複合索引 `ORDER BY` ，則查詢仍然會成功。 不過，使用複合索引可以減少查詢的 RU 成本，特別是如果子句中的屬性 `ORDER BY` 具有較高的基數。
* 針對 `ORDER BY` 具有多個屬性的查詢以及具有多個屬性篩選準則的查詢，建立複合索引的所有考慮仍適用。


| **複合索引**                      | **範例 `ORDER BY` 查詢**                                  | **支援複合索引嗎？** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>修改編製索引原則

您可以[使用 Azure 入口網站或其中一個支援的 sdk](how-to-manage-indexing-policy.md)，隨時更新容器的索引編制原則。 索引編制原則的更新會觸發從舊索引轉換成新的索引，而這項作業會在線上和就地執行 (，因此作業) 期間不會耗用額外的儲存空間。 舊原則的索引會有效率地轉換成新的原則，而不會影響容器上所布建的寫入可用性、讀取可用性或輸送量。 索引轉換是非同步作業，完成所需的時間取決於布建的輸送量、專案數目和其大小。

> [!NOTE]
> 您可以[使用其中一個 sdk](how-to-manage-indexing-policy.md)來追蹤索引轉換的進度。

在任何索引轉換期間，都不會影響寫入可用性。 「索引」轉換會使用您所布建的 ru，但優先順序會低於您的 CRUD 作業或查詢。

加入新的索引時，不會影響讀取可用性。 只有在索引轉換完成後，查詢才會使用新的索引。 在索引轉換期間，查詢引擎會繼續使用現有的索引，因此您會在索引轉換期間發現類似的讀取效能，這是您在開始編制索引變更之前所觀察到的結果。 加入新的索引時，也不會有不完整或不一致查詢結果的風險。

移除索引並立即執行篩選已卸載索引的查詢時，並不保證會有一致或完整的查詢結果。 如果您在單一索引編制原則變更中移除多個索引並這麼做，則查詢引擎會在整個索引轉換期間保證一致且完整的結果。 不過，如果您透過多個索引編制原則變更來移除索引，則在所有索引轉換完成之前，查詢引擎不保證會有一致或完整的結果。 大部分的開發人員都不會捨棄索引，然後立即嘗試執行利用這些索引的查詢，因此在實務上，這種情況不太可能發生。

> [!NOTE]
> 可能的話，您應該一律嘗試將多個索引變更分組成一個單一索引編制原則修改

## <a name="indexing-policies-and-ttl"></a>編制索引原則和 TTL

使用生存[時間 (TTL) 功能](time-to-live.md)需要編制索引。 這表示：

- 在編制索引模式設定為 None 的容器上，無法啟用 TTL。
- 在啟用 TTL 的容器上，無法將索引編制模式設定為 [無]。

針對不需要編制屬性路徑的情況，但需要 TTL，您可以搭配使用索引編制原則：

- 編制索引模式設定為 [一致]，
- 沒有包含的路徑，以及
- `/*`做為唯一排除的路徑。

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

- [編製索引概觀](index-overview.md)
- [如何管理編製索引原則](how-to-manage-indexing-policy.md)
