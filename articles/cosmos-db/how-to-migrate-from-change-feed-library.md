---
title: 從更改饋送處理器庫遷移到 Azure 宇宙 DB .NET V3 SDK
description: 瞭解如何將應用程式從使用更改饋送處理器庫遷移到 Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588878"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>從更改饋送處理器庫遷移到 Azure 宇宙 DB .NET V3 SDK

本文介紹了將使用[更改源處理器庫](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet)的現有應用程式代碼遷移到最新版本 .NET SDK（也稱為 .NET V3 SDK）中的[更改源](change-feed.md)功能所需的步驟。

## <a name="required-code-changes"></a>所需的代碼更改

.NET V3 SDK 有幾個重大更改，以下是遷移應用程式的關鍵步驟：

1. 將`DocumentCollectionInfo`實例轉換為`Container`受監視容器的引用，並租用容器。
1. `WithProcessorOptions`使用的`WithLeaseConfiguration`自訂項應更新為 使用 和`WithPollInterval`間隔、`WithStartTime`[開始時間](how-to-configure-change-feed-start-time.md)以及`WithMaxItems`定義最大項計數。
1. 將`processorName`on`GetChangeFeedProcessorBuilder`設置為匹配 在`ChangeFeedProcessorOptions.LeasePrefix`上配置的值`string.Empty`，否則使用。
1. 更改不再作為 傳遞，`IReadOnlyList<Document>`而是一種`IReadOnlyCollection<T>``T`需要定義的類型，不再有基項類。
1. 要處理更改，不再需要實現，而是需要[定義委託](change-feed-processor.md#implementing-the-change-feed-processor)。 委託可以是靜態函數，或者，如果需要跨執行保持狀態，則可以創建自己的類並將實例方法作為委託傳遞。

例如，如果用於生成更改饋送處理器的原始代碼如下所示：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

遷移的代碼將如下所示：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

委託，可以是一個靜態方法：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>狀態和租賃容器

與更改源處理器庫類似，.NET V3 SDK 中的更改源功能使用[租約容器](change-feed-processor.md#components-of-the-change-feed-processor)來存儲狀態。 但是，架構是不同的。

SDK V3 更改饋送處理器將檢測任何舊的庫狀態，並在首次執行遷移的應用程式代碼時自動將其遷移到新架構。 

您可以使用舊代碼安全地停止應用程式、將代碼遷移到新版本、啟動遷移的應用程式，並且新版本將拾取和處理在應用程式停止期間發生的任何更改。

> [!NOTE]
> 使用庫的應用程式遷移到 .NET V3 SDK 是單向的，因為狀態（租約）將遷移到新架構。 遷移不向後相容。


## <a name="additional-resources"></a>其他資源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的使用範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 上的其他範例](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要處理器：

* [變更摘要處理器的概觀](change-feed-processor.md)
* [使用變更摘要估算器](how-to-use-change-feed-estimator.md)
* [變更摘要處理器開始時間](how-to-configure-change-feed-start-time.md)
