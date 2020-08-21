---
title: Azure Cosmos DB 中的變更摘要處理器
description: 了解如何使用 Azure Cosmos DB 變更摘要處理器來讀取變更摘要，也就是變更摘要處理器的元件
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/13/2020
ms.reviewer: sngun
ms.openlocfilehash: faf46a90a04e35eb041deb31913980575b1e0d28
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688040"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Azure Cosmos DB 中的變更摘要處理器

變更摘要處理器是 [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) 的一部分。 它可簡化讀取變更摘要的程序，並有效地將事件處理散發到多個取用者。

變更摘要處理器程式庫的主要優點是它的容錯行為，可確保「至少一次」傳遞變更摘要中的所有事件。

## <a name="components-of-the-change-feed-processor"></a>變更摘要處理器的元件

實作變更摘要處理器時會用到四個主要元件：

1. **受監視的容器：** 受監視的容器含有會產生變更摘要的資料。 對於受監視的容器所進行的任何插入和更新，都會反映在容器的變更摘要中。

1. **租用容器：** 租用容器是作為狀態儲存體，會協調如何處理多個背景工作角色的變更摘要。 租用容器可以儲存在與受監視容器相同的帳戶中，或儲存在個別帳戶中。

1. **主機：** 主機是應用程式執行個體，會使用變更摘要處理器來接聽變更。 具有相同租用設定的多個執行個體可以平行執行，但是每個執行個體應該有不同的**執行個體名稱**。

1. **委派：** 委派是一種程式碼，定義身為開發人員的您想要對變更摘要處理器讀取的每個批次變更進行哪些動作。 

為了進一步了解變更摘要處理器的這四個元素是如何一起運作的，我們來看看下圖中的範例。 受監視的容器會儲存文件，並使用「City」來作為分割區索引鍵。 我們看到資料分割索引鍵值散發在包含項目的範圍中。 有兩個主控件執行個體，而變更摘要處理器會將不同範圍的資料分割索引鍵值指派給每個執行個體，以將計算散發最大化。 每個範圍會以平行方式讀取，而且其進度會與租用容器中的其他範圍各自維護。

:::image type="content" source="./media/change-feed-processor/changefeedprocessor.png" alt-text="變更摘要處理器範例" border="false":::

## <a name="implementing-the-change-feed-processor"></a>實作變更摘要處理器

進入點一律是受監視的容器，來自您呼叫 `GetChangeFeedProcessorBuilder` 的 `Container` 執行個體：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

其中，第一個參數是描述此處理器目標的相異名稱，而第二個名稱是將處理變更的委派實作。 

委派的範例如下：


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

最後，您會使用 `WithInstanceName` 來定義此處理器執行個體的名稱，而這是使用 `WithLeaseContainer` 維護租用狀態的容器。

呼叫 `Build` 將提供可藉由呼叫 `StartAsync` 來啟動的處理器執行個體。

## <a name="processing-life-cycle"></a>處理生命週期

主機執行個體的正常生命週期為：

1. 讀取變更摘要。
1. 如果沒有任何變更，則睡眠一段預先定義的時間 (可使用建立器中的 `WithPollInterval` 自訂)，然後移至 #1。
1. 如果有變更，則將其傳送至**委派**。
1. 當委派**成功**完成處理變更時，請以最新的處理時間點更新租用存放區，並移至 #1。

## <a name="error-handling"></a>錯誤處理

