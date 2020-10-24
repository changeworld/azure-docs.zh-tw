---
title: Azure Cosmos DB 同步 Java SDK v2 的效能提示
description: 了解用以改善 Azure Cosmos DB 資料庫針對同步 Java SDK v2 之效能的用戶端設定選項
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 633cfe64e5978b1802a7c4b6c1f7842872ab665a
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92475204"
---
# <a name="performance-tips-for-azure-cosmos-db-sync-java-sdk-v2"></a>Azure Cosmos DB 同步 Java SDK v2 的效能提示

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [非同步 Java SDK v2](performance-tips-async-java.md)
> * [同步 Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> 這「不是」適用於 Azure Cosmos DB 的最新 Java SDK！ 您應該將專案升級到 [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md)，然後閱讀 Azure Cosmos DB Java SDK v4 [效能提示指南](performance-tips-java-sdk-v4-sql.md)。 遵循[移轉到 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南及 [Reactor 與 RxJava 之間的比較](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) \(英文\) 指南來升級。 
> 
> 這些效能提示僅適用於 Azure Cosmos DB 同步 Java SDK v2。 請參閱 Azure Cosmos DB 同步 Java SDK v2 [版本資訊](sql-api-sdk-java.md)及 [Maven 存放庫](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) \(英文\) 以取得詳細資訊。
>

Azure Cosmos DB 是一個既快速又彈性的分散式資料庫，可在獲得延遲與輸送量保證的情況下順暢地調整。 使用 Azure Cosmos DB 時，您不須進行主要的架構變更，或是撰寫複雜的程式碼來調整您的資料庫。 相應增加和減少就像進行單一 API 呼叫一樣簡單。 若要深入了解，請參閱[如何佈建容器輸送量](how-to-provision-container-throughput.md)或[如何佈建資料庫輸送量](how-to-provision-database-throughput.md)。 不過，由於 Azure Cosmos DB 是透過網路呼叫存取，所以您可以在使用 [Azure Cosmos DB 同步 Java SDK v2](./sql-api-sdk-java.md) 時，進行用戶端最佳化以達到最高效能。

如果您詢問「如何改善我的資料庫效能？ 」，請考慮下列選項：

## <a name="networking"></a>網路功能
<a id="direct-connection"></a>

1. **連線模式：使用 DirectHttps**

    用戶端連線到 Azure Cosmos DB 的方式，對於效能有重大影響 (尤其對觀察到的用戶端延遲而言)。 設定用戶端的 [ConnectionPolicy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) 時有一個主要的組態設定，那就是 [ConnectionMode](/java/api/com.microsoft.azure.documentdb.connectionmode)。  有兩個可用的 ConnectionMode：

   1. [閘道 (預設)](/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](/java/api/com.microsoft.azure.documentdb.connectionmode)

      預設會設定所有 SDK 平台都支援的閘道模式。  如果您的應用程式在有嚴格防火牆限制的公司網路中執行，則閘道會是最佳的選擇，因為它會使用標準 HTTPS 連接埠與單一端點。 不過，對於效能的影響是每次讀取或寫入 Azure Cosmos DB 資料時，閘道模式都會涉及額外的網路躍點。 因此，DirectHttps 模式因為網路躍點較少，所以可提供較佳的效能。 

      Azure Cosmos DB 同步 Java SDK v2 是使用 HTTPS 作為傳輸通訊協定。 HTTPS 是使用 TLS 來進行初始驗證和流量加密。 使用 Azure Cosmos DB 同步 Java SDK v2 時，只需要開啟 HTTPS 連接埠 443。 

      ConnectionMode 設定於使用 ConnectionPolicy 參數建構 DocumentClient 執行個體期間。 

    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-connectionpolicy"></a>同步 Java SDK V2 (Maven com.microsoft.azure::azure-documentdb)

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      :::image type="content" source="./media/performance-tips-java/connection-policy.png" alt-text="圖顯示 Azure Cosmos D B 連接原則。" border="false":::

   <a id="same-region"></a>
2. **為了效能在相同 Azure 區域中共置用戶端**

    可能的話，請將呼叫 Azure Cosmos DB 的所有應用程式放在與 Azure Cosmos 資料庫相同的區域中。 以約略的比較來說，在相同區域內對 Azure Cosmos DB 進行的呼叫會在 1-2 毫秒內完成，但美國西岸和美國東岸之間的延遲則會大於 50 毫秒。 視要求所採用的路由而定，各項要求從用戶端傳遞至 Azure 資料中心界限時的這類延遲可能有所不同。 確保呼叫端應用程式與佈建的 Azure Cosmos DB 端點位於相同的 Azure 區域中，將可能達到最低的延遲。 如需可用區域的清單，請參閱 [Azure 區域](https://azure.microsoft.com/regions/#services)。

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="圖顯示 Azure Cosmos D B 連接原則。" border="false":::
   
## <a name="sdk-usage"></a>SDK 的使用方式
1. **安裝最新的 SDK**

    Azure Cosmos DB SDK 會持續改善以提供最佳效能。 請參閱 [Azure Cosmos DB SDK](./sql-api-sdk-java.md) 頁面來判斷最新的 SDK 並檢閱改善項目。
2. **在應用程式存留期內使用單一 Azure Cosmos DB 用戶端**

    每個 [DocumentClient](/java/api/com.microsoft.azure.documentdb.documentclient) 執行個體都具備執行緒安全，並且在直接模式中運作時執行有效率的連線管理和位址快取。 若要藉由 DocumentClient 獲得有效率的連接管理和更佳的效能，建議在應用程式存留期內對每個 AppDomain 使用單一 DocumentClient 執行個體。

   <a id="max-connection"></a>
3. **使用閘道模式時增加每部主機的 MaxPoolSize**

    使用閘道模式時，Azure Cosmos DB 要求是透過 HTTPS/REST 發出，並受制於每個主機名稱或 IP 位址的預設連線限制。 您可能必須將 MaxPoolSize 設定成較高的值 (200-1000)，以便讓用戶端程式庫能夠利用多個同時對 Azure Cosmos DB 進行的連線。 在 Azure Cosmos DB 同步 Java SDK v2 中，[ConnectionPolicy.getMaxPoolSize](/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) \(英文\) 的預設值為 100。 使用 [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) 可變更此值。

4. **微調分割之集合的平行查詢**

    Azure Cosmos DB 同步 Java SDK 1.9.0 版和更新版本支援平行查詢，可讓您平行查詢分割的集合。 如需詳細資訊，請參閱使用 SDK 的相關[程式碼範例](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples)。 平行查詢的設計目的是要改善其連續對應項目的查詢延遲和輸送量。

     () **_微調 setMaxDegreeOfParallelism \: _*_ 平行查詢的運作方式是以平行方式查詢多個資料分割。 不過，對於查詢會以循序方式擷取來自個別分割集合的資料。 因此，使用 [setMaxDegreeOfParallelism](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) 設定分割數目會最有機會達到最高效能的查詢，但前提是其他所有系統條件皆維持不變。 如果您不知道分割數目，您可以使用 setMaxDegreeOfParallelism 設定為較高的數字，然後系統會選擇最小值 (分割數目、使用者提供的輸入) 作為平行處理原則的最大刻度。 

    請務必注意，若對於查詢是以平均方式將資料分佈於所有分割，平行查詢便會產生最佳效益。 如果分割之集合的分割方式是查詢所傳回的所有或大多數資料集中在少數幾個分割中 (最差的情況是集中在一個分割)，則這些分割會成為查詢效能的瓶頸。

     (b) _*_微調 setMaxBufferedItemCount \: _*_ 平行查詢的設計目的是要在用戶端處理目前的結果批次時預先提取結果。 預先擷取有助於改善查詢的整體延遲。 setMaxBufferedItemCount 可限制預先擷取的結果數目。 藉由將 [setMaxBufferedItemCount](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) 設定為預期傳回的結果數目 (或更高的數目)，即可讓查詢透過預先擷取獲得最大效益。

    預先擷取會以相同方式運作，不受 MaxDegreeOfParallelism 的影響，而且來自所有分割的資料會有單一緩衝區。  

5. _ 依 *>getretryafterinmilliseconds 間隔執行*輪詢*

    在進行效能測試期間，您應該增加負載，直到系統對小部分要求進行節流處理為止。 如果進行節流處理，用戶端應用程式應該在節流時降速，且持續時間達伺服器指定的重試間隔。 採用降速可確保您在重試之間花費最少的等待時間。 [Azure Cosmos DB 同步 Java SDK](./sql-api-sdk-java.md) 1.8.0 版和更新版本包含重試原則支援。 如需詳細資訊，請參閱 [getRetryAfterInMilliseconds](/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds) \(英文\)。

6. **擴增用戶端工作負載**

    如果您是以高輸送量層級 (> 50,000 RU/秒) 進行測試，用戶端應用程式可能會成為瓶頸，因為電腦對 CPU 或網路的使用率將達到上限。 如果到了這一刻，您可以將用戶端應用程式向外延展至多部伺服器，以繼續將 Azure Cosmos DB 帳戶再往前推進一步。

7. **使用名稱定址**

    使用連結格式為 `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` 的名稱定址來取代格式為 `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` 的 SelfLinks (\_self)，以避免擷取用來建構連結之所有資源的 ResourceId。 此外，由於會重新建立這些資源 (可能使用相同名稱)，因此快取這些資源並沒有幫助。

   <a id="tune-page-size"></a>
8. **調整查詢/讀取摘要的頁面大小以獲得更好的效能**

    使用讀取摘要功能 (例如 [readDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)) 執行大量文件讀取時，或發出 SQL 查詢時，如果結果集太大，則會以分段方式傳回結果。 根據預設，會以 100 個項目或 1 MB 的區塊傳回結果 (以先達到的限制為準)。

    若要減少擷取所有適用結果所需的網路來回行程次數，您可以使用 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 要求標頭將頁面大小最高增加至 1000。 在您只需要顯示幾個結果的情況下 (例如，您的使用者介面或應用程式 API 一次只傳回 10 筆結果)，您也可以將頁面大小縮小為 10，以降低讀取和查詢所耗用的輸送量。

    您也可以使用 [setPageSize method](/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize) 設定頁面大小。

## <a name="indexing-policy"></a>索引原則
 
1. **從索引編製中排除未使用的路徑以加快寫入速度**

    Azure Cosmos DB 的索引編製原則可讓您利用檢索路徑 ([setIncludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) 和 [setExcludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths))，指定要在索引編製中包含或排除的文件路徑。 在事先知道查詢模式的案例中，使用檢索路徑可改善寫入效能並降低索引儲存空間，因為檢索成本與檢索的唯一路徑數目直接相互關聯。  例如，下列程式碼示範如何使用 "*" 萬用字元，將文件的整個區段 (樹狀子目錄) 自索引編製作業中排除。


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-indexing"></a>同步 Java SDK V2 (Maven com.microsoft.azure::azure-documentdb)

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

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

1. **測量和調整較低的要求單位/秒使用量**

    Azure Cosmos DB 提供許多的資料庫作業，包括使用 UDF、預存程序和觸發程序進行關聯式和階層式查詢，而這些作業全都是對資料庫集合內的文件來進行。 與上述各項作業相關聯的成本，會因為完成作業所需的 CPU、IO 和記憶體而不同。 您不需要考慮和管理硬體資源，您可以將要求單位 (RU) 想成是執行各種資料庫作業以及服務應用程式要求時所需的資源數量。

    輸送量是根據為每個容器所設定的[要求單位](request-units.md)數量來佈建。 要求單位消耗量是以每秒的速率來計算。 如果應用程式的速率超過為其容器佈建的要求單位速率，便會受到限制，直到該速率降到容器的佈建層級以下。 如果您的應用程式需要較高的輸送量，您可以藉由佈建其他的要求單位來增加輸送量。 

    查詢的複雜性會影響針對作業所耗用的要求單位數量。 述詞數目、述詞性質、UDF 數目，以及來源資料集的大小，全都會影響查詢作業的成本。

    若要測量任何作業 (建立、更新或刪除) 的額外負荷，請檢查 [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 標頭 (或是 [ResourceResponse\<T>](/java/api/com.microsoft.azure.documentdb.resourceresponse) 或 [FeedResponse\<T>](/java/api/com.microsoft.azure.documentdb.feedresponse) 中同等的 RequestCharge 屬性) 來測量這些作業所耗用的要求單位數量。


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-requestcharge"></a>同步 Java SDK V2 (Maven com.microsoft.azure::azure-documentdb)

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    在此標頭中傳回的要求費用是佈建輸送量的一小部分。 例如，如果您佈建了 2000 RU/秒，且前述查詢傳回 1000 份 1 KB 文件，則作業成本會是 1000。 因此在一秒內，伺服器在對後續要求進行速率限制前，只會接受兩個這類要求。 如需詳細資訊，請參閱[要求單位](request-units.md)和[要求單位計算機](https://www.documentdb.com/capacityplanner)。
   <a id="429"></a>
1. **處理速率限制/要求速率太大**

    當用戶端嘗試超過帳戶保留的輸送量時，伺服器的效能不會降低，而且不會使用超過保留層級的輸送量容量。 伺服器將預先使用 RequestRateTooLarge (HTTP 狀態碼 429) 來結束要求，並傳回 [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 標頭，以指出使用者重試要求之前必須等候的時間量 (毫秒)。
    
    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```
    SDK 全都隱含地攔截這個回應，採用伺服器指定的 retry-after 標頭，並重試此要求。 除非有多個用戶端同時存取您的帳戶，否則下次重試將會成功。

    如果您有多個用戶端都以高於要求速率的方式累積運作，則用戶端在內部設定為 9 的預設重試計數可能會不敷使用；在此情況下，用戶端會擲回 [DocumentClientException](/java/api/com.microsoft.azure.documentdb.documentclientexception) (狀態碼 429) 到應用程式。 在 [ConnectionPolicy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) 執行個體上使用 [setRetryOptions](/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions)，即可變更預設重試計數。 根據預設，如果要求繼續以高於要求速率的方式運作，則會在 30 秒的累計等候時間後傳回 DocumentClientException (狀態碼 429)。 即使目前的重試計數小於最大重試計數 (預設值 9 或使用者定義的值)，也會發生這種情況。

    雖然自動重試行為有助於改善大部分應用程式的恢復功能和可用性，但是在進行效能基準測試時可能會有所歧異 (尤其是在測量延遲時)。  如果實驗達到伺服器節流並導致用戶端 SDK 以無訊息模式重試，則用戶端觀察到的延遲將會突然增加。 若要避免效能實驗期間的延遲尖峰，測量每個作業所傳回的費用，並確保要求是以低於保留要求速率的方式運作。 如需詳細資訊，請參閱 [要求單位](request-units.md)。
3. **輸送量較高之少量文件的設計**

    指定之作業的要求費用 (要求處理成本) 與文件大小直接相互關聯。 大型文件的作業成本高於小型文件的作業成本。

## <a name="next-steps"></a>後續步驟
若要深入了解如何針對規模和高效能設計您的應用程式，請參閱 [Azure Cosmos DB 的資料分割與調整規模](partitioning-overview.md)。