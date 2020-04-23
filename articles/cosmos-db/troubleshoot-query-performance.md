---
title: 使用 Azure Cosmos DB 時針對查詢問題進行疑難排解
description: 瞭解如何識別、診斷和疑難排解 Azure Cosmos DB SQL 查詢問題。
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/22/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: b3c6926f17e8378fd3b53bfd59a7c5ea8141adb4
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097229"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>使用 Azure Cosmos DB 時針對查詢問題進行疑難排解

本文會逐步解說在 Azure Cosmos DB 中針對查詢進行疑難排解的一般建議方法。 雖然您不應考慮這篇文章中所述的步驟，以避免潛在查詢問題，我們已在此納入最常見的效能秘訣。 您應該使用這篇文章做為疑難排解 Azure Cosmos DB core （SQL） API 中緩慢或昂貴查詢的起點。 您也可以使用[診斷記錄](cosmosdb-monitor-resource-logs.md)來識別速度變慢或耗用大量輸送量的查詢。

您可以廣泛地分類 Azure Cosmos DB 中的查詢優化：

- 減少查詢的要求單位（RU）費用的優化
- 只會降低延遲的優化

如果您降低查詢的 RU 費用，幾乎也會降低延遲。

本文提供您可以使用[營養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)資料集重新建立的範例。

## <a name="common-sdk-issues"></a>常見的 SDK 問題

閱讀本指南之前，請先考慮與查詢引擎無關的常見 SDK 問題。

- 為了達到最佳效能，請遵循這些[效能秘訣](performance-tips.md)。
    > [!NOTE]
    > 為了改善效能，我們建議 Windows 64 位主機處理。 SQL SDK 包含原生 Microsoft.azure.documents.serviceinterop.dll，可在本機剖析和優化查詢。 只有在 Windows x64 平臺上才支援 Microsoft.azure.documents.serviceinterop.dll。 針對 Linux 和其他不支援 Microsoft.azure.documents.serviceinterop.dll 的平臺，將會對閘道進行額外的網路呼叫，以取得優化的查詢。
- SDK 允許`MaxItemCount`為您的查詢設定，但您無法指定最小專案計數。
    - 程式碼應處理任何頁面大小，從零到`MaxItemCount`。
    - 頁面中的專案數一律會小於或等於指定`MaxItemCount`的。 不過， `MaxItemCount`嚴格地是最大值，而且結果可能會比此數量少。
- 有時候查詢可能會有空白頁面，即使在未來的頁面上有結果也一樣。 這種情況的原因可能是：
    - SDK 可能會進行多個網路呼叫。
    - 查詢可能需要很長的時間來取得檔。
- 所有查詢都有接續 token，可讓查詢繼續進行。 請務必完全清空查詢。 查看 SDK 範例，並使用上`while` `FeedIterator.HasMoreResults`的迴圈來清空整個查詢。

## <a name="get-query-metrics"></a>取得查詢計量

當您將 Azure Cosmos DB 中的查詢優化時，第一個步驟一律會[取得查詢的查詢計量](profile-sql-api-query.md)。 這些計量也可透過 Azure 入口網站取得。 在資料總管中執行查詢之後，[**結果**] 索引標籤旁會顯示查詢計量：