變更摘要處理器具有使用者程式碼錯誤的復原性。 這表示如果您的委派實作有未處理的例外狀況 (步驟 #4)，處理該特定批次變更的執行緒將會停止，並會建立新的執行緒。 新的執行緒會檢查哪一個是租用存放區對於該範圍的資料分割索引鍵值而言最新的時間點，然後從該時間點重新開機，以有效地將相同的變更批次傳送至委派。 此行為會繼續進行，直到您的委派正確處理變更，而且這是變更摘要處理器至少具有「一次」保證的原因，因為如果委派程式碼擲回例外狀況，則會重試該批次。

為了避免您的變更摘要處理器「停滯」持續重試相同的變更批次，您應該在委派程式碼中新增邏輯，以在例外狀況時，將檔寫入無效信件佇列。 這項設計可確保您可以追蹤未處理的變更，同時仍能繼續處理未來的變更。 無效信件佇列可能只是另一個 Cosmos 容器。 確切的資料存放區並不重要，只是保存未處理的變更。

此外，您可以使用[變更摘要估算器](how-to-use-change-feed-estimator.md)，以監視變更摘要處理器執行個體讀取變更摘要時的進度。 除了監視變更摘要處理器是否「停滯」不斷重試相同的變更批次之外，您也可以了解變更摘要處理器是否因為可用的資源 (例如 CPU、記憶體和網路頻寬) 而延遲落後。

## <a name="deployment-unit"></a>部署單位

單一變更摘要處理器部署單位是由一或多個具有相同 `processorName` 和租用容器設定的執行個體所組成。 您可以有許多部署單位，其中每一個都有不同的商務流程來進行變更，而每個部署單位都包含一或多個執行個體。 

例如，您可能會有一個部署單位，每當您的容器有變更時，就會觸發外部 API。 另一個部署單位可能會在每次有變更時，即時移動資料。 當受監視的容器中發生變更時，您所有的部署單位都會收到通知。

## <a name="dynamic-scaling"></a>動態調整

如先前所述，您可以在部署單位內有一或多個執行個體。 若要利用部署單位內的計算散發，唯一的主要需求是：

1. 所有執行個體都應該具有相同的租用容器設定。
1. 所有執行個體都應該具有相同的 `processorName`。
1. 每個執行個體都必須有不同的執行個體名稱 (`WithInstanceName`)。

如果這三個條件都適用，則變更摘要處理器會使用相等的散發演算法，將租用容器中的所有租用散發到該部署單位的所有執行中執行個體並平行處理計算。 一個租用在指定時間內只能由一個執行個體擁有，因此執行個體數目上限等於租用數目。

執行個體數目可以擴大和縮小，而變更摘要處理器會視情況，以動態方式調整負載。

此外，變更摘要處理器可以根據輸送量或儲存體的增加，以動態方式調整容器規模。 當您的容器成長時，變更摘要處理器會以動態方式增加租用，並在現有的執行個體之間散發新的租用，以透明的方式處理這些案例。

## <a name="change-feed-and-provisioned-throughput"></a>變更摘要和佈建的輸送量

您需針對耗用的 RU 支付費用，因為資料移入或移出 Cosmos 容器一律會耗用 RU。 您必須針對租用容器耗用的 RU 支付費用。

## <a name="where-to-host-the-change-feed-processor"></a>變更摘要處理器的裝載位置

變更摘要處理器可以裝載于任何支援長時間執行之進程或工作的平臺上：

* 持續執行的 [Azure WebJob](https://docs.microsoft.com/learn/modules/run-web-app-background-task-with-webjobs/)。
* [Azure 虛擬機器](https://docs.microsoft.com/azure/architecture/best-practices/background-jobs#azure-virtual-machines)中的進程。
* [Azure Kubernetes Service](https://docs.microsoft.com/azure/architecture/best-practices/background-jobs#azure-kubernetes-service)中的背景工作。
* [ASP.NET 託管服務](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services)。

雖然變更摘要處理器可以在短期的環境中執行，因為租用容器會維護狀態，而這些環境的啟動和停止週期將會增加接收通知的延遲， (因為每次啟動環境時啟動處理器的額外負荷) 。

## <a name="additional-resources"></a>其他資源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [在 GitHub 上完成範例應用程式](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)
* [GitHub 上的其他使用範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [變更摘要處理器的 Cosmos DB 研討會實驗室](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html#consume-cosmos-db-change-feed-via-the-change-feed-processor)

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要處理器：

* [變更摘要的概觀](change-feed.md)
* [變更摘要提取模型](change-feed-pull-model.md)
* [如何從變更摘要處理器程式庫遷移](how-to-migrate-from-change-feed-library.md)
* [使用變更摘要估算器](how-to-use-change-feed-estimator.md)
* [變更摘要處理器開始時間](how-to-configure-change-feed-start-time.md)
