---
title: 針對使用適用于 MongoDB 的 Azure Cosmos DB API 時的查詢問題進行疑難排解
description: 瞭解如何識別、診斷 Azure Cosmos DB 的 MongoDB API 查詢問題，以及對其進行疑難排解。
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/12/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 7a34b4a3a0f9fe75b5e252f20a8b0924b0ce01d7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488379"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>針對使用適用于 MongoDB 的 Azure Cosmos DB API 時的查詢問題進行疑難排解

本文會逐步解說針對 Azure Cosmos DB 中查詢進行疑難排解的一般建議方法。 雖然您不應將本文所述的步驟視為潛在查詢問題的完整防禦方法，但我們已在此納入最常見的效能秘訣。 您應使用本文作為在 Azure Cosmos DB 的 MongoDB API 中針對緩慢或昂貴的查詢進行疑難排解的起點。 如果您使用 Azure Cosmos DB core (SQL) API，請參閱《 [SQL API 查詢疑難排解指南》](troubleshoot-query-performance.md) 一文。

Azure Cosmos DB 中的查詢優化廣泛分類如下：

- 減少查詢要求單位 (RU) 費用的最佳化
- 只降低延遲的最佳化

如果您減少查詢的 RU 費用，通常也會降低延遲。

本文提供的範例可讓您使用 [營養資料集](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)來重新建立。

> [!NOTE] 
> 本文假設您使用3.6 版的 Azure Cosmos DB "s API for MongoDB。 某些在3.2 版中執行效能不佳的查詢，在3.6 版中有顯著的改善。 藉由 [提出支援要求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)來升級至3.6 版。

## <a name="use-explain-command-to-get-metrics"></a>使用 $explain 命令取得度量

當您在 Azure Cosmos DB 中優化查詢時，第一個步驟一律是 [取得查詢的 RU 費用](find-request-unit-charge-mongodb.md) 。 作為粗略的指導方針，您應該探索如何針對費用大於 50 RU 的查詢降低 RU 費用。 

除了取得 RU 費用之外，您還應該使用 `$explain` 命令來取得查詢和索引使用計量。 以下範例會執行查詢，並使用 `$explain` 命令來顯示查詢和索引使用計量：

**$explain 命令：**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**輸出：**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`$explain`命令輸出很冗長，而且有查詢執行的詳細資訊。 但一般而言，在優化查詢效能時，您應該專注于幾個部分：

