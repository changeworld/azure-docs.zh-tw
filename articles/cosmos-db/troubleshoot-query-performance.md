---
title: 使用 Azure Cosmos DB 時針對查詢問題進行疑難排解
description: 瞭解如何識別、診斷和疑難排解 Azure Cosmos DB SQL 查詢問題。
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: aae11facd2fea5413b2996b3088cb2edc23f0dc1
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77424927"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>使用 Azure Cosmos DB 時針對查詢問題進行疑難排解

本文會逐步解說在 Azure Cosmos DB 中針對查詢進行疑難排解的一般建議方法。 雖然本檔中所述的步驟不應視為潛在查詢問題的「全部攔截」，但我們在此納入了最常見的效能秘訣。 您應該使用本檔做為疑難排解 Azure Cosmos DB core （SQL） API 中緩慢或昂貴查詢的起點。 您也可以使用[診斷記錄](cosmosdb-monitor-resource-logs.md)來識別速度變慢或耗用大量輸送量的查詢。

您可以廣泛地分類 Azure Cosmos DB 中的查詢優化：優化可減少查詢的要求單位（RU）費用，以及只會降低延遲的優化。 藉由減少查詢的 RU 費用，您幾乎也會降低延遲。

本檔將使用可使用[營養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)資料集重新建立的範例。

### <a name="obtaining-query-metrics"></a>取得查詢計量：

在 Azure Cosmos DB 中優化查詢時，第一個步驟一律會[取得查詢的查詢計量](profile-sql-api-query.md)。 這些也可透過 Azure 入口網站取得，如下所示：

