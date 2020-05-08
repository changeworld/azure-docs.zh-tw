---
title: Azure Cosmos DB JAVA SDK v4 的效能秘訣
description: 瞭解用戶端設定選項，以改善 JAVA SDK v4 的 Azure Cosmos 資料庫效能
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: ce4e4d11ead41ee8cc4a4bd1d85f1fbad2af4b07
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982525"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Azure Cosmos DB JAVA SDK v4 的效能秘訣

> [!div class="op_single_selector"]
> * [JAVA SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [非同步 Java SDK v2](performance-tips-async-java.md)
> * [同步處理 Java SDK v2](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

> [!IMPORTANT]  
> 本文中的效能秘訣僅適用于 Azure Cosmos DB JAVA SDK v4。 如需詳細資訊，請參閱 Azure Cosmos DB JAVA SDK v4 版本資訊、 [Maven 存放庫](https://mvnrepository.com/artifact/com.azure/azure-cosmos)和 AZURE COSMOS DB java sdk v4[疑難排解指南](troubleshoot-java-sdk-v4-sql.md)。 如果您目前使用的版本比 v4 舊，請參閱[遷移至 Azure Cosmos DB JAVA SDK v4](migrate-java-v4-sdk.md)指南，以取得升級至 v4 的協助。
>

Azure Cosmos DB 是一個既快速又彈性的分散式資料庫，可在獲得延遲與輸送量保證的情況下順暢地調整。 使用 Azure Cosmos DB 時，您不須進行主要的架構變更，或是撰寫複雜的程式碼來調整您的資料庫。 相應增加和減少就像進行單一 API 呼叫或 SDK 方法呼叫一樣簡單。 不過，因為 Azure Cosmos DB 是透過網路呼叫存取，所以您可以進行用戶端優化，以在使用 Azure Cosmos DB JAVA SDK v4 時達到尖峰效能。

如果您詢問「如何改善我的資料庫效能？ 」，請考慮下列選項：

## <a name="networking"></a>網路功能

* **連接模式：使用直接模式**
<a id="direct-connection"></a>
    
    用戶端連接到 Azure Cosmos DB 對於效能有重要影響，特別是在用戶端延遲方面。 *ConnectionMode*是可用來設定用戶端*ConnectionPolicy*的金鑰設定。 針對 Azure Cosmos DB JAVA SDK v4，這兩個可用的*ConnectionMode*是：  
      
    * [閘道 (預設)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [直接](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    這些*ConnectionMode*基本上會將要求從用戶端電腦到 Azure Cosmos DB 後端的分割區的路由條件。 一般直接模式是最佳效能的慣用選項，它可讓您的用戶端直接開啟 Azure Cosmos DB 後端中分割區的 TCP 連線，並傳送要求*直接*去年年初，而不會有任何媒介。 相反地，在閘道模式中，用戶端所提出的要求會路由傳送至 Azure Cosmos DB 前端的所謂「閘道」伺服器，進而將您的要求呈現給 Azure Cosmos DB 後端中適當的磁碟分割。 如果您的應用程式在具有嚴格防火牆限制的公司網路中執行，則閘道模式是最佳選擇，因為它會使用標準 HTTPS 埠和單一端點。 不過，效能的缺點是每次讀取或寫入資料到 Azure Cosmos DB 時，閘道模式都會牽涉到額外的網路躍點（用戶端到閘道加閘道來分割）。 因此，由於網路躍點較少，直接模式可提供較佳的效能。

    *ConnectionMode*是在使用*ConnectionPolicy*參數來 Azure Cosmos DB 用戶端實例的結構中進行設定：
    
   #### <a name="async"></a>[Async](#tab/api-async)

   ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>JAVA SDK V4 （Maven com）。 azure：： azure-cosmos）非同步 API

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[同步](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-connection-policy-sync"></a>JAVA SDK V4 （Maven com. azure：： azure-cosmos）同步 API

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildClient();
    ```

    --- 

<a name="collocate-clients"></a>
* **在相同的 Azure 區域中共置用戶端的效能**<a id="same-region"></a>

    可能的話，請將任何呼叫 Azure Cosmos DB 的應用程式放在與 Azure Cosmos 資料庫相同的區域中。 以約略的比較來說，在相同區域內對 Azure Cosmos DB 進行的呼叫會在 1-2 毫秒內完成，但美國西岸和美國東岸之間的延遲則會大於 50 毫秒。 視要求所採用的路由而定，各項要求從用戶端傳遞至 Azure 資料中心界限時的這類延遲可能有所不同。 確保呼叫端應用程式與佈建的 Azure Cosmos DB 端點位於相同的 Azure 區域中，將可能達到最低的延遲。 如需可用區域的清單，請參閱 [Azure 區域](https://azure.microsoft.com/regions/#services)。

    ![Azure Cosmos DB 連接原則的圖例](./media/performance-tips/same-region.png)

    與多區域 Azure Cosmos DB 帳戶互動的應用程式必須設定[慣用位置]()，以確保要求會進入共置區域。

* **在您的 Azure VM 上啟用加速網路，以降低延遲。**

建議您依照指示，在您的視窗中啟用加速網路[（按一下以取得指示）](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)或[Linux （按一下以取得指示）](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) Azure VM，以便將效能最大化。

如果沒有加速網路，在 Azure VM 和其他 Azure 資源之間傳輸的 IO，可能會不必要地透過位於 VM 和其網路卡之間的主機和虛擬交換器來路由傳送。 將主機和虛擬交換器內嵌在資料路徑中，不僅會增加通道的延遲和抖動，還會竊取 VM 的 CPU 週期。 使用加速網路時，VM 會直接使用不含媒介的 NIC;主機和虛擬交換器正在處理的任何網路原則詳細資料，現在都會在 NIC 的硬體中進行處理;已略過主機和虛擬交換器。 一般來說，您可以預期較低的延遲和更高的輸送量，以及當您啟用加速網路時，會有更*一致*的延遲和降低的 CPU 使用率。

限制： VM OS 必須支援加速網路，而且只有在 VM 停止並解除配置時，才能啟用。 無法使用 Azure Resource Manager 來部署 VM。

如需詳細資訊，請參閱[Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)和[Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)指示。

## <a name="sdk-usage"></a>SDK 使用方式
* **安裝最新的 SDK**

    Azure Cosmos DB SDK 會持續改善以提供最佳效能。 請參閱 [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) 頁面來判斷最新的 SDK 並檢閱改善項目。

* **在應用程式存留期內使用單一 Azure Cosmos DB 用戶端**

    每個 Azure Cosmos DB 的用戶端實例都是安全線程，並可執行有效率的連線管理和位址快取。 為了讓 Azure Cosmos DB 用戶端能夠有效率地進行連線管理和提升效能，建議在應用程式的存留期間，針對每個 AppDomain 使用單一 Azure Cosmos DB 用戶端實例。

   <a id="max-connection"></a>

* **使用應用程式所需的最低一致性層級**

    當您建立*CosmosClient*時，如果未明確設定，則會使用預設的一致性 is *Session*。 如果您的應用程式邏輯不需要*會話*一致性，請將*一致性*設定為*最終*。 注意：建議您在採用 Azure Cosmos DB 變更摘要處理器的應用程式中，至少使用*會話*一致性。

* **使用非同步 API 來達到已布建的輸送量上限**

    Azure Cosmos DB JAVA SDK v4 會組合兩個 Api，也就是同步和非同步。 大致來說，非同步 API 會執行 SDK 功能，而同步處理 API 則是讓呼叫非同步 API 的精簡包裝函式。 這與 Azure Cosmos DB 舊版的非同步 JAVA SDK v2 （僅限非同步）和較舊的 Azure Cosmos DB Sync JAVA SDK v2 （僅限同步處理，並具有完全不同的執行方式）相反。 
    
    API 的選擇是在用戶端初始化期間決定;*CosmosAsyncClient*支援非同步 api，而*COSMOSCLIENT*支援同步 api。 
    
    非同步 API 會執行非封鎖 IO，而且如果您的目標是在發出要求給 Azure Cosmos DB 時要達到最大的輸送量，這是最佳選擇。 
    
    如果您想要或需要 API 來封鎖對每個要求的回應，或如果同步作業是應用程式中的主要架構，則使用同步 API 可以是正確的選擇。 例如，您可能會想要在微服務應用程式中將資料保存到 Azure Cosmos DB 時使用同步處理 API，而提供的輸送量並不重要。  
    
    請注意，同步 API 輸送量會隨著要求的回應時間增加而降低，而非同步 API 會使硬體的完整頻寬功能飽和。 
    
    當您使用同步 API 時，地理共置可以提供更高且更一致的輸送量（請參閱[在相同的 Azure 區域中共置用戶端的效能](#collocate-clients)），但仍不應超過非同步 API 可達成的輸送量。

    有些使用者可能也不熟悉[Project Reactor](https://projectreactor.io/)，這是用來執行 JAVA SDK V4 Async API Azure Cosmos DB 的被動串流架構。 如果這是一項考慮，建議您閱讀我們的簡介[Reactor 模式指南](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)，然後查看這段回應式程式[設計的簡介](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro)，以讓自己熟悉。 如果您已使用 Azure Cosmos DB 搭配非同步介面，而且您使用的 SDK Azure Cosmos DB 非同步 JAVA SDK v2，則您可能已熟悉[ReactiveX](http://reactivex.io/)/[RxJAVA](https://github.com/ReactiveX/RxJava) ，但不確定專案 Reactor 中有哪些變更。 在此情況下，請參閱我們的[Reactor 與 RxJAVA 指南](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)，以成為熟悉。

    下列程式碼片段示範如何分別針對非同步 API 或同步 API 作業初始化 Azure Cosmos DB 用戶端：

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-async-client"></a>JAVA SDK V4 （Maven com）。 azure：： azure-cosmos）非同步 API

    ```java
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[同步](#tab/api-sync)
 
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-sync-client"></a>JAVA SDK V4 （Maven com. azure：： azure-cosmos）同步 API

    ```java
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildClient();
    ```    

    ---

* **調整 ConnectionPolicy**

    根據預設，使用 Azure Cosmos DB JAVA SDK v4 時，直接模式 Cosmos DB 要求會透過 TCP 來進行。 在內部，SDK 會使用特殊的直接模式架構來動態管理網路資源，並獲得最佳效能。

    在 Azure Cosmos DB JAVA SDK v4 中，直接模式是使用大部分工作負載改善資料庫效能的最佳選擇。 

    * ***直接模式總覽***

        ![直接模式架構的圖例](./media/performance-tips-async-java/rntbdtransportclient.png)

        以 Direct 模式採用的用戶端架構可預測網路使用率，以及多工存取 Azure Cosmos DB 複本。 上圖顯示 Direct 模式如何將用戶端要求路由傳送至 Cosmos DB 後端中的複本。 直接模式架構會針對每個 DB 複本在用戶端上配置最多10個**通道**。 通道是 TCP 連線，前面會加上要求緩衝區，也就是30個要求的深度。 屬於複本的通道是由複本的**服務端點**所需的動態配置。 當使用者在直接模式下發出要求時， **TransportClient**會根據資料分割索引鍵，將要求路由傳送至適當的服務端點。 **要求佇列**會緩衝處理服務端點之前的要求。

    * ***直接模式的 ConnectionPolicy 設定選項***

        這些設定會控制管理直接模式 SDK 行為的 RNTBD 架構行為。
        
        第一個步驟是使用下列建議的設定，如下所示。 這些*ConnectionPolicy*選項是先進的設定，可能會以非預期的方式影響 SDK 效能;我們建議使用者避免修改它們，除非他們覺得很樂意瞭解取捨，而且絕對必要。 如果您遇到此特定主題的問題，請洽詢[Azure Cosmos DB 小組](mailto:CosmosDBPerformanceSupport@service.microsoft.com)。

        如果您使用 Azure Cosmos DB 做為參考資料庫（亦即，資料庫用於許多點讀取作業和少數寫入作業），則將*idleEndpointTimeout*設定為0（也就是沒有超時）可能是可接受的。


        | 組態選項       | 預設值    |
        | :------------------:       | :-----:    |
        | bufferPageSize             | 8192       |
        | connectionTimeout          | "PT1M"     |
        | idleChannelTimeout         | PT0S     |
        | idleEndpointTimeout        | "PT1M10S"  |
        | maxBufferCapacity          | 8388608    |
        | maxChannelsPerEndpoint     | 10         |
        | maxRequestsPerChannel      | 30         |
        | receiveHangDetectionTime   | "PT1M5S"   |
        | requestExpiryInterval      | PT5S     |
        | requestTimeout             | "PT1M"     |
        | requestTimerResolution     | "PT 0.5 S"   |
        | sendHangDetectionTime      | "PT10S"    |
        | shutdownTimeout            | PT15S    |

* **微調分割之集合的平行查詢**

    Azure Cosmos DB JAVA SDK v4 支援平行查詢，可讓您以平行方式查詢分割的集合。 如需詳細資訊，請參閱與使用 JAVA SDK v4 Azure Cosmos DB 相關的程式[代碼範例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)。 平行查詢的設計目的是要改善其連續對應項目的查詢延遲和輸送量。

    * ***微調 setMaxDegreeOfParallelism\:***
    
        平行查詢是以平行方式查詢多個分割區來工作。 不過，對於查詢會以循序方式擷取來自個別分割集合的資料。 因此，使用 setMaxDegreeOfParallelism 設定分割數目會最有機會達到最高效能的查詢，但前提是其他所有系統條件皆維持不變。 如果您不知道分割數目，您可以使用 setMaxDegreeOfParallelism 設定為較高的數字，然後系統會選擇最小值 (分割數目、使用者提供的輸入) 作為平行處理原則的最大刻度。

        請務必注意，若對於查詢是以平均方式將資料分佈於所有分割，平行查詢便會產生最佳效益。 如果分割之集合的分割方式是查詢所傳回的所有或大多數資料集中在少數幾個分割中 (最差的情況是集中在一個分割)，則這些分割會成為查詢效能的瓶頸。

    * ***微調 setMaxBufferedItemCount\:***
    
        平行查詢的設計目的是要在用戶端處理目前的結果批次時預先提取結果。 預先擷取有助於改善查詢的整體延遲。 setMaxBufferedItemCount 可限制預先擷取的結果數目。 將 setMaxBufferedItemCount 設定為預期傳回的結果數目 (或更高的數目)，可讓查詢透過預先擷取獲得最大效益。

        預先擷取會以相同方式運作，不受 MaxDegreeOfParallelism 的影響，而且來自所有分割的資料會有單一緩衝區。

* **相應放大用戶端工作負載**

    如果您是以高輸送量層級進行測試，用戶端應用程式可能會成為瓶頸，因為電腦會使 CPU 或網路使用率達到上限。 如果到了這一刻，您可以將用戶端應用程式向外延展至多部伺服器，以繼續將 Azure Cosmos DB 帳戶再往前推進一步。

    理想的經驗法則不會超過任何指定伺服器上 >50% CPU 使用率，讓延遲變低。

   <a id="tune-page-size"></a>

* **調整查詢/讀取摘要的頁面大小以獲得更好的效能**

    使用讀取摘要功能（例如*readItems*）來執行大量檔讀取時，或發出 SQL 查詢（*queryItems*）時，如果結果集太大，則會以分段方式傳回結果。 根據預設，會以 100 個項目或 1 MB 的區塊傳回結果 (以先達到的限制為準)。

    假設您的應用程式發出查詢以 Azure Cosmos DB，並假設您的應用程式需要一組完整的查詢結果，才能完成其工作。 若要減少抓取所有適用結果所需的網路來回行程次數，您可以藉由調整 [ [x 毫秒-最大專案計數](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)] 要求標頭欄位來增加頁面大小。 

    * 對於單一資料分割查詢，將 [ [x-毫秒-最大專案計數](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)] 域值調整為-1 （頁面大小沒有限制）會將查詢回應頁面的數目降到最低，以最大化延遲：完整結果集將會在單一頁面中傳回，或者如果查詢花費的時間超過逾時間隔，則會以最少的頁數來傳回完整的結果集。 這可節省要求來回時間的倍數。
    
    * 針對跨分割區查詢，將 [ [x-毫秒-最大專案計數](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)] 欄位設定為-1，並移除頁面大小限制風險會使 SDK 具有無法管理的頁面大小。 在跨資料分割的情況下，建議您將頁面大小限制提高至一些大型但有限的值（或許是1000），以減少延遲。 
    
    在某些應用程式中，您可能不需要完整的查詢結果集。 在您只需要顯示幾個結果的情況下（例如，如果您的使用者介面或應用程式 API 一次只傳回10個結果），您也可以將頁面大小減少為10，以降低讀取和查詢所耗用的輸送量。

    您也可以設定*byPage*方法的慣用 [頁面大小] 引數，而不是直接修改 REST 標頭欄位。 請記住， *byPage*的 [ [x-ms-最大專案計數](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)] 或 [慣用頁面大小] 引數只會設定頁面大小的上限，而不是絕對需求;因此，基於各種原因，您可能會看到 Azure Cosmos DB 傳回的頁面小於您慣用的頁面大小。 

* **使用適當排程器 (避免竊取事件迴圈 IO Netty 執行緒)**

    Azure Cosmos DB JAVA SDK 的非同步功能是以[netty](https://netty.io/)的非封鎖 IO 為基礎。 SDK 會使用固定數目的 IO netty 事件迴圈執行緒 (和您電腦所擁有的 CPU 核心數一樣多) 來執行 IO 作業。 API 所傳回的 Flux 會在其中一個共用的 IO 事件迴圈 netty 執行緒上發出結果。 因此，請切勿封鎖共用的 IO 事件迴圈 netty 執行緒。 若執行 CPU 密集工作或封鎖 IO 事件迴圈 netty 執行緒上的作業，可能會導致鎖死或大幅降低 SDK 輸送量。

    例如，下列程式碼會在事件迴圈 IO netty 執行緒上執行 CPU 密集工作：
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>JAVA SDK V4 （Maven com）。 azure：： azure-cosmos）非同步 API

    ```java
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub.subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    在收到結果之後，如果您需要對結果進行 CPU 密集工作，請避免在事件迴圈 IO netty 執行緒上進行。 您可以改為提供自己的排程器，以提供您自己的執行緒來執行您的工作，如下所示。

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>JAVA SDK V4 （Maven com）。 azure：： azure-cosmos）非同步 API

    ```java
    import reactor.core.scheduler.Schedulers;
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub
        .subscribeOn(Schedulers.elastic())
        .subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    根據您的工作類型，您應該使用適當的現有 Reactor 排程器來處理您的工作。 請在[``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html)這裡閱讀。

    如需有關 Azure Cosmos DB JAVA SDK v4 的詳細資訊，請參閱[GitHub 上的 AZURE SDK For JAVA monorepo Cosmos DB 目錄](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)。

* **優化應用程式中的記錄設定**

    基於各種原因，您可能會想要或需要在產生高要求輸送量的執行緒中新增記錄。 如果您的目標是要將容器的布建輸送量與此執行緒產生的要求完全飽和，記錄優化可以大幅提升效能。

    * ***設定非同步記錄器***

        同步記錄器的延遲一定會影響到要求產生執行緒的整體延遲計算。 建議使用非同步記錄器（例如[log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0) ），將記錄額外負荷與高效能應用程式執行緒分離。

    * ***停用 netty 的記錄***

        Netty 程式庫記錄是多對話的，必須關閉（隱藏設定可能不夠的情況），以避免額外的 CPU 成本。 如果您不是在偵錯模式中，請停用 netty 全部的記錄。 因此，如果您要使用 log4j 來移除 netty 中的 ``org.apache.log4j.Category.callAppenders()`` 所產生的額外 CPU 成本，請將下列行新增至程式碼基底：

        ```java
        org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
        ```

 * **OS 開啟檔案資源限制**
 
    某些 Linux 系統（例如 Red Hat）的開啟檔案數目上限，以及連線總數。 執行下列命令來檢視目前的限制：

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

    若要改善點寫入的效能，請在點寫入 API 呼叫中指定專案分割區索引鍵，如下所示：

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-good-async"></a>JAVA SDK V4 （Maven com）。 azure：： azure-cosmos）非同步 API

    ```java
    asyncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions()).block();
    ```

    #### <a name="sync"></a>[同步](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-good-sync"></a>JAVA SDK V4 （Maven com. azure：： azure-cosmos）同步 API

    ```java
    syncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions());
    ```

    ---

    而不只是提供專案實例，如下所示：

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-bad-async"></a>JAVA SDK V4 （Maven com）。 azure：： azure-cosmos）非同步 API

    ```java
    asyncContainer.createItem(item).block();
    ```

    #### <a name="sync"></a>[同步](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-bad-sync"></a>JAVA SDK V4 （Maven com. azure：： azure-cosmos）同步 API

    ```java
    syncContainer.createItem(item);
    ```

    ---

    支援後者，但會將延遲新增至您的應用程式;SDK 必須剖析專案，並將分割區索引鍵解壓縮。

## <a name="indexing-policy"></a>編製索引原則
 
* **從索引中排除未使用的路徑以加快寫入速度**

    Azure Cosmos DB 的索引編製原則可讓您利用檢索路徑 (setIncludedPaths 和 setExcludedPaths)，指定要在索引編製中包含或排除的文件路徑。 在事先知道查詢模式的案例中，使用檢索路徑可改善寫入效能並降低索引儲存空間，因為檢索成本與檢索的唯一路徑數目直接相互關聯。 例如，下列程式碼會示範如何使用 "*" 萬用字元，將檔的整個區段（也稱為子樹）從索引編制中排除。

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>JAVA SDK V4 （Maven com）。 azure：： azure-cosmos）
    ```java
    Index numberIndex = Index.Range(DataType.Number);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);        
    containerProperties.setIndexingPolicy(indexingPolicy);
    ``` 

    如需詳細資訊，請參閱 [Azure Cosmos DB 索引編製原則](indexing-policies.md)。

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

* **測量和調整較低的要求單位/秒使用量**

    Azure Cosmos DB 提供許多的資料庫作業，包括使用 UDF、預存程序和觸發程序進行關聯式和階層式查詢，而這些作業全都是對資料庫集合內的文件來進行。 與上述各項作業相關聯的成本，會因為完成作業所需的 CPU、IO 和記憶體而不同。 您不需要考慮和管理硬體資源，您可以將要求單位 (RU) 想成是執行各種資料庫作業以及服務應用程式要求時所需的資源數量。

    輸送量是根據為每個容器所設定的[要求單位](request-units.md)數量來佈建。 要求單位消耗量是以每秒的速率來計算。 如果應用程式的速率超過為其容器佈建的要求單位速率，便會受到限制，直到該速率降到容器的佈建層級以下。 如果您的應用程式需要較高的輸送量，您可以藉由佈建其他的要求單位來增加輸送量。

    查詢的複雜性會影響針對作業所耗用的要求單位數量。 述詞數目、述詞性質、UDF 數目，以及來源資料集的大小，全都會影響查詢作業的成本。

    若要測量任何作業 (建立、更新或刪除) 的額外負荷，請檢查 [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 標頭，來測量這些作業所耗用的要求單位數量。 您也可以查看 ResourceResponse\<T> 或 FeedResponse\<T> 中的對等 RequestCharge 屬性。

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-request-charge-async"></a>JAVA SDK V4 （Maven com）。 azure：： azure-cosmos）非同步 API

    ```java
    CosmosAsyncItemResponse<CustomPOJO> response = asyncContainer.createItem(item).block();

    response.getRequestCharge();
    ```     

    #### <a name="sync"></a>[同步](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-request-charge-sync"></a>JAVA SDK V4 （Maven com. azure：： azure-cosmos）同步 API    

    ```java
    CosmosItemResponse<CustomPOJO> response = syncContainer.createItem(item);

    response.getRequestCharge();
    ```     

    ---

    在此標頭中傳回的要求費用是佈建輸送量的一小部分。 例如，如果您佈建了 2000 RU/秒，且前述查詢傳回 1000 份 1 KB 文件，則作業成本會是 1000。 因此在一秒內，伺服器在對後續要求進行速率限制前，只會接受兩個這類要求。 如需詳細資訊，請參閱[要求單位](request-units.md)和[要求單位計算機](https://www.documentdb.com/capacityplanner)。

<a id="429"></a>
* **處理速率限制/要求速率太大**

    當用戶端嘗試超過帳戶保留的輸送量時，伺服器的效能不會降低，而且不會使用超過保留層級的輸送量容量。 伺服器會事先以 RequestRateTooLarge （HTTP 狀態碼429）結束要求，並傳回[x-ms-重試後](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)端標頭，指出使用者在重新嘗試要求之前必須等待的時間量（以毫秒為單位）。

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK 全都隱含地攔截這個回應，採用伺服器指定的 retry-after 標頭，並重試此要求。 除非有多個用戶端同時存取您的帳戶，否則下次重試將會成功。

    如果您有多個用戶端累積以高於要求速率的方式運作，則用戶端目前在內部設定為9的預設重試計數可能不足夠;在此情況下，用戶端會擲回狀態碼為429的*CosmosClientException*至應用程式。 在 ConnectionPolicy 執行個體上使用 setRetryOptions，即可變更預設重試計數。 根據預設，如果要求繼續以高於要求速率的方式運作，則在30秒的累計等候時間之後，就會傳回具有狀態碼429的*CosmosClientException* 。 即使目前的重試計數小於最大重試計數 (預設值 9 或使用者定義的值)，也會發生這種情況。

    雖然自動重試行為有助於改善大部分應用程式的恢復功能和可用性，但是在進行效能基準測試時可能會有所歧異 (尤其是在測量延遲時)。 如果實驗達到伺服器節流並導致用戶端 SDK 以無訊息模式重試，則用戶端觀察到的延遲將會突然增加。 若要避免效能實驗期間的延遲尖峰，測量每個作業所傳回的費用，並確保要求是以低於保留要求速率的方式運作。 如需詳細資訊，請參閱 [要求單位](request-units.md)。

* **設計輸送量較高之少量文件**

    指定之作業的要求費用 (要求處理成本) 與文件大小直接相互關聯。 大型文件的作業成本高於小型文件的作業成本。 在理想的情況下，架構您的應用程式和工作流程，讓您的專案大小等於1KB 或類似的順序或大小。 對於延遲敏感應用程式，應該避免大型專案-多 MB 檔會使您的應用程式變慢。

## <a name="next-steps"></a>後續步驟

若要深入了解如何針對規模和高效能設計您的應用程式，請參閱 [Azure Cosmos DB 的資料分割與調整規模](partition-data.md)。
