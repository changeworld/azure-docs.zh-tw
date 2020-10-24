---
title: 針對使用 Azure Cosmos DB 時發生的查詢問題進行疑難排解
description: 了解如何針對 Azure Cosmos DB SQL 查詢問題進行識別、診斷及疑難排解。
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/12/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: b7e57656a6749f600d07b679aad6b8c77ac96551
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476700"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>針對使用 Azure Cosmos DB 時發生的查詢問題進行疑難排解

本文會逐步解說針對 Azure Cosmos DB 中查詢進行疑難排解的一般建議方法。 雖然您不應將本文所述的步驟視為潛在查詢問題的完整防禦方法，但我們已在此納入最常見的效能秘訣。 您應該使用本文作為 Azure Cosmos DB 核心 (SQL) API 中查詢緩慢或成本高昂的疑難排解起點。 您也可以使用[診斷記錄](cosmosdb-monitor-resource-logs.md)來識別緩慢或耗用大量輸送量的查詢。 如果您使用 Azure Cosmos DB 適用于 MongoDB 的 API，則應使用 [Azure Cosmos DB 適用于 mongodb 的 api 查詢疑難排解指南](mongodb-troubleshoot-query.md)

Azure Cosmos DB 中的查詢優化廣泛分類如下：

- 減少查詢要求單位 (RU) 費用的最佳化
- 只降低延遲的最佳化

如果您減少查詢的 RU 費用，通常也會降低延遲。

本文提供的範例可讓您使用 [營養資料集](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)來重新建立。

## <a name="common-sdk-issues"></a>常見的 SDK 問題

閱讀本指南之前，請先考量與查詢引擎無關的常見 SDK 問題。

- 遵循這些 [SDK 效能秘訣](performance-tips.md)。
    - [.NET SDK 疑難排解指南](troubleshoot-dot-net-sdk.md)
    - [JAVA SDK 疑難排解指南](troubleshoot-java-sdk-v4-sql.md)
- SDK 允許您為查詢設定 `MaxItemCount`，但您不能指定最小項目計數。
    - 程式碼應處理任何頁面大小，從零到 `MaxItemCount`。
- 有時候查詢可能會有空白頁面，即使未來的頁面上有結果也一樣。 這種情況的原因可能是：
    - SDK 可能會進行多個網路呼叫。
    - 查詢可能花費很長的時間來取得文件。