[![取得查詢計量](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

取得查詢計量之後，請將抓取的**檔計數**與查詢的**輸出檔案計數**進行比較。 使用此比較來識別本文中要檢查的相關章節。

抓取的**檔計數**是查詢引擎載入所需的檔數目。 **輸出檔案計數**是查詢結果所需的檔數目。 如果抓取的**檔計數**明顯高於**輸出檔案計數**，則表示查詢中至少有一個部分無法使用索引，而且需要進行掃描。

請參閱下列各節，以瞭解適用于您案例的相關查詢優化。

### <a name="querys-ru-charge-is-too-high"></a>查詢的 RU 費用過高

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>抓取的檔計數明顯高於輸出檔案計數

- [在編制索引原則中包含必要的路徑。](#include-necessary-paths-in-the-indexing-policy)

- [瞭解哪些系統函數會使用索引。](#understand-which-system-functions-use-the-index)

- [瞭解哪些匯總查詢會使用索引。](#understand-which-aggregate-queries-use-the-index)

- [優化同時具有篩選和 ORDER BY 子句的查詢。](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [使用子查詢來優化聯結運算式。](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>抓取的檔計數大約等於輸出檔案計數

- [最小化跨分割區查詢。](#minimize-cross-partition-queries)

- [將具有多個屬性篩選的查詢優化。](#optimize-queries-that-have-filters-on-multiple-properties)

- [優化同時具有篩選和 ORDER BY 子句的查詢。](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>查詢的 RU 費用是可接受的，但延遲仍然太高

- [改善鄰近性。](#improve-proximity)

- [增加布建的輸送量。](#increase-provisioned-throughput)

- [增加 MaxConcurrency。](#increase-maxconcurrency)

- [增加 MaxBufferedItemCount。](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>抓取的檔計數超過輸出檔案計數的查詢

 抓取的**檔計數**是查詢引擎載入所需的檔數目。 **輸出檔案計數**是查詢所傳回的檔數目。 如果抓取的**檔計數**明顯高於**輸出檔案計數**，則表示查詢中至少有一個部分無法使用索引，而且需要進行掃描。

以下是索引未完全提供的掃描查詢範例：

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

抓取的**檔計數**（60951）明顯高於**輸出檔案計數**（7），表示此查詢造成檔掃描。 在此情況下，系統函數[UPPER （）](sql-query-upper.md)不會使用索引。

### <a name="include-necessary-paths-in-the-indexing-policy"></a>在編制索引原則中包含必要的路徑

您的索引編制原則應涵蓋子句、 `WHERE` `ORDER BY`子句、 `JOIN`和大部分系統函數中包含的任何屬性。 在索引原則中指定的所需路徑應符合 JSON 檔中的屬性。

> [!NOTE]
> Azure Cosmos DB 編制索引原則中的屬性會區分大小寫

如果您在[營養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)資料集上執行下列簡單查詢，當`WHERE`子句中的屬性編制索引時，您會看到較低的 RU 費用：

#### <a name="original"></a>原始

查詢：

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
```

編制索引原則：

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

**RU 費用：** 409.51 RUs

#### <a name="optimized"></a>最佳化的

已更新的編制索引原則：

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

**RU 費用：** 2.98 RUs

您可以隨時將屬性新增至索引編制原則，而不會影響寫入可用性或效能。 如果您將新的屬性加入至索引，使用屬性的查詢將會立即使用新的可用索引。 查詢會在建立時使用新的索引。 因此，當索引重建正在進行中時，查詢結果可能會不一致。 如果新的屬性已編制索引，則在索引重建期間，只會影響使用現有索引的查詢。 您可以[追蹤索引轉換進度](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)。

### <a name="understand-which-system-functions-use-the-index"></a>瞭解哪些系統函數會使用索引

如果運算式可以轉譯成字串值的範圍，它可以使用索引。 否則，就不能這麼做。

以下是一些可使用索引的一般字串函數清單：

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING （str_expr、num_expr、num_expr） = str_expr，但只有在第一個 num_expr 為0時

以下是一些不使用索引且必須載入每份檔的一般系統函數：

| **系統函數**                     | **優化的想法**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | 使用 Azure 搜尋服務進行全文檢索搜尋。                        |
| 上/下                             | 不使用系統函數來將資料正規化以進行比較，而是在插入時將大小寫標準化。 之類```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'```的查詢就```SELECT * FROM c WHERE c.name = 'BOB'```會變成。 |
| 數學函數（非匯總） | 如果您需要在查詢中經常計算值，請考慮將值儲存為 JSON 檔中的屬性。 |

------

查詢的其他部分可能仍會使用索引，即使系統函式沒有。

### <a name="understand-which-aggregate-queries-use-the-index"></a>瞭解哪些匯總查詢使用索引

在大部分情況下，Azure Cosmos DB 中的匯總系統函數會使用索引。 不過，視匯總查詢中的篩選準則或其他子句而定，查詢引擎可能需要載入大量的檔。 查詢引擎通常會先套用相等和範圍篩選。 套用這些篩選之後，查詢引擎可以評估其他篩選準則，並視需要將其餘檔載入以計算匯總。

例如，假設有這兩個範例查詢，同時具有相等和`CONTAINS`系統函數篩選的查詢，通常會比僅具有`CONTAINS`系統函數篩選的查詢更有效率。 這是因為會先套用相等性篩選，並在需要載入檔之前使用索引，以獲得更昂貴`CONTAINS`的篩選準則。

只有`CONTAINS`篩選的查詢-較高的 RU 費用：

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

具有相等篩選準則和`CONTAINS`篩選-較低 RU 費用的查詢：

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

以下是不會完整使用索引之匯總查詢的其他範例：

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>具有不使用索引之系統函數的查詢

您應該參考相關的[系統函數頁面](sql-query-system-functions.md)，以查看它是否使用索引。

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>使用使用者定義函數（UDF）的匯總查詢

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>使用群組依據的查詢

具有`GROUP BY`之查詢的 RU 費用會隨著`GROUP BY`子句中屬性的基數增加而增加。 例如，在下列查詢中，查詢的 RU 費用會隨著唯一描述數目的增加而增加。

具有`GROUP BY`子句的彙總函式的 ru 費用，將會高於僅匯總函數的 ru 費用。 在此範例中，查詢引擎必須載入符合`c.foodGroup = "Sausages and Luncheon Meats"`篩選準則的每份檔，如此一來，RU 費用才會是高。

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

如果您打算經常執行相同的匯總查詢，使用[Azure Cosmos DB 變更](change-feed.md)摘要來建立即時的具體化視圖，會比執行個別查詢更有效率。

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>優化同時具有篩選和 ORDER BY 子句的查詢

雖然具有篩選準則和`ORDER BY`子句的查詢通常會使用範圍索引，但如果它們可以從複合索引提供服務，它們將會更有效率。 除了修改索引編制原則以外，您還應該將複合索引中的所有屬性加入至`ORDER BY`子句。 這種查詢變更會確保它使用複合索引。  您可以藉由對[營養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)資料集執行查詢來觀察影響：

#### <a name="original"></a>原始

查詢：

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
```

編制索引原則：

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

**RU 費用：** 44.28 RUs

#### <a name="optimized"></a>最佳化的

已更新的查詢（同時包含子句`ORDER BY`中的兩個屬性）：

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

已更新的編制索引原則：

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

**RU 費用：** 8.86 RUs

### <a name="optimize-join-expressions-by-using-a-subquery"></a>使用子查詢優化聯結運算式

多重值子查詢可以優化`JOIN`運算式，方法是在每個 select-many 運算式之後推送述詞，而不`WHERE`是在子句中的所有交叉聯結之後。

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

**RU 費用：** 167.62 RUs

在此查詢中，索引會比對具有名稱`infant formula`、 `nutritionValue`大於0且`amount`大於1之標記的任何檔。 此處`JOIN`的運算式會在套用任何篩選之前，針對每個相符的檔，執行標記、nutrients 和 servings 陣列之所有專案的交叉乘積。 然後`WHERE` ，子句會將篩選述詞套用至`<c, t, n, s>`每個元組。

例如，如果相符的檔在三個數組中的每一個都有10個專案，它會擴展為 1 x 10 x 10 x 10 （也就是1000）元組。 在這裡使用子查詢有助於篩選出聯結的陣列專案，然後再與下一個運算式聯結。

此查詢相當於上述其中一項，但使用子查詢：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU 費用：** 22.17 RUs

假設標記陣列中只有一個專案符合篩選準則，而且 nutrients 和 servings 陣列都有五個專案。 `JOIN`運算式會展開為 1 x 1 x 5 x 5 = 25 個專案，而不是第一個查詢中的1000個專案。

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>抓取的檔計數等於輸出檔案計數的查詢

如果抓取的**檔計數**大約等於**輸出檔案計數**，則查詢引擎不需要掃描許多不必要的檔。 對於許多查詢，如同使用`TOP`關鍵字的查詢，抓取的**檔計數**可能會超過**輸出檔案計數**1。 您不需要擔心這一點。

### <a name="minimize-cross-partition-queries"></a>最小化跨分割區查詢

Azure Cosmos DB 會使用[分割](partitioning-overview.md)區來調整個別容器，因為要求單位和資料儲存體需求也會隨之增加。 每個實體分割區都有個別且獨立的索引。 如果您的查詢具有符合容器分割區索引鍵的相等篩選準則，您就只需要檢查相關的資料分割索引。 此優化會減少查詢所需的 ru 總數。

如果您有大量布建的 ru （超過30000）或儲存大量的資料（大約超過 100 GB），您可能會有夠大的容器，以查看查詢 RU 費用的大幅縮減。

例如，如果您建立具有分割區索引鍵 foodGroup 的容器，下列查詢將只需要檢查單一實體分割區：

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

具有資料分割索引`IN`鍵之篩選的查詢只會檢查相關的實體分割區，而且不會「展開傳送」：

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

具有資料分割索引鍵之範圍篩選的查詢，或沒有資料分割索引鍵上任何篩選的查詢，將需要「展開傳送」，並檢查每個實體分割區的結果索引：

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

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>優化具有多個屬性篩選的查詢

雖然具有多個屬性篩選的查詢通常會使用範圍索引，如果可以從複合索引提供服務，它們將會更有效率。 對於少量的資料，這項優化並不會有重大影響。 不過，對於大量資料而言，這可能很有用。 每個複合索引最多隻能優化一個非相等的篩選準則。 如果您的查詢有多個不相等的篩選準則，請挑選其中一個將使用複合索引。 其餘部分將繼續使用範圍索引。 不相等的篩選準則必須定義于複合索引中的最後一個。 [深入瞭解複合索引](index-policy.md#composite-indexes)。

以下是一些可使用複合索引優化的查詢範例：

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

以下是相關的複合索引：

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

## <a name="optimizations-that-reduce-query-latency"></a>減少查詢延遲的優化

在許多情況下，當查詢延遲仍然太高時，可以接受 RU 費用。 下列各節概述減少查詢延遲的秘訣。 如果您在相同的資料集上多次執行相同的查詢，每次都會有相同的 RU 費用。 但是查詢的延遲可能會因查詢執行而異。

### <a name="improve-proximity"></a>改善鄰近性

從與 Azure Cosmos DB 帳戶不同的區域執行的查詢，其延遲會高於在相同區域內執行的情況。 例如，如果您在桌上型電腦上執行程式碼，您應該預期延遲為數十或數百毫秒以上（或更多），而不是查詢來自相同 Azure 區域內 Azure Cosmos DB 的虛擬機器。 [在 Azure Cosmos DB 中全域散發資料](distribute-data-globally.md)很簡單，以確保您可以將資料帶到更接近應用程式的範圍。

### <a name="increase-provisioned-throughput"></a>增加布建的輸送量

在 Azure Cosmos DB 中，您布建的輸送量會以要求單位（ru）來測量。 假設您有一個會耗用 5 ru 輸送量的查詢。 例如，如果您布建 1000 ru，您就能夠每秒執行該查詢200次。 如果您嘗試在沒有足夠的可用輸送量時執行查詢，Azure Cosmos DB 會傳回 HTTP 429 錯誤。 任何目前的核心（SQL） API Sdk 都會在等候短時間之後自動重試此查詢。 節流的要求需要較長的時間，因此增加布建的輸送量可以改善查詢延遲。 您可以在 Azure 入口網站的 [**計量**] 分頁上觀察[節流的要求總數](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors)。

### <a name="increase-maxconcurrency"></a>增加 MaxConcurrency

平行查詢是以平行方式查詢多個分割區來工作。 但是，來自個別資料分割集合的資料會根據查詢順序來進行序列提取。 因此，如果您將 MaxConcurrency 設定為數據分割的數目，就有機會達到最高效能的查詢，但前提是所有其他系統條件都維持不變。 如果您不知道分割區數目，可以將 MaxConcurrency （或較舊 SDK 版本中的 MaxDegreesOfParallelism）設定為較高的數位。 系統會選擇最小值（資料分割數目、使用者提供的輸入）做為平行處理原則的最大程度。

### <a name="increase-maxbuffereditemcount"></a>增加 MaxBufferedItemCount

查詢的設計目的是要在用戶端處理目前的結果批次時預先提取結果。 預先提取有助於改善查詢的整體延遲。 設定 MaxBufferedItemCount 會限制預先提取的結果數目。 如果您將此值設定為預期傳回的結果數目（或較大的數位），查詢就可以從預先提取取得最大的好處。 如果您將此值設定為-1，系統就會自動判斷要緩衝的專案數。

## <a name="next-steps"></a>後續步驟
如需如何測量每個查詢的 ru、取得執行統計資料來微調查詢的詳細資訊，請參閱下列文章：

* [使用 .NET SDK 取得 SQL 查詢執行計量](profile-sql-api-query.md)
* [使用 Azure Cosmos DB 調整查詢效能](sql-api-sql-query-metrics.md)
* [.NET SDK 的效能秘訣](performance-tips.md)
