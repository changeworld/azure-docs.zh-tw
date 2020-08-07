---
title: 適用于 .NET SDK v3 的 Azure Cosmos DB 效能秘訣
description: 瞭解用戶端設定選項，以改善 .NET v3 SDK 效能 Azure Cosmos DB。
author: j82w
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/16/2020
ms.author: jawilley
ms.openlocfilehash: 9816ea7dd9f5aef9dcdd62319f8cc4408eff3fd8
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987251"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Azure Cosmos DB 和 .NET 的效能祕訣

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [非同步 Java SDK v2](performance-tips-async-java.md)
> * [同步 Java SDK v2](performance-tips-java.md)

Azure Cosmos DB 是一個既快速又彈性的分散式資料庫，可在獲得延遲與輸送量保證的情況下順暢地調整。 您不需要進行主要的架構變更，或是撰寫複雜的程式碼以 Azure Cosmos DB 調整您的資料庫。 相應增加和減少就像進行單一 API 呼叫一樣簡單。 若要深入了解，請參閱[如何佈建容器輸送量](how-to-provision-container-throughput.md)或[如何佈建資料庫輸送量](how-to-provision-database-throughput.md)。 但是因為 Azure Cosmos DB 是透過網路呼叫存取，所以您可以在使用[SQL .NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3)時進行用戶端優化以達到尖峰效能。

因此，如果您正嘗試改善資料庫效能，請考慮下列選項：

## <a name="hosting-recommendations"></a>裝載建議

**針對需要大量查詢的工作負載，請使用 Windows 64 位，而不是 Linux 或 Windows 32 位主機處理**

我們建議 Windows 64 位主機處理以獲得更好的效能。 SQL SDK 包含原生 ServiceInterop.dll，可在本機剖析和最佳化查詢。 ServiceInterop.dll 只能在 Windows x64 平台上受到支援。 針對 Linux 和其他不支援 ServiceInterop.dll 無法使用的平臺，會對閘道進行額外的網路呼叫，以取得優化的查詢。 下列應用程式類型預設使用32位主機處理。 若要將主機處理變更為64位處理，請根據應用程式的類型，執行下列步驟：

- 對於可執行檔應用程式，您可以在 [**專案屬性**] 視窗的 [**組建**] 索引標籤上，將[平臺目標](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019)設定為**x64** ，藉以變更主機處理。

- 針對以 VSTest 為基礎的測試專案，您可以在**Test**  >  **Test Settings**  >  [Visual Studio**測試**] 功能表上選取 [測試] [測試設定] [**預設處理器架構] 做為 [X64** ] 來變更主控制項

- 針對本機部署的 ASP.NET web 應用程式，您可以在 [**工具**] [選項] [專案和方案] [Web 專案] 底下，選取 **[使用 web sites 和專案的64位版本 IIS Express** ] 來變更主控制項處理  >  **Options**  >  **Projects and Solutions**  >  ** **。

- 針對部署在 Azure 上的 ASP.NET web 應用程式，您可以在 Azure 入口網站的 [**應用程式設定**] 中選取**64 位**平臺，以變更主機處理。

> [!NOTE] 
> 根據預設，新的 Visual Studio 專案會設定為 [**任何 CPU**]。 我們建議您將專案設定為**x64** ，使其不會切換至**x86**。 如果加入 x86 專用的相依性，則設定為 [**任何 CPU** ] 的專案可以輕易地切換至**x86** 。<br/>
> ServiceInterop.dll 必須位於 SDK DLL 執行所在的資料夾中。 只有當您手動複製 Dll 或擁有自訂群組建/部署系統時，才需要考慮這一點。
    
**開啟伺服器端垃圾收集 (GC) **

