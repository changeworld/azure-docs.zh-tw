---
title: 使用變更摘要估算器 - Azure Cosmos DB
description: 了解如何使用變更摘要估算器來分析變更摘要處理器的進度
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/15/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: b785479a1f3169c32be0d9fbd7accf37bc7ba8ed
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93085778"
---
# <a name="use-the-change-feed-estimator"></a>使用變更摘要估算器
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文說明如何在[變更摘要處理器](./change-feed-processor.md)執行個體讀取變更摘要時，監視處理器的進度。

## <a name="why-is-monitoring-progress-important"></a>為什麼監視進度很重要？

變更摘要處理器會以指標形式在[變更摘要](./change-feed.md)中向前移動，並將變更傳遞至委派執行。 

您的變更摘要處理器部署可以根據其可用資源 (例如 CPU、記憶體、網路等等)，以特定的速率處理變更。

如果此速率比您在 Azure Cosmos 容器中發生變更的速率慢，您的處理器將會開始落後。

識別此情況有助於了解我們是否需要調整變更摘要處理器的部署。

## <a name="implement-the-change-feed-estimator"></a>實作變更摘要估算器

如同[變更摘要處理器](./change-feed-processor.md)，變更摘要估算器會作為推播模型來運作。 估算器會測量上次處理的項目 (由租用容器的狀態所定義) 與容器中最新變更之間的差異，並將此值推送至委派。 測量的執行間隔也可以自訂，預設值為 5 秒。

例如，如果您變更摘要處理器的定義如下：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

初始化估算器以測量該處理器的正確方式是使用 `GetChangeFeedEstimatorBuilder`，如下所示：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

處理器和估算器會共用相同的 `leaseContainer` 和相同的名稱。

另外兩個參數是委派，其收到的數字代表處理器 **有多少項變更等候讀取** ，以及您要取得此測量的時間間隔。

接收估計的委派範例如下：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

您可以將此估計傳送到監視解決方案，並用其了解您的進度在一段時間內的行為。

> [!NOTE]
> 變更摘要估算器不需要部署為變更摘要處理器的一部分，也不是相同專案的一部分。 它可以是獨立的，並在完全不同的執行個體中執行。 它只需要使用相同的名稱和租用設定。

## <a name="additional-resources"></a>其他資源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的使用範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 上的其他範例](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要處理器：

* [變更摘要處理器的概觀](change-feed-processor.md)
* [變更摘要處理器開始時間](./change-feed-processor.md#starting-time)