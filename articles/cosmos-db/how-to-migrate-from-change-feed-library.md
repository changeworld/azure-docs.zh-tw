---
title: 從變更摘要處理器程式庫遷移至 Azure Cosmos DB .NET V3 SDK
description: 瞭解如何使用變更摘要處理器程式庫將您的應用程式遷移至 Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588878"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>從變更摘要處理器程式庫遷移至 Azure Cosmos DB .NET V3 SDK

本文說明將使用[變更摘要處理器程式庫](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet)之現有應用程式的程式碼，遷移到最新版 .net SDK （也稱為 .NET V3 sdk）中[變更](change-feed.md)摘要功能的必要步驟。

## <a name="required-code-changes"></a>必要的程式碼變更

.NET V3 SDK 有數個重大變更，以下是遷移應用程式的主要步驟：

1. 將`DocumentCollectionInfo`實例轉換成`Container`受監視和租用容器的參考。
1. 使用`WithProcessorOptions`的自訂應該更新為使用`WithLeaseConfiguration`和`WithPollInterval`作為間隔、 `WithStartTime` [開始時間](how-to-configure-change-feed-start-time.md)，以及`WithMaxItems`定義最大專案計數。
1. 設定 [ `processorName`開啟`GetChangeFeedProcessorBuilder` ] 以符合上`ChangeFeedProcessorOptions.LeasePrefix`所設定的值， `string.Empty`或使用其他方式。
1. 這些變更不再以形式傳遞`IReadOnlyList<Document>`，而是您需要定義的`IReadOnlyCollection<T>`其中`T`一個類型，不會再有基底專案類別。
1. 若要處理變更，您不再需要執行，而是必須[定義委派](change-feed-processor.md#implementing-the-change-feed-processor)。 委派可以是靜態函式，或者，如果您需要維護跨執行的狀態，您可以建立自己的類別，並將實例方法當做委派傳遞。

例如，如果用來建立變更摘要處理器的原始程式碼看起來如下：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

遷移後的程式碼看起來會像這樣：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

而委派則可以是靜態方法：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>狀態和租用容器

類似于變更摘要處理器程式庫，.NET V3 SDK 中的變更摘要功能會使用[租用容器](change-feed-processor.md#components-of-the-change-feed-processor)來儲存狀態。 不過，架構是不同的。

SDK V3 變更摘要處理器會偵測到任何舊的程式庫狀態，並在第一次執行已遷移的應用程式程式碼時，自動將其遷移至新的架構。 

您可以使用舊的程式碼安全地停止應用程式、將程式碼遷移至新版本、啟動已遷移的應用程式，以及在應用程式停止時所發生的任何變更，將會由新版本收取並處理。

> [!NOTE]
> 從使用程式庫的應用程式遷移至 .NET V3 SDK 是單向的，因為狀態（租用）將會遷移至新的架構。 遷移無法回溯相容。


## <a name="additional-resources"></a>其他資源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的使用範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 上的其他範例](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要處理器：

* [變更摘要處理器的概觀](change-feed-processor.md)
* [使用變更摘要估算器](how-to-use-change-feed-estimator.md)
* [變更摘要處理器開始時間](how-to-configure-change-feed-start-time.md)