在某些情況下，減少垃圾收集的頻率可能會有説明。 在 .NET 中，將[gcServer](https://docs.microsoft.com/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection)設定為 `true` 。

**相應放大您的用戶端工作負載**

如果您測試的是高輸送量層級 (超過 50000 RU/秒) ，用戶端應用程式可能會成為瓶頸，因為電腦會在 CPU 或網路使用率上達到上限。 如果到了這一刻，您可以將用戶端應用程式向外延展至多部伺服器，以繼續將 Azure Cosmos DB 帳戶再往前推進一步。

> [!NOTE] 
> 高 CPU 使用率可能會導致延遲增加和要求超時例外狀況。

## <a name="networking"></a>網路功能
<a id="direct-connection"></a>

**原則︰使用直接連接模式**

用戶端如何連線到 Azure Cosmos DB 會對效能造成重大影響，特別是針對觀察到的用戶端延遲。 設定用戶端連線原則有兩個可用的金鑰設定：連接*模式*和連接*通訊協定*。  兩個可用的模式︰

   * 直接模式 (預設) 

     直接模式支援透過 TCP 通訊協定連線，而且如果您使用[Cosmos/.Net V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3)，則是預設連線模式。 這可提供較佳的效能，而且需要比閘道模式更少的網路躍點。

   * 閘道模式
      
     如果您的應用程式在具有嚴格防火牆限制的公司網路中執行，則閘道模式是最佳選擇，因為它會使用標準 HTTPS 埠和單一端點。 不過，效能的缺點是每次讀取或寫入資料到 Azure Cosmos DB 時，閘道模式都會涉及額外的網路躍點。 因為網路躍點較少，所以 direct 模式可提供較佳的效能。 當您在具有有限數目的通訊端連線的環境中執行應用程式時，我們也建議使用閘道連線模式。

     當您在 Azure Functions 中使用 SDK 時，特別是在取用[方案](../azure-functions/functions-scale.md#consumption-plan)中，請留意目前的連線[限制](../azure-functions/manage-connections.md)。 在此情況下，如果您也在 Azure Functions 應用程式中使用其他以 HTTP 為基礎的用戶端，則閘道模式可能會更好。


在閘道模式中，當您使用適用于 MongoDB 的 Azure Cosmos DB API 時，Azure Cosmos DB 會使用埠443和埠10250、10255和10256。 埠10250對應至預設 MongoDB 實例，而不進行異地複寫。 埠10255和10256會對應到具有異地複寫的 MongoDB 實例。
     
當您在直接模式中使用 TCP 時，除了閘道埠之外，您還需要確保10000和20000之間的埠範圍已開啟，因為 Azure Cosmos DB 使用動態 TCP 埠 (在[私人端點](./how-to-configure-private-endpoints.md)上使用直接模式時，必須開啟) 的完整 tcp 埠範圍（從0到65535）。 預設會針對標準 Azure VM 設定開啟埠。 如果未開啟這些埠，而您嘗試使用 TCP，您會收到503服務無法使用的錯誤。 下表顯示適用于各種 Api 的連線模式，以及用於每個 API 的服務埠：

|連線模式  |支援的通訊協定  |支援的 SDK  |API/服務連接埠  |
|---------|---------|---------|---------|
|閘道  |   HTTPS    |  所有 Sdk    |   SQL (443) 、MongoDB (10250、10255、10256) 、Table (443) 、Cassandra (10350) 、Graph (443)     |
|直接    |     TCP    |  .NET SDK    | 使用公用/服務端點時：10000到20000範圍中的埠<br>使用私用端點時：0到65535範圍內的埠 |

Azure Cosmos DB 提供透過 HTTPS 的簡單開放式 RESTful 程式設計模型。 此外，它可提供有效率的 TCP 通訊協定，此 TCP 通訊協定在通訊模型中也符合 REST 限制，並且可以透過 .NET 用戶端 SDK 取得。 TCP 通訊協定會使用 TLS 進行初始驗證和加密流量。 為了達到最佳效能，儘可能使用 TCP 通訊協定。

針對 SDK V3，您可以在中建立實例時設定連接模式 `CosmosClient` `CosmosClientOptions` 。 請記住，direct 模式是預設值。

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

由於只有直接模式才支援 TCP，因此如果您使用閘道模式，則會一律使用 HTTPS 通訊協定來與閘道通訊。

:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Azure Cosmos DB 連線原則" border="false":::

**暫時連接埠耗盡**

如果您在實例上看到較高的連接磁片區或高埠使用方式，請先確認您的用戶端實例是否已單次個體。 換句話說，用戶端實例在應用程式的存留期內應該是唯一的。

在 TCP 通訊協定上執行時，用戶端會使用長期連線（而不是 HTTPS 通訊協定）來優化延遲，這會在閒置2分鐘後終止連接。

在您具有稀疏存取的案例中，如果您在與閘道模式存取相比，發現連線計數較高，您可以：

* 設定[CosmosClientOptions 的 PortReuseMode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode)屬性，使其 `PrivatePortPool` (符合 framework 版本>= 4.6.1 和 .net Core 版本 >= 2.0) ：此屬性可讓 SDK 針對不同的 Azure Cosmos DB 目的地端點使用小型的暫時埠集區。
* 設定[CosmosClientOptions. IdleConnectionTimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout)屬性必須大於或等於10分鐘。 建議的值介於20分鐘到24小時之間。

<a id="same-region"></a>

**針對效能，請在相同的 Azure 區域中共置用戶端**

可能的話，請將任何呼叫 Azure Cosmos DB 的應用程式放在與 Azure Cosmos DB 資料庫相同的區域中。 以下是大致的比較：在相同區域內 Azure Cosmos DB 的呼叫會在1毫秒到2毫秒內完成，但美國西部與東 coast 之間的延遲會超過50毫秒。 這項延遲可能會隨著要求的要求而有所不同，這取決於它從用戶端傳遞至 Azure 資料中心界限時所採取的路由。 藉由確保呼叫應用程式位於與已布建的 Azure Cosmos DB 端點相同的 Azure 區域內，您可以取得最低的可能延遲。 如需可用區域的清單，請參閱 [Azure 區域](https://azure.microsoft.com/regions/#services)。

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB 連線原則" border="false":::

   <a id="increase-threads"></a>

**增加執行緒/工作的數目**

由於 Azure Cosmos DB 的呼叫是透過網路進行，因此您可能需要改變要求的並行程度，讓用戶端應用程式花最少的時間在要求之間等待。 例如，如果您使用的是 .NET 工作[平行程式庫](https://msdn.microsoft.com//library/dd460717.aspx)，請建立從 Azure Cosmos DB 讀取或寫入的數百個工作順序。

**啟用加速網路**
 
 若要減少延遲和 CPU 抖動，建議您在用戶端虛擬機器上啟用加速網路。 請參閱[使用加速網路建立 Windows 虛擬機器](../virtual-network/create-vm-accelerated-networking-powershell.md)或[使用加速網路建立 Linux 虛擬機器](../virtual-network/create-vm-accelerated-networking-cli.md)。

## <a name="sdk-usage"></a>SDK 使用方式
**安裝最新的 SDK**

Azure Cosmos DB SDK 會持續改善以提供最佳效能。 請參閱 [Azure Cosmos DB SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) 頁面來判斷最新的 SDK 並檢閱改善項目。

**使用串流 Api**

[.NET SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3)包含可接收和傳回資料而不進行序列化的串流 api。 

不會直接從 SDK 取用回應的仲介層應用程式，但會將它們轉送至其他應用層，可受益于串流 Api。 如需串流處理的範例，請參閱[專案管理](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement)範例。

**在應用程式存留期內使用單一 Azure Cosmos DB 用戶端**

每個 `CosmosClient` 實例都是安全線程，並且會在直接模式中運作時執行有效率的連線管理和位址快取。 若要允許有效率的連線管理和更佳的 SDK 用戶端效能，建議您在 `AppDomain` 應用程式的存留期內，每個都使用單一實例。

當您使用 Azure Functions 時，實例也應該遵循現有的[指導方針](../azure-functions/manage-connections.md#static-clients)，並維護單一實例。

<a id="max-connection"></a>

**在寫入作業上停用內容回應**

對於具有大量建立承載的工作負載，請將 EnableContentResponseOnWrite 要求選項設為 false。 服務將不會再將已建立或更新的資源傳回至 SDK。 應用程式通常會建立物件，因此不需要服務傳回它。 標頭值仍可存取，例如要求費用。 這可能會改善效能，因為 SDK 不再需要配置記憶體或序列化回應的主體。 這也可以減少網路頻寬使用量，以進一步協助效能。  

```csharp
ItemRequestOption requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**啟用 Bulk 以優化輸送量，而不是延遲**針對工作負載需要大量輸送量的案例啟用大量，而延遲並不重要。 如需如何啟用此功能的詳細資訊，以及應該使用的案例，請參閱[大量簡介](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk)。

**使用閘道模式時增加每部主機的 System.Net MaxConnections**

當您使用閘道模式時，Azure Cosmos DB 要求是透過 HTTPS/REST 進行。 它們會受到每個主機名稱或 IP 位址的預設連線限制。 您可能需要將設定 `MaxConnections` 為較高的值 (100 到 1000) ，用戶端程式庫才能使用多個同時連線來 Azure Cosmos DB。 在 .NET SDK 1.8.0 和更新版本中， [ServicePointManager. servicepointmanager.defaultconnectionlimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx)的預設值是50。 若要變更此值，您可以將[ConnectionPolicy. documents.client.connectionpolicy.maxconnectionlimit 設定](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx)設定為較高的值。

**微調分割集合的平行查詢**

SQL .NET SDK 支援平行查詢，可讓您以平行方式查詢分割的容器。 如需詳細資訊，請參閱使用 SDK 的相關[程式碼範例](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs)。 平行查詢的設計目的是要提供比其序列對應更佳的查詢延遲和輸送量。 平行查詢提供兩個參數，您可以調整以符合您的需求： 
- `MaxConcurrency`控制可平行查詢的最大分割區數目。 
- `MaxBufferedItemCount`控制預先提取結果的數目。

***微調並行程度***

平行查詢的運作方式是以平行方式查詢多個分割區。 但是來自個別分割區的資料會依查詢的順序提取。 將 `MaxConcurrency` [SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3)中的設定為分割區數目，有可能達到最高效能的查詢，但前提是所有其他系統條件都維持不變。 如果您不知道分割區數目，可以將平行處理原則的程度設定為較高的數位。 系統會選擇最小 (資料分割數目、使用者提供的輸入) 做為平行處理原則的程度。

如果資料平均分散于與查詢相關的所有分割區，平行查詢會產生最大的好處。 如果分割的集合已分割，以便查詢所傳回的所有或大部分資料都集中在少數幾個分割區中 (一個分割區是最糟的) ，則這些磁碟分割會造成查詢的效能瓶頸。

***微調 MaxBufferedItemCount***
    
平行查詢的設計是可在用戶端處理目前的結果批次時，先預先擷取結果。 這個預先提取可協助改善查詢的整體延遲。 `MaxBufferedItemCount`參數會限制預先提取的結果數目。 設定 `MaxBufferedItemCount` 為預期傳回的結果數目 (或較高的數目) 以允許查詢接收預先提取的最大效益。

不論平行處理原則的程度為何，預先提取的運作方式都相同，而且所有分割區的資料都有單一緩衝區。  

**在 RetryAfter 間隔實作降速**

在效能測試期間，您應該增加負載，直到節流要求的小型速率為止。 如果要求受到節流處理，用戶端應用程式應該在伺服器指定的重試間隔的節流上關閉。 採用輪詢可確保您在重試之間花費最少的等待時間。 

如需詳細資訊，請參閱[RetryAfter](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter?view=azure-dotnet#Microsoft_Azure_Cosmos_CosmosException_RetryAfter)。
    
有一種機制可記錄其他診斷資訊和疑難排解延遲問題，如下列範例所示。 您可以針對具有較高讀取延遲的要求記錄診斷字串。 已捕獲的診斷字串可協助您瞭解針對指定的要求收到429個錯誤的次數。

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**增加執行緒/工作的數目**

請參閱這篇文章的網路功能一節中的[增加執行緒/工作的數目](#increase-threads)。

## <a name="indexing-policy"></a>編製索引原則
 
**從索引編製中排除未使用的路徑以加快寫入速度**

Azure Cosmos DB 編制索引原則也可讓您指定要在編制索引中包含或排除的檔路徑，方法是使用索引路徑 (Indexingpolicy.includedpaths 和 IndexingPolicy. Indexingpolicy.excludedpaths) 。 僅針對您所需的路徑編制索引，可以改善寫入效能、減少寫入作業的 RU 費用，以及針對事先知道查詢模式的案例，減少索引儲存空間。 這是因為索引成本會直接與索引的唯一路徑數目相互關聯。 例如，此程式碼示範如何使用 "*" 萬用字元，將檔的整個區段從編制索引中排除 (子樹) ：

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

如需詳細資訊，請參閱 [Azure Cosmos DB 索引編製原則](index-policy.md)。

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

**測量和調整較低的要求單位/秒使用量**

Azure Cosmos DB 提供一組豐富的資料庫作業。 這些作業包括使用 Udf、預存程式和觸發程式的關聯式和階層式查詢，全都是在資料庫集合中的檔上運作。 與上述各項作業相關聯的成本會根據完成此操作所需的 CPU、IO 和記憶體而有所不同。 不需要考慮和管理硬體資源，您可以將要求單位的 (RU) 作為執行各種資料庫作業和服務應用程式要求所需資源的單一量值。

輸送量是根據為每個容器所設定的[要求單位](request-units.md)數目來布建。 要求單位耗用量會評估為每秒的速率。 超過其容器布建的要求單位速率的應用程式會受到限制，直到該速率降到容器的布建層級以下為止。 如果您的應用程式需要較高層級的輸送量，您可以藉由布建額外的要求單位來增加輸送量。

查詢的複雜性會影響針對作業所耗用的要求單位數目。 述詞數目、述詞性質、Udf 數目，以及源資料集的大小，全都會影響查詢作業的成本。

若要測量任何作業 (建立、更新或刪除) 的額外負荷，請檢查[x 毫秒要求-費用](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)標頭 (或 `RequestCharge` .net SDK) 中的對等屬性， `ResourceResponse\<T>` `FeedResponse\<T>` 以測量作業所耗用的要求單位數目：

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

在此標頭中傳回的要求費用是布建輸送量的一小部分， (也就是 2000 ru/秒) 。 例如，如果上述查詢傳回 1000 1 KB 的檔，則作業成本為1000。 因此，在一秒內，伺服器只會接受兩個這類要求，再于之後速率限制要求。 如需詳細資訊，請參閱[要求單位](request-units.md)和[要求單位計算機](https://www.documentdb.com/capacityplanner)。
<a id="429"></a>

**處理速率限制/要求速率太大**

當用戶端嘗試超過帳戶的保留輸送量時，伺服器的效能不會降低，而且不會使用超過保留層級的輸送量容量。 伺服器會事先結束要求，RequestRateTooLarge (HTTP 狀態碼 429) 。 它會傳回一個[x 毫秒-重試後](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)端標頭，表示使用者在再次嘗試要求之前必須等待的時間量（以毫秒為單位）。

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

SDK 全都隱含地攔截這個回應，採用伺服器指定的 retry-after 標頭，並重試此要求。 除非有多個用戶端同時存取您的帳戶，否則下次重試將會成功。

如果您有多個用戶端不斷地以高於要求速率的方式運作，則用戶端目前在內部設定為9的預設重試計數可能不足夠。 在此情況下，用戶端會擲回狀態碼為429的 CosmosException 至應用程式。 

您可以藉由在實例上設定來變更預設重試計數 `RetryOptions` `CosmosClientOptions` 。 根據預設，如果要求繼續以高於要求速率的方式運作，則在30秒的累計等候時間之後，就會傳回具有狀態碼429的 CosmosException。 即使目前的重試計數小於最大重試計數，此錯誤還是會傳回，不論目前的值是9的預設值還是使用者定義的值。

自動重試行為可協助改善大部分應用程式的復原能力和可用性。 但是當您在進行效能基準測試時，它可能不是最佳的行為，尤其是在測量延遲時。 如果實驗達到伺服器節流並導致用戶端 SDK 以無訊息模式重試，則用戶端觀察到的延遲將會突然增加。 若要避免效能實驗期間的延遲尖峰，測量每個作業所傳回的費用，並確保要求是以低於保留要求速率的方式運作。 如需詳細資訊，請參閱[要求單位](request-units.md)。

**如需更高的輸送量，請設計較小的檔**

要求費用 (也就是，指定作業的要求處理成本) 會直接與檔案大小產生關聯。 大型檔的作業成本高於小型檔的作業。

## <a name="next-steps"></a>後續步驟
如需用來評估一些用戶端電腦上高效能案例 Azure Cosmos DB 的範例應用程式，請參閱[使用 Azure Cosmos DB 的效能和規模測試](performance-testing.md)。

若要深入了解如何針對規模和高效能設計您的應用程式，請參閱 [Azure Cosmos DB 的資料分割與調整規模](partition-data.md)。
