---
title: 變更 Azure Cosmos DB 的來源設計模式
description: 常見變更來源模式概述
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 012d27b44ecfbdd460adf241742df397880f78c6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450346"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>變更 Azure Cosmos DB 的來源設計模式

Azure Cosmos DB 更改源支援高效處理具有大量寫入量的大型數據集。 變更摘要也提供了替代方式，以查詢整個資料集來識別已變更的項目。 本文檔重點介紹常見的更改源設計模式、設計權衡和更改源限制。

Azure Cosmos DB 非常適合用於 IoT、遊戲、零售，以及操作記錄應用程式。 這類應用程式常用的設計模式，是利用資料的變更觸發其他動作。 其他動作的範例包括：

* 於插入或修改項目時觸發通知或呼叫 API。
* IoT 的即時串流處理或在操作資料上的即時分析處理。
* 數據移動,如與緩存、搜尋引擎、數據倉庫或冷存儲同步。

Azure Cosmos DB 中的變更摘要可讓您針對每一個模式建置有效率且可調整的解決方案，如下圖所示：

![使用 Azure Cosmos DB 變更摘要來提供即時分析和事件導向的計算案例](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>事件計算與通知

Azure Cosmos DB 更改來源可以簡化需要根據特定事件觸發通知或調用 API 的方案。 您可以使用[更改源程序庫](change-feed-processor.md)自動輪詢容器以進行更改,並在每次寫入或更新時調用外部 API。

您還可以根據特定條件有選擇地觸發通知或向 API 發送調用。 例如,如果使用[Azure 函數](change-feed-functions.md)從更改源讀取 ,則可以將邏輯放入函數中,以便僅在滿足特定條件時發送通知。 雖然 Azure 函數代碼將在每次寫入和更新期間執行,但只有滿足特定條件時才會發送通知。

## <a name="real-time-stream-processing"></a>即時流程處理

Azure Cosmos DB 更改源可用於 IoT 的即時流處理或操作數據的即時分析處理。
例如,您可能會從設備、感測器、基礎設施和應用程式接收和存儲事件數據,並使用[Spark](../hdinsight/spark/apache-spark-overview.md)即時處理這些事件。 下圖顯示了如何透過變更來源使用 Azure Cosmos DB 實現 lambda 體系結構:

![運用 Azure Cosmos DB 進行擷取和查詢的 lambda 管線](./media/change-feed/lambda.png)

在許多情況處理實現首先接收大量傳入數據到臨時消息佇列(如 Azure 事件中心或 Apache Kafka )。 更改源是一個偉大的替代方法,因為 Azure Cosmos DB 能夠支援持續高速率的數據引入,並且保證低讀取和寫入延遲。 Azure Cosmos DB 更改饋送相對於消息佇列的優點包括:

### <a name="data-persistence"></a>資料持續性

寫入 Azure Cosmos DB 的資料將顯示在更改來源中,並保留到刪除。 消息佇列通常具有最大保留期。 例如[,Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)提供最多 90 天的數據保留時間。

### <a name="querying-ability"></a>查詢能力

除了從Cosmos容器的更改源中讀取外,還可以對存儲在Azure Cosmos DB中的數據運行SQL查詢。 更改源不是容器中已有數據的重複,而只是讀取數據的不同機制。 因此,如果從更改源讀取數據,它將始終與同一 Azure Cosmos DB 容器的查詢一致。

### <a name="high-availability"></a>高可用性

Azure Cosmos DB 提供高達 99.999% 的讀寫可用性。 與許多消息佇列不同,Azure Cosmos DB 數據可以輕鬆全域分佈,並且配置的[RTO(恢復時間目標)](consistency-levels-tradeoffs.md#rto)為零。

在更改源中處理項後,可以生成具體化視圖,並在 Azure Cosmos DB 中保留聚合值。 例如，如果您使用 Azure Cosmos DB 來建置遊戲，就可以根據完成遊戲的分數，使用變更摘要來實作即時排行榜。

## <a name="data-movement"></a>資料移動

您還可以從更改源中讀取即時資料移動。

例如,更改來源可説明您高效地執行以下任務:

* 使用存儲在 Azure Cosmos DB 中的數據更新緩存、搜索索引或數據倉庫。

* 執行零停機時間移轉至另一個 Azure Cosmos 帳戶或具有不同邏輯分割區索引鍵的另一個 Azure Cosmos 容器。

* 實現應用程式級數據分層和存檔。 例如,您可以將「熱資料」儲存在 Azure Cosmos DB 中,並將「冷資料」 老化到其他儲存系統,如[Azure Blob 儲存](../storage/common/storage-introduction.md)。

當必須[跨分區和容器取消數據規範化](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)時,可以從容器的更改源中讀取此數據複製的源。 使用更改源進行即時數據複製只能保證最終的一致性。 您可以[監視更改饋送處理器](how-to-use-change-feed-estimator.md)在處理 Cosmos 容器中的更改時落後多遠。

## <a name="event-sourcing"></a>活動採購

[事件來源模式](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)涉及使用僅追加儲存來記錄對該資料的完整系列操作。 Azure Cosmos DB 的更改源是事件源體系結構中的中央數據存儲的絕佳選擇,其中所有數據引入都建模為寫入(不更新或刪除)。 在這種情況下,每個寫入 Azure Cosmos DB 都是一個「事件」,您將在更改源中記錄過去的事件的完整記錄。 中心事件存儲發佈的事件的典型用途是維護具體化視圖或與外部系統集成。 由於更改源中沒有保留時間限制,因此可以通過從Cosmos容器的更改源的開頭讀取來重播所有過去的事件。

您可以[讓多個變更來源使用者訂閱一個容器的變更來源](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers)。 除了[租用容器的](change-feed-processor.md#components-of-the-change-feed-processor)預配輸送量外,使用更改源不花費任何成本。 無論更改源是否被使用,更改源在每個容器中都可用。

Azure Cosmos DB 是事件源模式中僅集中追加的持久數據存儲,因為它具有水準可擴展性和高可用性的優勢。 此外,更改源處理器庫提供[「至少一次」](change-feed-processor.md#error-handling)保證,確保您不會錯過處理任何事件。

## <a name="current-limitations"></a>目前的限制

更改源具有您應該瞭解的重要限制。 雖然Cosmos容器中的項將始終保留在更改源中,但更改源不是完整的操作日誌。 在設計利用更改源的應用程式時,需要考慮一些重要方面。

### <a name="intermediate-updates"></a>中間更新

更改源中僅包含給定項的最新更改。 處理更改時,您將讀取最新的可用物料版本。 如果在短時間內對同一項有多個更新,則可能會錯過處理中間更新。 如果要跟蹤更新並能夠重播項目過去的更新,我們建議將這些更新建模為一系列寫入。

### <a name="deletes"></a>Deletes

更改源不捕獲刪除。 如果從容器中刪除專案,也會從更改源中刪除該專案。 處理此操作的最常見方法是在要刪除的項上添加軟標記。 您可以添加名為"已刪除"的屬性,並在刪除時將其設置為"true"。 此文件更新將顯示在更改源中。 您可以在此專案上設置 TTL,以便以後可以自動刪除它。

### <a name="guaranteed-order"></a>保證訂單

分區鍵值中的更改源中有保證的順序,但不跨分區鍵值。 您應該選擇一個分區鍵,為您提供有意義的訂單保證。

例如,考慮使用事件源設計模式的零售應用程式。 在此應用程式中,不同的使用者操作是每個"事件",這些"事件"建模為寫入 Azure Cosmos DB。 想像一下,如果一些範例事件按以下順序發生:

1. 客戶將專案 A 新增到購物車
2. 客戶將專案 B 新增到購物車
3. 客戶從購物車中移除專案 A
4. 客戶簽出和購物車內容已發貨

為每個客戶維護當前購物車內容的物化視圖。 此應用程式必須確保按這些事件發生的順序處理這些事件。 例如,如果在專案 A 刪除之前處理購物車結帳,則客戶很可能已裝運專案 A,而不是所需的專案 B。為了保證這四個事件按其發生順序進行處理,它們應屬於相同的分區鍵值。 如果選擇**使用者名**(每個客戶都有唯一的使用者名)作為分區鍵,則可以保證這些事件以寫入 Azure Cosmos DB 的相同順序顯示在更改源中。

## <a name="examples"></a>範例

下面是一些超出 Microsoft 文件中範例範圍的實際更改原始碼範例:

- [變更來源簡介](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [以變更來源為中心的 IoT 使用例](https://github.com/AzureCosmosDB/scenario-based-labs)
- [以變更來源為中心的零售用例](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>後續步驟

* [變更摘要概觀](change-feed.md)
* [讀取變更摘要的選項](read-change-feed.md)
* [搭配使用變更摘要與 Azure Functions](change-feed-functions.md)