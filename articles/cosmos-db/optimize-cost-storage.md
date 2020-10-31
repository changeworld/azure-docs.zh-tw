---
title: 在 Azure Cosmos DB 中最佳化儲存體成本
description: 本文說明如何針對儲存在 Azure Cosmos DB 中的資料管理儲存體成本
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 92bd3ff925080def4b2f074d07e662dfdbdbee01
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080845"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中最佳化儲存體成本
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 能提供無限制的儲存體和輸送量。 不同於輸送量 (需要在 Azure Cosmos 容器或資料庫上進行佈建/設定)，儲存體是根據耗用量來計費。 您只需為自己所耗用的邏輯儲存體支付費用，且不需要事先保留任何儲存體。 儲存體會根據您在 Azure Cosmos 容器中新增或移除的資料，自動相應增加和減少。

## <a name="storage-cost"></a>儲存成本

儲存體是以 GB 為單位進行計費。 SSD 本機儲存體是用於您的資料和編制索引。 所使用的總儲存體等於用在您使用 Azure Cosmos DB 之所有區域上所需的儲存體。 如果您以全域方式將 Azure Cosmos 帳戶跨越三個區域進行複寫，您將需個別支付那三個區域中的總儲存體成本。 若要預估您的儲存體需求，請參閱 [Capacity Planner](https://www.documentdb.com/capacityplanner) \(英文\) 工具。 Azure Cosmos DB 中儲存體的成本為每月每 GB 0.25 美元，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)以取得最新更新。 您可以設定警示以判斷您 Azure Cosmos 容器所使用的儲存體；若要監視您的儲存體，請參閱[監視 Azure Cosmos DB](./monitor-cosmos-db.md) 一文。

## <a name="optimize-cost-with-item-size"></a>搭配項目大小以最佳化成本

若要取得最佳化的效能與成本效益，Azure Cosmos DB 預期項目大小為 2 MB 或更小。 如果您需要讓項目儲存大於 2 MB 的資料，請考慮重新設計該項目的結構描述。 在您無法重新設計結構描述的罕見情況下，您可以將項目分割成子項目，並搭配通用識別碼 (ID) 以邏輯方式連結它們。 所有的 Azure Cosmos DB 功能都能透過錨定到該邏輯識別碼來一致地運作。

## <a name="optimize-cost-with-indexing"></a>搭配編製索引以最佳化成本

根據預設，系統會自動對資料編製索引，而這可能會增加所耗用的總儲存體。 不過，您可以套用自訂索引原則來減少此額外負荷。 沒有透過原則進行調整的自動編製索引，大約為項目大小的 10-20%。 透過移除或自訂索引原則，您便不需要支付寫入的額外費用，也不需要額外的輸送量容量。 請參閱[在 Azure Cosmos DB 中編製索引](index-policy.md)以設定自訂編製索引原則。 如果您曾使用過關聯式資料庫，您可能會認為「對所有項目編製索引」會讓所使用的儲存體變成兩倍或是更多。 不過，在 Azure Cosmos DB 中，就中間值來說，其使用量相對低許多。 在 Azure Cosmos DB 中，就算是搭配自動編製索引，儲存體的索引額外負荷通常很低 (10-20%)，因為它具有低儲存體耗用量的設計。 透過管理編制索引原則，您可以更精細地控制索引耗用量和查詢效能之間的取捨。

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>搭配存留時間和變更摘要以最佳化成本

在您不需要資料之後，您可以正常地將它從您的 Azure Cosmos 帳戶中刪除，方法是使用[存留時間](time-to-live.md)、[變更摘要](change-feed.md)，或是將舊資料遷移到另一個資料存放區 (例如 Azure Blob 儲存體或 Azure 資料倉儲)。 Azure Cosmos DB 可讓您利用存留時間 (簡稱 TTL)，在一段時間後自動從容器中刪除項目。 根據預設，您可以在容器層級設定存留時間，且覆寫每個項目的值。 在容器或項目層級設定 TTL 之後，Azure Cosmos DB 會從上次修改該項目的時間開始算起，在經過所設定的時間週期後自動移除那些項目。 透過使用變更摘要，您可以將資料遷移到 Azure Cosmos DB 中的另一個容器，或是外部資料存放區。 遷移不需要花費任何停機時間，且當您完成遷移時，便可以刪除或設定存留時間來刪除來源 Azure Cosmos 容器。

## <a name="optimize-cost-with-rich-media-data-types"></a>搭配豐富媒體資料類型以最佳化成本 

如果您想要儲存豐富媒體類型 (例如影片、影像等)，Azure Cosmos DB 中有數個選項可供您選擇。 其中一個選項是將這些豐富媒體類型儲存為 Azure Cosmos 項目。 每個項目有 2-MB 的限制，而您可以透過將資料項目鏈結為多個子項目來避開這個限制。 或者，您可以將它們儲存在 Azure Blob 儲存體中，並使用中繼資料來從 Azure Cosmos 項目參考它們。 此方法有數個優點和缺點。 第一個方法可讓您針對豐富媒體資料類型及一般的 Azure Cosmos 項目，在延遲、輸送量 SLA 及周全全域散發功能上取得最佳效能。 不過此支援需支付較高的成本。 透過將媒體儲存在 Azure Blob 儲存體中，您可以降低整體成本。 如果延遲對您來說很重要，您可以針對從 Azure Cosmos 項目參考的豐富媒體檔案使用進階儲存體。 這原生地與 CDN 整合，可迴避地理限制，以較低的成本從邊緣伺服器提供影像。 此案例的缺點在於您必須使用兩個服務 (Azure Cosmos DB 和 Azure Blob 儲存體)，這可能會增加營運成本。 

## <a name="check-storage-consumed"></a>檢查已耗用的儲存體

若要檢查 Azure Cosmos 容器的儲存體耗用量，您可以在容器上執行 HEAD 或 GET 要求，然後檢查 `x-ms-request-quota` 和 `x-ms-request-usage` 標頭。 或者，在使用 .NET SDK 時，您可以使用 [DocumentSizeQuota](/previous-versions/azure/dn850325(v%3Dazure.100))和 [>documentsizeusage](/previous-versions/azure/dn850324(v=azure.100)) 屬性來取得所耗用的儲存體。

## <a name="using-sdk"></a>使用 SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>下一步

接下來，您可以利用下列文章繼續深入了解 Azure Cosmos DB 中有關成本最佳化的詳細資訊：

* 深入了解[最佳化開發與測試](optimize-dev-test.md)
* 深入了解 [Azure Cosmos DB 帳單](understand-your-bill.md)
* 深入了解[最佳化輸送量成本](optimize-cost-throughput.md)
* 深入了解[最佳化讀取和寫入的成本](optimize-cost-reads-writes.md)
* 深入了解[最佳化查詢成本](./optimize-cost-reads-writes.md)
* 深入了解[最佳化多重區域 Azure Cosmos 帳戶的成本](optimize-cost-regions.md)
