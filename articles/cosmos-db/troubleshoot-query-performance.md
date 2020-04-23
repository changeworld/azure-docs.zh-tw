---
title: 使用 Azure 宇宙 DB 時解決查詢問題
description: 瞭解如何識別、診斷和排除 Azure Cosmos DB SQL 查詢問題。
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/20/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 4a8b61f3719a60af567d10f8839987e613babc9e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870461"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>使用 Azure 宇宙 DB 時解決查詢問題

本文介紹了一種常規推薦的方法,用於在 Azure Cosmos DB 中排除查詢故障。 儘管您不應將本文中概述的步驟視為針對潛在查詢問題的完整防禦,但此處包含了最常見的性能提示。 應使用本文作為在 Azure Cosmos DB 核心 (SQL) API 中解決慢速或昂貴查詢的起始位置。 您還可以使用[診斷日誌](cosmosdb-monitor-resource-logs.md)來標識速度慢或消耗大量輸送量的查詢。

您可以在 Azure Cosmos DB 中對查詢最佳化進行廣泛分類:

- 減少查詢要求儲存(RU) 費用的優化
- 只減少延遲的優化

如果減少查詢的 RU 費用,幾乎肯定會減少延遲。

本文提供了可以使用[營養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)數據集重新創建的範例。

## <a name="common-sdk-issues"></a>常見 SDK 問題

- 為了獲得最佳性能,請遵循[效能提示](performance-tips.md)。
    > [!NOTE]
    > 為提高性能,我們建議處理 Windows 64 位主機。 SQL SDK 包括一個本機 ServiceInterop.dll,用於在本地解析和優化查詢。 服務 Interop.dll 僅在 Windows x64 平臺上受支援。 對於 Linux 和其他不支援的平臺,其中 ServiceInterop.dll 不可用,將對閘道進行額外的網路調用以獲取優化的查詢。
- 您可以為查詢設定`MaxItemCount`a,但不能指定最小項計數。
    - 代碼應處理任何頁面大小,從零到`MaxItemCount`。
    - 頁面中的項目將始終小於指定的`MaxItemCount`。 但是,`MaxItemCount`嚴格來說,結果可能少於此金額。
- 有時,即使將來頁上有結果,查詢也可能有空頁。 原因可能是:
    - SDK 可以執行多個網路調用。
    - 查詢可能需要很長時間才能檢索文檔。
- 所有查詢都有一個延續權杖,允許查詢繼續。 請確保完全耗盡查詢。 查看 SDK 範例,`while`並`FeedIterator.HasMoreResults`使用循環來耗盡整個查詢。

## <a name="get-query-metrics"></a>取得查詢指標

在 Azure Cosmos DB 中優化查詢時,第一步始終是[取得查詢的查詢指標](profile-sql-api-query.md)。 這些指標也可通過 Azure 門戶獲得:

