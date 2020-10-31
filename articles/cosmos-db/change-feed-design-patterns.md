---
title: 變更 Azure Cosmos DB 中的摘要設計模式
description: 一般變更摘要設計模式的概觀
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 0c890d50bbfe498f9d90698394b2cc2d373c0d8b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93072989"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>變更 Azure Cosmos DB 中的摘要設計模式
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 變更摘要可有效處理具有大量寫入的大型資料集。 變更摘要也提供了替代方式，以查詢整個資料集來識別已變更的項目。 本文件著重於常見的變更摘要設計模式、設計取捨和變更摘要限制。

Azure Cosmos DB 非常適合用於 IoT、遊戲、零售，以及操作記錄應用程式。 這類應用程式常用的設計模式，是利用資料的變更觸發其他動作。 其他動作的範例包括：

* 於插入或修改項目時觸發通知或呼叫 API。
* IoT 的即時串流處理或在操作資料上的即時分析處理。
* 資料移動，例如與快取、搜尋引擎、資料倉儲或冷儲存體進行同步處理。

Azure Cosmos DB 中的變更摘要可讓您針對每一個模式建置有效率且可調整的解決方案，如下圖所示：

:::image type="content" source="./media/change-feed/changefeedoverview.png" alt-text="使用 Azure Cosmos DB 變更摘要來提供即時分析和事件導向的計算案例" border="false":::

## <a name="event-computing-and-notifications"></a>事件計算和通知

Azure Cosmos DB 變更摘要可以簡化需要觸發通知的案例，或根據特定事件傳送對 API 的呼叫。 您可以使用[變更摘要程序程式庫](change-feed-processor.md)以自動輪詢容器的變更，並在每次有寫入或更新時呼叫外部 API。

您也可以根據特定準則，選擇性地觸發通知或傳送對 API 的呼叫。 例如，如果您要使用 [Azure Functions](change-feed-functions.md) 從變更摘要進行讀取，您可以將邏輯放入函式，以便只在符合特定準則時才傳送通知。 雖然 Azure Function 程式碼會在每次寫入和更新期間執行，但只有在符合特定準則時才會傳送通知。

## <a name="real-time-stream-processing"></a>即時串流處理

Azure Cosmos DB 變更摘要可用於 IoT 的即時串流處理，或在操作資料上的即時分析處理。
例如，您可能會接收與儲存來自裝置、感應器、基礎結構和應用程式的事件資料，並即時處理這些事件，例如使用 [Spark](../hdinsight/spark/apache-spark-overview.md)。 下圖顯示如何透過變更摘要使用 Azure Cosmos DB 來實作 lambda 架構：

:::image type="content" source="./media/change-feed/lambda.png" alt-text="使用 Azure Cosmos DB 變更摘要來提供即時分析和事件導向的計算案例" border="false":::

在許多情況下，串流處理實作會先將大量的傳入資料接收到臨時訊息佇列，例如 Azure 事件中樞或 Apache Kafka。 變更摘要是絕佳的替代方案，因為 Azure Cosmos DB 能夠以保證的低讀取和寫入延遲來支援持續性的高速資料擷取。 透過訊息佇列的 Azure Cosmos DB 變更摘要優點包括：

### <a name="data-persistence"></a>資料持續性

寫入 Azure Cosmos DB 的資料會顯示在變更摘要中，並保留到刪除為止。 訊息佇列通常具有最大保留期間。 例如，[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)提供 90 天的最大資料保留期。

### <a name="querying-ability"></a>查詢能力

除了從 Cosmos 容器的變更摘要讀取之外，您也可以對儲存在 Azure Cosmos DB 中的資料執行 SQL 查詢。 變更摘要不是容器中已有的資料重複，而只是不同的資料讀取機制。 因此，如果您從變更摘要讀取資料，其一律會與相同 Azure Cosmos DB 容器的查詢一致。

### <a name="high-availability"></a>高可用性

