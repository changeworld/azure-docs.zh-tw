---
title: 適用于 .NET SDK v2 的 Azure Cosmos DB 效能秘訣
description: 瞭解用戶端設定選項，以改善 Azure Cosmos DB .NET v2 SDK 效能。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: sngun
ms.custom: devx-track-dotnet, contperfq2
ms.openlocfilehash: a1c986663c42b87e7e5d4530b26200d48fe612cb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314271"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Azure Cosmos DB 和 .NET SDK v2 的效能秘訣
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [非同步 Java SDK v2](performance-tips-async-java.md)
> * [同步 Java SDK v2](performance-tips-java.md)

Azure Cosmos DB 是一個既快速又彈性的分散式資料庫，可在獲得延遲與輸送量保證的情況下順暢地調整。 您不需要進行主要的架構變更，也不需要撰寫複雜的程式碼以 Azure Cosmos DB 來調整您的資料庫。 相應增加和減少就像進行單一 API 呼叫一樣簡單。 若要深入了解，請參閱[如何佈建容器輸送量](how-to-provision-container-throughput.md)或[如何佈建資料庫輸送量](how-to-provision-database-throughput.md)。 但因為 Azure Cosmos DB 是透過網路呼叫存取，所以您可以進行用戶端優化以達到使用 [SQL .NET SDK](sql-api-sdk-dotnet-standard.md)時的尖峰效能。

因此，如果您想要改善資料庫效能，請考慮下列選項：

## <a name="upgrade-to-the-net-v3-sdk"></a>升級至 .NET V3 SDK

[.Net V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3)已發行。 如果您使用 .NET v3 SDK，請參閱 [.net v3 效能指南](performance-tips-dotnet-sdk-v3-sql.md) 以取得下列資訊：

- 預設為直接 TCP 模式
- 串流 API 支援
- 支援自訂序列化程式以允許 System.Text.JS使用方式
- 整合式批次和大量支援

## <a name="hosting-recommendations"></a>裝載建議

**針對需要大量查詢的工作負載，請使用 Windows 64 位，而不是 Linux 或 Windows 32 位主機處理**

我們建議 Windows 64 位主機處理，以改善效能。 SQL SDK 包含原生 ServiceInterop.dll，可在本機剖析和最佳化查詢。 ServiceInterop.dll 只能在 Windows x64 平台上受到支援。 若為 Linux 和其他不支援 ServiceInterop.dll 的平臺，則會對閘道進行額外的網路呼叫，以取得優化的查詢。 下列類型的應用程式預設會使用32位主機處理。 若要將主機處理變更為64位處理，請根據應用程式的類型，遵循下列步驟：

- 針對可執行檔應用程式，您可以在 [ **專案屬性** ] 視窗的 [ **組建** ] 索引標籤中，將 [ [平臺目標](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019)] 設定為 [ **x64** ] 來變更主機處理

- 針對以 VSTest 為基礎的測試專案，您可以在 **Test**  >  [Visual Studio 測試] 功能表上選取 [測試 **測試設定**  >  **預設處理器架構為 X64** **Test** ] 來變更主處理。

- 針對本機部署的 ASP.NET web 應用程式，您可以在 [ **工具** 選項專案和方案 Web 專案] 下，選取 **[使用適用于網站和專案的64位版本 IIS Express** ] 來變更主機處理  >  **Options**  >  **Projects and Solutions**  >  **** 。

- 針對部署在 Azure 上的 ASP.NET web 應用程式，您可以在 Azure 入口網站的 [ **應用程式設定** ] 中選取 **64** 位平臺，以變更主機處理。

> [!NOTE] 
> 根據預設，新 Visual Studio 專案會設定為 [ **任何 CPU** ]。 建議您將專案設定為 **x64** ，使其不會切換至 **x86** 。 如果加入僅限 x86 的相依性，則將專案設定為 **任何 CPU** 可以輕鬆地切換至 **x86** 。<br/>
> ServiceInterop.dll 必須在執行 SDK DLL 的資料夾中。 只有當您手動複製 Dll 或擁有自訂群組建/部署系統時，才需要考慮這一點。
    