- 所有查詢都有接續權杖，可讓查詢繼續進行。 請務必完全清空查詢。 深入瞭解如何 [處理多個頁面的結果](sql-query-pagination.md#handling-multiple-pages-of-results)

## <a name="get-query-metrics"></a>取得查詢計量

當您將 Azure Cosmos DB 中的查詢最佳化時，第一個步驟一律是[取得查詢的查詢計量](profile-sql-api-query.md)。 這些計量也可透過 Azure 入口網站取得。 在資料總管中執行查詢後，您就可以在 [結果] 索引標籤旁看到查詢計量：

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="取得查詢計量" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

取得查詢計量之後，請將**擷取的文件計數**與查詢的**輸出文件計數**進行比較。 使用此比較來識別本文中要檢查的相關章節。

**擷取的文件計數**是查詢引擎需要載入的文件數目。 **輸出文件計數**是查詢結果所需的文件數目。 如果**擷取的文件計數**明顯高於**輸出文件計數**，則表示查詢中至少有一個部分無法使用索引，而且需要進行掃描。

請參閱下列各節，以了解您案例適用的相關查詢最佳化。

### <a name="querys-ru-charge-is-too-high"></a>查詢的 RU 費用過高

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>擷取的文件計數明顯高於輸出文件計數

- [在編製索引原則中包含必要的路徑。](#include-necessary-paths-in-the-indexing-policy)

- [了解哪些系統函數會使用索引。](#understand-which-system-functions-use-the-index)

- [了解哪些彙總查詢會使用索引。](#understand-which-aggregate-queries-use-the-index)

- [最佳化同時具有篩選和 ORDER BY 子句的查詢。](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [使用子查詢來最佳化 JOIN 運算式。](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>擷取的文件計數大約等於輸出文件計數

- [將跨分割區查詢最小化。](#minimize-cross-partition-queries)

- [將具有多個屬性篩選的查詢最佳化。](#optimize-queries-that-have-filters-on-multiple-properties)

- [最佳化同時具有篩選和 ORDER BY 子句的查詢。](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>查詢的 RU 費用是可接受的，但延遲仍然太高

- [改善鄰近性。](#improve-proximity)

- [增加佈建的輸送量。](#increase-provisioned-throughput)

- [增加 MaxConcurrency。](#increase-maxconcurrency)

- [增加 MaxBufferedItemCount。](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>擷取的文件計數超過輸出文件計數的查詢

 **擷取的文件計數**是查詢引擎需要載入的文件數目。 **輸出文件計數**是查詢所傳回的文件數目。 如果**擷取的文件計數**明顯高於**輸出文件計數**，則表示查詢中至少有一個部分無法使用索引，而且需要進行掃描。

以下是未完全由索引處理的掃描查詢範例：

查詢：

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

查詢計量：

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

**擷取的文件計數** (60,951) 明顯高於**輸出文件計數** (7)，表示此查詢會促使文件進行掃描。 在此情況下，系統函數 [UPPER()](sql-query-upper.md) 不會使用索引。

### <a name="include-necessary-paths-in-the-indexing-policy"></a>在編製索引原則中包含必要的路徑

您的索引編製原則應涵蓋 `WHERE` 子句、`ORDER BY` 子句、`JOIN` 和大部分系統函數中包含的任何屬性。 在索引原則中指定的所需路徑應符合 JSON 文件中的屬性。

> [!NOTE]
> Azure Cosmos DB 編製索引原則中的屬性會區分大小寫

如果您在[營養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)資料集上執行下列簡單查詢，當 `WHERE` 子句中的屬性已加入索引時，您會看到明顯降低的 RU 費用：

#### <a name="original"></a>原始

查詢：

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
```

編製索引原則：

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**RU 費用：** 409.51 RU

#### <a name="optimized"></a>已最佳化

已更新的編製索引原則：

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

**RU 費用：** 2.98 RU

您可以隨時將屬性新增至索引編制原則，而不會影響寫入或讀取可用性。 您可以[追蹤索引轉換進度](./how-to-manage-indexing-policy.md#dotnet-sdk)。

### <a name="understand-which-system-functions-use-the-index"></a>了解哪些系統函數會使用索引

如果運算式可以轉譯成字串值的範圍，則可以使用索引。 否則，就不能這麼做。

以下是一些可使用索引的一般字串函式清單：

- STARTSWITH(str_expr1, str_expr2, bool_expr)  
- CONTAINS(str_expr, str_expr, bool_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr，但僅限第一個 num_expr 為 0 的時候

以下是一些不使用索引且必須載入每份文件的一般系統函數：

| **系統函數**                     | **最佳化的概念**             |
| --------------------------------------- |------------------------------------------------------------ |
| UPPER/LOWER                             | 不使用系統函數來將資料標準化以進行比較，而是在插入時將大小寫標準化。 ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` 之類的查詢就會變成 ```SELECT * FROM c WHERE c.name = 'BOB'```。 |
| 數學函數 (非彙總) | 如果您需要經常在查詢中計算值，請考慮將值儲存為 JSON 文件中的屬性。 |

------

查詢的其他部分可能仍會使用索引，即使系統函數沒有使用。

### <a name="understand-which-aggregate-queries-use-the-index"></a>了解哪些彙總查詢彙使用索引

在大部分情況下，Azure Cosmos DB 中的彙總系統函數會使用索引。 不過，視彙總查詢中的篩選條件或其他子句而定，查詢引擎可能需要載入大量的文件。 查詢引擎通常會先套用相等和範圍的篩選條件。 套用這些篩選之後，查詢引擎就可以評估其他篩選條件，並在有需要時，藉由載入其餘文件來計算彙總。

例如，假設有兩個範例查詢，同時具有相等和 `CONTAINS` 系統函數篩選條件的查詢，通常會比僅具有 `CONTAINS` 系統函數篩選條件的查詢更有效率。 這是因為會先套用「相等」篩選條件，並且會先使用索引，然後才載入必要文件來取得高成本的 `CONTAINS` 篩選條件。

僅使用 `CONTAINS` 篩選條件進行查詢 - RU 費用較高：

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

使用相等篩選條件和 `CONTAINS` 篩選條件進行查詢 - RU 費用較低：

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

以下是不會完全使用索引的其他彙總查詢範例：

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>查詢具有不使用索引的系統函數

您應該參考相關的[系統函數頁面](sql-query-system-functions.md)，了解其是否使用索引。

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>搭配使用者定義函式 (UDF) 的彙總查詢

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>使用 GROUP BY 的查詢

如果使用 `GROUP BY`，查詢的 RU 費用會隨著 `GROUP BY` 子句中屬性基數的增加而提高。 例如，在下列查詢中，查詢的 RU 費用會隨著唯一描述數目的增加而增加。

具有 `GROUP BY` 子句的彙總函式，其 RU 費用將會高於彙總函式本身的 RU 費用。 在此範例中，查詢引擎必須載入符合 `c.foodGroup = "Sausages and Luncheon Meats"` 篩選條件的每個文件，因此預期的 RU 費用較高。

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

如果您打算經常執行相同的彙總查詢，使用 [Azure Cosmos DB 變更摘要](change-feed.md)來建立即時的具體化檢視，可能會比執行個別查詢更有效率。

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>最佳化同時具有篩選和 ORDER BY 子句的查詢

雖然具有篩選條件和 `ORDER BY` 子句的查詢通常會使用範圍索引，但如果可以使用複合式索引，則會更有效率。 除了修改索引編製原則以外，您還應該將複合式索引中的所有屬性新增至 `ORDER BY` 子句。 此查詢變更可確保其能夠使用複合式索引。  您可以藉由在[營養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)資料集上執行查詢來觀察影響：

#### <a name="original"></a>原始

查詢：

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
```

編製索引原則：

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**RU 費用：** 44.28 RU

#### <a name="optimized"></a>已最佳化

已更新的查詢 (同時在 `ORDER BY` 子句中包含兩個屬性)：

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

已更新的編製索引原則：

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**RU 費用：** 8.86 RU

### <a name="optimize-join-expressions-by-using-a-subquery"></a>使用子查詢來最佳化 JOIN 運算式

多值的子查詢可以藉由在每個 select-many 運算式之後推送述詞 (不是在 `WHERE` 子句中的所有交叉聯結之後)，來最佳化 `JOIN` 運算式。

請考量這項查詢：

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**RU 費用：** 167.62 RU

在此查詢中，索引會比對標記名稱為 `infant formula`、`nutritionValue` 大於 0 且 `amount` 大於 1 的任何文件。 此處的 `JOIN` 運算式會在套用任何篩選之前，針對每個相符文件執行所有標記、營養和份數項目陣列的交叉乘積。 `WHERE` 子句接著會在每個 `<c, t, n, s>` 元組上套用篩選述詞。

例如，如果相符文件在三個陣列中分別都有 10 個項目，其會擴展為 1 x 10 x 10 x 10 (也就是 1,000) 個元組。 在這裡使用子查詢有助於篩選出聯結的陣列項目，然後再與下一個運算式聯結。

此查詢相當於上述其中一項，但是使用的是子查詢：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU 費用：** 22.17 RU

假設標記陣列中只有一個項目符合篩選條件，而且營養和份數陣列都有五個項目。 `JOIN` 運算式會展開為 1 x 1 x 5 x 5 = 25 個項目，而不是第一個查詢中的 1,000 個項目。

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>擷取的文件計數等於輸出文件計數的查詢

如果**擷取的文件計數**大約等於**輸出文件計數**，則查詢引擎就不需要掃描許多不必要的文件。 對於許多查詢，例如使用 `TOP` 關鍵字的查詢，**擷取的文件計數**可能會比**輸出文件計數**多出 1 個。 此狀況無須擔心。

### <a name="minimize-cross-partition-queries"></a>將跨分割區查詢最小化

在要求單位和資料儲存體需求增加時，Azure Cosmos DB 會使用[資料分割](partitioning-overview.md)來調整個別容器。 每個實體分割區都有個別且獨立的索引。 如果您的查詢具有符合容器分割區索引鍵的相等篩選條件，您就只需要檢查相關分割區的索引。 此最佳化會減少查詢所需的 RU 總數。

如果您已佈建大量的 RU (超過 30,000) 或儲存大量的資料 (大約超過 100 GB)，表示您可能有夠大的容器，可看到查詢 RU 費用大幅縮減。

例如，如果您建立具有分割區索引鍵 foodGroup 的容器，下列查詢將只需要檢查單一實體分割區：

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

對分割區索引鍵使用 `IN` 篩選條件的查詢只會檢查相關的實體分割區，而不會將查詢「展開傳送」：

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

對分割區索引鍵使用範圍篩選的查詢，或沒有對分割區索引鍵使用任何篩選的查詢，將需要「展開傳送」並檢查每個實體分割區的索引來取得結果：

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>將具有多個屬性篩選的查詢最佳化

雖然在多個屬性上具有篩選條件的查詢通常會使用範圍索引，但如果可以使用複合式索引，則會更有效率。 對於少量的資料，這項最佳化並不會有重大影響。 不過，對於大量資料而言，這可能很實用。 您最多只能對每個複合式索引最佳化一個非相等篩選條件。 如果您的查詢有多個非相等篩選條件，請挑選其中一個來使用複合式索引。 其餘條件將繼續使用範圍索引。 非相等篩選條件必須定義在複合式索引中的最後一個。 [深入了解複合式索引](index-policy.md#composite-indexes)。

以下是一些可使用複合式索引最佳化的查詢範例：

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

以下是相關的複合式索引：

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>減少查詢延遲的最佳化

在許多情況下，RU 費用是可接受的，但查詢延遲仍然太高。 下列各節概述減少查詢延遲的秘訣。 如果您在相同的資料集上多次執行相同的查詢，則每次通常會有相同的 RU 費用。 但是查詢的延遲可能會因查詢執行不同而有所差異。

### <a name="improve-proximity"></a>改善鄰近性

如果從與 Azure Cosmos DB 帳戶不同的區域執行查詢，其延遲會高於在相同區域內執行的查詢。 例如，相較於從 Azure Cosmos DB 所在 Azure 區域內的虛擬機器執行查詢，您在桌上型電腦上執行程式碼時，應該會多出數十或數百毫秒 (或更多) 的延遲。 [在 Azure Cosmos DB 中全域散發資料](distribute-data-globally.md)，以確保您可以將資料帶到應用程式附近，這是很簡單的工作。

### <a name="increase-provisioned-throughput"></a>增加佈建的輸送量

在 Azure Cosmos DB 中，您佈建的輸送量會以要求單位 (RU) 來測量。 假設您有一個會耗用 5 RU 輸送量的查詢。 例如，如果您佈建 1,000 RU，您就能夠每秒執行該查詢 200 次。 如果您嘗試在可用輸送量不足時執行查詢，Azure Cosmos DB 會傳回 HTTP 429 錯誤。 目前任何核心 (SQL) API SDK 都會在等候短時間之後自動重試此查詢。 節流的要求需要較長的時間，因此增加佈建的輸送量可以改善查詢延遲。 您可以在 Azure 入口網站的 [計量] 分頁上，觀察[已節流的要求總數](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors)。

### <a name="increase-maxconcurrency"></a>增加 MaxConcurrency

平行查詢的運作方式是以平行方式查詢多個分割。 不過，對於查詢會以循序方式擷取來自個別分割集合的資料。 因此，如果將 MaxConcurrency 設定為分割數目，將最有機會達到最高效能的查詢，但前提是其他所有系統條件皆維持不變。 如果您不知道分割區數目，可以將 MaxConcurrency (或較舊 SDK 版本中的 MaxDegreesOfParallelism) 設定為較高的數字。 系統會選擇最小值 (分割區數目、使用者提供的輸入) 作為平行處理原則的最大程度。

### <a name="increase-maxbuffereditemcount"></a>增加 MaxBufferedItemCount

查詢已設計成可以在用戶端處理目前的結果批次時預先擷取結果。 預先擷取有助於改善查詢的整體延遲。 設定 MaxBufferedItemCount 可限制預先擷取的結果數目。 如果您將此值設定為預期傳回的結果數目 (或較大的數目)，查詢就可以從預先擷取中獲得最大的好處。 如果您將此值設定為 -1，系統就會自動判斷要緩衝的項目數目。

## <a name="next-steps"></a>後續步驟
如需如何測量每個查詢的 RU 和取得執行統計資料來微調查詢等等的詳細資訊，請參閱下列文章：

* [使用 .NET SDK 取得 SQL 查詢執行計量](profile-sql-api-query.md)
* [使用 Azure Cosmos DB 調整查詢效能](./sql-api-query-metrics.md)
* [.NET SDK 的效能秘訣](performance-tips.md)