| 計量 | 描述 | 
| ------ | ----------- |
| `timeInclusiveMS` | 後端查詢延遲 |
| `pathsIndexed` | 顯示查詢使用的索引 | 
| `pathsNotIndexed` | 顯示查詢可能已使用的索引（如果有的話） | 
| `shardInformation` | 特定[實體資料分割](./partitioning-overview.md#physical-partitions)的查詢效能摘要 | 
| `retrievedDocumentCount` | 查詢引擎載入的檔數目 | 
| `outputDocumentCount` | 查詢結果中傳回的檔數目 | 
| `estimatedDelayFromRateLimitingInMilliseconds` | 由於速率限制而預估額外的查詢延遲 | 

取得查詢計量之後，請將與查詢的進行比較 `retrievedDocumentCount` `outputDocumentCount` 。 使用此比較來識別本文中要檢查的相關章節。 `retrievedDocumentCount`是查詢引擎需要載入的檔數目。 `outputDocumentCount`是查詢結果所需的檔數目。 如果的高度 `retrievedDocumentCount`  高於 `outputDocumentCount` ，表示查詢中至少有一個部分無法使用索引，且需要進行掃描。

請參閱下列各節，以了解您案例適用的相關查詢最佳化。

### <a name="querys-ru-charge-is-too-high"></a>查詢的 RU 費用過高

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>擷取的文件計數明顯高於輸出文件計數

- [包含必要的索引。](#include-necessary-indexes)

- [瞭解哪些匯總作業使用索引。](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>擷取的文件計數大約等於輸出文件計數

- [將跨分割區查詢最小化。](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>查詢的 RU 費用是可接受的，但延遲仍然太高

- [改善鄰近性。](#improve-proximity)

- [增加佈建的輸送量。](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>已取出檔計數超過輸出檔案計數的查詢

 `retrievedDocumentCount`是查詢引擎需要載入的檔數目。 `outputDocumentCount`是查詢所傳回的檔數目。 如果的高度 `retrievedDocumentCount` 高於 `outputDocumentCount` ，表示查詢中至少有一個部分無法使用索引，且需要進行掃描。

以下是未完全由索引處理的掃描查詢範例：

**$explain 命令：**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**輸出：**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`retrievedDocumentCount` (8618) 明顯高於 `outputDocumentCount` (1) ，表示此查詢需要檔掃描。 

### <a name="include-necessary-indexes"></a>包含必要的索引

您應該檢查 `pathsNotIndexed` 陣列，並新增這些索引。 在此範例中， `foodGroup` `description` 應該為路徑和編制索引。

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

Azure Cosmos DB 的 MongoDB API 中編制索引的最佳作法與 MongoDB 不同。 在 Azure Cosmos DB 適用于 MongoDB 的 API 中，複合索引只適用于需要以多個屬性有效率排序的查詢。 如果您有多個屬性的篩選準則，則應該為每個屬性建立單一欄位索引。 查詢述詞可以使用多個單一欄位索引。

[萬用字元索引](mongodb-indexing.md#wildcard-indexes) 可以簡化索引編制。 與 MongoDB 不同的是，萬用字元索引可以支援查詢述詞中的多個欄位。 如果您使用一個單一萬用字元索引，而不是為每個屬性建立個別的索引，則查詢效能不會有差異。 為所有屬性新增萬用字元索引是優化所有查詢最簡單的方式。

您可以隨時加入新的索引，而不會影響寫入或讀取可用性。 您可以[追蹤索引轉換進度](./how-to-manage-indexing-policy.md#dotnet-sdk)。

### <a name="understand-which-aggregation-operations-use-the-index"></a>瞭解哪些匯總作業使用索引

在大部分的情況下，Azure Cosmos DB 適用于 MongoDB 的 API 中的匯總作業將會部分使用索引。 通常，查詢引擎會先套用相等和範圍篩選，並使用索引。 套用這些篩選之後，查詢引擎就可以評估其他篩選條件，並在有需要時，藉由載入其餘文件來計算彙總。 

以下為範例：

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

在此情況下，索引可以優化 `$match` 階段。 加入索引 `foodGroup` 可大幅提升查詢效能。 就像在 MongoDB 中一樣，您應該 `$match` 在匯總管線中儘早放置，以充分利用索引。

在 Azure Cosmos DB 適用于 MongoDB 的 API 中，索引不會用於實際的匯總，在此案例中為 `$max` 。 在上加入索引 `version` 不會改善查詢效能。

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>已取出的檔計數等於輸出檔案計數的查詢

如果 `retrievedDocumentCount` 大約等於 `outputDocumentCount` ，則查詢引擎不需要掃描許多不必要的檔。

### <a name="minimize-cross-partition-queries"></a>將跨分割區查詢最小化

在要求單位和資料儲存體需求增加時，Azure Cosmos DB 會使用[資料分割](partitioning-overview.md)來調整個別容器。 每個實體分割區都有個別且獨立的索引。 如果您的查詢具有符合容器分割區索引鍵的相等篩選條件，您就只需要檢查相關分割區的索引。 此最佳化會減少查詢所需的 RU 總數。 [深入瞭解分割區內查詢與跨資料分割查詢之間的差異](how-to-query-container.md)。

如果您已佈建大量的 RU (超過 30,000) 或儲存大量的資料 (大約超過 100 GB)，表示您可能有夠大的容器，可看到查詢 RU 費用大幅縮減。 

您可以檢查 `shardInformation` 陣列，以瞭解每個個別實體資料分割的查詢度量。 唯一值的數目 `shardKeyRangeId` 是需要執行查詢的實體分割區數目。 在此範例中，查詢是在四個實體分割區上執行。 請務必瞭解，執行與索引使用完全無關。 換句話說，跨分割區查詢仍然可以使用索引。

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>減少查詢延遲的最佳化

在許多情況下，RU 費用是可接受的，但查詢延遲仍然太高。 下列各節概述減少查詢延遲的秘訣。 如果您在相同的資料集上多次執行相同的查詢，則每次通常會有相同的 RU 費用。 但是查詢的延遲可能會因查詢執行不同而有所差異。

### <a name="improve-proximity"></a>改善鄰近性

如果從與 Azure Cosmos DB 帳戶不同的區域執行查詢，其延遲會高於在相同區域內執行的查詢。 例如，相較於從 Azure Cosmos DB 所在 Azure 區域內的虛擬機器執行查詢，您在桌上型電腦上執行程式碼時，應該會多出數十或數百毫秒 (或更多) 的延遲。 [在 Azure Cosmos DB 中全域散發資料](tutorial-global-distribution-mongodb.md)，以確保您可以將資料帶到應用程式附近，這是很簡單的工作。

### <a name="increase-provisioned-throughput"></a>增加佈建的輸送量

在 Azure Cosmos DB 中，您佈建的輸送量會以要求單位 (RU) 來測量。 假設您有一個會耗用 5 RU 輸送量的查詢。 例如，如果您佈建 1,000 RU，您就能夠每秒執行該查詢 200 次。 如果您嘗試在沒有足夠的可用輸送量時執行查詢，Azure Cosmos DB 將會限制要求的速率。 Azure Cosmos DB 的 MongoDB API 會在等候一小段時間之後，自動重試此查詢。 節流的要求需要較長的時間，因此增加佈建的輸送量可以改善查詢延遲。

`estimatedDelayFromRateLimitingInMilliseconds`如果您增加輸送量，此值可讓您瞭解可能的延遲優勢。

## <a name="next-steps"></a>後續步驟

* [針對 SQL API)  (查詢效能進行疑難排解 ](troubleshoot-query-performance.md)
* [管理 Azure Cosmos DB 的 MongoDB API 中的編制索引](mongodb-indexing.md)