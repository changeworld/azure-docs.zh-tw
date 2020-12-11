---
title: Azure Cosmos DB Java SDK v4 的效能秘訣
description: 了解用以改善 Java SDK v4 Azure Cosmos 資料庫效能的用戶端設定選項
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 10/13/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: 79f8c868b68cba1cff3e99e88e989fcc4d2a3df2
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029017"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Azure Cosmos DB Java SDK v4 的效能秘訣
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [非同步 Java SDK v2](performance-tips-async-java.md)
> * [同步 Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> 本文提供的效能秘訣僅適用於 Azure Cosmos DB JAVA SDK v4。 如需更多資訊，請參閱 Azure Cosmos DB Java SDK v4 [版本資訊](sql-api-sdk-java-v4.md)、[Maven 存放庫](https://mvnrepository.com/artifact/com.azure/azure-cosmos)和 Azure Cosmos DB Java SDK v4 [疑難排解指南](troubleshoot-java-sdk-v4-sql.md)。 如果您目前使用的版本比 v4 舊，請參閱[遷移至 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南，以取得升級至 v4 的協助。
>

Azure Cosmos DB 是一個既快速又彈性的分散式資料庫，可在獲得延遲與輸送量保證的情況下順暢地調整。 使用 Azure Cosmos DB 時，您不須進行主要的架構變更，或是撰寫複雜的程式碼來調整您的資料庫。 相應增加和減少就像進行單一 API 呼叫或 SDK 方法呼叫一樣簡單。 不過，由於 Azure Cosmos DB 是透過網路呼叫存取，所以您可以在使用 Azure Cosmos DB Java SDK v4 時，進行用戶端最佳化以達到最高效能。

如果您詢問「如何改善我的資料庫效能？ 」，請考慮下列選項：

## <a name="networking"></a>網路功能

* **連線模式：使用直接模式**
<a id="direct-connection"></a>
    
    JAVA SDK 預設連接模式是 direct。 您可以使用 *directMode ( # B1* 或 *GatewayMode ( # B3* 方法，在用戶端建立器中設定連接模式，如下所示。 若要使用預設值設定任一種模式，請呼叫其中一個沒有引數的方法。 否則，請將設定類別實例傳遞為引數 (*DirectConnectionConfig* for *DirectMode ( # B2*，  *GatewayConnectionConfig* for *gatewayMode ( # B4*. ) 。 若要深入瞭解不同的連線選項，請參閱連線 [模式](sql-sdk-connection-modes.md) 文章。
    
    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[非同步](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) 非同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientConnectionModeAsync)]

    # <a name="sync"></a>[同步](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) 同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientConnectionModeSync)]

    --- 

    *DirectMode ( # B1* 方法有其他覆寫，原因如下。 控制平面作業（例如資料庫和容器 CRUD） *一律* 會使用閘道模式;當使用者已設定資料平面作業的直接模式時，控制平面作業會使用預設閘道模式設定。 這適合大部分的使用者。 不過，如果使用者想要直接模式進行資料平面作業，以及控制平面閘道模式參數的 tunability，則可以使用下列 *directMode ( # B1* 覆寫：

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[非同步](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) 非同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientDirectOverrideAsync)]

    # <a name="sync"></a>[同步](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) 同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientDirectOverrideSync)]

    --- 

