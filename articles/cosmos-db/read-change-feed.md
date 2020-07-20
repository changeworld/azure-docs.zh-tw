---
title: 讀取 Azure Cosmos DB 變更摘要
description: 此文章說明各種可用來在 Azure Cosmos DB 中讀取和存取變更摘要的選項。
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.reviewer: sngun
ms.openlocfilehash: d7408f3b3e955d397ba4a54d07323f80dd72c3d3
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697338"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>讀取 Azure Cosmos DB 變更摘要

您可以使用推送模型或提取模型來處理 Azure Cosmos DB 變更摘要。 使用推送模型時，用戶端可向伺服器要求工作，並具備處理變更的商務邏輯。 但是，伺服器上會處理檢查變更及儲存上次處理之變更狀態的複雜度。

使用提取模型，伺服器可要求工作，通常是向中央工作佇列提出要求。 在此案例中，用戶端不僅具備處理變更的商務邏輯，也會儲存上次處理之變更的狀態、處理多個平行處理變更之用戶端間的負載平衡，以及處理錯誤。

若從 Azure Cosmos DB 變更摘要讀取，我們通常建議使用推送模型，因為您不需要擔心下列事項：

- 輪詢變更摘要，以用於未來變更。
- 儲存上次處理之變更的狀態。 從變更摘要讀取時，這會自動儲存於[租用容器](change-feed-processor.md#components-of-the-change-feed-processor)中。
- 在取用變更的多個用戶端之間進行負載平衡。 例如，如果有一個用戶端趕不上處理變更，而另一個具有可用容量。
- [處理錯誤](change-feed-processor.md#error-handling)。 例如，自動重試失敗的變更，系統無法在程式碼中有未處理的例外狀況或暫時性網路問題之後正確處理這類變更。

大部分使用 Azure Cosmos DB 變更摘要的案例都將使用其中一個推送模型選項。 不過，在某些案例中，您可能想要讓提取模型具備額外的低階控制。 其中包括：

- 讀取特定分割區索引鍵的變更
- 控制用戶端接收變更以進行處理的步調
- 在變更摘要中對現有資料進行一次性讀取 (例如，進行資料移轉)

## <a name="reading-change-feed-with-a-push-model"></a>使用推送模型讀取變更摘要

使用推送模型，是從變更摘要中讀取的最簡單方式。 有兩種方式可讓您使用推送模型以從變更摘要中進行讀取：[Azure Functions Cosmos DB 觸發程序](change-feed-functions.md)和[變更摘要處理器程式庫](change-feed-processor.md)。 Azure Functions 會在幕後使用變更摘要處理器，因此，這兩者讀取變更摘要的方式非常類似。 只需將 Azure Functions 想像為適用於變更摘要處理器的裝載平台，而非讀取變更摘要的完全不同方式。

### <a name="azure-functions"></a>Azure Functions

如果您剛開始使用變更摘要，Azure Functions 就是最簡單的選項。 因其簡潔性之故，其也是大多數變更摘要使用案例的建議選項。 當您建立適用於 Azure Cosmos DB 的 Azure Functions 觸發程序時，您會選取要連線的容器，如此即會在容器中有所變更時觸發 Azure 函式。 由於 Azure Functions 會在幕後使用變更摘要處理器，因此，其會自動跨容器的[分割區](partition-data.md)平行處理變更。

使用 Azure Functions 進行開發是一種簡單的體驗，而且可能為比自行部署變更摘要處理器更快。 您可以使用 Azure Functions 入口網站來建立觸發程序，或使用 SDK 以程式設計方式來建立。 Visual Studio 和 VS Code 提供撰寫 Azure Functions 的支援，您甚至可以使用 Azure Functions CLI 進行跨平台開發。 您可以在電腦上撰寫和偵錯程式碼，然後只需按一下即可部署該功能。 若要深入了解，請參閱[使用 Azure Functions 的無伺服器資料庫計算](serverless-computing-database.md)和[搭配使用變更摘要與 Azure Functions](change-feed-functions.md) 文章。

### <a name="change-feed-processor-library"></a>變更摘要處理器程式庫

變更摘要處理器讓您更能控制變更摘要，而且仍會隱藏大多數的複雜度。 變更摘要處理器程式庫會遵循觀察者模式，而您的處理函式會由程式庫呼叫。 變更摘要處理器程式庫將自動檢查是否有變更，如果發現變更，則會將這些變更「推送」到用戶端。 如果您有高輸送量的變更摘要，則可將多個用戶端具現化，以讀取變更摘要。 變更摘要處理器程式庫會自動將負載分散到不同的用戶端。 您不需要在多個用戶端之間實作負載平衡的邏輯，也不需實作任何邏輯來維護租用狀態。

變更摘要處理器程式庫保證所有變更會傳遞「至少一次」。 換句話說，如果您使用變更摘要處理器程式庫，則將針對變更摘要中的每個項目成功呼叫您的處理函式。 如果處理函式中的商務邏輯有未處理的例外狀況，失敗的變更將會重試，直到成功處理為止。 為了避免您的變更摘要處理器「停滯」持續重試相同變更，請在您的處理函式中新增邏輯，以便在發生例外狀況時，將文件寫入至無效信件佇列。 深入了解[錯誤處理](change-feed-processor.md#error-handling)。

在 Azure Functions 中，適用於處理錯誤的建議都一樣。 您仍然應該在委派程式碼中新增邏輯，以便在發生例外狀況時，將文件寫入至無效信件佇列。 不過，如果您的 Azure 函式中有未處理的例外狀況，則產生例外狀況的變更將不會自動重試。 如果商務邏輯中有未處理的例外狀況，Azure 函式將會繼續處理下一個變更。 Azure 函式不會重試相同的失敗變更。

如同 Azure Functions，使用變更摘要處理器程式庫進行開發很容易。 不過，您必須負責為變更摘要處理器部署一或多部主機。 主機是應用程式執行個體，會使用變更摘要處理器來接聽變更。 雖然 Azure Functions 具有自動調整功能，但您還是必須負責調整主機。 若要深入了解，請參閱[使用變更摘要處理器](change-feed-processor.md#dynamic-scaling)。 變更摘要處理器程式庫是 [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) \(英文\) 的一部分。

## <a name="reading-change-feed-with-a-pull-model"></a>使用提取模型讀取變更摘要

[變更摘要提取模型](change-feed-pull-model.md)讓您能夠依自己的步調取用變更摘要。 變更必須由用戶端提出要求，而且不會自動輪詢變更。 如果您想要將上次處理的變更設定為永久「書籤」(類似於推送模型的租用容器)，您將必須[儲存接續權杖](change-feed-pull-model.md#saving-continuation-tokens)。

使用變更摘要提取模型，您可以針對變更摘要取得更低階的控制。 使用提取模型讀取變更摘要時，您有三個選項：

- 讀取整個容器的變更
- 讀取特定 [FeedRange](change-feed-pull-model.md#using-feedrange-for-parallelization) 的變更
- 讀取特定分割區索引鍵值的變更

您可以在多個用戶端之間平行處理變更，就像使用變更摘要處理器一樣。 不過，提取模型不會自動處理用戶端之間的負載平衡。 當您使用提取模型來平行處理變更摘要時，您將先取得 FeedRanges 的清單。 FeedRange 會跨越分割區索引鍵值的範圍。 您將需要有協調器程序，才能取得 FeedRanges，並將其分散到您的機器中。 然後，您可以使用這些 FeedRanges，讓多部機器能夠以平行方式讀取變更摘要。

提取模型並未內建「至少一次」傳遞保證。 提取模型可讓您進行低階控制，以決定您想要如何處理錯誤。

> [!NOTE]
> 變更摘要提取模型目前僅在 [Azure Cosmos DB .NET SDK 中處於預覽狀態](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview)。 此預覽尚未提供給其他 SDK 版本使用。

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Cassandra API 和 MongoDB API 中的變更摘要

變更摘要功能會呈現為 MongoDB API 中的變更資料流，並在 Cassandra API 中使用述詞進行查詢。 若要深入了解 MongoDB API 的實作詳細資料，請參閱[適用於 MongoDB 的 Azure Cosmos DB API 中的變更資料流](mongodb-change-streams.md)。

原生 Apache Cassandra 提供異動資料擷取 (CDC)，這是一種標幟特定資料表以便封存的機制，而且會在達到 CDC 記錄的可設定磁碟大小之後，拒絕那些資料表的寫入。 適用於 Cassandra 的 Azure Cosmos DB API 中的變更摘要功能可增強透過 CQL 使用述詞查詢變更的能力。 若要深入了解實作詳細資料，請參閱[適用於 Cassandra 的 Azure Cosmos DB API 中的變更摘要](cassandra-change-feed.md)。

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要：

* [變更摘要的概觀](change-feed.md)
* [搭配使用變更摘要與 Azure Functions](change-feed-functions.md)
* [使用變更摘要處理器程式庫](change-feed-processor.md)
