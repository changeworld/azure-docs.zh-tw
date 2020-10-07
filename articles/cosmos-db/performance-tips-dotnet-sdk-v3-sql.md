---
title: 適用于 .NET SDK v3 的 Azure Cosmos DB 效能秘訣
description: 瞭解用戶端設定選項，以協助改善 Azure Cosmos DB .NET v3 SDK 效能。
author: j82w
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/16/2020
ms.author: jawilley
ms.custom: devx-track-dotnet
ms.openlocfilehash: f8e610531eaf3e7e5dbee9c40c88683a05029303
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802985"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Azure Cosmos DB 和 .NET 的效能祕訣

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [非同步 Java SDK v2](performance-tips-async-java.md)
> * [同步 Java SDK v2](performance-tips-java.md)

Azure Cosmos DB 是快速、彈性的分散式資料庫，可透過保證的延遲和輸送量層級順暢地進行調整。 您不需要進行主要的架構變更，也不需要撰寫複雜的程式碼以 Azure Cosmos DB 來調整您的資料庫。 相應增加和減少就像進行單一 API 呼叫一樣簡單。 若要深入瞭解，請參閱布建 [容器輸送量](how-to-provision-container-throughput.md) 或布建 [資料庫輸送量](how-to-provision-database-throughput.md)。 

因為 Azure Cosmos DB 可透過網路呼叫存取，所以您可以在使用 [SQL .NET SDK](sql-api-sdk-dotnet-standard.md)時，進行用戶端優化以達到尖峰效能。

如果您想要改善資料庫效能，請考慮下列各節所提供的選項。

## <a name="hosting-recommendations"></a>裝載建議

**針對需要大量查詢的工作負載，請使用 Windows 64 位，而不是 Linux 或 Windows 32 位主機處理**

我們建議 Windows 64 位主機處理，以改善效能。 SQL SDK 包含原生 ServiceInterop.dll，可在本機剖析和最佳化查詢。 ServiceInterop.dll 只能在 Windows x64 平台上受到支援。 

若為 Linux 和其他不支援 ServiceInterop.dll 的平臺，則會對閘道進行額外的網路呼叫，以取得優化的查詢。 

此處列出的四個應用程式類型預設使用32位主機處理。 若要針對您的應用程式類型將主機處理變更為64位處理，請執行下列動作：

- **針對可執行檔應用程式**：在 [ **專案屬性** ] 視窗的 [ **組建** ] 窗格中，將 [ [平臺目標](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019&preserve-view=true) ] 設定為 [ **x64**]。

- **針對以 VSTest 為基礎的測試專案**：在 [Visual Studio**測試**] 功能表上，選取 [**測試**  >  **測試設定**]，然後將 [**預設處理器架構**] 設定為 [ **X64**]。

- **針對本機部署的 ASP.NET web 應用程式**：選取 [**工具**  >  **選項**  >  **專案和方案**  >  **Web 專案**]，然後選取 **[使用 web sites 和專案的64位版 IIS Express**]。

- **針對部署在 Azure 上的 ASP.NET web 應用程式**：在 Azure 入口網站的 [ **應用程式設定**] 中，選取 **64 位** 平臺。

> [!NOTE] 
> 根據預設，新 Visual Studio 專案會設定為 [ **任何 CPU**]。 建議您將專案設定為 **x64** ，使其不會切換至 **x86**。 如果加入僅限 x86 的相依性，則設定為 **任何 CPU** 的專案可以輕鬆地切換至 **x86** 。<br/>
> ServiceInterop.dll 檔案必須位於執行 SDK DLL 的資料夾中。 只有當您手動複製 Dll 或擁有自訂群組建或部署系統時，才需要考慮這一點。
    
**開啟伺服器端垃圾收集**