<a name="collocate-clients"></a>
* **為了效能在相同 Azure 區域中共置用戶端**  <a id="same-region"></a>

    可能的話，請將任何呼叫 Azure Cosmos DB 的應用程式放在與 Azure Cosmos DB 資料庫相同的區域中。 以約略的比較來說，在相同區域內對 Azure Cosmos DB 進行的呼叫會在 1-2 毫秒內完成，但美國西岸和美國東岸之間的延遲則會大於 50 毫秒。 視要求所採用的路由而定，各項要求從用戶端傳遞至 Azure 資料中心界限時的這類延遲可能有所不同。 確保呼叫端應用程式與佈建的 Azure Cosmos DB 端點位於相同的 Azure 區域中，將可能達到最低的延遲。 如需可用區域的清單，請參閱 [Azure 區域](https://azure.microsoft.com/regions/#services)。

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB 連接原則的圖例" border="false":::

    與多重區域 Azure Cosmos DB 帳戶互動的應用程式必須設定[慣用位置](tutorial-global-distribution-sql-api.md#preferred-locations)，以確保要求會進入共置區域。

* **請在您的 Azure VM 上啟用加速網路，以降低延遲。**

建議您依照指示，在 [Windows (按一下以取得指示)](../virtual-network/create-vm-accelerated-networking-powershell.md) 或 [Linux (按一下以取得指示)](../virtual-network/create-vm-accelerated-networking-cli.md) Azure VM中啟用加速網路，以求達到最大效能。

如果沒有加速網路，在 Azure VM 和其他 Azure 資源之間傳輸的 IO，可能反而會透過位於 VM 和其網路卡之間的主機和虛擬交換器來路由傳送。 將主機和虛擬交換器內嵌在資料路徑中，不僅會增加通道的延遲和抖動，還會佔用 VM 的 CPU 週期。 使用加速網路時，VM 會直接使用不含中繼的 NIC；由主機和虛擬交換器處理的任何網路原則詳細資料，都會在 NIC 的硬體中加以處理；完全略過主機和虛擬交換器。 一般來說可以預期降低延遲並提高輸送量，而且啟用加速網路時，延遲情形會更為 *一致*，CPU 使用率也會降低。

限制：VM OS 必須支援加速網路，而且只有在 VM 停止並解除配置時，才能啟用加速網路。 無法使用 Azure Resource Manager 部署 VM。

如需詳細資訊，請參閱 [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) 和 [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 指示。

## <a name="sdk-usage"></a>SDK 使用方式
* **安裝最新的 SDK**

    Azure Cosmos DB SDK 會持續改善以提供最佳效能。 請參閱 [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) 頁面來判斷最新的 SDK 並檢閱改善項目。

* **在應用程式存留期內使用單一 Azure Cosmos DB 用戶端**

    每個 Azure Cosmos DB 用戶端執行個體都是安全執行緒，並且會有效率地執行連線管理和位址快取處理。 若要藉由 Azure Cosmos DB 用戶端獲得有效率的連線管理和更佳的效能，建議在應用程式存留期內，對每個 AppDomain 使用單一 Azure Cosmos DB 用戶端執行個體。

   <a id="max-connection"></a>

* **使用應用程式所需的最低一致性層級**

    建立 *CosmosClient* 時，如果未明確設定為 *工作階段*，則會使用預設的一致性。 如果您的應用程式邏輯不需要 *工作階段* 一致性，請將 [一致性] 設定為 [最終]。 注意：建議您在採用 Azure Cosmos DB 變更摘要處理器的應用程式中，至少使用 *工作階段* 一致性。

* **使用非同步 API 盡可能利用佈建的輸送量**

    Azure Cosmos DB JAVA SDK v4 會組合兩個 API，也就是同步和非同步。 大致來說，非同步 API 會實作 SDK 功能，而同步 API 則是對非同步 API 發出封鎖呼叫的精簡包裝函式。 這與舊版 Azure Cosmos DB 非同步 JAVA SDK v2 (僅限非同步) 和舊版 Azure Cosmos DB 同步 JAVA SDK v2 (僅限同步處理，並具有完全獨立的實作方式) 相反。 
    
    在用戶端初始化期間要決定所選擇的 API；*CosmosAsyncClient* 支援非同步 API，而 *CosmosClient* 支援同步 API。 
    
    非同步 API 會實作非封鎖 IO，而且如果目標是在對 Azure Cosmos DB 發出要求時，要達到最大的輸送量，這是最佳選擇。 
    
    如果想要或需要 API 封鎖對每個要求的回應，或如果同步作業是應用程式中的主要架構，則使用同步 API 會是正確選擇。 舉例來說，如果輸送量並非重點，在微服務應用程式中將資料保存到 Azure Cosmos DB 時，建議使用同步 API。  
    
    請注意，同步 API 輸送量會隨著要求的回應時間增加而降低，而非同步 API 會使硬體的所有頻寬達到飽和。 
    
    您使用同步 API 時，可透過地理共置獲得更高且更一致的輸送量 (請參閱[為了效能在相同 Azure 區域中共置用戶端](#collocate-clients))，但仍不應超過非同步 API 可達成的輸送量。

    某些使用者可能也不太熟悉 [Project Reactor](https://projectreactor.io/)，這是用來實作 Azure Cosmos DB JAVA SDK v4 Async API 的回應式串流架構。 如果對此有所顧慮，建議參閱我們的簡介[反應器模式指南](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) (英文)，然後查看[回應式程式設計簡介](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro) (英文)，提高自己的熟悉度。 如果您已使用 Azure Cosmos DB 搭配非同步介面，而且您使用的 SDK 是 Azure Cosmos DB 非同步 JAVA SDK v2，則您可能熟知 [ReactiveX](http://reactivex.io/)/[RxJava](https://github.com/ReactiveX/RxJava)，但不確定 Project Reactor 有哪些變更。 在此情況下，請參閱我們的[反應器與RxJava 指南](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) (英文) 加深了解。

    下列程式碼片段示範了如何分別針對非同步 API 或同步 API 作業初始化 Azure Cosmos DB 用戶端：

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[非同步](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) 非同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientAsync)]

    # <a name="sync"></a>[同步](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) 同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientSync)]

    --- 

* **調整 ConnectionPolicy**

    根據預設，使用 Azure Cosmos DB JAVA SDK v4 時，會透過 TCP 提出直接模式 Cosmos DB 要求。 在內部直接模式中，會使用特殊的架構來動態管理網路資源，並獲得最佳效能。

    在 Azure Cosmos DB JAVA SDK v4 中，直接模式最適合用來改善工作負載最大的資料庫效能。 

    * ***直接模式 _ 總覽**

        :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="直接模式架構圖例" border="false":::

        直接模式中採用的用戶端架構，能讓網路使用率得以預測，並實現對 Azure Cosmos DB 複本的多工存取。 上圖顯示直接模式如何將用戶端要求路由傳送到 Cosmos DB 後端複本。 直接模式架構會在用戶端為每個 DB 複本配置最多 10 _ *通道**。 通道是前面加上要求緩衝區的 TCP 連線，深度為 30 個要求。 屬於複本的通道會視複本 **服務端點** 的需求動態配置。 當使用者在直接模式下發出要求時，**TransportClient** 會根據分割區索引鍵，將要求路由傳送到適當的服務端點。 **要求佇列** 會在服務端點之前對要求進行緩衝處理。

    * ***Direct mode 的設定選項** _

        如果需要非預設的直接模式行為，請建立 _DirectConnectionConfig * 實例，並自訂其屬性，然後將自訂的屬性實例傳遞至 Azure Cosmos DB 用戶端產生器中的 *directMode ( # B1* 方法。

        這些設定會控制上述基礎直接模式架構的行為。

        首先，請使用下列建議的組態設定。 這些 *DirectConnectionConfig* 選項為 advanced configuration 設定，可能會以非預期的方式影響 SDK 效能;我們建議使用者避免修改它們，除非他們覺得很樂意瞭解取捨，而且是絕對必要的。 如果遇到關於此特定主題的問題，請連絡 [Azure Cosmos DB 小組](mailto:CosmosDBPerformanceSupport@service.microsoft.com)。

        | 組態選項       | 預設   |
        | :------------------:       | :-----:   |
        | idleConnectionTimeout      | "PT0"     |
        | maxConnectionsPerEndpoint  | "130"     |
        | connectTimeout             | "PT5S"    |
        | idleEndpointTimeout        | PT1H    |
        | maxRequestsPerConnection   | 30      |

* **微調分割之集合的平行查詢**

    Azure Cosmos DB Java SDK v4 支援平行查詢，可讓您平行查詢分割的集合。 如需詳細資訊，請參閱使用 Azure Cosmos DB Java SDK v4 的相關[程式碼範例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)。 平行查詢的設計目的是要改善其連續對應項目的查詢延遲和輸送量。

    * ***微調 setMaxDegreeOfParallelism \:** _
    
        平行查詢的運作方式是以平行方式查詢多個分割。 不過，對於查詢會以循序方式擷取來自個別分割集合的資料。 因此，使用 setMaxDegreeOfParallelism 設定分割數目會最有機會達到最高效能的查詢，但前提是其他所有系統條件皆維持不變。 如果您不知道分割數目，您可以使用 setMaxDegreeOfParallelism 設定為較高的數字，然後系統會選擇最小值 (分割數目、使用者提供的輸入) 作為平行處理原則的最大刻度。

        請務必注意，若對於查詢是以平均方式將資料分佈於所有分割，平行查詢便會產生最佳效益。 如果分割之集合的分割方式是查詢所傳回的所有或大多數資料集中在少數幾個分割中 (最差的情況是集中在一個分割)，則這些分割會成為查詢效能的瓶頸。

    _ ***微調 setMaxBufferedItemCount \:** _
    
        Parallel query is designed to pre-fetch results while the current batch of results is being processed by the client. The pre-fetching helps in overall latency improvement of a query. setMaxBufferedItemCount limits the number of pre-fetched results. Setting setMaxBufferedItemCount to the expected number of results returned (or a higher number) enables the query to receive maximum benefit from pre-fetching.

        Pre-fetching works the same way irrespective of the MaxDegreeOfParallelism, and there is a single buffer for the data from all partitions.

_ **向外擴充您的用戶端-工作負載**

    If you are testing at high throughput levels, the client application may become the bottleneck due to the machine capping out on CPU or network utilization. If you reach this point, you can continue to push the Azure Cosmos DB account further by scaling out your client applications across multiple servers.

    A good rule of thumb is not to exceed >50% CPU utilization on any given server, to keep latency low.

   <a id="tune-page-size"></a>

* **調整查詢/讀取摘要的頁面大小以獲得更好的效能**

    使用讀取摘要功能 (例如 readItems) 執行大量文件讀取時，或發出 SQL 查詢 (queryItems) 時，如果結果集太大，則會以分段方式傳回結果。 根據預設，會以 100 個項目或 1 MB 的區塊傳回結果 (以先達到的限制為準)。

    假設您的應用程式發出查詢至 Azure Cosmos DB，再假設您的應用程式需要一組完整的查詢結果才能完成其工作。 若要減少擷取所有適用結果所需的網路來回行程次數，您可以調整 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 要求標題欄位，以此方式增加頁面大小。 

    * 對於單一資料分割區查詢，將 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 欄位值調整為 -1 (不限制頁面大小)，即會將查詢回應頁面數目降至最低，而延遲度會最高：會以單一頁面傳回完整結果集，如果查詢花費的時間超過逾時間隔，則會盡可能以最少頁數傳回完整結果集。 如此可省下數倍的要求來回時間。
    
    * 針對跨分割區查詢，將 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 欄位設定為 -1，並移除頁面大小限制，可能會造成 SDK 出現無法管理的頁面大小。 若為跨資料分割區，建議將頁面大小限制提高有限範圍內的較大值 (例如 1000)，以減少延遲。 
    
    在某些應用程式中，您可能不需要完整的查詢結果集。 在您只需要顯示幾筆結果的情況下 (例如，您的使用者介面或應用程式 API 一次只傳回 10 筆結果)，也可以將頁面大小縮小為 10，以降低讀取和查詢所耗用的輸送量。

    您也可以設定 byPage 方法的慣用頁面大小引數，而不是直接修改 REST 標題欄位。 請記住，[x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 或 byPage 的慣用頁面大小引數，均只會設定頁面大小的上限，而非強制規定；因此基於各種原因，可能會看到 Azure Cosmos DB 傳回的頁面小於您慣用的頁面大小。 

* **使用適當排程器 (避免竊取事件迴圈 IO Netty 執行緒)**

    Azure Cosmos DB JAVA SDK 的非同步功能是以 [netty](https://netty.io/) 非封鎖 IO 為基礎。 SDK 會使用固定數目的 IO netty 事件迴圈執行緒 (和您電腦所擁有的 CPU 核心數一樣多) 來執行 IO 作業。 API 所傳回的 Flux 會在其中一個共用的 IO 事件迴圈 netty 執行緒上發出結果。 因此，請切勿封鎖共用的 IO 事件迴圈 netty 執行緒。 若執行 CPU 密集工作或封鎖 IO 事件迴圈 netty 執行緒上的作業，可能會導致鎖死或大幅降低 SDK 輸送量。

    例如，下列程式碼會在事件迴圈 IO netty 執行緒上執行 CPU 密集工作：
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) 非同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNeedsSchedulerAsync)]

    在收到結果之後，如果您需要對結果進行 CPU 密集工作，請避免在事件迴圈 IO netty 執行緒上進行。 您可以改為提供自己的排程器來提供您自己的執行緒來執行工作，如下所示 (需要 `import reactor.core.scheduler.Schedulers`) 。

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) 非同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddSchedulerAsync)]

    根據您的工作類型，您應該將適當的現有 Reactor 排程器用於您的工作。 閱讀這裡 [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html)。

    如需 Azure Cosmos DB JAVA SDK v4 的詳細資訊，請參閱 [GitHub 上適用於 Java monorepo 的 Azure SDK Cosmos DB 目錄](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) (英文)。

* **將應用程式中的記錄設定最佳化**

    您可能會基於各種原因，而想要或需要在產生高要求輸送量的執行緒中新增記錄。 如果您的目標是使用此執行緒產生的要求，使容器的佈建輸送量完全飽和，記錄最佳化即可大幅提升效能。

    * ***設定非同步記錄器** _

        同步記錄器的延遲一定會影響到產生要求的執行緒的整體延遲計算。 建議使用非同步記錄器 (例如 [log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0))，從高效能應用程式執行緒將將記錄作業的額外負荷分離出去。

    _ ***停用 netty 的記錄** _

        Netty library logging is chatty and needs to be turned off (suppressing sign in the configuration may not be enough) to avoid additional CPU costs. If you are not in debugging mode, disable netty's logging altogether. So if you are using log4j to remove the additional CPU costs incurred by ``org.apache.log4j.Category.callAppenders()`` from netty add the following line to your codebase:

        ```java
        org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
        ```

 _ **OS 開啟檔案資源限制**
 
    某些 Linux 系統 (例如 Red Hat) 有開啟檔案數目的上限，因此有連線總數的上限。 執行下列命令來檢視目前的限制：

    ```bash
    ulimit -a
    ```

    開啟檔案 (nofile) 的數目必須夠大，才能有足夠空間供您設定的連線集區大小和 OS 的其他開啟檔案使用。 您可以進行修改，以允許較大的連線集區大小。

    開啟 limits.conf 檔案：

    ```bash
    vim /etc/security/limits.conf
    ```
    
    新增/修改下列幾行：

    ```
    * - nofile 100000
    ```

* **在點寫入中指定分割區索引鍵**

    若要改善點寫入的效能，請在點寫入 API 呼叫中指定項目分割區索引鍵，如下所示：

    # <a name="async"></a>[非同步](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) 非同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNoPKAsync)]

    # <a name="sync"></a>[同步](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) 同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceNoPKSync)]

    --- 

    而不只是提供項目執行個體，如下所示：

    # <a name="async"></a>[非同步](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) 非同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddPKAsync)]

    # <a name="sync"></a>[同步](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) 同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceAddPKSync)]

    --- 

    後者受到支援，但會導致應用程式增加延遲；SDK 必須剖析項目，並將分割區索引鍵解壓縮。

## <a name="indexing-policy"></a>編製索引原則
 
* **從索引編製中排除未使用的路徑以加快寫入速度**

    Azure Cosmos DB 的索引編製原則可讓您利用檢索路徑 (setIncludedPaths 和 setExcludedPaths)，指定要在索引編製中包含或排除的文件路徑。 在事先知道查詢模式的案例中，使用檢索路徑可改善寫入效能並降低索引儲存空間，因為檢索成本與檢索的唯一路徑數目直接相互關聯。 例如，下列程式碼示範如何包含和排除檔的整個區段 (也稱為子樹，) 使用 "*" 萬用字元進行編制索引。

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

    如需詳細資訊，請參閱 [Azure Cosmos DB 索引編製原則](index-policy.md)。

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

* **測量和調整較低的要求單位/秒使用量**

    Azure Cosmos DB 提供許多的資料庫作業，包括使用 UDF、預存程序和觸發程序進行關聯式和階層式查詢，而這些作業全都是對資料庫集合內的文件來進行。 與上述各項作業相關聯的成本，會因為完成作業所需的 CPU、IO 和記憶體而不同。 您不需要考慮和管理硬體資源，您可以將要求單位 (RU) 想成是執行各種資料庫作業以及服務應用程式要求時所需的資源數量。

    輸送量是根據為每個容器所設定的[要求單位](request-units.md)數量來佈建。 要求單位消耗量是以每秒的速率來計算。 如果應用程式的速率超過為其容器佈建的要求單位速率，便會受到限制，直到該速率降到容器的佈建層級以下。 如果您的應用程式需要較高的輸送量，您可以藉由佈建其他的要求單位來增加輸送量。

    查詢的複雜性會影響針對作業所耗用的要求單位數量。 述詞數目、述詞性質、UDF 數目，以及來源資料集的大小，全都會影響查詢作業的成本。

    若要測量任何作業 (建立、更新或刪除) 的額外負荷，請檢查 [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 標頭，來測量這些作業所耗用的要求單位數量。 您也可以查看 ResourceResponse\<T> 或 FeedResponse\<T> 中的對等 RequestCharge 屬性。

    # <a name="async"></a>[非同步](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) 非同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceRequestChargeAsync)]

    # <a name="sync"></a>[同步](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) 同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceRequestChargeSync)]

    --- 

    在此標頭中傳回的要求費用是佈建輸送量的一小部分。 例如，如果您佈建了 2000 RU/秒，且前述查詢傳回 1000 份 1 KB 文件，則作業成本會是 1000。 因此在一秒內，伺服器在對後續要求進行速率限制前，只會接受兩個這類要求。 如需詳細資訊，請參閱[要求單位](request-units.md)和[要求單位計算機](https://www.documentdb.com/capacityplanner)。

<a id="429"></a>
* **處理速率限制/要求速率太大**

    當用戶端嘗試超過帳戶保留的輸送量時，伺服器的效能不會降低，而且不會使用超過保留層級的輸送量容量。 伺服器將預先使用 RequestRateTooLarge (HTTP 狀態碼 429) 來結束要求，並傳回 [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 標頭，以指出使用者重試要求之前必須等候的時間量 (毫秒)。

    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```

    SDK 全都隱含地攔截這個回應，採用伺服器指定的 retry-after 標頭，並重試此要求。 除非有多個用戶端同時存取您的帳戶，否則下次重試將會成功。

    如果您有多個用戶端都以高於要求速率的方式累積運作，則用戶端在內部設定為 9 的預設重試計數可能會不敷使用；在此情況下，用戶端會擲回 CosmosClientException (狀態碼 429) 到應用程式。 在 ConnectionPolicy 執行個體上使用 setRetryOptions，即可變更預設重試計數。 根據預設，如果要求繼續以高於要求速率的方式運作，則會在 30 秒的累計等候時間後傳回 CosmosClientException (狀態碼 429)。 即使目前的重試計數小於最大重試計數 (預設值 9 或使用者定義的值)，也會發生這種情況。

    雖然自動重試行為有助於改善大部分應用程式的恢復功能和可用性，但是在進行效能基準測試時可能會有所歧異 (尤其是在測量延遲時)。 如果實驗達到伺服器節流並導致用戶端 SDK 以無訊息模式重試，則用戶端觀察到的延遲將會突然增加。 若要避免效能實驗期間的延遲尖峰，測量每個作業所傳回的費用，並確保要求是以低於保留要求速率的方式運作。 如需詳細資訊，請參閱 [要求單位](request-units.md)。

* **輸送量較高之少量文件的設計**

    指定之作業的要求費用 (要求處理成本) 與文件大小直接相互關聯。 大型文件的作業成本高於小型文件的作業成本。 在理想情況下，架構應用程式和工作流程時，請讓項目大小約為 1KB 或類似的順序或大小。 對於注重延遲的應用程式，請避免大型項目，因為好幾 MB 的文件會造成應用程式變慢。

## <a name="next-steps"></a>後續步驟

若要深入了解如何針對規模和高效能設計您的應用程式，請參閱 [Azure Cosmos DB 的資料分割與調整規模](partitioning-overview.md)。
