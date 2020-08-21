---
title: 從變更摘要處理器程式庫遷移至 Azure Cosmos DB .NET V3 SDK
description: 瞭解如何將您的應用程式從使用變更摘要處理器程式庫遷移至 Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: b610748e425b9497e12c389cca4d797d6da54087
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718996"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>從變更摘要處理器程式庫遷移至 Azure Cosmos DB .NET V3 SDK

本文說明將使用 [變更摘要處理器程式庫](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) 的現有應用程式程式碼遷移至最新版 .net SDK 的 [變更](change-feed.md) 摘要功能所需的步驟 (也稱為 .net V3 sdk) 。

## <a name="required-code-changes"></a>必要的程式碼變更

.NET V3 SDK 有幾個重大變更，以下是遷移您的應用程式的主要步驟：

1. 將 `DocumentCollectionInfo` 實例轉換成 `Container` 受監視和租用容器的參考。
1. 使用的自訂 `WithProcessorOptions` 應該更新為使用 `WithLeaseConfiguration` 和（ `WithPollInterval` 適用于 `WithStartTime` [開始時間](how-to-configure-change-feed-start-time.md)），以及 `WithMaxItems` 定義最大專案計數。
1. 將設 `processorName` `GetChangeFeedProcessorBuilder` 為，以符合所設定的值 `ChangeFeedProcessorOptions.LeasePrefix` ，或使用 `string.Empty` 其他方式。
1. 這些變更不再以的形式傳遞 `IReadOnlyList<Document>` ，而是 `IReadOnlyCollection<T>` `T` 您需要定義的類型，沒有基底專案類別。
1. 若要處理這些變更，您不再需要執行程式，而是必須 [定義委派](change-feed-processor.md#implementing-the-change-feed-processor)。 委派可以是靜態函式，或者，如果您需要維護跨執行的狀態，您可以建立自己的類別，並將實例方法傳遞為委派。

例如，如果建立變更摘要處理器的原始程式碼看起來如下所示：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

遷移後的程式碼看起來會像這樣：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

而且委派可以是靜態方法：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>狀態和租用容器

類似于變更摘要處理器程式庫，.NET V3 SDK 中的變更摘要功能會使用 [租用容器](change-feed-processor.md#components-of-the-change-feed-processor) 來儲存狀態。 不過，架構不同。

SDK V3 變更摘要處理器會偵測到任何舊的程式庫狀態，並在第一次執行已遷移的應用程式程式碼時，自動將其遷移至新的架構。 

您可以使用舊的程式碼安全地停止應用程式、將程式碼遷移至新版本、啟動已遷移的應用程式，以及在應用程式停止時所發生的任何變更，都將由新版本挑選並處理。

## <a name="additional-resources"></a>其他資源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的使用範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 上的其他範例](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要處理器：

* [變更摘要處理器的概觀](change-feed-processor.md)
* [使用變更摘要估算器](how-to-use-change-feed-estimator.md)
* [變更摘要處理器開始時間](how-to-configure-change-feed-start-time.md)