[![取得查詢計量](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

取得查詢計量之後，請將抓取的檔計數與查詢的輸出檔案計數進行比較。 使用此比較來識別下方要參考的相關區段。

抓取的檔計數是查詢載入所需的檔數目。 輸出檔案計數是查詢結果所需的檔數目。 如果抓取的檔計數明顯高於輸出檔案計數，則查詢中至少有一個部分無法利用索引，而且需要進行掃描。

您可以參考下一節，以瞭解適用于您案例的相關查詢優化：

### <a name="querys-ru-charge-is-too-high"></a>查詢的 RU 費用過高

#### <a name="retrieved-document-count-is-significantly-greater-than-output-document-count"></a>抓取的檔計數明顯大於輸出檔案計數

- [在編制索引原則中包含必要的路徑](#include-necessary-paths-in-the-indexing-policy)

- [瞭解哪些系統函數會使用索引](#understand-which-system-functions-utilize-the-index)

- [同時具有篩選準則和 ORDER BY 子句的查詢](#queries-with-both-a-filter-and-an-order-by-clause)

- [使用子查詢優化聯結運算式](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>抓取的檔計數大約等於輸出檔案計數

- [避免跨分割區查詢](#avoid-cross-partition-queries)

- [多個屬性的篩選](#filters-on-multiple-properties)

- [同時具有篩選準則和 ORDER BY 子句的查詢](#queries-with-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>查詢的 RU 費用是可接受的，但延遲仍然太高

- [改善鄰近性](#improve-proximity)

- [增加布建的輸送量](#increase-provisioned-throughput)

- [增加 MaxConcurrency](#increase-maxconcurrency)

- [增加 MaxBufferedItemCount](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>抓取的檔計數超過輸出檔案計數的查詢

 抓取的檔計數是查詢載入所需的檔數目。 輸出檔案計數是查詢結果所需的檔數目。 如果抓取的檔計數明顯高於輸出檔案計數，則查詢中至少有一個部分無法利用索引，而且需要進行掃描。

 以下是索引未完全提供的掃描查詢範例。

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

抓取的檔計數（60951）明顯大於輸出檔案計數（7），因此此查詢需要執行掃描。 在此情況下，系統函數[UPPER （）](sql-query-upper.md)不會使用索引。

## <a name="include-necessary-paths-in-the-indexing-policy"></a>在編制索引原則中包含必要的路徑

您的索引編制原則應涵蓋 `WHERE` 子句、`ORDER BY` 子句、`JOIN`和大部分系統函數中包含的任何屬性。 在索引原則中指定的路徑應符合（區分大小寫） JSON 檔中的屬性。

如果我們在[營養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)資料集上執行簡單的查詢，當 `WHERE` 子句中的屬性編制索引時，我們會看到較低的 RU 費用。

### <a name="original"></a>原始

查詢：

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
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

**RU 費用：** 409.51 ru

### <a name="optimized"></a>最佳化的

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

**RU 費用：** 2.98 ru

您可以隨時將其他屬性新增至索引編制原則，而不會影響寫入可用性或效能。 如果您將新的屬性加入至索引，則使用這個屬性的查詢會立即使用新的可用索引。 查詢會在建立時使用新的索引。 因此，查詢結果可能會不一致，因為索引重建正在進行中。 如果新的屬性已編制索引，則在索引重建期間，只會影響使用現有索引的查詢不會受到影響。 您可以[追蹤索引轉換進度](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)。

## <a name="understand-which-system-functions-utilize-the-index"></a>瞭解哪些系統函數會使用索引

如果運算式可以轉譯成一個範圍內的字串值，就可以利用索引；否則，不能這麼做。

以下是可利用索引的字串函式清單：

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr，但僅限第一個 num_expr 為 0 的時候

某些不使用索引且必須載入每份檔的一般系統函數如下：

| **系統函數**                     | **優化的想法**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | 使用 Azure 搜尋服務進行全文檢索搜尋                        |
| 上/下                             | 不使用系統函數來每次進行比較來將資料正規化，而是改為在插入時將大小寫標準化。 然後，```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` 之類的查詢就會變成 ```SELECT * FROM c WHERE c.name = 'BOB'``` |
| 數學函數（非匯總） | 如果您需要經常計算查詢中的值，請考慮將此值儲存為 JSON 檔中的屬性。 |

------

雖然系統不會使用索引，但查詢的其他部分仍然可以利用索引。

## <a name="queries-with-both-a-filter-and-an-order-by-clause"></a>同時具有篩選準則和 ORDER BY 子句的查詢

具有篩選準則和 `ORDER BY` 子句的查詢通常會利用範圍索引，如果可以從複合索引提供服務，則會更有效率。 除了修改索引編制原則以外，您還應該將複合索引中的所有屬性加入至 `ORDER BY` 子句。 此查詢修改會確保它利用複合索引。  您可以藉由對[營養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)資料集執行查詢來觀察影響。

### <a name="original"></a>原始

查詢：

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
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

**RU 費用：** 44.28 ru

### <a name="optimized"></a>最佳化的

已更新的查詢（同時包含 `ORDER BY` 子句中的兩個屬性）：

```sql
SELECT * FROM c
WHERE c.foodGroup = “Soups, Sauces, and Gravies”
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

**RU 費用：** 8.86 ru

## <a name="optimize-join-expressions-by-using-a-subquery"></a>使用子查詢優化聯結運算式
多重值子查詢可以將述詞推送至每個 select-many 運算式之後，而不是在 `WHERE` 子句中的所有交叉聯結之後，藉以優化 `JOIN` 運算式。

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

**RU 費用：** 167.62 ru

在此查詢中，索引會比對具有名稱 "嬰兒 formula"、nutritionValue 大於0且提供大於1之標記的任何檔。 此處的 `JOIN` 運算式會在套用任何篩選之前，針對每個相符的檔，執行標記、nutrients 和 servings 陣列之所有專案的交叉乘積。 `WHERE` 子句接著會在每個 `<c, t, n, s>` 元組上套用篩選述詞。

比方說，如果相符的檔在三個數組的每一個都有10個專案，它會擴展為 1 x 10 x 10 x 10 （也就是1000）元組。 在這裡使用子查詢可以協助篩選出聯結的陣列專案，然後再與下一個運算式聯結。

此查詢相當於上述其中一項，但使用子查詢：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU 費用：** 22.17 ru

假設標記陣列中只有一個專案符合篩選準則，而且 nutrients 和 servings 陣列都有五個專案。 然後，`JOIN` 運算式會展開為 1 x 1 x 5 x 5 = 25 個專案，而不是第一個查詢中的1000個專案。

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>抓取的檔計數等於輸出檔案計數的查詢

如果抓取的檔計數大約等於輸出檔案計數，則表示查詢不需要掃描許多不必要的檔。 對於許多查詢，例如使用 TOP 關鍵字的查詢，抓取的檔計數可能會超過輸出檔案計數1。 這應該不會造成問題。

## <a name="avoid-cross-partition-queries"></a>避免跨分割區查詢

Azure Cosmos DB 會使用[分割](partitioning-overview.md)區來調整個別容器，因為要求單位和資料儲存體需求也會隨之增加。 每個實體分割區都有個別且獨立的索引。 如果您的查詢具有符合容器分割區索引鍵的相等篩選準則，您就只需要檢查相關的分割區索引。 此優化會減少查詢所需的 RU 總數。

如果您有大量布建的 RU （超過30000）或儲存大量的資料（大約超過 100 GB），您可能會有夠大的容器，以查看查詢 RU 費用的大幅縮減。

例如，如果我們建立具有分割區索引鍵 foodGroup 的容器，下列查詢只需要檢查單一實體分割區：

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

這些查詢也會藉由在查詢中包含分割區索引鍵來進行優化：

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

對於資料分割索引鍵具有範圍篩選的查詢，或沒有資料分割索引鍵上的任何篩選，將需要「展開傳送」，並檢查每個實體分割區的結果索引。

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

## <a name="filters-on-multiple-properties"></a>多個屬性的篩選

當具有多個屬性篩選的查詢通常會使用範圍索引時，如果可以從複合索引提供服務，它們就會更有效率。 對於少量的資料，這項優化並不會有重大影響。 但對於大量資料而言，它可能很有用。 每個複合索引最多隻能優化一個非相等的篩選準則。 如果您的查詢有多個不相等的篩選準則，您應該挑選其中一個將會利用複合索引。 其餘部分將繼續利用範圍索引。 不相等的篩選準則必須定義于複合索引中的最後一個。 [深入瞭解複合索引](index-policy.md#composite-indexes)

以下是一些可使用複合索引優化的查詢範例：

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
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

## <a name="optimizations-that-reduce-query-latency"></a>可減少查詢延遲的優化：

在許多情況下，RU 費用可能是可接受的，但查詢延遲仍然太高。 下列各節概述減少查詢延遲的秘訣。 如果您在相同的資料集上多次執行相同的查詢，每次都會有相同的 RU 費用。 但查詢的延遲可能會因查詢執行而異。

## <a name="improve-proximity"></a>改善鄰近性

從與 Azure Cosmos DB 帳戶不同的區域執行的查詢，其延遲會高於在相同區域內執行的情況。 例如，如果您在桌上型電腦上執行程式碼，您應該預期延遲超過數十個或數百個（或更多）毫秒，大於 Azure Cosmos DB 的相同 Azure 區域內的虛擬機器。 [在 Azure Cosmos DB 中，全域散發資料](distribute-data-globally.md)很簡單，以確保您可以將資料帶到更接近應用程式的範圍。

## <a name="increase-provisioned-throughput"></a>增加布建的輸送量

在 Azure Cosmos DB 中，您布建的輸送量會以要求單位（RU）來測量。 假設您有一個使用 5 RU 輸送量的查詢。 例如，如果您布建 1000 RU 的，您就能夠每秒執行該查詢200次。 如果您嘗試在沒有足夠的可用輸送量時執行查詢，Azure Cosmos DB 會傳回 HTTP 429 錯誤。 任何目前的 Core （SQL） API sdk 都會在等候短暫的時間之後自動重試此查詢。 節流的要求需要較長的時間，因此增加布建的輸送量可以改善查詢延遲。 您可以在 Azure 入口網站的 [計量] 分頁中，觀察[節流要求的總數](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors)。

## <a name="increase-maxconcurrency"></a>增加 MaxConcurrency

平行查詢是以平行方式查詢多個分割區來工作。 不過，對於查詢會以循序方式擷取來自個別分割集合的資料。 因此，如果所有其他系統條件維持不變，則將 MaxConcurrency 調整為數據分割數目的機率最大，可以達到最高效能的查詢。 如果您不知道分割區數目，可以將 MaxConcurrency （或舊版 sdk 中的 MaxDegreesOfParallelism）設定為較高的數位，然後系統會選擇最小值（資料分割數目、使用者提供的輸入）做為平行處理原則的最大程度。

## <a name="increase-maxbuffereditemcount"></a>增加 MaxBufferedItemCount

查詢的設計目的是要在用戶端處理目前的結果批次時預先提取結果。 預先擷取有助於改善查詢的整體延遲。 設定 MaxBufferedItemCount 會限制預先提取的結果數目。 藉由將此值設定為預期傳回的結果數目（或較大的數位），查詢可以從預先提取取得最大效益。 將此值設定為-1 可讓系統自動決定要緩衝的專案數。

## <a name="next-steps"></a>後續步驟
請參閱下列檔，以瞭解如何測量每個查詢的 ru、取得執行統計資料來微調查詢等等：

* [使用 .NET SDK 取得 SQL 查詢執行計量](profile-sql-api-query.md)
* [使用 Azure Cosmos DB 調整查詢效能](sql-api-sql-query-metrics.md)
* [.NET SDK 的效能秘訣](performance-tips.md)
