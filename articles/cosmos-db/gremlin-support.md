---
title: Azure Cosmos DB Gremlin 支援
description: 從 Apache TinkerPop 了解 Gremlin 語言。 了解 Azure Cosmos DB 提供哪些可用的功能和步驟
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 04/23/2020
ms.author: jasonh
ms.openlocfilehash: 2629cfc40a9f3c0745df78d9a22883be8476beb9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91409739"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Azure Cosmos DB Gremlin graph 支援
Azure Cosmos DB 支援 [Apache Tinkerpop 的](https://tinkerpop.apache.org)圖形周遊語言，稱為 [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps)。 您可以使用 Gremlin 語言建立圖表實體 (頂點和邊緣)、修改這些實體內的屬性、執行查詢和周遊，以及刪除實體。 

在本文中，我們提供 Gremlin 的快速逐步解說，並列舉 Gremlin API 所支援的 Gremlin 功能。

## <a name="compatible-client-libraries"></a>相容的用戶端程式庫

下表顯示您可以運用在 Azure Cosmos DB 上的常用 Gremlin 驅動程式︰

| 下載 | 來源 | 開始使用 | 支援的連接器版本 |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.4.6/reference/#gremlin-DotNet) | [GitHub 上的 Gremlin.NET](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [使用 .NET 建立圖表](create-graph-dotnet.md) | 3.4.6 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [使用 JAVA 建立圖表](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [GitHub 上的 Gremlin-JavaScript](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [使用 Node.js 建立圖表](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [GitHub 上的 Gremlin-Python](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [使用 Python 建立圖表](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [GitHub 上的 Gremlin-PHP](https://github.com/PommeVerte/gremlin-php) | [使用 PHP 建立圖表](create-graph-php.md) | 3.1.0 |
| [Gremlin 主控台](https://tinkerpop.apache.org/downloads.html) | [TinkerPop 文件](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [使用 Gremlin 主控台建立圖表](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>支援的圖形物件
TinkerPop 是一套涵蓋各種圖表技術的標準。 因此，它採用標準術語來描述圖表提供者所提供的功能。 Azure Cosmos DB 提供持續、高度並行、可寫入的圖表資料庫，可分割至多個伺服器或叢集。 

下表列出 Azure Cosmos DB 所實作的 TinkerPop 功能︰ 

| 類別 | Azure Cosmos DB 實作 |  注意 | 
| --- | --- | --- |
| Graph 功能 | 提供 Persistence 和 ConcurrentAccess。 設計為支援交易 | 可以透過 Spark 連接器實作電腦方法。 |
| 變數功能 | 支援布林值、整數、位元組、Double、Float、整數、Long、字串 | 支援基本類型、透過資料模型而與複雜類型相容 |
| 頂點功能 | 支援 RemoveVertices、MetaProperties、AddVertices、MultiProperties、StringIds、UserSuppliedIds、AddProperty、RemoveProperty  | 支援建立、修改和刪除端點 |
| 頂點屬性功能 | StringIds、UserSuppliedIds、AddProperty、RemoveProperty、BooleanValues、ByteValues、DoubleValues、FloatValues、IntegerValues、LongValues、StringValues | 支援建立、修改和刪除頂點屬性 |
| 邊緣功能 | AddEdges、RemoveEdges、StringIds、UserSuppliedIds、AddProperty、RemoveProperty | 支援建立、修改和刪除邊緣 |
| 邊緣屬性功能 | Properties、BooleanValues、ByteValues、DoubleValues、FloatValues、IntegerValues、LongValues、StringValues | 支援建立、修改和刪除邊緣屬性 |

## <a name="gremlin-wire-format"></a>Gremlin 電傳格式

從 Gremlin 作業傳回結果時，Azure Cosmos DB 會使用 JSON 格式。 Azure Cosmos DB 目前支援 JSON 格式。 例如，下列程式碼片段顯示從 Azure Cosmos DB 傳回用戶端  之頂點的 JSON 表示法：

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

JSON 格式用於頂點的屬性說明如下︰

| 屬性 | 描述 | 
| --- | --- | --- |
| `id` | 頂點的識別碼。 必須是唯一的 (適合的話，與 `_partition` 的值結合)。 如果未提供任何值，則會自動使用 GUID 來提供 | 
| `label` | 頂點的標籤。 此屬性可用來描述實體類型。 |
| `type` | 用來區別頂端和非圖表文件 |
| `properties` | 與頂點相關聯的使用者定義屬性包。 每個屬性可以有多個值。 |
| `_partition` | 頂點的資料分割索引鍵。 用於[圖表分割](graph-partitioning.md)。 |
| `outE` | 此屬性包含頂點的外邊緣清單。 儲存頂點的相鄰資訊可以加速周遊。 邊緣會根據標籤而分組。 |

邊緣還包含下列資訊，有助於瀏覽至圖表的其他部分。

| 屬性 | 描述 |
| --- | --- |
| `id` | 邊緣的識別碼。 必須是唯一的 (適合的話，與 `_partition` 的值結合) |
| `label` | 邊緣的標籤。 這是選擇性屬性，用來描述關聯性類型。 |
| `inV` | 此屬性包含特定邊緣的頂點清單。 儲存邊緣的相鄰資訊可以加速周遊的執行。 頂點會根據標籤而分組。 |
| `properties` | 與邊緣相關聯的使用者定義屬性包。 每個屬性可以有多個值。 |

每個屬性可以將多個值儲存在陣列中。 

| 屬性 | 描述 |
| --- | --- |
| `value` | 屬性的值

## <a name="gremlin-steps"></a>Gremlin 步驟
現在，讓我們看看 Azure Cosmos DB 支援的 Gremlin 步驟。 如需 Gremlin 的完整參考，請參閱 [TinkerPop 參考](https://tinkerpop.apache.org/docs/3.3.2/reference)。

| 步驟 | 描述 | TinkerPop 3.2 文件 |
| --- | --- | --- |
| `addE` | 在兩個頂點之間新增邊緣 | [addE 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | 將頂點新增至圖表 | [addV 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | 確保所有周遊都會傳回值 | [and 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | 將變數指派給步驟輸出的步驟調變器 | [as 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | 搭配 `group` 和 `order` 一起使用的步驟調變器 | [by 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | 傳回第一次有傳回結果的周遊 | [coalesce 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | 傳回常數值。 搭配 `coalesce` 使用| [constant 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | 從周遊傳回計數 | [count 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | 傳回已移除重複項的值 | [dedup 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | 捨棄值 (頂點/邊緣) | [drop 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | 會針對已執行的 Gremlin 步驟所產生的所有作業建立描述 | [executionProfile 步驟](graph-execution-profile.md) |
| `fold` | 作為屏障來計算結果的彙總| [fold 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | 根據指定的標籤將值分組| [group 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | 用於篩選屬性、頂點和邊緣。 支援 `hasLabel`、`hasId`、`hasNot` 和 `has` 變體。 | [has 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | 將值插入資料流| [inject 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | 用來執行使用布林運算式的篩選條件 | [is 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | 用來限制周遊中的項目數| [limit 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | 局部包裝周遊的一個區段，類似於子查詢 | [local 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | 用來否定篩選條件 | [not 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | 如果指定的周遊產生結果，則傳回結果，否則傳回呼叫端元素 | [optional 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | 確保至少一個周遊會傳回值 | [or 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | 依指定的排序次序傳回結果 | [order 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | 傳回周遊的完整路徑 | [path 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | 將屬性投射為 Map | [project 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | 傳回指定之標籤的屬性 | [properties 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | 篩選為指定的值範圍| [range 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | 將步驟重複執行指定的次數。 用於迴圈處理 | [repeat 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | 用於取樣周遊的結果 | [sample 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | 用於投射周遊的結果 |  [select 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | 用於來自周遊的非封鎖彙總 | [store 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | 字串篩選函式。 此函式是作為 `has()` 步驟的述詞使用，以比對屬性與指定字串的開頭 | [TextP 述詞](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) \(英文\) |
| `TextP.endingWith(string)` |  字串篩選函式。 此函式是作為 `has()` 步驟的述詞使用，以比對屬性與指定字串的結尾 | [TextP 述詞](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) \(英文\) |
| `TextP.containing(string)` | 字串篩選函式。 此函式是作為 `has()` 步驟的述詞使用，以比對屬性與指定字串的內容 | [TextP 述詞](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) \(英文\) |
| `TextP.notStartingWith(string)` | 字串篩選函式。 此函式是作為 `has()` 步驟的述詞使用，以比對不是以指定字串開頭的屬性 | [TextP 述詞](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) \(英文\) |
| `TextP.notEndingWith(string)` | 字串篩選函式。 此函式是作為 `has()` 步驟的述詞使用，以比對不是以指定字串結尾的屬性 | [TextP 述詞](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) \(英文\) |
| `TextP.notContaining(string)` | 字串篩選函式。 此函式是作為 `has()` 步驟的述詞使用，以比對未包含指定字串的屬性 | [TextP 述詞](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) \(英文\) |
| `tree` | 將頂點的路徑彙總至樹狀目錄 | [tree 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | 將迭代器展開為步驟| [unfold 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | 合併來自多個周遊的結果| [unfold 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | 包含頂點和邊緣之間周遊的必要步驟 `V`、`E`、`out`、`in`、`both`、`outE`、`inE`、`bothE`、`outV`、`inV`、`bothV` 和 `otherV` | [vertex 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | 用於篩選周遊的結果。 支援 `eq`、`neq`、`lt`、`lte`、`gt`、`gte` 和 `between` 運算子  | [where 步驟](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

根據預設，Azure Cosmos DB 提供的寫入最佳化引擎支援自動編製頂點和邊緣內所有屬性的索引。 因此，在任何屬性上執行附有篩選條件的查詢、範圍查詢、排序或彙總時，都是從索引來處理，而且有效率地提供。 如需 Azure Cosmos DB 中索引運作方式的詳細資訊，請參閱[無從驗證結構描述的索引編製](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)一文。

## <a name="next-steps"></a>後續步驟
* [使用我們的 SDK](create-graph-dotnet.md) 開始建置圖表應用程式 
* 深入了解 Azure Cosmos DB 中的[圖表支援](graph-introduction.md)