**開啟伺服器端垃圾收集 (GC)**

在某些情況下，降低垃圾收集的頻率可能會有所説明。 在 .NET 中，將 [>gcserver>](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element) 設定為 `true` 。

**擴充您的用戶端工作負載**

如果您是以高輸送量層級進行測試 (超過 50000 RU/秒) ，用戶端應用程式可能會因為電腦在 CPU 或網路使用量上的上限而變成瓶頸。 如果到了這一刻，您可以將用戶端應用程式向外延展至多部伺服器，以繼續將 Azure Cosmos DB 帳戶再往前推進一步。

> [!NOTE] 
> 高 CPU 使用率可能會導致延遲增加和要求超時例外狀況。

## <a name="networking"></a><a id="networking"></a> 網路

**原則︰使用直接連接模式**

.NET V2 SDK 預設連線模式是 gateway。 您可以使用參數，在實例的結構期間設定連接模式 `DocumentClient` `ConnectionPolicy` 。 如果您使用直接模式，您也必須 `Protocol` 使用參數來設定 `ConnectionPolicy` 。 若要深入瞭解不同的連線選項，請參閱連線 [模式](sql-sdk-connection-modes.md) 文章。

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

**暫時連接埠耗盡**

如果您在實例上看到的連接量或高埠使用量很高，請先確認您的用戶端實例已 singleton。 換句話說，用戶端實例在應用程式的存留期內應該是唯一的。

在 TCP 通訊協定上執行時，用戶端會使用長時間執行的連線（而不是 HTTPS 通訊協定）來優化延遲，這會在閒置2分鐘後終止連接。

在您有稀疏存取的情況下，如果您在與閘道模式存取相較之下發現連接計數較高，您可以：

* 將 [ConnectionPolicy. PortReuseMode](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) 屬性設定為 `PrivatePortPool` (有效的 framework 版本>= 4.6.1 和 .net core 版本 >= 2.0) ：此屬性可讓 SDK 針對不同的 Azure Cosmos DB 目的地端點使用較小的暫時埠集區。
* 設定 [ConnectionPolicy. IdleConnectionTimeout](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) 屬性必須大於或等於10分鐘。 建議的值介於20分鐘到24小時之間。

**呼叫 OpenAsync 以避免第一次要求的啟動延遲**

根據預設，第一個要求會有較高的延遲，因為它需要提取位址路由表。 當您使用 [SDK V2](sql-api-sdk-dotnet.md)時，請在 `OpenAsync()` 初始化期間呼叫一次，以避免第一個要求的啟動延遲。 呼叫看起來像這樣： `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` 將會產生要求，以取得帳戶中所有容器的位址路由表。 對於具有許多容器的帳戶，但其應用程式會存取一部分的容器， `OpenAsync` 會產生不必要的流量量，這會導致初始化變慢。 因此， `OpenAsync` 在此案例中使用可能不會很有用，因為它會減緩應用程式啟動的速度。

**針對效能，請在相同的 Azure 區域中共置用戶端**