Azure Cosmos DB 最多可提供 99.999% 的讀取和寫入可用性。 不同於許多訊息佇列，Azure Cosmos DB 資料可以透過 [RTO (復原時間目標)](./consistency-levels.md#rto) 為零，輕鬆地進行全域散發和設定。

在處理變更摘要中的項目之後，您可以建置具體化檢視，並在 Azure Cosmos DB 中保存彙總的值。 例如，如果您使用 Azure Cosmos DB 來建置遊戲，就可以根據完成遊戲的分數，使用變更摘要來實作即時排行榜。

## <a name="data-movement"></a>資料移動

您也可以從變更摘要讀取以進行即時資料移動。

例如，變更摘要可協助您有效率地執行下列工作︰

* 透過儲存在 Azure Cosmos DB 中的資料來更新快取、搜尋索引或資料倉儲。

* 執行零停機時間移轉至另一個 Azure Cosmos 帳戶或具有不同邏輯分割區索引鍵的另一個 Azure Cosmos 容器。

* 實作應用程式層級的資料層和封存。 例如，您可以在 Azure Cosmos DB 中儲存「經常性存取資料」，並將「非經常性存取資料」存留到其他儲存體系統，例如 [Azure Blob 儲存體](../storage/common/storage-introduction.md)。

當您必須[跨分割區和容器反正規化資料](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)時，您可以從容器的變更摘要讀取，做為此資料複寫的來源。 具有變更摘要的即時資料複寫只能保證最終一致性。 您可以[監視變更摘要處理器在處理 Cosmos 容器中的變更時落後的程度](how-to-use-change-feed-estimator.md)。

## <a name="event-sourcing"></a>事件來源

[事件來源模式](/azure/architecture/patterns/event-sourcing)牽涉到使用附加專用存放區來記錄該資料的完整系列動作。 Azure Cosmos DB 的變更摘要是一個絕佳的選擇，做為事件來源架構中的中央資料存放區，其中所有的資料擷取都會模型化為寫入 (無更新或刪除)。 在此情況下，每次寫入 Azure Cosmos DB 都是「事件」，且您在變更摘要中會有過去事件的完整記錄。 中央事件存放區所發佈事件的一般用法，是用來維護具體化檢視或與外部系統整合。 由於變更摘要中沒有保留時間限制，因此您可以從 Cosmos 容器的變更摘要開頭進行讀取，藉此重新執行所有過去的事件。

您可以讓[多個變更摘要取用者訂閱相同容器的變更摘要](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers)。 除了[租用容器的](change-feed-processor.md#components-of-the-change-feed-processor)佈建輸送量以外，利用變更摘要也不會產生任何費用。 變更摘要適用於每個容器，不論是否已使用。

Azure Cosmos DB 是事件來源模式中的絕佳中央附加專用持續性資料存放區，因為其優勢在於水平擴縮性和高可用性。 此外，變更摘要處理器程式庫會提供[「至少一次」](change-feed-processor.md#error-handling)保證，以確保您不會錯過處理任何事件。

## <a name="current-limitations"></a>目前的限制

變更摘要具有您應該了解的重要限制。 儘管 Cosmos 容器中的項目一律會保留在變更摘要中，但變更摘要並非完整的作業記錄。 設計使用變更摘要的應用程式時，需要考慮一些重要的區域。

### <a name="intermediate-updates"></a>中繼更新

變更摘要中只會包含指定項目的最新變更。 在處理變更時，您將會讀取最新可用的項目版本。 如果在短時間內相同的項目有多個更新，可能會遺漏處理中繼更新。 如果您想要追蹤更新並能夠重新執行項目的過去更新，建議您改為將這些更新模型化為一系列的寫入。

### <a name="deletes"></a>Deletes

變更摘要不會擷取刪除。 如果您從容器中刪除項目，也會將其從變更摘要中移除。 最常見的處理方法是在要刪除的項目上新增軟標記。 您可以新增名為「已刪除」的屬性，並在刪除時將其設定為「true」。 此文件更新會顯示在變更摘要中。 您可以在此項目上設定 TTL，以便稍後可以自動刪除。

### <a name="guaranteed-order"></a>保證順序

分割區索引鍵值 (而非跨分割區索引鍵值) 內的變更摘要有保證的順序。 您應該選取分割區索引鍵，以提供有意義的順序保證。

例如，請考慮使用「事件來源」設計模式的零售應用程式。 在此應用程式中，不同的使用者動作分別是模型化為 Azure Cosmos DB 寫入的「事件」。 假設有一些範例事件依照下列順序發生：

1. 客戶將項目 A 新增至其購物車
2. 客戶將項目 B 新增至其購物車
3. 客戶從其購物車移除項目 A
4. 客戶簽出且購物車內容已出貨

針對每個客戶維護目前購物車內容的具體化檢視。 此應用程式必須確保這些事件會依照其發生的順序進行處理。 例如，如果在項目 A 移除之前要先處理購物車結帳，則客戶可能已將項目 A 出貨，而不是所需的項目 B 出貨。為了保證這四個事件會依照發生的順序進行處理，其應該落在相同的分割區索引鍵值內。 如果您選取 **username** (每個客戶都有唯一的使用者名稱) 做為分割區索引鍵，則可以保證這些事件在變更摘要中會依照其寫入至 Azure Cosmos DB 的相同順序來顯示。

## <a name="examples"></a>範例

以下是一些真實世界的變更摘要程式碼範例，其延伸超出 Microsoft 文件中提供的範例範圍：

- [變更摘要簡介](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [以變更摘要為中心的 IoT 使用案例](https://github.com/AzureCosmosDB/scenario-based-labs)
- [以變更摘要為中心的零售使用案例](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>後續步驟

* [變更摘要概觀](change-feed.md)
* [讀取變更摘要的選項](read-change-feed.md)
* [搭配使用變更摘要與 Azure Functions](change-feed-functions.md)