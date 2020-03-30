---
title: Azure Cosmos DB 索引編製原則
description: 瞭解如何配置和更改預設索引策略，以便自動索引和提高 Azure Cosmos DB 中的性能。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tisande
ms.openlocfilehash: 930f156ebec76be860e7af02d41540ce67982f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292079"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引編製原則

在 Azure Cosmos DB 中，每個容器都有一個索引策略，該策略規定了如何對容器的項進行索引。 新創建的容器的預設索引策略索引每個專案的每個屬性，為任何字串或數位強制實施範圍索引，並為類型 Point 的任何 GeoJSON 物件強制實施空間索引。 這允許您獲得較高的查詢性能，而無需預先考慮索引和索引管理。

在某些情況下，您可以覆寫此自動行為，使其更符合您的需求。 您可以通過設置容器的*索引模式*來自訂容器的索引策略，並包括或排除*屬性路徑*。

> [!NOTE]
> 本文中描述的更新索引策略的方法僅適用于 Azure Cosmos DB 的 SQL（核心）API。

## <a name="indexing-mode"></a>索引模式

Azure 宇宙 DB 支援兩種索引模式：

- **一致**： 在創建、更新或刪除項時，索引會同步更新。 這意味著讀取查詢的一致性將是[為帳戶配置的一致性](consistency-levels.md)。
- **無**：在容器上禁用索引。 當容器用作純鍵值存儲而無需輔助索引時，通常使用此方法。 它還可用於提高批量操作的性能。 批量操作完成後，索引模式可以設置為"一致"，然後使用[索引轉換進度](how-to-manage-indexing-policy.md#use-the-net-sdk-v2)進行監視，直到完成。

> [!NOTE]
> Azure 宇宙資料庫還支援延遲索引模式。 當引擎不執行任何其他工作時，延遲索引以低得多的優先順序執行索引的更新。 這可能會導致查詢結果**不一致或不完整**。 如果計畫查詢 Cosmos 容器，則不應選擇延遲索引。

預設情況下，索引策略設置為`automatic`。 它是通過在索引策略中將`automatic`屬性設置為`true`來實現的。 將此屬性設置為`true`允許 Azure CosmosDB 在文檔寫入時自動對文檔進行索引。

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>包括和排除屬性路徑

自訂索引策略可以指定顯式包含或從索引中排除的屬性路徑。 通過優化索引的路徑數，可以降低容器使用的存儲量，並改善寫入操作的延遲。 這些路徑按照[索引概述部分中描述的方法](index-overview.md#from-trees-to-property-paths)定義，並添加以下內容：

- 導致標量值（字串或數位）的路徑以`/?`
- 陣列中的元素通過`/[]`標記法（而不是 等`/0``/1`）一起定址。
- 通`/*`配符可用於匹配節點下的任何元素

再次採用同一示例：

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

- 路徑`headquarters``employees`是`/headquarters/employees/?`

- '`locations``country`路徑是`/locations/[]/country/?`

- 通往任何下`headquarters`的東西的路徑是`/headquarters/*`

例如，我們可以包括`/headquarters/employees/?`路徑。 此路徑將確保索引員工屬性，但不會索引此屬性中的其他嵌套 JSON。

## <a name="includeexclude-strategy"></a>包括/排除策略

任何索引策略必須將根路徑`/*`作為包含路徑或排除路徑。

- 包括用於有選擇地排除不需要索引的路徑的根路徑。 這是推薦的方法，因為它允許 Azure Cosmos DB 主動索引可能添加到模型的任何新屬性。
- 排除要有選擇地包含需要索引的路徑的根路徑。

- 對於具有常規字元（包括：字母數位字元和 * （底線）的路徑，您不必繞雙引號（例如，"/路徑/？"）逃離路徑字串。 對於具有其他特殊字元的路徑，您需要圍繞雙引號（例如，"/\"路徑-abc\"/？"）來轉義路徑字串。 如果您期望路徑中出現特殊字元，則可以為了安全而逃離每個路徑。 功能上，如果你逃離每個路徑 Vs 只是具有特殊字元的路徑，則沒有任何區別。

- 預設情況下，系統`_etag`屬性從索引中排除，除非 etag 添加到包含的索引路徑中。

- 如果索引模式設置為**一致**，則系統屬性`id`將自動`_ts`編制索引。

在包含和排除路徑時，可能會遇到以下屬性：

- `kind`可以是 或`range``hash`。 範圍索引功能提供雜湊索引的所有功能，因此建議使用範圍索引。

- `precision`是在包含路徑的索引級別定義的數位。 值 表示`-1`最大精度。 我們建議始終將此值設置為`-1`。

- `dataType`可以是 或`String``Number`。 這指示將編制索引的 JSON 屬性的類型。

如果未指定，這些屬性將具有以下預設值：

| **屬性名稱**     | **預設值** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` 和 `Number` |

有關包含和排除路徑的索引策略示例，請參閱[此部分](how-to-manage-indexing-policy.md#indexing-policy-examples)。

## <a name="spatial-indexes"></a>空間索引

在索引策略中定義空間路徑時，應定義應應用於該路徑的索引```type```。 空間索引的可能類型包括：

* Point

* Polygon

* MultiPolygon

* LineString

預設情況下，Azure Cosmos DB 不會創建任何空間索引。 如果要使用空間 SQL 內建函數，則應在所需屬性上創建空間索引。 有關添加空間索引的策略示例的索引，請參閱[此部分](geospatial.md)。

## <a name="composite-indexes"></a>複合索引

具有具有兩個或多個屬性`ORDER BY`的子句的查詢需要複合索引。 還可以定義複合索引，以提高許多相等性和範圍查詢的性能。 預設情況下，不定義複合索引，因此應根據需要[添加複合索引](how-to-manage-indexing-policy.md#composite-indexing-policy-examples)。

定義複合索引時，指定：

- 兩個或多個屬性路徑。 定義屬性路徑的順序很重要。

- 順序（昇冪或降冪）。

> [!NOTE]
> 添加複合索引時，查詢將使用現有範圍索引，直到完成新的複合索引添加。 因此，添加複合索引時，可能不會立即觀察到性能改進。 [可以使用其中一個 SDK](how-to-manage-indexing-policy.md)跟蹤索引轉換的進度。

### <a name="order-by-queries-on-multiple-properties"></a>對多個屬性進行查詢：

對於具有兩個或多個屬性`ORDER BY`的子句的查詢使用複合索引時，使用以下注意事項：

- 如果複合索引路徑與`ORDER BY`子句中屬性的順序不匹配，則複合索引不能支援查詢。

- 複合索引路徑的順序（昇冪或降冪）也應與 子句`order`中的`ORDER BY`。

- 複合索引還支援在所有路徑`ORDER BY`上順序相反的子句。

請考慮以下示例，其中在屬性名稱、年齡和_ts上定義複合索引：

| **綜合指數**     | **示例`ORDER BY`查詢**      | **受綜合指數支援？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

應自訂索引策略，以便提供所有必要的`ORDER BY`查詢。

### <a name="queries-with-filters-on-multiple-properties"></a>具有多屬性篩選條件的查詢

如果查詢具有兩個或多個屬性的篩選器，則為這些屬性創建複合索引可能會有所説明。

例如，請考慮以下查詢，該查詢在兩個屬性上具有相等性篩選器：

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

如果此查詢能夠利用上的複合索引（名稱 ASC、年齡 ASC），則此查詢將更高效、耗時更少、使用更少的 RU。

還可以使用複合索引優化具有範圍篩選器的查詢。 但是，查詢只能有一個範圍篩選器。 範圍篩選器包括`>``<`、、`<=``>=`和`!=`。 範圍篩選器應在複合索引中最後定義。

請考慮以下具有相等性和範圍篩選器的查詢：

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

此查詢將更有效地使用複合索引（名稱 ASC，年齡 ASC）。 但是，查詢不會使用上的複合索引（年齡 ASC，名稱 ASC），因為相等篩選器必須首先在複合索引中定義。

在為具有多個屬性的篩選器的查詢創建複合索引時，使用以下注意事項

- 查詢篩選器中的屬性應與複合索引中的屬性匹配。 如果屬性位於複合索引中，但不包括在查詢中作為篩選器，則查詢將不會使用複合索引。
- 如果查詢在篩選器中具有未在複合索引中定義的其他屬性，則複合索引和範圍索引的組合將用於評估查詢。 這將需要更少的 RU 比完全使用範圍索引。
- 如果屬性具有範圍篩選器`>`（、、、、`>=``!=``<``<=`或 ），則應在複合索引中最後定義此屬性。 如果查詢具有多個範圍篩選器，則不會使用複合索引。
- 創建複合索引以優化具有多個篩選器的查詢時，`ORDER`複合索引不會影響結果。 這是選用屬性。
- 如果不為具有多個屬性篩選器的查詢定義複合索引，則查詢仍將成功。 但是，可以使用複合索引降低查詢的 RU 成本。

請考慮以下在屬性名稱、年齡和時間戳記上定義複合索引的示例：

| **綜合指數**     | **依例查詢**      | **受綜合指數支援？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>具有篩選條件和 ORDER BY 子句的查詢

如果查詢篩選了一個或多個屬性，並且 ORDER BY 子句中具有不同的屬性，則將篩選器中的屬性添加到`ORDER BY`子句可能會有所説明。

例如，通過將篩選器中的屬性添加到 ORDER BY 子句，可以重寫以下查詢以利用複合索引：

使用範圍索引進行查詢：

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

使用複合索引進行查詢：

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

對於具有多個相等篩選器的查詢，可以概括相同的模式和查詢優化：

使用範圍索引進行查詢：

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

使用複合索引進行查詢：

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

創建複合索引以使用篩選器和`ORDER BY`子句優化查詢時，使用以下注意事項：

* 如果查詢篩選屬性，則應首先將這些屬性包含在子句中`ORDER BY`。
* 如果不在查詢上定義複合索引，在一個屬性上使用篩選器，使用其他屬性定義`ORDER BY`單獨的子句，則查詢仍將成功。 但是，可以使用複合索引降低查詢的 RU 成本，尤其是在`ORDER BY`子句中的屬性具有較高的基數時。
* 為`ORDER BY`具有多個屬性的查詢創建複合索引以及對多個屬性具有篩選器的查詢的所有注意事項仍然適用。


| **綜合指數**                      | **示例`ORDER BY`查詢**                                  | **受綜合指數支援？** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>修改編製索引原則

容器的索引策略可以隨時[使用 Azure 門戶或受支援的 SDK 之一](how-to-manage-indexing-policy.md)進行更新。 索引策略的更新將觸發從舊索引到新索引的轉換，該索引在連線和就地執行（因此在操作過程中不會佔用額外的存儲空間）。 舊策略的索引有效地轉換為新策略，而不會影響容器上的寫入可用性或輸送量。 索引轉換是非同步作業，完成所需的時間取決於預配輸送量、項數及其大小。

> [!NOTE]
> 在添加範圍或空間索引時，查詢可能不會返回所有匹配的結果，並且不會在不返回任何錯誤的情況下返回。 這意味著在索引轉換完成之前，查詢結果可能不一致。 [可以使用其中一個 SDK](how-to-manage-indexing-policy.md)跟蹤索引轉換的進度。

如果新索引策略的模式設置為"一致"，則在索引轉換進行時，無法應用其他索引策略更改。 通過將索引策略的模式設置為 None（將立即刪除索引），可以取消正在運行的索引轉換。

## <a name="indexing-policies-and-ttl"></a>索引策略和 TTL

即時時間[（TTL） 功能](time-to-live.md)要求索引在打開的容器上處於活動狀態。 這表示：

- 無法在將索引模式設置為"無"的容器上啟動 TTL，
- 無法在啟動 TTL 的容器上將索引模式設置為"無"。

對於不需要索引屬性路徑但需要 TTL 的情況，可以使用以下策略使用索引策略：

- 索引模式設置為"一致"，以及
- 不包括路徑，和
- `/*`作為唯一排除的路徑。

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

- [索引編製概觀](index-overview.md)
- [如何管理索引策略](how-to-manage-indexing-policy.md)