可能的話，請將呼叫 Azure Cosmos DB 的任何應用程式放在與 Azure Cosmos DB 資料庫相同的區域中。 以下是大約的比較：在相同區域內 Azure Cosmos DB 的呼叫會在1毫秒到2毫秒內完成，但是美國西部與東部海岸之間的延遲超過50毫秒。 這項延遲可能會因要求的要求而有所不同，這取決於要求從用戶端傳遞至 Azure 資料中心界限時所採取的路由。 您可以確保呼叫的應用程式位於與布建的 Azure Cosmos DB 端點相同的 Azure 區域內，以取得可能的最低延遲。 如需可用區域的清單，請參閱 [Azure 區域](https://azure.microsoft.com/regions/#services)。

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB 連接原則" border="false":::

**增加執行緒/工作數目**
<a id="increase-threads"></a>

由於呼叫 Azure Cosmos DB 是透過網路進行，因此您可能需要改變要求的平行處理原則程度，以便讓用戶端應用程式花費最短時間在要求之間等待。 例如，如果您使用的是 .NET 工作 [平行程式庫](/dotnet/standard/parallel-programming/task-parallel-library-tpl)，請建立從 Azure Cosmos DB 讀取或寫入的數百個工作的順序。

**啟用加速網路**
 
若要減少延遲和 CPU 抖動，建議您在用戶端虛擬機器上啟用加速網路。 請參閱使用 [加速網路建立 Windows 虛擬機器](../virtual-network/create-vm-accelerated-networking-powershell.md) ，或 [建立具有加速網路功能的 Linux 虛擬](../virtual-network/create-vm-accelerated-networking-cli.md)機。

## <a name="sdk-usage"></a>SDK 使用方式

**安裝最新的 SDK**

Azure Cosmos DB SDK 會持續改善以提供最佳效能。 請參閱 [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) 頁面來判斷最新的 SDK 並檢閱改善項目。

**在應用程式存留期內使用單一 Azure Cosmos DB 用戶端**

每個 `DocumentClient` 實例都是安全線程，並且在直接模式中運作時執行有效率的連接管理和位址快取。 若要允許有效率的連線管理和更好的 SDK 用戶端效能，建議您在 `AppDomain` 應用程式的存留期內使用單一實例。

**使用閘道模式時，每一主機增加 System.Net MaxConnections**

當您使用閘道模式時，會透過 HTTPS/REST 發出 Azure Cosmos DB 要求。 它們受限於每個主機名稱或 IP 位址的預設連線限制。 您可能需要將設定 `MaxConnections` 為較高的值 (100 至 1000) 因此，用戶端程式庫可以使用多個同時連線來 Azure Cosmos DB。 在 .NET SDK 1.8.0 和更新版本中， [ServicePointManager >servicepointmanager.defaultconnectionlimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) 的預設值是50。 若要變更此值，您可以將 [ConnectionPolicy. MaxConnectionLimit](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) 設定為較高的值。

**調整資料分割集合的平行查詢**

SQL .NET SDK 1.9.0 和更新版本支援平行查詢，可讓您以平行方式查詢分割的集合。 如需詳細資訊，請參閱使用 SDK 的相關[程式碼範例](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs)。 平行查詢的設計目的是要提供比其序列對應更好的查詢延遲和輸送量。 平行查詢提供兩個參數，可供您調整以符合您的需求： 
- `MaxDegreeOfParallelism` 控制可以平行查詢的資料分割數目上限。 
- `MaxBufferedItemCount` 控制預先提取的結果數目。

**_調整平行處理原則的程度_* _

平行查詢的運作方式是以平行方式查詢多個資料分割。 但是個別分割區中的資料會根據查詢順序提取。 `MaxDegreeOfParallelism`若將[SDK V2](sql-api-sdk-dotnet.md)中的資料分割設定為數據分割數目，就有機會達到最高效能的查詢，但前提是其他所有系統條件維持不變。 如果您不知道資料分割數目，您可以將平行處理原則的程度設定為較高的數位。 系統會選擇最小 (的資料分割數目、使用者提供的輸入) 作為平行處理原則的程度。

如果資料平均分佈在與查詢相關的所有資料分割中，平行查詢會產生最大效益。 如果分割的集合已分割，以便查詢所傳回的所有或大部分資料都集中在少數幾個資料分割中 (一個資料分割是最糟的情況) ，則這些資料分割將會造成查詢的效能瓶頸。

_*_微調 MaxBufferedItemCount_*_
    
平行查詢的設計是可在用戶端處理目前的結果批次時，先預先擷取結果。 這項預先提取有助於改善查詢的整體延遲。 `MaxBufferedItemCount`參數會限制預先提取的結果數目。 設定 `MaxBufferedItemCount` 為預期傳回的結果數目 (或更高的數目) ，讓查詢能獲得預先提取的最大效益。

不論平行處理原則的程度為何，預先提取的運作方式都相同，而且所有分割區的資料都有一個緩衝區。  

_ 依 *RetryAfter 間隔執行* 輪詢*

在效能測試期間，您應該增加負載，直到低比率的要求受到節流。 如果要求受到節流處理，用戶端應用程式應該會在伺服器指定的重試間隔中，針對伺服器指定的重試間隔關閉。 遵循輪詢可確保您在重試之間花費最少的等候時間。 

這些 Sdk 包含重試原則支援：
- [適用于 sql 的 .NET sdk](sql-api-sdk-dotnet.md)版本1.8.0 和更新版本，以及[適用于 SQL 的 JAVA sdk](sql-api-sdk-java.md)
- [適用于 sql 的Node.js SDK](sql-api-sdk-node.md)版本1.9.0 和更新版本，以及[適用于 SQL 的 Python sdk](sql-api-sdk-python.md)
- 所有支援的 [.Net Core](sql-api-sdk-dotnet-core.md) sdk 版本 

如需詳細資訊，請參閱 [RetryAfter](/dotnet/api/microsoft.azure.documents.documentclientexception.retryafter)。
    
在 .NET SDK 1.19 版和更新版本中，有一種機制可記錄其他診斷資訊和針對延遲問題進行疑難排解，如下列範例所示。 您可以針對具有較高讀取延遲的要求記錄診斷字串。 所捕獲的診斷字串將協助您瞭解在指定的要求中收到429錯誤的次數。

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**快取較低讀取延遲的文件 URI**

盡可能快取文件 URI 以達到最佳讀取效能。 當您建立資源時，您需要定義邏輯來快取資源識別碼。 以資源識別碼為基礎的查閱速度比以名稱為基礎的查閱更快，因此快取這些值可改善效能。

**調整查詢/讀取摘要的頁面大小以獲得更好的效能**

當您使用讀取摘要功能來大量讀取檔時 (例如， `ReadDocumentFeedAsync`) 或當您發出 SQL 查詢時，如果結果集太大，則會以分段方式傳回結果。 根據預設，會以 100 個項目或 1 MB 的區塊傳回結果 (以先達到的限制為準)。

若要減少取出所有適用結果所需的網路來回行程次數，您可以使用 [x-毫秒-最大專案計數](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 來要求最多1000的標頭，以增加頁面大小。 當您只需要顯示幾個結果時（例如，如果您的使用者介面或應用程式 API 一次只傳回10個結果），您也可以將頁面大小縮小為10，以降低讀取和查詢所耗用的輸送量。

> [!NOTE] 
> `maxItemCount`屬性不應只用于分頁。 其主要用途是減少在單一頁面中傳回的最大專案數，藉以改善查詢的效能。  

您也可以使用可用的 Azure Cosmos DB Sdk 來設定頁面大小。 中的 [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet&preserve-view=true) 屬性可 `FeedOptions` 讓您設定列舉作業中要傳回的專案數目上限。 當 `maxItemCount` 設定為-1 時，SDK 會自動尋找最佳值，視檔案大小而定。 例如：
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
執行查詢時，會在 TCP 封包內傳送產生的資料。 如果您指定的值太低，則在 `maxItemCount` TCP 封包中傳送資料所需的行程次數很高，而這會影響效能。 因此，如果您不確定要為屬性設定的值 `maxItemCount` ，最好將它設定為-1，並讓 SDK 選擇預設值。

**增加執行緒/工作數目**

請參閱本文的「網路功能」一節中的「 [增加執行緒/工作數目](#increase-threads) 」。

## <a name="indexing-policy"></a>編製索引原則
 
**從索引編製中排除未使用的路徑以加快寫入速度**

Azure Cosmos DB 的編制索引原則也可讓您使用索引路徑 (IndexingPolicy IncludedPaths 和 IndexingPolicy. Indexingpolicy.excludedpaths) ，指定要包含在索引中或從中排除的檔路徑。 在事先已知查詢模式的情況下，編制索引路徑可改善寫入效能並降低索引儲存體。 這是因為索引編制成本會直接與索引的唯一路徑數目產生關聯。 例如，這段程式碼會示範如何使用 "*" 萬用字元，將檔的整個區段 (子樹) 從索引中排除：

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

如需詳細資訊，請參閱 [Azure Cosmos DB 索引編製原則](index-policy.md)。

## <a name="throughput"></a><a id="measure-rus"></a> 輸送量

**測量和調整較低的要求單位/秒使用量**

Azure Cosmos DB 提供一組豐富的資料庫作業。 這些作業包括使用 Udf、預存程式和觸發程式進行關聯式和階層式查詢，而這些查詢都是在資料庫集合內的檔上運作。 與每個作業相關聯的成本會根據完成作業所需的 CPU、IO 和記憶體而有所不同。 與其考慮和管理硬體資源，您可以將要求單位 (RU) 作為執行各種資料庫作業和服務應用程式要求所需資源的單一量值。

輸送量是根據為每個容器設定的 [要求單位](request-units.md) 數目來布建。 要求單位耗用量是以每秒的速率來評估。 超過布建之容器要求單位速率的應用程式會受到限制，直到該速率低於容器的布建層級。 如果您的應用程式需要較高層級的輸送量，您可以布建額外的要求單位來增加輸送量。

查詢的複雜性會影響針對作業所耗用的要求單位數量。 述詞數目、述詞性質、Udf 數目，以及源資料集的大小，全都會影響查詢作業的成本。

若要測量任何作業的額外負荷 (建立、更新或刪除) ，請檢查 [x-ms-要求費用](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 標頭 (或 `RequestCharge` .net SDK) 中或中的對等屬性， `ResourceResponse\<T>` `FeedResponse\<T>` 以測量作業所耗用的要求單位數目：

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

此標頭中傳回的要求費用是布建輸送量的一部分， (也就是 2000 ru/秒) 。 例如，如果上述查詢傳回 1000 1 KB 檔，則作業的成本是1000。 因此，在一秒內，伺服器只接受兩個這類要求，再對稍後的要求進行速率限制。 如需詳細資訊，請參閱 [要求單位](request-units.md) 和 [要求單位計算機](https://www.documentdb.com/capacityplanner)。
<a id="429"></a>

**處理速率限制/要求速率太大**

當用戶端嘗試超過帳戶的保留輸送量時，伺服器的效能不會降低，而且不會使用超過保留層級的輸送量容量。 伺服器會事先使用 RequestRateTooLarge (HTTP 狀態碼 429) 來結束要求。 它會傳回一次 [x 毫秒-](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) -毫秒的標頭，表示使用者必須等待的時間長度（以毫秒為單位），然後再重試要求。

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

SDK 全都隱含地攔截這個回應，採用伺服器指定的 retry-after 標頭，並重試此要求。 除非有多個用戶端同時存取您的帳戶，否則下次重試將會成功。

如果您有多個用戶端的累積作業高於要求速率，則在用戶端內部設定的預設重試計數目前可能不會足夠。 在此情況下，用戶端會對應用程式擲回狀態碼為429的 Documentclientexception (。 

您可以藉由在實例上設定，來變更預設的重試計數 `RetryOptions` `ConnectionPolicy` 。 根據預設，如果要求繼續以高於要求速率的方式運作，則會在 30 秒的累計等候時間後傳回 DocumentClientException (狀態碼 429)。 這項錯誤會在目前的重試計數小於最大重試次數時傳回，無論目前的值是預設值9或使用者定義值。

自動重試行為可協助改善大部分應用程式的復原能力和可用性。 但是當您執行效能基準測試時，可能不是最佳的行為，尤其是在測量延遲的時候。 如果實驗達到伺服器節流並導致用戶端 SDK 以無訊息模式重試，則用戶端觀察到的延遲將會突然增加。 若要避免效能實驗期間的延遲尖峰，測量每個作業所傳回的費用，並確保要求是以低於保留要求速率的方式運作。 如需詳細資訊，請參閱 [要求單位](request-units.md)。

**針對較高的輸送量，請針對較小的檔進行設計**

要求費用 (也就是指定作業的要求處理成本) 直接與檔案大小相互關聯。 大型檔的作業成本高於小型檔上的作業。

## <a name="next-steps"></a>後續步驟

如需在少數用戶端電腦上用來評估高效能案例 Azure Cosmos DB 的範例應用程式，請參閱 [Azure Cosmos DB 的效能和規模測試](performance-testing.md)。

若要深入了解如何針對規模和高效能設計您的應用程式，請參閱 [Azure Cosmos DB 的資料分割與調整規模](partitioning-overview.md)。