---
title: Azure Cosmos DB Gremlin API 中的資料分割
description: 了解如何使用 Azure Cosmos DB 中的資料分割圖表。 本文也說明資料分割圖表的需求和最佳做法。
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623363"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的資料分割圖表

Azure Cosmos DB 中的 Gremlin API 的主要功能之一，是能夠透過水平縮放處理大規模的圖形。 容器可以在儲存體和輸送量方面獨立調整。 您可以在 Azure Cosmos DB 中，建立能自動縮放來儲存圖表資料的容器。 資料根據指定的**分割區金鑰**來自動取得平衡。

如果容器預期會儲存 20 GB 以上的大小，或者您想要配置超過每秒10000個要求單位（ru），**則需要進行分割**。 [Azure Cosmos DB 分割機制](partition-data.md)中的相同一般原則，適用于下面所述的一些圖形特定的優化。

![圖表資料分割。](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>圖表分割機制

下列指導方針說明 Azure Cosmos DB 中的資料分割策略如何運作：

- **頂點和邊緣會儲存為 JSON 文件**。

- **端點需要資料分割索引鍵**。 此索引鍵會決定頂點將透過雜湊演算法儲存在哪個資料分割中。 資料分割索引鍵屬性名稱是在建立新容器時定義的，其格式為： `/partitioning-key-name`。

- **邊緣將會連同其來源頂點一起儲存**。 換句話說，對每個頂點而言，其分割區金鑰將會定義其本身及其外傳邊緣的儲存位置。 這項優化的目的是為了避免在圖表查詢中使用 `out()` 基數時的跨資料分割查詢。

- **邊緣包含其指向之頂點的參考**。 所有邊緣都會與其指向之頂點的分割區索引鍵和識別碼一起儲存。 這種計算會使所有 `out()` 方向查詢一律是已設定範圍的資料分割查詢，而不是盲人跨資料分割查詢。 

- **圖形查詢必須指定資料分割索引鍵**。 若要在 Azure Cosmos DB 中充分利用水平資料分割，則應在選取單一端點時盡可能指定資料分割索引鍵。 以下是在資料分割的圖表中選取一或多個頂點的查詢：

    - 不支援 Gremlin API 中的容器以 `/id` 和 `/label` 作為資料分割索引鍵。


    - 依識別碼選取頂點，然後**使用 `.has()` 步驟指定資料分割索引鍵屬性**： 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - 藉由**指定包含資料分割索引鍵值和識別碼的 Tuple** 來選取頂點： 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - 指定**資料分割索引鍵值和識別碼的 Tuple 陣列**：
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - 選取一組含有其識別碼的頂點，並**指定資料分割索引鍵值的清單**： 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - 在查詢的開頭使用資料**分割策略**，並針對其餘的 Gremlin 查詢範圍指定資料分割： 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>使用資料分割圖表時的最佳做法

使用無限制容器進行資料分割圖表時，您可以使用下列指導方針來確保效能和延展性：

- **在查詢頂點時務必要指定資料分割索引鍵值**。 從已知資料分割取得頂點，是可達到效能的方法。 所有後續的相鄰作業一律會設為分割區的範圍，因為邊緣包含其目標頂點的參考識別碼和資料分割索引鍵。

- **查詢邊緣時應盡可能使用外傳方向**。 如前所述，邊緣會連同其外傳方向的來源頂點一起儲存。 依此模式為原則設計資料和查詢時，將可盡量降低進行跨資料分割查詢的可能性。 相反地，`in()` 查詢一律會是昂貴的展開傳送查詢。

- **選擇可將資料平均分散到每個資料分割的資料分割索引鍵**。 此決策高度仰賴解決方案的資料模型。 請閱讀 [Azure Cosmos DB 中的資料分割和調整](partition-data.md)，以深入了解如何建立適當的資料分割索引鍵。

- **將查詢最佳化以在資料分割的界限內取得資料**。 最佳的資料分割策略將與查詢模式相對應。 叢單一資料分割取得資料的查詢，將可提供最佳效能。

## <a name="next-steps"></a>後續步驟

接下來，您可以繼續閱讀下列文章：

* 了解 [Azure Cosmos DB 中的資料分割和調整](partition-data.md)。
* 了解 [Gremlin API 中的 Gremlin 支援](gremlin-support.md)。
* 了解 [Gremlin API 簡介](graph-introduction.md)。