[![取得查詢指標](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

獲取查詢指標后,將檢索的文檔計數與查詢的輸出文檔計數進行比較。 使用此比較可以確定本文中要審閱的相關部分。

檢索的文件計數是查詢需要載入的文件數。 輸出文件計數是查詢結果所需的文件數。 如果檢索的文檔計數明顯高於輸出文檔計數,則查詢中至少有一部分無法使用索引,需要執行掃描。

請參閱以下各節以瞭解方案的相關查詢優化。

### <a name="querys-ru-charge-is-too-high"></a>查詢的 RU 費用過高

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>檢索的文件計數明顯高於輸出文件計數

- [在索引策略中包括必要的路徑。](#include-necessary-paths-in-the-indexing-policy)

- [瞭解哪些系統函數使用索引。](#understand-which-system-functions-use-the-index)

- [瞭解使用索引的聚合查詢。](#understand-which-aggregate-queries-use-the-index)

- [修改同時具有篩選器和 ORDER BY 子句的查詢。](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

- [使用子查詢優化 JOIN 表達式。](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>檢索的文件計數大致等於輸出文件計數

- [避免跨分區查詢。](#avoid-cross-partition-queries)

- [優化具有多個屬性篩選器的查詢。](#optimize-queries-that-have-filters-on-multiple-properties)

- [修改同時具有篩選器和 ORDER BY 子句的查詢。](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>查詢的 RU 電荷是可以接受的,但延遲仍然過高

- [提高接近性。](#improve-proximity)

- [增加預配輸送量。](#increase-provisioned-throughput)

- [增加最大貨幣。](#increase-maxconcurrency)

- [增加最大緩衝項目計數。](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>檢索的文件計數超過輸出文件計數的查詢

 檢索的文件計數是查詢需要載入的文件數。 輸出文件計數是查詢結果所需的文件數。 如果檢索的文檔計數明顯高於輸出文檔計數,則查詢中至少有一部分無法使用索引,需要執行掃描。

下面是一個未完全由索引提供的掃描查詢範例:

查詢：

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

查詢指標:

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

檢索的文件計數 (60,951) 明顯高於輸出文檔計數 (7),因此此查詢需要執行掃描。 在這種情況下,系統函數[UPPER()](sql-query-upper.md)不使用索引。

### <a name="include-necessary-paths-in-the-indexing-policy"></a>在索引原則中包括必要的路徑

索引策略應涵蓋子句、`WHERE``ORDER BY``JOIN`子句和大多數系統函數中包含的任何屬性。 索引策略中指定的路徑應與 JSON 文檔中的屬性匹配(區分大小寫)。

如果在[營養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)資料集上執行簡單查詢,則當子句中的屬性編制索`WHERE`引時, 觀察到 RU 費用要低得多:

#### <a name="original"></a>原始

查詢：

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

索引原則:

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

**RU 充電:** 409.51 RU

#### <a name="optimized"></a>最佳化的

更新索引原則:

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

**RU 充電:** 2.98 RU

您可以隨時向索引策略添加屬性,而不會影響寫入可用性或性能。 如果向索引添加新屬性,則使用 該屬性的查詢將立即使用新的可用索引。 查詢將在生成新索引時使用。 因此,在索引重建進行中,查詢結果可能不一致。 如果對新屬性進行索引,則在索引重建期間,僅使用現有索引的查詢不會受到影響。 您可以[追蹤索引轉換進度](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)。

### <a name="understand-which-system-functions-use-the-index"></a>瞭解哪些系統函式使用索引

如果表達式可以轉換為字串值範圍,則可以使用索引。 否則,它不能。

下面是一些可以使用索引的常見字串函數的清單:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr、num_expr、num_expr) = str_expr,但前提是第一個num_expr為 0

以下是一些不使用索引且必須載入每個文件的常見系統函數:

| **系統功能**                     | **優化思路**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | 使用 Azure 搜索進行全文搜索。                        |
| 上/下                             | 而不是使用系統函數來規範化數據進行比較,而是在插入時規範化套管。 類似```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'```查詢```SELECT * FROM c WHERE c.name = 'BOB'```變更 。 |
| 數學函數(非聚合) | 如果需要在查詢中頻繁計算值,請考慮將該值存儲為 JSON 文檔中的屬性。 |

------

查詢的其他部分可能仍使用索引,即使系統功能不。

### <a name="understand-which-aggregate-queries-use-the-index"></a>瞭解使用索引的聚合查詢

在大多數情況下,Azure Cosmos DB 中的聚合系統函數將使用索引。 但是,根據聚合查詢中的篩選器或其他子句,可能需要查詢引擎來載入大量文檔。 通常,查詢引擎將首先應用相等和範圍篩選器。 應用這些篩選器后,查詢引擎可以評估其他篩選器,並根據需要載入剩餘文檔以計算聚合。

例如,給定這兩個範例查詢,具有相等性和`CONTAINS`系統函數篩選器的查詢通常比僅具有系統函數篩選器的`CONTAINS`查詢更有效。 這是因為首先應用相等性篩選器,並在需要載入文檔之前使用索引來為更昂貴`CONTAINS`的篩選器載入。

只`CONTAINS`具有篩選器查詢 - 較高的 RU 費用:

```sql
SELECT COUNT(1) FROM c WHERE CONTAINS(c.description, "spinach")
```

同時使用相等篩選器和`CONTAINS`篩選器的查詢 - 較低的 RU 電荷:

```sql
SELECT AVG(c._ts) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

下面是不會完全使用索引的聚合查詢的其他範例:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>具有不使用索引的系統函數的查詢

您應該參考相關[系統函數的頁面](sql-query-system-functions.md),看看它是否使用索引。

```sql
SELECT MAX(c._ts) FROM c WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>使用使用者定義的函數聚合查詢(UDF)

```sql
SELECT AVG(c._ts) FROM c WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>與群組 BY 的查詢

RU`GROUP BY`的電荷將`GROUP BY`隨著子句中屬性基數的增加而增加。 在此範例中,查詢引擎必須載入與`c.foodGroup = "Sausages and Luncheon Meats"`篩選器匹配的每個文件,以便 RU 電荷預期很高。

```sql
SELECT COUNT(1) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" GROUP BY c.description
```

如果計劃經常運行相同的聚合查詢,則使用[Azure Cosmos DB 更改源](change-feed.md)構建即時具體檢視可能比運行單個查詢更有效。

### <a name="modify-queries-that-have-both-a-filter-and-an-order-by-clause"></a>變更同時具有篩選器與 ORDER BY 子句的查詢

儘管具有篩選器和`ORDER BY`子句的查詢通常使用範圍索引,但如果可以從複合索引中提供它們,則它們的效率會更高。 除了修改索引策略外,還應將複合索引中的所有屬性添加到子`ORDER BY`句。 對查詢的此更改將確保它使用複合索引。  您可以通過對[營養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)資料集執行查詢來觀察影響:

#### <a name="original"></a>原始

查詢：

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

索引原則:

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

**RU 充電:** 44.28 RU

#### <a name="optimized"></a>最佳化的

更新查詢(包括子句中的`ORDER BY`兩個屬性):

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

更新索引原則:

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

**RU 充電:** 8.86 RU

### <a name="optimize-join-expressions-by-using-a-subquery"></a>使用子查詢優化 JOIN 運算式
多值子查詢可以通過在每個選擇`JOIN`多 表達式之後推送謂詞來優化表達式,`WHERE`而不是在子句中的所有交叉聯接之後。

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

**RU 充電:** 167.62 RU

對於此查詢,索引將匹配任何標籤上的名稱為「嬰兒配方奶粉」、營養值大於 0 且服務量大於 1 的文檔。 此處`JOIN`的運算式將在應用任何篩選器之前為每個匹配文檔執行所有標記、營養和服務陣列項的交叉產品。 然後`WHERE`,子句將在`<c, t, n, s>`每個 元組上應用篩選器謂詞。

例如,如果匹配的文檔在三個陣列中每個陣列中有 10 個專案,它將擴展到 1 x 10 x 10 x 10(即 1,000 個)tup。 此處使用子查詢有助於在加入下一個運算式之前篩選出聯接的陣列項。

此查詢等效於前一個查詢,但使用子查詢:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU 充電:** 22.17 RU

假設標記陣中只有一個專案與篩選器匹配,並且營養和服務陣列有五個專案。 運算`JOIN`式 將擴展到 1 x 1 x 5 x 5 = 25 項,而第一個查詢中的 1,000 項。

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>檢索的文件計數等於輸出文件計數的查詢

如果檢索的文檔計數大致等於輸出文檔計數,則查詢不必掃描許多不必要的文檔。 對於許多查詢(如使用 TOP 關鍵字的查詢),檢索的文檔計數可能超過輸出文檔計數 1。 你不需要擔心這個。

### <a name="avoid-cross-partition-queries"></a>避免跨分區查詢

Azure Cosmos DB 使用[分區](partitioning-overview.md)來縮放單個容器,因為請求單位和數據存儲需求增加。 每個物理分區都有一個單獨的索引。 如果查詢具有與容器分區鍵匹配的相等篩選器,則只需檢查相關分區的索引。 此優化減少了查詢所需的總 R。

如果預配的 RU 數量很大(超過 30,000 個)或存儲了大量數據(超過 100 GB),則可能有足夠的容器,可以顯著減少查詢 RU 費用。

例如,如果建立具有分區金鑰 foodGroup 的容器,則以下查詢只需檢查單個物理分區:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

這些查詢還將透過查詢中新增分區鍵進行優化:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

分割區鍵上具有範圍篩選器的查詢,或者分區鍵上沒有任何篩選器的查詢,將需要檢查每個物理分區的索引的結果:

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>優化具有多個屬性篩選器的查詢

儘管具有多個屬性篩選器的查詢通常使用範圍索引,但如果可以從複合索引中提供篩選器,則它們的效率會更高。 對於少量數據,此優化不會產生重大影響。 然而,對於大量數據來說,它可能是有用的。 每個複合索引最多只能優化一個非相等篩選器。 如果查詢有多個非相等篩選器,請選擇其中一個將使用複合索引的篩選器。 其餘將繼續使用範圍索引。 非相等性篩選器必須最後在複合索引中定義。 [瞭解有關複合索引的更多](index-policy.md#composite-indexes)。

下面是一些可以使用複合索引進行優化的查詢示例:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

下面是相關的複合索引:

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

在許多情況下,當查詢延遲仍然過高時,RU 費用可能是可以接受的。 以下各節概述了減少查詢延遲的提示。 如果在同一數據集上多次運行同一查詢,則每次將具有相同的 RU 費用。 但是查詢延遲可能因查詢執行而異。

### <a name="improve-proximity"></a>提高鄰近度

從與 Azure Cosmos DB 帳戶不同的區域運行的查詢的延遲將高於在同一區域內運行的查詢。 例如,如果在臺式電腦上運行代碼,則預計延遲比查詢來自與 Azure Cosmos DB 位於同一 Azure 區域中的虛擬機的延遲高出數十毫秒或數百毫秒。 在[Azure Cosmos DB 中全域分發數據](distribute-data-globally.md)非常簡單,以確保數據更接近應用。

### <a name="increase-provisioned-throughput"></a>增加預先配吐量

在 Azure Cosmos DB 中,預配輸送量以請求單位 (R) 為單位進行測量。 假設您的查詢消耗了 5 個 R 的輸送量。 例如,如果預配 1,000 個 R,則可以每秒運行該查詢 200 次。 如果嘗試在沒有足夠的可用輸送量時運行查詢,Azure Cosmos DB 將返回 HTTP 429 錯誤。 任何當前核心 (SQL) API SDK 將在短暫等待後自動重試此查詢。 限制請求需要更長的時間,因此增加預配輸送量可以提高查詢延遲。 您可以在 Azure 門戶的 **「指標」** 邊欄選項卡上觀察[受限制的請求總數](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors)。

### <a name="increase-maxconcurrency"></a>增加最大貨幣

並行查詢通過並行查詢多個分區來工作。 但是,單個分區集合中的數據相對於查詢進行串行提取。 因此,如果將 MaxConcurrency 設置為分區數,則只要所有其他系統條件保持不變,則最有可能實現性能最優的查詢。 如果您不知道分區的數量,則可以將 MaxConcurrency(或舊 SDK 版本中的 MaxAtoins Parallelism)設置為高數位。 系統將選擇最小(分區數,使用者提供的輸入)作為最大並行度。

### <a name="increase-maxbuffereditemcount"></a>增加最大緩衝項目計數

查詢旨在在用戶端處理當前結果批處理時預提取結果。 預提取有助於提高查詢的總體延遲。 設置 MaxBufferedItemCount 會限制預獲取結果的數量。 如果將此值設置為返回的預期結果數(或較高的數位),則查詢可以從預提取中獲得最大好處。 如果將此值設置為 -1,系統將自動確定要緩衝的項數。

## <a name="next-steps"></a>後續步驟
有關如何測量每個查詢的 R,獲取用於調整查詢的執行統計資訊的資訊,請參閱以下文章,以及詳細資訊:

* [使用 .NET SDK 取得 SQL 查詢執行指標](profile-sql-api-query.md)
* [使用 Azure Cosmos DB 調整查詢效能](sql-api-sql-query-metrics.md)
* [.NET SDK 的效能秘訣](performance-tips.md)
