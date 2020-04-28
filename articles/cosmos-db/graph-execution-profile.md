---
title: 使用執行設定檔來評估 Azure Cosmos DB Gremlin API 中的查詢
description: 瞭解如何使用執行設定檔步驟來疑難排解和改善您的 Gremlin 查詢。
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75441843"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>如何使用執行設定檔步驟來評估您的 Gremlin 查詢

本文概述如何使用 Azure Cosmos DB Gremlin API 圖形資料庫的執行設定檔步驟。 此步驟提供疑難排解和查詢最佳化的相關資訊，並與可針對 Cosmos DB Gremlin API 帳戶執行的任何 Gremlin 查詢相容。

若要使用此步驟，只要在`executionProfile()` Gremlin 查詢的結尾附加函式呼叫即可。 **您的 Gremlin 查詢將會執行**，且作業的結果會傳回具有查詢執行設定檔的 JSON 回應物件。

例如：

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

呼叫`executionProfile()`步驟之後，回應將會是 JSON 物件，其中包含已執行的 Gremlin 步驟、所花費的總時間，以及語句產生的 Cosmos DB 執行時間運算子陣列。

> [!NOTE]
> Apache Tinkerpop 規格中未定義執行設定檔的這個實作為。 這是 Azure Cosmos DB Gremlin API 的實作為特定。


## <a name="response-example"></a>回應範例

以下是將傳回之輸出的批註範例：

> [!NOTE]
> 這個範例會加上批註，說明回應的一般結構。 實際的 executionProfile 回應不會包含任何批註。

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
> ExecutionProfile 步驟將會執行 Gremlin 查詢。 這包括`addV`或`addE`步驟，這會導致建立並認可查詢中指定的變更。 因此，Gremlin 查詢所產生的要求單位也會收費。

## <a name="execution-profile-response-objects"></a>執行設定檔回應物件

ExecutionProfile （）函數的回應將會產生具有下列結構的 JSON 物件階層：
  - **Gremlin operation 物件**：代表已執行的整個 Gremlin 作業。 包含下列屬性。
    - `gremlin`：已執行的明確 Gremlin 語句。
    - `totalTime`：執行中所產生之步驟的時間（以毫秒為單位）。 
    - `metrics`：陣列，其中包含為了滿足查詢而執行的每個 Cosmos DB 執行時間運算子。 這份清單會依執行順序排序。
    
  - **Cosmos DB 執行時間運算子**：代表整個 Gremlin 作業的每個元件。 這份清單會依執行順序排序。 每個物件都包含下列屬性：
    - `name`：操作員的名稱。 這是已評估和執行的步驟類型。 如需詳細資訊，請參閱下表。
    - `time`：指定運算子所花的時間量（以毫秒為單位）。
    - `annotations`：包含已執行之運算子特有的其他資訊。
    - `annotations.percentTime`：執行特定運算子所花費的總時間百分比。
    - `counts`：由這個運算子從儲存層傳回的物件數目。 這會包含在內`counts.resultCount`的純量值中。
    - `storeOps`：表示可以跨越一個或多個分割區的儲存體作業。
    - `storeOps.fanoutFactor`：表示此特定儲存體作業存取的資料分割數目。
    - `storeOps.count`：表示此儲存體作業傳回的結果數目。
    - `storeOps.size`：代表給定儲存作業的結果大小（以位元組為單位）。

Cosmos DB Gremlin 執行時間運算子|描述
---|---
`GetVertices`| 此步驟會從持續性層取得一組前提的物件。 
`GetEdges`| 此步驟會取得與一組頂點連續的邊緣。 此步驟可能會產生一或多個儲存體作業。
`GetNeighborVertices`| 此步驟會取得連接到一組邊緣的頂點。 邊緣包含其來源和目標頂點的分割區索引鍵和識別碼。
`Coalesce`| 此步驟會說明每次執行`coalesce()` Gremlin 步驟時，評估兩項作業。
`CartesianProductOperator`| 此步驟會計算兩個資料集之間的笛卡兒乘積。 通常會在每次`to()`使用`from()`述詞或時執行。
`ConstantSourceOperator`| 此步驟會計算運算式，以產生常數值做為結果。
`ProjectOperator`| 此步驟會使用先前作業的結果來準備和序列化回應。
`ProjectAggregation`| 此步驟會準備並序列化匯總作業的回應。

> [!NOTE]
> 新增操作員時，此清單會繼續更新。

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>如何分析執行設定檔回應的範例

以下是使用執行設定檔回應可以找出的一般優化範例：
  - 盲人展開查詢。
  - 未篩選的查詢。

### <a name="blind-fan-out-query-patterns"></a>盲人展開查詢模式

從**分割的圖形**假設下列執行設定檔回應：

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

您可以從它進行下列結論：
- 查詢是單一識別碼查詢，因為 Gremlin 語句會遵循模式`g.V('id')`。
- 從`time`度量判斷，此查詢的延遲似乎很高，因為它的[10 毫秒只是用於單一點讀取](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)作業。
- 如果`storeOps`我們查看物件，我們可以`fanoutFactor`看到是`5`，這表示這項作業已存取[5 個](https://docs.microsoft.com/azure/cosmos-db/partition-data)資料分割。

這項分析的結論是，我們可以判斷第一個查詢所存取的資料分割數目超出所需的數目。 將查詢中的分割索引鍵指定為述詞，即可解決此問題。 這會導致每個查詢的延遲較少且成本較低。 深入了解[圖表分割](graph-partitioning.md)。 更理想的查詢是`g.V('tt0093640').has('partitionKey', 't1001')`。

### <a name="unfiltered-query-patterns"></a>未篩選的查詢模式

比較下列兩個執行設定檔回應。 為了簡單起見，這些範例會使用單一分割的圖形。

第一個查詢會抓取具有標籤`tweet`的所有頂點，然後取得其相鄰頂點：

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

在探索連續的頂點之前，請注意相同查詢的設定檔， `has('lang', 'en')`但現在有額外的篩選準則：

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

這兩個查詢已達到相同的結果，但是第一個會需要更多的要求單位，因為在查詢連續的專案之前，需要先反覆運算較大的初始資料集。 比較兩個回應中的下列參數時，我們可以看到此行為的指標：
- 第`metrics[0].time`一個回應中的值較高，表示這個單一步驟需要較長的時間來解決。
- `metrics[0].counts.resultsCount`值在第一個回應中也會更高，這表示初始工作資料集較大。

## <a name="next-steps"></a>後續步驟
* 瞭解 Azure Cosmos DB 中[支援的 Gremlin 功能](gremlin-support.md)。 
* 在 Azure Cosmos DB 深入瞭解[GREMLIN API](graph-introduction.md)。
