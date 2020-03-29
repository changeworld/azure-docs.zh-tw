---
title: 使用執行設定檔評估 Azure Cosmos DB Gremlin API 中的查詢
description: 瞭解如何使用執行設定檔步驟對 Gremlin 查詢進行故障排除和改進。
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441843"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>如何使用執行設定檔步驟來評估您的 Gremlin 查詢

本文概述如何使用 Azure Cosmos DB Gremlin API 圖形資料庫的執行設定檔步驟。 此步驟提供疑難排解和查詢最佳化的相關資訊，並與可針對 Cosmos DB Gremlin API 帳戶執行的任何 Gremlin 查詢相容。

要使用此步驟，只需在 Gremlin 查詢末尾追加`executionProfile()`函式呼叫即可。 **將執行 Gremlin 查詢**，操作的結果將返回具有查詢執行設定檔的 JSON 回應物件。

例如：

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

調用`executionProfile()`步驟後，回應將是一個 JSON 物件，其中包括執行的 Gremlin 步驟、所花的總時間以及語句產生的 Cosmos DB 運行時運算子的陣列。

> [!NOTE]
> 在 Apache Tinkerpop 規範中未定義執行設定檔的此實現。 它特定于 Azure 宇宙 DB Gremlin API 的實現。


## <a name="response-example"></a>回應範例

以下是將返回的輸出的批帶示例：

> [!NOTE]
> 此示例帶有解釋回應的一般結構的注釋。 實際執行設定檔回應不包含任何注釋。

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> 執行設定檔步驟將執行格雷姆林查詢。 這包括 或`addV``addE`步驟，這將導致創建，並將提交查詢中指定的更改。 因此，Gremlin 查詢生成的請求單位也將收費。

## <a name="execution-profile-response-objects"></a>執行設定檔回應物件

執行設定檔（）函數的回應將生成具有以下結構的 JSON 物件的層次結構：
  - **格雷姆林操作物件**：表示執行的整個格雷姆林操作。 包含以下屬性。
    - `gremlin`：執行的顯式格雷姆林語句。
    - `totalTime`：發生步驟執行的時間（以毫秒為單位）。 
    - `metrics`： 包含為完成查詢而執行的每個 Cosmos DB 運行時運算子的陣列。 此清單按執行順序排序。
    
  - **Cosmos DB 運行時運算子**：表示整個 Gremlin 操作的每個元件。 此清單按執行順序排序。 每個物件都包含以下屬性：
    - `name`：運算子的名稱。 這是計算和執行的步驟類型。 請閱讀下表中的更多內容。
    - `time`：給定運算子所花費的時間量（以毫秒為單位）。
    - `annotations`：包含特定于已執行的運算子的其他資訊。
    - `annotations.percentTime`：執行特定運算子所花的總時間的百分比。
    - `counts`：此運算子從存儲層返回的物件數。 這包含在 其中的`counts.resultCount`標量值中。
    - `storeOps`：表示可以跨一個或多個分區的存儲操作。
    - `storeOps.fanoutFactor`：表示此特定存儲操作訪問的分區數。
    - `storeOps.count`：表示此存儲操作返回的結果數。
    - `storeOps.size`： 表示給定存儲操作結果的大小（以位元組為單位）。

宇宙 DB 格雷姆林運行時運算子|描述
---|---
`GetVertices`| 此步驟從持久性層獲取一組謂物件。 
`GetEdges`| 此步驟獲取與一組頂點連續的邊。 此步驟可能會導致一個或多個存儲操作。
`GetNeighborVertices`| 此步驟獲取連接到一組邊的頂點。 邊包含其源頂點和目標頂點的分區鍵和 ID。
`Coalesce`| 每當執行 Gremlin 步驟時，`coalesce()`此步驟將考慮對兩個操作的評估。
`CartesianProductOperator`| 此步驟計算兩個資料集之間的點菜產品。 通常在使用謂詞`to()`或使用`from()`時執行。
`ConstantSourceOperator`| 此步驟計算運算式以生成常量值。
`ProjectOperator`| 此步驟使用前面的操作的結果準備和序列化回應。
`ProjectAggregation`| 此步驟為聚合操作準備並序列化回應。

> [!NOTE]
> 此清單將繼續更新，因為添加新運算子。

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>有關如何分析執行設定檔回應的示例

以下是可以使用執行設定檔回應發現的常見優化示例：
  - 盲扇出查詢。
  - 未篩選查詢。

### <a name="blind-fan-out-query-patterns"></a>盲扇出查詢模式

從**區域圖形**假設以下執行設定檔回應：

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

可以從中得出以下結論：
- 查詢是單個 ID 查找，因為 Gremlin 語句遵循模式`g.V('id')`。
- 從`time`該指標來看，此查詢的延遲似乎很高，因為[單個點讀取操作的延遲超過 10 毫秒](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)。
- 如果我們`storeOps`查看物件，我們可以看到`fanoutFactor`is `5`，這意味著此操作可以訪問 5[個分區](https://docs.microsoft.com/azure/cosmos-db/partition-data)。

作為此分析的結論，我們可以確定第一個查詢訪問的分區超過必要的分區。 這可以通過將查詢中的分區鍵指定為謂詞來解決。 這將導致延遲減少，每個查詢的成本更低。 深入了解[圖表分割](graph-partitioning.md)。 更優化的查詢是`g.V('tt0093640').has('partitionKey', 't1001')`。

### <a name="unfiltered-query-patterns"></a>未篩選的查詢模式

比較以下兩個執行設定檔回應。 為簡單起見，這些示例使用單個區域圖形。

第一個查詢檢索帶有標籤`tweet`的所有頂點，然後獲取其連續的頂點：

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

在流覽連續的頂點之前，請注意同一查詢的設定檔，`has('lang', 'en')`但現在使用附加篩選器：

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

但是，這兩個查詢達到相同的結果，但是，第一個查詢將需要更多的請求單位，因為它需要在查詢相鄰項之前反覆運算更大的初始資料集。 在比較來自兩個回應的以下參數時，我們可以看到此行為的指示器：
- 第`metrics[0].time`一個回應中的值較高，這表明此單步驟的解析時間更長。
- 該`metrics[0].counts.resultsCount`值在第一個回應中也較高，這表明初始工作資料集較大。

## <a name="next-steps"></a>後續步驟
* 瞭解 Azure Cosmos DB 中[支援的 Gremlin 功能](gremlin-support.md)。 
* 瞭解有關 Azure[宇宙 DB 中的 Gremlin API](graph-introduction.md)的更多資訊。
