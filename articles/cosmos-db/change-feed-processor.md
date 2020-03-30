---
title: 更改 Azure Cosmos DB 中的饋送處理器庫
description: 瞭解如何使用 Azure Cosmos DB 更改饋送處理器庫讀取更改源、更改饋送處理器的元件
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: e71b2807595aebeb1f0c8682fde119f4e267e55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273313"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Azure Cosmos DB 中的變更摘要處理器 

更改饋送處理器是 Azure[宇宙 DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3)的一部分。 它簡化了讀取更改源的過程，並將事件處理有效地分發給多個消費者。

更改源處理器庫的主要好處是其容錯行為，確保"至少一次"地傳遞更改源中的所有事件。

## <a name="components-of-the-change-feed-processor"></a>更改饋送處理器的元件

實現更改饋送處理器有四個主要元件： 

1. **受監視的容器：** 受監視的容器含有會產生變更摘要的資料。 對受監視容器的任何插入和更新都反映在容器的更改源中。

1. **租賃容器：** 租約容器充當狀態存儲，並協調處理跨多個輔助器的更改源。 租約容器可以存儲在與受監視容器相同的帳戶中，也可以存儲在單獨的帳戶中。 

1. **主機：** 主機是使用更改饋送處理器偵聽更改的應用程式實例。 具有相同租約配置的多個實例可以並行運行，但每個實例應具有不同的**實例名稱**。 

1. **委託：** 委託是定義開發人員想要對更改饋送處理器讀取的每一批更改執行哪些操作的代碼。 

為了進一步了解變更摘要處理器的這四個元素是如何一起運作的，我們來看看下圖中的範例。 受監視的容器存儲文檔並使用"城市"作為分區鍵。 我們看到分區鍵值分佈在包含項的範圍內。 有兩個主機實例，更改饋送處理器為每個實例分配不同的分區鍵值範圍，以最大化計算分佈。 每個範圍是並行讀取的，其進度與租賃容器中的其他範圍分開維護。

![變更摘要處理器範例](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>實現更改饋送處理器

進入點始終是受監視的容器，來自您調用`Container``GetChangeFeedProcessorBuilder`的實例：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

其中第一個參數是描述此處理器目標的不同名稱，第二個名稱是處理更改的委託實現。 

委託的一個示例是：


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

最後，您為 此處理器實例定義一`WithInstanceName`個名稱，該實例是 維護 的`WithLeaseContainer`容器。

調用`Build`將為您提供處理器實例，您可以開始調用`StartAsync`。

## <a name="processing-life-cycle"></a>處理生命週期

主機實例的正常生命週期是：

1. 閱讀更改源。
1. 如果沒有更改，則睡一段時間（`WithPollInterval`在產生器中可自訂），然後轉到#1。
1. 如果有更改，請將其發送到**委託**。
1. 當委託**成功**處理更改時，使用最新的處理時間點更新租約存儲並轉到#1。

## <a name="error-handling"></a>錯誤處理

更改源處理器可抵禦使用者代碼錯誤。 這意味著，如果委託實現具有未處理的異常（步驟#4），則該特定更改批次處理的執行緒將停止，並將創建新執行緒。 新執行緒將檢查租約存儲對於該分區鍵值範圍的最新時間點，然後從那裡重新開機，從而有效地向委託發送同一批更改。 此行為將繼續，直到代理正確處理更改，並且更改源處理器具有"至少一次"保證的原因，因為如果委託代碼引發，它將重試該批次處理。

## <a name="dynamic-scaling"></a>動態調整

如簡介中所述，更改饋送處理器可以自動跨多個實例分配計算。 您可以使用更改饋送處理器部署應用程式的多個實例，並充分利用它，唯一的關鍵要求是：

1. 所有實例都應具有相同的租約容器配置。
1. 所有實例都應具有相同的工作流名稱。
1. 每個實例需要具有不同的實例名稱 （`WithInstanceName`。

如果這三個條件適用，則更改饋送處理器將使用相等的分佈演算法在所有正在運行的實例之間分配租約容器中的所有租約並並行化計算。 一個租約只能在給定時間由一個實例擁有，因此最大實例數等於租約數。

實例數量可以增加和縮小，並且更改饋送處理器將通過相應地重新分配來動態調整負載。

此外，由於輸送量或存儲的增加，更改饋送處理器可以動態調整到容器規模。 當容器增長時，更改饋送處理器通過動態增加租約並在現有實例之間分發新租約來透明地處理這些方案。

## <a name="change-feed-and-provisioned-throughput"></a>變更摘要和佈建的輸送量

您需針對耗用的 RU 支付費用，因為資料移入或移出 Cosmos 容器一律會耗用 RU。 您必須針對租用容器耗用的 RU 支付費用。

## <a name="additional-resources"></a>其他資源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的使用範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 上的其他範例](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要處理器：

* [變更摘要的概觀](change-feed.md)
* [如何從更改源處理器庫遷移](how-to-migrate-from-change-feed-library.md)
* [使用變更摘要估算器](how-to-use-change-feed-estimator.md)
* [變更摘要處理器開始時間](how-to-configure-change-feed-start-time.md)