在某些情況下，降低垃圾收集的頻率可能會有所説明。 在 .NET 中，將 [>gcserver>](https://docs.microsoft.com/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) 設定為 `true` 。

**擴充您的用戶端工作負載**

如果您是以高輸送量層級進行測試，或是以大於50000每秒要求單位數 (RU/s) 的費率進行測試，用戶端應用程式可能會變成工作負載瓶頸。 這是因為機器可能會在 CPU 或網路使用量上的上限。 如果到了這一刻，您可以將用戶端應用程式向外延展至多部伺服器，以繼續將 Azure Cosmos DB 帳戶再往前推進一步。

> [!NOTE] 
> 高 CPU 使用率可能會導致延遲增加和要求超時例外狀況。

## <a name="networking"></a>網路功能
<a id="direct-connection"></a>

**原則︰使用直接連接模式**

用戶端連線至 Azure Cosmos DB 的方式會影響重要的效能，特別是針對觀察到的用戶端延遲。 有兩個主要設定可用於設定用戶端連線原則：連接 *模式* 和連線 *通訊協定*。 可用的兩種連接模式如下：

   * 直接模式 (預設) 

     直接模式支援透過 TCP 通訊協定連線，而且如果您使用的是 [Cosmos/.Net V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3)，它是預設的連接模式。 Direct 模式提供較佳的效能，而且需要的網路躍點比閘道模式少。

   * 閘道模式
      
     如果您的應用程式在有嚴格防火牆限制的公司網路中執行，則閘道模式是最好的選擇，因為它會使用標準 HTTPS 埠和單一端點。 
     
     不過，效能的取捨在於，每次從 Azure Cosmos DB 讀取或寫入資料時，閘道模式都會涉及額外的網路躍點。 因為網路躍點較少，所以直接模式可提供較佳的效能。 當您在擁有有限數目之通訊端連線的環境中執行應用程式時，也建議使用閘道連線模式。

     當您在 Azure Functions 中使用 SDK 時（特別是在取用 [方案](../azure-functions/functions-scale.md#consumption-plan)中），請注意目前的 [連接限制](../azure-functions/manage-connections.md)。 在這種情況下，如果您也在 Azure Functions 應用程式中使用其他以 HTTP 為基礎的用戶端，則閘道模式可能更好。
     
當您在直接模式中使用 TCP 通訊協定時，除了閘道埠之外，您還必須確定從10000到20000的埠範圍是開啟的，因為 Azure Cosmos DB 使用動態 TCP 埠。 當您在 [私人端點](./how-to-configure-private-endpoints.md)上使用直接模式時，從0到65535的 TCP 埠的完整範圍應該是開啟的。 預設會開啟標準 Azure VM 設定的埠。 如果未開啟這些埠，而您嘗試使用 TCP，您將會收到「503服務無法使用」錯誤。 

下表顯示適用于各種 Api 的連線模式，以及用於每個 API 的服務埠：

|連線模式  |支援的通訊協定  |支援的 SDK  |API/服務連接埠  |
|---------|---------|---------|---------|
|閘道  |   HTTPS    |  所有 Sdk    |   SQL (443) 、MongoDB (10250、10255、10256) 、資料表 (443) 、Cassandra (10350) 、Graph (443)  <br><br> 埠10250對應至不含異地複寫的 MongoDB 實例預設 Azure Cosmos DB API，而埠10255和10256則對應至具有異地複寫的實例。   |
|直接    |     TCP    |  .NET SDK    | 當您使用公用/服務端點時：10000到20000範圍中的埠<br><br>當您使用私人端點時：0到65535範圍中的埠 |

Azure Cosmos DB 透過 HTTPS 提供簡單的開放 RESTful 程式設計模型。 此外，它可提供有效率的 TCP 通訊協定，此 TCP 通訊協定在通訊模型中也符合 REST 限制，並且可以透過 .NET 用戶端 SDK 取得。 TCP 通訊協定會使用傳輸層安全性 (TLS) 來進行初始驗證和加密流量。 為了達到最佳效能，儘可能使用 TCP 通訊協定。

針對 SDK V3，您可以在中建立實例時設定連接模式 `CosmosClient` `CosmosClientOptions` 。 請記住，Direct 模式是預設值。

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

由於 TCP 僅在直接模式中受到支援，因此如果您使用閘道模式，則一律會使用 HTTPS 通訊協定來與閘道通訊。

:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="使用不同的連線模式和通訊協定，建立與 Azure Cosmos DB 的連接。" border="false":::

**暫時連接埠耗盡**

如果您在實例上看到的連接量或高埠使用量很高，請先確認您的用戶端實例已 singleton。 換句話說，用戶端實例在應用程式的存留期內應該是唯一的。

當它在 TCP 通訊協定上執行時，用戶端會使用長時間執行的連線，將延遲優化。 這與 HTTPS 通訊協定相反，它會在兩分鐘無活動狀態後終止連接。

在您有稀疏存取的情況下，如果您在與閘道模式存取相較之下發現連接計數較高，您可以：

* 將 [CosmosClientOptions. PortReuseMode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) 屬性設定為 `PrivatePortPool` (使用 framework 版本4.6.1 和更新版本，以及 .net Core 2.0 版和更新版本的) 。 此屬性可讓 SDK 針對各種 Azure Cosmos DB 目的地端點，使用一小部分的暫時埠。
* 將 [CosmosClientOptions. IdleConnectionTimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) 屬性設定為大於或等於10分鐘。 建議的值是20分鐘到24小時。

<a id="same-region"></a>

**針對效能，請在相同的 Azure 區域中共置用戶端**

可能的話，請將呼叫 Azure Cosmos DB 的任何應用程式放在與 Azure Cosmos DB 資料庫相同的區域中。 以下是大約的比較：在相同區域內 Azure Cosmos DB 的呼叫會在1毫秒內完成（ (ms) 到2毫秒），但是美國西部與東部的距離之間的延遲超過50毫秒。 這項延遲可能會因要求的要求而有所不同，這取決於要求從用戶端傳遞至 Azure 資料中心界限時所採取的路由。 

您可以藉由確定呼叫的應用程式與布建的 Azure Cosmos DB 端點位於相同的 Azure 區域內，以取得可能的最低延遲。 如需可用區域的清單，請參閱 [Azure 區域](https://azure.microsoft.com/regions/#services)。

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="使用不同的連線模式和通訊協定，建立與 Azure Cosmos DB 的連接。" border="false":::

   <a id="increase-threads"></a>

**增加執行緒/工作數目**

由於對 Azure Cosmos DB 的呼叫是透過網路進行，因此您可能需要改變要求的並行程度，讓用戶端應用程式花費最短時間在要求之間等待。 例如，如果您使用的是 .NET 工作 [平行程式庫](https://msdn.microsoft.com//library/dd460717.aspx)，請建立從 Azure Cosmos DB 讀取或寫入的數百個工作的順序。

**啟用加速網路**
 
若要減少延遲和 CPU 抖動，建議您在用戶端虛擬機器上啟用加速網路。 如需詳細資訊，請參閱 [使用加速網路建立 Windows 虛擬機器](../virtual-network/create-vm-accelerated-networking-powershell.md) ，或 [建立具有加速網路功能的 Linux 虛擬](../virtual-network/create-vm-accelerated-networking-cli.md)機。

## <a name="sdk-usage"></a>SDK 使用方式

**安裝最新的 SDK**

Azure Cosmos DB SDK 會持續改善以提供最佳效能。 若要判斷最新的 SDK 和審核功能改進，請參閱 [AZURE COSMOS DB sdk](sql-api-sdk-dotnet-standard.md)。

**使用串流 Api**

[.NET SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) 包含可在不序列化的情況下接收和傳回資料的串流 api。 

不會直接從 SDK 取用回應，但是將它們轉送至其他應用層的仲介層應用程式，可受益于串流 Api。 如需串流處理的範例，請參閱 [專案管理](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) 範例。

**在應用程式存留期內使用單一 Azure Cosmos DB 用戶端**

每個 `CosmosClient` 實例都是安全線程，並且在直接模式中運作時執行有效率的連接管理和位址快取。 若要允許有效率的連線管理和更好的 SDK 用戶端效能，建議您在 `AppDomain` 應用程式的存留期內使用單一實例。

當您正在處理 Azure Functions 時，實例也應該遵循現有的 [指導方針](../azure-functions/manage-connections.md#static-clients) ，並維護單一實例。

<a id="max-connection"></a>

**停用寫入作業的內容回應**

對於具有大量建立承載的工作負載，請將 [ `EnableContentResponseOnWrite` 要求] 選項設定為 `false` 。 服務將不會再將已建立或更新的資源傳回至 SDK。 一般來說，因為應用程式具有正在建立的物件，所以不需要服務將它傳回。 標頭值仍可存取，例如要求費用。 停用內容回應有助於改善效能，因為 SDK 不再需要配置記憶體或序列化回應主體。 它也可減少網路頻寬使用量，以進一步協助效能。  

```csharp
ItemRequestOption requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**啟用大量以針對輸送量進行優化，而不是延遲**

大量 *針對工作* 負載需要大量輸送量的情節進行大量啟用，而且延遲不重要。 如需如何啟用大量功能的詳細資訊，以及瞭解應該使用的案例，請參閱 [大量支援簡介](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk)。

**當您使用閘道模式時，每一主機增加 System.Net MaxConnections**

當您使用閘道模式時，會透過 HTTPS/REST 發出 Azure Cosmos DB 要求。 它們受限於每個主機名稱或 IP 位址的預設連線限制。 您可能需要將設定 `MaxConnections` 為較高的值 (從100到 1000) ，如此用戶端程式庫才能使用多個同時連線來 Azure Cosmos DB。 在 .NET SDK 1.8.0 和更新版本中， [ServicePointManager >servicepointmanager.defaultconnectionlimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 的預設值是50。 若要變更此值，您可以將設定 [`Documents.Client.ConnectionPolicy.MaxConnectionLimit`](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) 為較高的值。

**調整資料分割集合的平行查詢**

SQL .NET SDK 支援平行查詢，可讓您以平行方式查詢分割的容器。 如需詳細資訊，請參閱使用 SDK 的相關[程式碼範例](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs)。 平行查詢的設計目的是要提供比其序列對應更好的查詢延遲和輸送量。 

平行查詢提供兩個參數，可供您調整以符合您的需求： 

- **>maxconcurrency**：控制可平行查詢的資料分割數目上限。

   平行查詢的運作方式是以平行方式查詢多個資料分割。 但是個別分割區中的資料會根據查詢順序提取。 `MaxConcurrency`若將[SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3)設定為分割區數目，就有機會達到最高效能的查詢，但前提是其他所有系統條件維持不變。 如果您不知道資料分割數目，您可以將平行處理原則的程度設定為較高的數位。 系統會選擇最小 (的資料分割數目、使用者提供的輸入) 作為平行處理原則的程度。

    如果資料平均分佈在與查詢相關的所有資料分割中，平行查詢會產生最大效益。 如果分割的集合已分割，以便查詢所傳回的所有或大部分資料都集中在少數幾個資料分割中 (一個資料分割是最糟的情況) ，則這些資料分割將會造成查詢的效能瓶頸。
   
- **MaxBufferedItemCount**：控制預先提取的結果數目。

   平行查詢的設計是可在用戶端處理目前的結果批次時，先預先擷取結果。 這項預先提取有助於改善查詢的整體延遲。 `MaxBufferedItemCount`參數會限制預先提取的結果數目。 設定 `MaxBufferedItemCount` 為預期傳回的結果數目 (或更高的數目) ，讓查詢能獲得預先提取的最大效益。

   不論平行處理原則的程度為何，預先提取的運作方式都相同，而且所有分割區的資料都有一個緩衝區。  

**在 RetryAfter 間隔實作降速**

在效能測試期間，您應該增加負載，直到低比率的要求受到節流。 如果要求受到節流處理，用戶端應用程式應該會在伺服器指定的重試間隔後關閉節流。 遵循輪詢有助於確保您在重試之間花費最少的等待時間。 

如需詳細資訊，請參閱 [RetryAfter](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_CosmosException_RetryAfter)。
    
有一種機制可記錄其他診斷資訊和針對延遲問題進行疑難排解，如下列範例所示。 您可以記錄讀取延遲較高之要求的診斷字串。 所捕獲的診斷字串將協助您瞭解在指定的要求中收到 *429* 錯誤的次數。

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**增加執行緒/工作數目**

請參閱本文的「網路功能」一節中的「 [增加執行緒/工作數目](#increase-threads) 」。

## <a name="indexing-policy"></a>編製索引原則
 
**從索引編製中排除未使用的路徑以加快寫入速度**

Azure Cosmos DB 的編制索引原則也可讓您使用索引路徑 (IndexingPolicy IncludedPaths 和 IndexingPolicy. Indexingpolicy.excludedpaths) ，指定要包含或排除的檔路徑。 

您只需為所需的路徑編制索引，即可改善寫入效能、減少寫入作業的 RU 費用，以及在事先已知查詢模式的情況下，減少索引儲存體。 這是因為索引編制成本會直接與索引的唯一路徑數目產生關聯。 例如，下列程式碼將示範如何使用 "*" 萬用字元，將檔的整個區段 (子樹中的索引) ：

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

如需詳細資訊，請參閱 [Azure Cosmos DB 索引編製原則](index-policy.md)。

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

**測量和調整較低的 RU/秒使用量**

Azure Cosmos DB 提供一組豐富的資料庫作業。 這些作業包括具有國際磁碟格式的關聯式和階層式查詢 (UDF) 檔案、預存程式和觸發程式，全都在資料庫集合內的檔上運作。 

與這些作業相關聯的成本會根據完成作業所需的 CPU、IO 和記憶體而有所不同。 與其考慮和管理硬體資源，您可以將要求單位視為執行各種資料庫作業以及服務應用程式要求所需之資源的單一量值。

輸送量是根據為每個容器設定的 [要求單位](request-units.md) 數目來布建。 要求單位耗用量是以每秒單位的速率來評估。 超過布建之容器要求單位速率的應用程式會受到限制，直到該速率低於容器的布建層級。 如果您的應用程式需要較高層級的輸送量，您可以布建額外的要求單位來增加輸送量。

查詢的複雜性會影響針對作業所耗用的要求單位數量。 述詞數目、述詞性質、UDF 檔案數目，以及源資料集的大小，全都會影響查詢作業的成本。

若要測量任何作業的額外負荷 (建立、更新或刪除) ，請檢查 [x-ms-要求費用](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 標頭 (或 `RequestCharge` .net SDK) 中或中的對等屬性， `ResourceResponse\<T>` `FeedResponse\<T>` 以測量作業所耗用的要求單位數目：

```csharp
// Measure the performance (Request Units) of writes
ItemResponse<Book> response = await container.CreateItemAsync<Book>(myBook, new PartitionKey(myBook.PkValue));
Console.WriteLine("Insert of item consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
FeedIterator<Book> queryable = container.GetItemQueryIterator<ToDoActivity>(queryString);
while (queryable.HasMoreResults)
    {
        FeedResponse<Book> queryResponse = await queryable.ExecuteNextAsync<Book>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

此標頭中傳回的要求費用是布建輸送量的一部分， (也就是 2000 RU/秒) 。 例如，如果上述查詢傳回 1000 1 KB 檔，則作業的成本是1000。 因此，在一秒內，伺服器只會接受兩個這類要求，再對稍後的要求進行速率限制。 如需詳細資訊，請參閱 [要求單位](request-units.md) 和 [要求單位計算機](https://www.documentdb.com/capacityplanner)。
<a id="429"></a>

**處理速率限制/要求速率太大**

當用戶端嘗試超過帳戶的保留輸送量時，伺服器的效能不會降低，而且不會使用超過保留層級的輸送量容量。 伺服器事先會結束具有 RequestRateTooLarge (HTTP 狀態碼 429) 的要求。 它會傳回以毫秒為單位的 [x 毫秒-毫秒](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 標頭，表示使用者必須等待多久才能再次嘗試要求。

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

SDK 全都隱含地攔截這個回應，採用伺服器指定的 retry-after 標頭，並重試此要求。 除非有多個用戶端同時存取您的帳戶，否則下次重試將會成功。

如果您有多個用戶端的累積作業高於要求速率，則目前在用戶端內部設定為9的預設重試計數可能不會足夠。 在此情況下，用戶端會對應用程式擲回狀態碼為429的 CosmosException。 

您可以藉由在實例上設定，來變更預設的重試計數 `RetryOptions` `CosmosClientOptions` 。 根據預設，如果要求繼續以要求速率以上的方式運作，則在30秒的累計等候時間之後，就會傳回 CosmosException，狀態碼為429。 即使目前的值是預設值9或使用者自訂值，當目前的重試計數小於最大重試次數時，也會傳回此錯誤。

自動重試行為可協助改善大部分應用程式的復原能力和可用性。 但是當您執行效能基準測試時，可能不是最佳的行為，尤其是在測量延遲的時候。 如果實驗達到伺服器節流並導致用戶端 SDK 以無訊息模式重試，則用戶端觀察到的延遲將會突然增加。 若要避免效能實驗期間的延遲尖峰，請測量每項作業所傳回的費用，並確定要求的運作低於保留的要求率。 

如需詳細資訊，請參閱 [要求單位](request-units.md)。

**針對較高的輸送量，請針對較小的檔進行設計**

要求費用 (也就是說，指定作業的要求處理成本) 會直接與檔案大小相互關聯。 大型檔的作業成本高於小型檔上的作業。

## <a name="next-steps"></a>後續步驟
如需在少數用戶端電腦上用來評估高效能案例 Azure Cosmos DB 的範例應用程式，請參閱 [Azure Cosmos DB 的效能和規模測試](performance-testing.md)。

若要深入了解如何針對規模和高效能設計您的應用程式，請參閱 [Azure Cosmos DB 的資料分割與調整規模](partition-data.md)。
