---
title: Azure Cosmos DB 非同步 Java SDK v2 的效能祕訣
description: 了解可用以改善非同步 Java SDK v2 之 Azure Cosmos 資料庫效能的用戶端設定選項
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperfq2
ms.openlocfilehash: c1dec2c8451ddd1feb4b5b0dac9c82e1716079b7
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325829"
---
# <a name="performance-tips-for-azure-cosmos-db-async-java-sdk-v2"></a>Azure Cosmos DB 非同步 Java SDK v2 的效能祕訣
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [非同步 Java SDK v2](performance-tips-async-java.md)
> * [同步 Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)


> [!IMPORTANT]  
> 這「不是」適用於 Azure Cosmos DB 的最新 Java SDK！ 您應該將專案升級到 [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md)，然後閱讀 Azure Cosmos DB Java SDK v4 [效能提示指南](performance-tips-java-sdk-v4-sql.md)。 遵循[移轉到 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南與 [Reactor 與 RxJava 之間的比較](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) \(英文\) 指南來升級。 
> 
> 此文章提供的效能祕訣僅適用於 Azure Cosmos DB 非同步 Java SDK v2。 如需詳細資訊，請參閱 Azure Cosmos DB 非同步 Java SDK v2 [版本資訊](sql-api-sdk-async-java.md)、[Maven 存放庫](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)與 Azure Cosmos DB 非同步 Java SDK v2 [疑難排解指南](troubleshoot-java-async-sdk.md)。
>

Azure Cosmos DB 是一個既快速又彈性的分散式資料庫，可在獲得延遲與輸送量保證的情況下順暢地調整。 使用 Azure Cosmos DB 時，您不須進行主要的架構變更，或是撰寫複雜的程式碼來調整您的資料庫。 相應增加和減少就像進行單一 API 呼叫或 SDK 方法呼叫一樣簡單。 不過，由於 Azure Cosmos DB 是透過網路呼叫存取，所以您可以在使用 [Azure Cosmos DB 非同步 Java SDK v2](sql-api-sdk-async-java.md) 時，進行用戶端最佳化以達到最高效能。

如果您詢問「如何改善我的資料庫效能？ 」，請考慮下列選項：

## <a name="networking"></a>網路功能

* **連線模式：使用直接模式**
    
  用戶端連線到 Azure Cosmos DB 的方式，對於效能有重大影響 (尤其對用戶端延遲而言)。 *ConnectionMode* 是設定用戶端 *ConnectionPolicy* 時的主要組態設定。 針對 Azure Cosmos DB 非同步 Java SDK v2，兩種可用的 ConnectionMode 如下：  
      
  * [閘道 (預設)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
  * [直接](/java/api/com.microsoft.azure.cosmosdb.connectionmode)
  
  所有 SDK 平台都支援閘道模式，而且是預設會設定的選項。 如果您的應用程式在有嚴格防火牆限制的公司網路中執行，則閘道模式是最好的選擇，因為它會使用標準 HTTPS 埠與單一端點。   不過，效能的取捨是每次讀取或寫入資料至 Azure Cosmos DB 時，閘道模式都會涉及額外的網路躍點。 因此，直接模式因為網路躍點較少，所以可提供較佳的效能。
  
  *ConnectionMode* 是在使用 *ConnectionPolicy* 參數建構 *DocumentClient* 執行個體期間設定的。

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-connectionpolicy"></a>非同步 Java SDK v2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.Direct);
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
```

* **為了效能在相同 Azure 區域中共置用戶端**

  可能的話，請將呼叫 Azure Cosmos DB 的所有應用程式放在與 Azure Cosmos 資料庫相同的區域中。 以約略的比較來說，在相同區域內對 Azure Cosmos DB 進行的呼叫會在 1-2 毫秒內完成，但美國西岸和美國東岸之間的延遲則會大於 50 毫秒。 視要求所採用的路由而定，各項要求從用戶端傳遞至 Azure 資料中心界限時的這類延遲可能有所不同。 確保呼叫端應用程式與佈建的 Azure Cosmos DB 端點位於相同的 Azure 區域中，將可能達到最低的延遲。 如需可用區域的清單，請參閱 [Azure 區域](https://azure.microsoft.com/regions/#services)。

  :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB 連接原則的圖例" border="false":::

## <a name="sdk-usage"></a>SDK 的使用方式

* **安裝最新的 SDK**

  Azure Cosmos DB SDK 會持續改善以提供最佳效能。 請參閱 Azure Cosmos DB 非同步 Java SDK v2 [版本資訊](sql-api-sdk-async-java.md)頁面，來判斷最新的 SDK 並檢閱改善項目。

* **在應用程式存留期內使用單一 Azure Cosmos DB 用戶端**

  每個 AsyncDocumentClient 執行個體都是安全執行緒，並且會執行有效率的連線管理和位址快取。 若要藉由 AsyncDocumentClient 獲得有效率的連線管理和更佳的效能，建議在應用程式存留期內，對每個 AppDomain 使用單一 AsyncDocumentClient 執行個體。

* **調整 ConnectionPolicy**

  根據預設，使用 Azure Cosmos DB 非同步 Java SDK v2 時，會透過 TCP 提出直接模式 Cosmos DB 要求。 SDK 會在內部使用特殊的直接模式架構來動態管理網路資源，並達到最佳效能。

  在 Azure Cosmos DB 非同步 Java SDK v2 中，直接模式最適合用來改善工作負載最大的資料庫效能。 

  * ***直接模式 _ 總覽**

  :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="直接模式架構的圖例" border="false":::
  
  直接模式中採用的用戶端架構可讓您以可預測的方式進行網路使用，並對 Azure Cosmos DB 複本進行多工存取。 上圖顯示直接模式如何將用戶端要求路由傳送到 Cosmos DB 後端複本。 直接模式架構會在用戶端為每個 DB 複本配置最多 10 _ *通道* *。 通道是一個 TCP 連線，其前面會有要求緩衝區，也就是30個要求的深度。 屬於複本的通道會視複本的 **服務端點** 的需要進行動態配置。 當使用者在直接模式中發出要求時， **TransportClient** 會根據資料分割索引鍵，將要求路由傳送至適當的服務端點。 **要求佇列** 會在服務端點之前對要求進行緩衝處理。

  * ***ConnectionPolicy Direct mode 的設定選項** _

    首先，請使用下列建議的組態設定。 如果遇到關於此特定主題的問題，請連絡 [Azure Cosmos DB 小組](mailto:CosmosDBPerformanceSupport@service.microsoft.com)。

    如果您使用 Azure Cosmos DB 做為參考資料庫 (亦即，資料庫用於許多點讀取作業，而不) 寫入作業，則可能可接受將 _idleEndpointTimeout * 設定為 0 (也就是不會有超時) 。


    | 組態選項       | 預設    |
    | :------------------:       | :-----:    |
    | bufferPageSize             | 8192       |
    | connectionTimeout          | "PT1M"     |
    | idleChannelTimeout         | "PT0S"     |
    | idleEndpointTimeout        | "PT1M10S"  |
    | maxBufferCapacity          | 8388608    |
    | maxChannelsPerEndpoint     | 10         |
    | maxRequestsPerChannel      | 30         |
    | receiveHangDetectionTime   | "PT1M5S"   |
    | requestExpiryInterval      | "PT5S"     |
    | requestTimeout             | "PT1M"     |
    | requestTimerResolution     | "PT0.5S"   |
    | sendHangDetectionTime      | "PT10S"    |
    | shutdownTimeout            | "PT15S"    |

* ***直接模式的程式設計秘訣** _

  請參閱 Azure Cosmos DB Async JAVA SDK v2 [疑難排解](troubleshoot-java-async-sdk.md) 文章，以取得解決任何 SDK 問題的基準。
  
  使用直接模式時的一些重要程式設計祕訣：
  
  _ **在您的應用程式中使用多執行緒處理以進行有效率的 TCP 資料傳輸** -提出要求之後，您的應用程式應該訂閱以接收另一個執行緒上的資料。 若未這麼做，則不會強制執行非預期的「半雙工」作業，而且後續的要求會被封鎖等候先前的要求回復。
  
  * **在專用的執行緒上執行需要大量計算的工作負載** -對於先前的提示而言，像是複雜資料處理等作業最好放在個別的執行緒中。 從另一個資料存放區提取資料的要求 (例如，如果執行緒同時使用 Azure Cosmos DB 和 Spark 資料存放區) 可能會遇到延遲增加，建議您產生額外的執行緒來等候其他資料存放區的回應。
  
    * Azure Cosmos DB Async JAVA SDK v2 中的基礎網路 IO 是由 Netty 管理，請參閱這些 [秘訣，以避免封鎖 NETTY IO 執行緒的程式碼模式](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread)。
  
  * **資料模型化** - Azure Cosmos DB SLA 假設文件大小小於 1KB。 將您的資料模型和程式設計優化，以利較小的檔案大小，通常會導致延遲減少。 如果您需要儲存和抓取大於1KB 的檔，建議的方法是將檔連結至 Azure Blob 儲存體中的資料。

* **微調分割之集合的平行查詢**

  Azure Cosmos DB 非同步 Java SDK v2 支援平行查詢，這可讓您平行查詢分割的集合。 如需詳細資訊，請參閱使用 SDK 的相關[程式碼範例](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples)。 平行查詢的設計目的是要改善其連續對應項目的查詢延遲和輸送量。

  * ***微調 setMaxDegreeOfParallelism \:** _
    
    平行查詢的運作方式是以平行方式查詢多個分割。 不過，對於查詢會以循序方式擷取來自個別分割集合的資料。 因此，使用 setMaxDegreeOfParallelism 設定分割數目會最有機會達到最高效能的查詢，但前提是其他所有系統條件皆維持不變。 如果您不知道分割數目，您可以使用 setMaxDegreeOfParallelism 設定為較高的數字，然後系統會選擇最小值 (分割數目、使用者提供的輸入) 作為平行處理原則的最大刻度。

    請務必注意，若對於查詢是以平均方式將資料分佈於所有分割，平行查詢便會產生最佳效益。 如果分割之集合的分割方式是查詢所傳回的所有或大多數資料集中在少數幾個分割中 (最差的情況是集中在一個分割)，則這些分割會成為查詢效能的瓶頸。

  _ * **微調 setMaxBufferedItemCount \:** _
    
    平行查詢的設計是可在用戶端處理目前的結果批次時，先預先擷取結果。 預先擷取有助於改善查詢的整體延遲。 setMaxBufferedItemCount 可限制預先擷取的結果數目。 將 setMaxBufferedItemCount 設定為預期傳回的結果數目 (或更高的數目)，可讓查詢透過預先擷取獲得最大效益。

    預先擷取會以相同方式運作，不受 MaxDegreeOfParallelism 的影響，而且來自所有分割的資料會有單一緩衝區。

_ 依 **>getretryafterinmilliseconds 間隔執行** 輪詢

  在進行效能測試期間，您應該增加負載，直到系統對小部分要求進行節流處理為止。 如果進行節流處理，用戶端應用程式應該降速，且持續時間達伺服器指定的重試間隔。 採用降速可確保您在重試之間花費最少的等待時間。

* **擴增用戶端工作負載**

  如果您是以高輸送量層級 (> 50,000 RU/秒) 進行測試，用戶端應用程式可能會成為瓶頸，因為電腦對 CPU 或網路的使用率將達到上限。 如果到了這一刻，您可以將用戶端應用程式向外延展至多部伺服器，以繼續將 Azure Cosmos DB 帳戶再往前推進一步。

* **使用名稱定址**

  使用連結格式為 `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` 的名稱定址來取代格式為 `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` 的 SelfLinks (\_self)，以避免擷取用來建構連結之所有資源的 ResourceId。 此外，由於會重新建立這些資源 (可能使用相同名稱)，因此快取這些資源並沒有幫助。

* **調整查詢/讀取摘要的頁面大小以獲得更好的效能**

  使用讀取摘要功能 (例如 readDocuments) 執行大量文件讀取時，或發出 SQL 查詢時，如果結果集太大，則會以分段方式傳回結果。 根據預設，會以 100 個項目或 1 MB 的區塊傳回結果 (以先達到的限制為準)。

  若要減少擷取所有適用結果所需的網路來回行程次數，您可以使用 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 要求標頭將頁面大小最高增加至 1000。 在您只需要顯示幾個結果的情況下 (例如，您的使用者介面或應用程式 API 一次只傳回 10 筆結果)，您也可以將頁面大小縮小為 10，以降低讀取和查詢所耗用的輸送量。

  您也可以使用 setMaxItemCount 方法來設定頁面大小。

* **使用適當排程器 (避免竊取事件迴圈 IO Netty 執行緒)**

  Azure Cosmos DB 非同步 Java SDK v2 會將 [netty](https://netty.io/) 用於非封鎖 IO。 SDK 會使用固定數目的 IO netty 事件迴圈執行緒 (和您電腦所擁有的 CPU 核心數一樣多) 來執行 IO 作業。 API 所傳回的 Observable 會在其中一個共用的 IO 事件迴圈 netty 執行緒上發出結果。 因此，請切勿封鎖共用的 IO 事件迴圈 netty 執行緒。 若執行 CPU 密集工作或封鎖 IO 事件迴圈 netty 執行緒上的作業，可能會導致鎖死或大幅降低 SDK 輸送量。

  例如，下列程式碼會在事件迴圈 IO netty 執行緒上執行 CPU 密集工作：

  **Async JAVA SDK V2 (Maven .com. azure：： azure-cosmosdb)**

  ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
  ```

  在收到結果之後，如果您需要對結果進行 CPU 密集工作，請避免在事件迴圈 IO netty 執行緒上進行。 您可以改為提供您自己的排程器，以提供自己的執行緒來執行工作。

  **Async JAVA SDK V2 (Maven .com. azure：： azure-cosmosdb)**

  ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
  ```

  根據您的工作類型，您應該將適當的現有 RxJava 排程器用於您的工作。 閱讀這裡 [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html)。

  如需詳細資訊，請查看適用於 Azure Cosmos DB 非同步 Java SDK v2 的 [GitHub 頁面](https://github.com/Azure/azure-cosmosdb-java) \(英文\)。

* **停用 netty 的記錄**

    Netty 程式庫記錄通訊頻繁，而且必須加以關閉 (隱藏組態中的登入可能不夠) 以避免額外的 CPU 成本。 如果您不是在偵錯模式中，請停用 netty 全部的記錄。 因此，如果您要使用 log4j 來移除 netty 中的 ``org.apache.log4j.Category.callAppenders()`` 所產生的額外 CPU 成本，請將下列行新增至程式碼基底：

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **OS 開啟檔案資源限制**
 
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

## <a name="indexing-policy"></a>索引原則
 
* **從索引編製中排除未使用的路徑以加快寫入速度**

    Azure Cosmos DB 的索引編製原則可讓您利用檢索路徑 (setIncludedPaths 和 setExcludedPaths)，指定要在索引編製中包含或排除的文件路徑。 在事先知道查詢模式的案例中，使用檢索路徑可改善寫入效能並降低索引儲存空間，因為檢索成本與檢索的唯一路徑數目直接相互關聯。 例如，下列程式碼示範如何使用 "*" 萬用字元，將文件的整個區段 (亦稱為樹狀子目錄) 從索引編製作業中排除。

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-indexing"></a>非同步 Java SDK v2 (Maven com.microsoft.azure::azure-cosmosdb)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    如需詳細資訊，請參閱 [Azure Cosmos DB 索引編製原則](/azure/cosmos-db/index-policy)。

## <a name="throughput"></a><a id="measure-rus"></a>Throughput

* **測量和調整較低的要求單位/秒使用量**

    Azure Cosmos DB 提供許多的資料庫作業，包括使用 UDF、預存程序和觸發程序進行關聯式和階層式查詢，而這些作業全都是對資料庫集合內的文件來進行。 與上述各項作業相關聯的成本，會因為完成作業所需的 CPU、IO 和記憶體而不同。 您不需要考慮和管理硬體資源，您可以將要求單位 (RU) 想成是執行各種資料庫作業以及服務應用程式要求時所需的資源數量。

    輸送量是根據為每個容器所設定的[要求單位](request-units.md)數量來佈建。 要求單位消耗量是以每秒的速率來計算。 如果應用程式的速率超過為其容器佈建的要求單位速率，便會受到限制，直到該速率降到容器的佈建層級以下。 如果您的應用程式需要較高的輸送量，您可以藉由佈建其他的要求單位來增加輸送量。

    查詢的複雜性會影響針對作業所耗用的要求單位數量。 述詞數目、述詞性質、UDF 數目，以及來源資料集的大小，全都會影響查詢作業的成本。

    若要測量任何作業 (建立、更新或刪除) 的額外負荷，請檢查 [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 標頭，來測量這些作業所耗用的要求單位數量。 您也可以查看 ResourceResponse\<T> 或 FeedResponse\<T> 中的對等 RequestCharge 屬性。

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-requestcharge"></a>非同步 Java SDK v2 (Maven com.microsoft.azure::azure-cosmosdb)

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    在此標頭中傳回的要求費用是佈建輸送量的一小部分。 例如，如果您佈建了 2000 RU/秒，且前述查詢傳回 1000 份 1 KB 文件，則作業成本會是 1000。 因此在一秒內，伺服器在對後續要求進行速率限制前，只會接受兩個這類要求。 如需詳細資訊，請參閱[要求單位](request-units.md)和[要求單位計算機](https://www.documentdb.com/capacityplanner)。

* **處理速率限制/要求速率太大**

    當用戶端嘗試超過帳戶保留的輸送量時，伺服器的效能不會降低，而且不會使用超過保留層級的輸送量容量。 伺服器將預先使用 RequestRateTooLarge (HTTP 狀態碼 429) 來結束要求，並傳回 [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 標頭，以指出使用者重試要求之前必須等候的時間量 (毫秒)。

   ```xml
   HTTP Status 429,
   Status Line: RequestRateTooLarge
   x-ms-retry-after-ms :100
   ```

    SDK 全都隱含地攔截這個回應，採用伺服器指定的 retry-after 標頭，並重試此要求。 除非有多個用戶端同時存取您的帳戶，否則下次重試將會成功。

    如果您有多個用戶端都以高於要求速率的方式累積運作，則用戶端在內部設定為 9 的預設重試計數可能會不敷使用；在此情況下，用戶端會擲回 DocumentClientException (狀態碼 429) 到應用程式。 在 ConnectionPolicy 執行個體上使用 setRetryOptions，即可變更預設重試計數。 根據預設，如果要求繼續以高於要求速率的方式運作，則會在 30 秒的累計等候時間後傳回 DocumentClientException (狀態碼 429)。 即使目前的重試計數小於最大重試計數 (預設值 9 或使用者定義的值)，也會發生這種情況。

    雖然自動重試行為有助於改善大部分應用程式的恢復功能和可用性，但是在進行效能基準測試時可能會有所歧異 (尤其是在測量延遲時)。 如果實驗達到伺服器節流並導致用戶端 SDK 以無訊息模式重試，則用戶端觀察到的延遲將會突然增加。 若要避免效能實驗期間的延遲尖峰，測量每個作業所傳回的費用，並確保要求是以低於保留要求速率的方式運作。 如需詳細資訊，請參閱 [要求單位](request-units.md)。

* **輸送量較高之少量文件的設計**

    指定之作業的要求費用 (要求處理成本) 與文件大小直接相互關聯。 大型文件的作業成本高於小型文件的作業成本。

## <a name="next-steps"></a>後續步驟

若要深入了解如何針對規模和高效能設計您的應用程式，請參閱 [Azure Cosmos DB 的資料分割與調整規模](partitioning-overview.md)。