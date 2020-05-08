---
title: Azure Cosmos DB 中的變更摘要處理器
description: 瞭解如何使用 Azure Cosmos DB 變更摘要處理器來讀取變更摘要，也就是變更摘要處理器的元件
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 4/29/2020
ms.reviewer: sngun
ms.openlocfilehash: d069df0a095cc0356cd61155dde875a5d92ed18d
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594146"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Azure Cosmos DB 中的變更摘要處理器

變更摘要處理器是[AZURE COSMOS DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3)的一部分。 它可簡化讀取變更摘要的程式，並有效地將事件處理散發到多個取用者。

變更摘要處理器程式庫的主要優點是它的容錯行為，可確保「至少一次」傳遞變更摘要中的所有事件。

## <a name="components-of-the-change-feed-processor"></a>變更摘要處理器的元件

有四個主要元件可執行變更摘要處理器：

1. **受監視的容器：** 受監視的容器含有會產生變更摘要的資料。 受監視容器的任何插入和更新都會反映在容器的變更摘要中。

1. **租用容器：** 租用容器會作為狀態儲存體，並協調跨多個背景工作角色處理變更摘要。 租用容器可以與受監視容器或個別帳戶中的相同帳戶儲存。

1. **主機：** 主機是使用變更摘要處理器來接聽變更的應用程式實例。 具有相同租用設定的多個實例可以平行執行，但每個實例應該有不同的**實例名稱**。

1. **委派：** 委派是一種程式碼，定義您的開發人員想要對變更摘要處理器讀取的每一批變更進行哪些動作。 

為了進一步了解變更摘要處理器的這四個元素是如何一起運作的，我們來看看下圖中的範例。 受監視的容器會儲存檔，並使用「城市」做為分割區索引鍵。 我們會看到資料分割索引鍵值分佈在包含專案的範圍中。 有兩個主控制項實例，而變更摘要處理器會將不同範圍的資料分割索引鍵值指派給每個實例，以將計算散發最大化。 每個範圍都會以平行方式讀取，而且其進度會與租用容器中的其他範圍分開維護。

![變更摘要處理器範例](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>執行變更摘要處理器

進入點一律是受監視的容器，來自您呼叫`Container` `GetChangeFeedProcessorBuilder`的實例：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

其中第一個參數是描述此處理器目標的相異名稱，而第二個名稱是將處理變更的委派執行。 

委派的範例如下：


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

最後，您會使用`WithInstanceName`和來定義此處理器實例的名稱，這是用`WithLeaseContainer`來維護租用狀態的容器。

呼叫`Build`會提供您可透過呼叫`StartAsync`來啟動的處理器實例。

## <a name="processing-life-cycle"></a>處理生命週期

主控制項實例的正常生命週期為：

1. 讀取變更摘要。
1. 如果沒有任何變更，請睡眠一段預先定義的時間（ `WithPollInterval`在產生器中可自訂），然後移至 #1。
1. 如果有變更，請將它們傳送至**委派**。
1. 當委派完成**成功**處理變更時，請以最新處理的時間點更新租用存放區，並移至 #1。

## <a name="error-handling"></a>錯誤處理

變更摘要處理器具有使用者程式碼錯誤的復原能力。 這表示如果您的委派執行有未處理的例外狀況（步驟 #4），處理該特定批次變更的執行緒將會停止，並會建立新的執行緒。 新的執行緒會檢查哪一個是租用存放區對於該範圍的資料分割索引鍵值而言最新的時間點，然後從該處重新開機，以有效地將相同的變更批次傳送至委派。 此行為會繼續進行，直到您的委派正確處理變更，而且這是變更摘要處理器至少具有「一次」保證的原因，因為如果委派程式碼擲回例外狀況，則會重試該批次。

若要防止您的變更摘要處理器「停滯」持續重試相同的變更批次，您應該在委派程式碼中新增邏輯，以在例外狀況時將檔寫入寄不出的信件佇列。 這項設計可確保您可以追蹤未處理的變更，同時仍能繼續處理未來的變更。 無效信件佇列可能只是另一個 Cosmos 容器。 確切的資料存放區並不重要，只是保存未處理的變更。

此外，您可以使用變更摘要[估計工具](how-to-use-change-feed-estimator.md)來監視變更摘要處理器實例讀取變更摘要時的進度。 除了監視變更摘要處理器是否「停滯」不斷重試相同的變更批次之外，您也可以瞭解您的變更摘要處理器是否因為可用的資源（如 CPU、記憶體和網路頻寬）而延遲落後。

## <a name="dynamic-scaling"></a>動態調整

如簡介中所述，變更摘要處理器可以自動將計算散發到多個實例。 您可以使用變更摘要處理器來部署應用程式的多個實例，並充分利用它，唯一的主要需求是：

1. 所有實例都應該具有相同的租用容器設定。
1. 所有實例都應該具有相同的工作流程名稱。
1. 每個實例都必須有不同的實例名稱`WithInstanceName`（）。

如果這三個條件都適用，則變更摘要處理器會使用相等的散發演算法，將租用容器中的所有租用散發到所有執行中的實例和平行處理計算。 一個租用只能由一個實例在指定時間擁有，因此實例數目上限等於租用數目。

實例數目可以擴大和縮小，而變更摘要處理器會據以動態方式調整負載。

此外，變更摘要處理器可以根據輸送量或儲存體的增加，以動態方式調整容器規模。 當您的容器成長時，變更摘要處理器會以動態方式增加租用，並在現有的實例之間散發新的租用，以明確地處理這些案例。

## <a name="change-feed-and-provisioned-throughput"></a>變更摘要和佈建的輸送量

您需針對耗用的 RU 支付費用，因為資料移入或移出 Cosmos 容器一律會耗用 RU。 您必須針對租用容器耗用的 RU 支付費用。

## <a name="additional-resources"></a>其他資源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的使用範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 上的其他範例](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要處理器：

* [變更摘要的概觀](change-feed.md)
* [如何從變更摘要處理器程式庫進行遷移](how-to-migrate-from-change-feed-library.md)
* [使用變更摘要估算器](how-to-use-change-feed-estimator.md)
* [變更摘要處理器開始時間](how-to-configure-change-feed-start-time.md)
