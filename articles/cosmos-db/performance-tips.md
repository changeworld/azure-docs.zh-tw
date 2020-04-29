---
title: 適用於 .NET 的 Azure Cosmos DB 效能提示
description: 瞭解用戶端設定選項，以改善 Azure Cosmos DB 效能。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: ee2743af2f8499aec04d8b6b733e1ba4c2a82083
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80546066"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Azure Cosmos DB 和 .NET 的效能祕訣

> [!div class="op_single_selector"]
> * [非同步 Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB 是一個既快速又彈性的分散式資料庫，可在獲得延遲與輸送量保證的情況下順暢地調整。 您不需要進行主要的架構變更，或是撰寫複雜的程式碼以 Azure Cosmos DB 調整您的資料庫。 相應增加和減少就像進行單一 API 呼叫一樣簡單。 若要深入了解，請參閱[如何佈建容器輸送量](how-to-provision-container-throughput.md)或[如何佈建資料庫輸送量](how-to-provision-database-throughput.md)。 但是因為 Azure Cosmos DB 是透過網路呼叫存取，所以您可以在使用[SQL .NET SDK](sql-api-sdk-dotnet-standard.md)時進行用戶端優化以達到尖峰效能。

因此，如果您正嘗試改善資料庫效能，請考慮下列選項：

## <a name="hosting-recommendations"></a>裝載建議

**針對需要大量查詢的工作負載，請使用 Windows 64 位，而不是 Linux 或 Windows 32 位主機處理**

我們建議 Windows 64 位主機處理以獲得更好的效能。 SQL SDK 包含原生 Microsoft.azure.documents.serviceinterop.dll，可在本機剖析和優化查詢。 只有在 Windows x64 平臺上才支援 Microsoft.azure.documents.serviceinterop.dll。 針對 Linux 和無法使用 Microsoft.azure.documents.serviceinterop.dll 的其他不支援平臺，會對閘道進行額外的網路呼叫以取得優化的查詢。 下列應用程式類型預設使用32位主機處理。 若要將主機處理變更為64位處理，請根據應用程式的類型，執行下列步驟：

- 對於可執行檔應用程式，您可以在 [**專案屬性**] 視窗的 [**組建**] 索引標籤上，將[平臺目標](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019)設定為**x64** ，藉以變更主機處理。

- 針對以 VSTest 為基礎的測試專案，您可以在 [Visual Studio**測試**] 功能表上選取 [**測試** > ] [測試**設定** > ] [**預設處理器架構] 做為 [X64** ] 來變更主控制項

- 針對本機部署的 ASP.NET web 應用程式，您可以在 [**工具** > ] [**選項** > ] [**專案和方案** > ] [**Web 專案**] 底下，選取 **[使用 web sites 和專案的64位版本 IIS Express** ] 來變更主控制項處理。

- 針對部署在 Azure 上的 ASP.NET web 應用程式，您可以在 Azure 入口網站的 [**應用程式設定**] 中選取**64 位**平臺，以變更主機處理。

> [!NOTE] 
> 根據預設，新的 Visual Studio 專案會設定為 [**任何 CPU**]。 我們建議您將專案設定為**x64** ，使其不會切換至**x86**。 如果加入 x86 專用的相依性，則設定為 [**任何 CPU** ] 的專案可以輕易地切換至**x86** 。<br/>
> Microsoft.azure.documents.serviceinterop.dll 必須位於 SDK DLL 執行所在的資料夾中。 只有當您手動複製 Dll 或擁有自訂群組建/部署系統時，才需要考慮這一點。
    
**開啟伺服器端垃圾收集（GC）**

在某些情況下，減少垃圾收集的頻率可能會有説明。 在 .NET 中， [gcServer](https://msdn.microsoft.com/library/ms229357.aspx)將 gcServer `true`設定為。

**相應放大您的用戶端工作負載**

如果您是以高輸送量層級（超過 50000 RU/秒）進行測試，用戶端應用程式可能會成為瓶頸，因為電腦會在 CPU 或網路使用率上達到上限。 如果到了這一刻，您可以將用戶端應用程式向外延展至多部伺服器，以繼續將 Azure Cosmos DB 帳戶再往前推進一步。

> [!NOTE] 
> 高 CPU 使用率可能會導致延遲增加和要求超時例外狀況。

## <a name="networking"></a>網路功能
<a id="direct-connection"></a>

**原則︰使用直接連接模式**

用戶端如何連線到 Azure Cosmos DB 會對效能造成重大影響，特別是針對觀察到的用戶端延遲。 設定用戶端連線原則有兩個可用的金鑰設定：連接*模式*和連接*通訊協定*。  兩個可用的模式︰

   * 閘道模式
      
     所有 SDK 平臺都支援閘道模式，而且是針對[Microsoft. Azure DOCUMENTDB SDK](sql-api-sdk-dotnet.md)設定的預設值。 如果您的應用程式在具有嚴格防火牆限制的公司網路中執行，則閘道模式是最佳選擇，因為它會使用標準 HTTPS 埠和單一端點。 不過，效能的缺點是每次讀取或寫入資料到 Azure Cosmos DB 時，閘道模式都會涉及額外的網路躍點。 因為網路躍點較少，所以 direct 模式可提供較佳的效能。 當您在具有有限數目的通訊端連線的環境中執行應用程式時，我們也建議使用閘道連線模式。

     當您在 Azure Functions 中使用 SDK 時，特別是在取用[方案](../azure-functions/functions-scale.md#consumption-plan)中，請留意目前的連線[限制](../azure-functions/manage-connections.md)。 在此情況下，如果您也在 Azure Functions 應用程式中使用其他以 HTTP 為基礎的用戶端，則閘道模式可能會更好。

   * 直接模式

     直接模式支援透過 TCP 通訊協定連線，而且如果您使用[Cosmos/.Net V3 SDK](sql-api-sdk-dotnet-standard.md)，則是預設連線模式。

在閘道模式中，當您使用適用于 MongoDB 的 Azure Cosmos DB API 時，Azure Cosmos DB 會使用埠443和埠10250、10255和10256。 埠10250對應至預設 MongoDB 實例，而不進行異地複寫。 埠10255和10256會對應到具有異地複寫的 MongoDB 實例。
     
當您在直接模式中使用 TCP 時，除了閘道埠之外，您還需要確保10000和20000之間的埠範圍已開啟，因為 Azure Cosmos DB 使用動態 TCP 埠。 如果未開啟這些埠，而您嘗試使用 TCP，您會收到503服務無法使用的錯誤。 下表顯示適用于各種 Api 的連線模式，以及用於每個 API 的服務埠：

|連線模式  |支援的通訊協定  |支援的 SDK  |API/服務連接埠  |
|---------|---------|---------|---------|
|閘道  |   HTTPS    |  所有 Sdk    |   SQL （443）、MongoDB （10250、10255、10256）、Table （443）、Cassandra （10350）、Graph （443）    |
|直接    |     TCP    |  .NET SDK    | 10000到20000範圍中的埠 |

Azure Cosmos DB 提供透過 HTTPS 的簡單開放式 RESTful 程式設計模型。 此外，它可提供有效率的 TCP 通訊協定，此 TCP 通訊協定在通訊模型中也符合 REST 限制，並且可以透過 .NET 用戶端 SDK 取得。 TCP 通訊協定會使用 TLS 進行初始驗證和加密流量。 為了達到最佳效能，儘可能使用 TCP 通訊協定。

針對 SDK V3，您可以在中`CosmosClient` `CosmosClientOptions`建立實例時設定連接模式。 請記住，direct 模式是預設值。

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

針對 Microsoft. Azure DocumentDB SDK，您可以使用`DocumentClient` `ConnectionPolicy`參數，在實例的結構中設定連接模式。 如果您使用 direct 模式，您也可以使用`Protocol` `ConnectionPolicy`參數來設定。

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

由於只有直接模式才支援 TCP，因此，如果您使用閘道模式，則 HTTPS 通訊協定一律會用來與閘道`Protocol`通訊， `ConnectionPolicy`而中的值會被忽略。

![Azure Cosmos DB 連線原則](./media/performance-tips/connection-policy.png)

**呼叫 OpenAsync 以避免第一次要求的啟動延遲**

根據預設，第一個要求會有較高的延遲，因為它需要提取位址路由表。 當您使用[SDK V2](sql-api-sdk-dotnet.md)時， `OpenAsync()`請在初始化期間呼叫一次，以避免第一次要求的啟動延遲：

    await client.OpenAsync();

> [!NOTE] 
> `OpenAsync`會產生要求，以取得帳戶中所有容器的位址路由表。 對於具有許多容器但其應用程式存取其子集的帳戶， `OpenAsync`會產生不必要的流量量，使初始化變慢。 因此， `OpenAsync`在此案例中使用可能不會很有用，因為它會使應用程式啟動變慢。

   <a id="same-region"></a>
**針對效能，請在相同的 Azure 區域中共置用戶端**

可能的話，請將任何呼叫 Azure Cosmos DB 的應用程式放在與 Azure Cosmos DB 資料庫相同的區域中。 以下是大致的比較：在相同區域內 Azure Cosmos DB 的呼叫會在1毫秒到2毫秒內完成，但美國西部與東 coast 之間的延遲會超過50毫秒。 這項延遲可能會隨著要求的要求而有所不同，這取決於它從用戶端傳遞至 Azure 資料中心界限時所採取的路由。 藉由確保呼叫應用程式位於與已布建的 Azure Cosmos DB 端點相同的 Azure 區域內，您可以取得最低的可能延遲。 如需可用區域的清單，請參閱[Azure 區域](https://azure.microsoft.com/regions/#services)。

![Azure Cosmos DB 連線原則](./media/performance-tips/same-region.png)<a id="increase-threads"></a>

**增加執行緒/工作的數目**

由於 Azure Cosmos DB 的呼叫是透過網路進行，因此您可能需要改變要求的平行處理原則程度，讓用戶端應用程式花最少的時間在要求之間等待。 例如，如果您使用的是 .NET 工作[平行程式庫](https://msdn.microsoft.com//library/dd460717.aspx)，請建立從 Azure Cosmos DB 讀取或寫入的數百個工作順序。

**啟用加速網路**
 
 若要減少延遲和 CPU 抖動，建議您在用戶端虛擬機器上啟用加速網路。 請參閱[使用加速網路建立 Windows 虛擬機器](../virtual-network/create-vm-accelerated-networking-powershell.md)或[使用加速網路建立 Linux 虛擬機器](../virtual-network/create-vm-accelerated-networking-cli.md)。

## <a name="sdk-usage"></a>SDK 使用方式
**安裝最新的 SDK**

Azure Cosmos DB SDK 會持續改善以提供最佳效能。 請參閱 [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) 頁面來判斷最新的 SDK 並檢閱改善項目。

**使用串流 Api**

[.NET SDK V3](sql-api-sdk-dotnet-standard.md)包含可接收和傳回資料而不進行序列化的串流 api。 

不會直接從 SDK 取用回應的仲介層應用程式，但會將它們轉送至其他應用層，可受益于串流 Api。 如需串流處理的範例，請參閱[專案管理](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement)範例。

**在應用程式存留期內使用單一 Azure Cosmos DB 用戶端**

每`DocumentClient`個`CosmosClient`和實例都是安全線程，並且會在直接模式中運作時執行有效率的連線管理和位址快取。 若要允許有效率的連線管理和更佳的 SDK 用戶端效能，建議您在應用`AppDomain`程式的存留期內，每個都使用單一實例。

   <a id="max-connection"></a>

**使用閘道模式時增加每部主機的 System.Net MaxConnections**

當您使用閘道模式時，Azure Cosmos DB 要求是透過 HTTPS/REST 進行。 它們會受到每個主機名稱或 IP 位址的預設連線限制。 您可能需要將設定`MaxConnections`為較高的值（100到1000），讓用戶端程式庫可以使用多個同時連線來 Azure Cosmos DB。 在 .NET SDK 1.8.0 和更新版本中， [ServicePointManager. servicepointmanager.defaultconnectionlimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx)的預設值是50。 若要變更此值，您可以將[ConnectionPolicy. documents.client.connectionpolicy.maxconnectionlimit 設定](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx)設定為較高的值。

**微調分割集合的平行查詢**

SQL .NET SDK 1.9.0 和更新版本支援平行查詢，可讓您以平行方式查詢分割的集合。 如需詳細資訊，請參閱使用 SDK 的相關[程式碼範例](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs)。 平行查詢的設計目的是要提供比其序列對應更佳的查詢延遲和輸送量。 平行查詢提供兩個參數，您可以調整以符合您的需求： 
- `MaxDegreeOfParallelism`控制可平行查詢的最大分割區數目。 
- `MaxBufferedItemCount`控制預先提取結果的數目。

***調整平行處理原則的程度***

平行查詢的運作方式是以平行方式查詢多個分割區。 但是來自個別分割區的資料會依查詢的順序提取。 在`MaxDegreeOfParallelism` [Sdk V2](sql-api-sdk-dotnet.md)或`MaxConcurrency` [sdk V3](sql-api-sdk-dotnet-standard.md)中將設定為分割區數目，可讓您獲得最高效能的查詢，但前提是所有其他系統條件都維持不變。 如果您不知道分割區數目，可以將平行處理原則的程度設定為較高的數位。 系統會選擇最小值（資料分割數目、使用者提供的輸入）做為平行處理原則的程度。

請注意，如果資料平均分佈在與查詢相關的所有分割區，平行查詢會產生最大的好處。 如果分割的集合已分割，以便查詢所傳回的所有或大部分資料都集中在幾個分割區中（一個分割區是最壞的情況），則這些磁碟分割會造成查詢的效能瓶頸。

***微調 MaxBufferedItemCount***
    
平行查詢的設計目的是要在用戶端處理目前的結果批次時預先提取結果。 這個預先提取可協助改善查詢的整體延遲。 `MaxBufferedItemCount`參數會限制預先提取的結果數目。 設定`MaxBufferedItemCount`為預期傳回的結果數目（或較大的數位），以允許查詢從預先提取取得最大效益。

不論平行處理原則的程度為何，預先提取的運作方式都相同，而且所有分割區的資料都有單一緩衝區。  

**在 RetryAfter 間隔實作降速**

在效能測試期間，您應該增加負載，直到節流要求的小型速率為止。 如果要求受到節流處理，用戶端應用程式應該在伺服器指定的重試間隔的節流上關閉。 採用輪詢可確保您在重試之間花費最少的等待時間。 

這些 Sdk 中包含重試原則支援：
- [.NET sdk FOR sql](sql-api-sdk-dotnet.md)和[JAVA sdk for sql](sql-api-sdk-java.md)的版本1.8.0 和更新版本
- [適用于 sql](sql-api-sdk-node.md)的 node.js sdk 和適用于[SQL 的 Python sdk](sql-api-sdk-python.md)的1.9.0 和更新版本
- 所有支援的[.Net Core](sql-api-sdk-dotnet-core.md) sdk 版本 

如需詳細資訊，請參閱[RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx)。
    
在 .NET SDK 的1.19 版和更新版本中，有一種機制可記錄其他診斷資訊和疑難排解延遲問題，如下列範例所示。 您可以針對具有較高讀取延遲的要求記錄診斷字串。 已捕獲的診斷字串可協助您瞭解針對指定的要求收到429個錯誤的次數。

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**快取較低讀取延遲的文件 URI**

盡可能快取文件 URI 以達到最佳讀取效能。 當您建立資源時，您必須定義邏輯來快取資源識別碼。 根據資源識別碼的查閱速度會比以名稱為基礎的查閱更快，因此快取這些值可改善效能。

   <a id="tune-page-size"></a>
**調整查詢/讀取摘要的頁面大小以獲得更好的效能**

當您使用讀取摘要功能（例如`ReadDocumentFeedAsync`）對檔進行大量讀取時，或發出 SQL 查詢時，如果結果集太大，則會以分段方式傳回結果。 根據預設，會以 100 個項目或 1 MB 的區塊傳回結果 (以先達到的限制為準)。

若要減少抓取所有適用結果所需的網路來回行程次數，您可以使用 [ [x-毫秒-最大-專案計數](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)] 來增加頁面大小，以要求多達1000標頭。 當您只需要顯示幾個結果時（例如，如果您的使用者介面或應用程式 API 一次只傳回10個結果），您也可以將頁面大小減少為10，以降低讀取和查詢所耗用的輸送量。

> [!NOTE] 
> `maxItemCount`屬性不應該只用于分頁。 其主要用途是減少在單一頁面中傳回的最大專案數，藉此改善查詢的效能。  

您也可以使用可用的 Azure Cosmos DB Sdk 來設定頁面大小。 中[MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) `FeedOptions`的 feedoptions.maxitemcount 屬性可讓您設定要在列舉作業中傳回的最大專案數。 當`maxItemCount`設定為-1 時，SDK 會根據檔案大小自動尋找最佳值。 例如：
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
執行查詢時，會在 TCP 封包內傳送產生的資料。 如果您指定的值太低`maxItemCount`，則在 TCP 封包內傳送資料所需的往返次數很高，這會影響效能。 因此，如果您不確定要為`maxItemCount`屬性設定的值，最好將它設為-1，並讓 SDK 選擇預設值。

**增加執行緒/工作的數目**

請參閱這篇文章的網路功能一節中的[增加執行緒/工作的數目](#increase-threads)。

## <a name="indexing-policy"></a>編製索引原則
 
**從索引中排除未使用的路徑以加快寫入速度**

Azure Cosmos DB 編制索引原則也可讓您使用索引路徑（IndexingPolicy. Indexingpolicy.includedpaths 和 IndexingPolicy），指定要在索引編制中包含或排除的檔路徑。 在事先知道查詢模式的案例中，索引路徑可以改善寫入效能並減少索引儲存空間。 這是因為索引成本會直接與索引的唯一路徑數目相互關聯。 例如，此程式碼示範如何使用 "*" 萬用字元，從索引編制中排除檔的整個區段（子樹）：

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
```

如需詳細資訊，請參閱 [Azure Cosmos DB 索引編製原則](index-policy.md)。

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

**測量和調整較低的要求單位/秒使用量**

Azure Cosmos DB 提供一組豐富的資料庫作業。 這些作業包括使用 Udf、預存程式和觸發程式的關聯式和階層式查詢，全都是在資料庫集合中的檔上運作。 與上述各項作業相關聯的成本會根據完成此操作所需的 CPU、IO 和記憶體而有所不同。 除了考慮和管理硬體資源，您可以將要求單位（RU）視為執行各種資料庫作業和服務應用程式要求所需資源的單一量值。

輸送量是根據為每個容器所設定的[要求單位](request-units.md)數目來布建。 要求單位耗用量會評估為每秒的速率。 超過其容器布建的要求單位速率的應用程式會受到限制，直到該速率降到容器的布建層級以下為止。 如果您的應用程式需要較高層級的輸送量，您可以藉由布建額外的要求單位來增加輸送量。

查詢的複雜性會影響針對作業所耗用的要求單位數目。 述詞數目、述詞性質、Udf 數目，以及源資料集的大小，全都會影響查詢作業的成本。

若要測量任何作業（建立、更新或刪除）的額外負荷，請檢查[x 毫秒要求-費用](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)標頭（或 .net SDK 中`RequestCharge` `ResourceResponse\<T>` `FeedResponse\<T>`的對等屬性），以測量作業所耗用的要求單位數目：

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

在此標頭中傳回的要求費用是布建輸送量的一小部分（也就是 2000 ru/秒）。 例如，如果上述查詢傳回 1000 1 KB 的檔，則作業成本為1000。 因此，在一秒內，伺服器只會接受兩個這類要求，再于之後速率限制要求。 如需詳細資訊，請參閱[要求單位](request-units.md)和[要求單位計算機](https://www.documentdb.com/capacityplanner)。
<a id="429"></a>

**處理速率限制/要求速率太大**

當用戶端嘗試超過帳戶的保留輸送量時，伺服器的效能不會降低，而且不會使用超過保留層級的輸送量容量。 伺服器會事先以 RequestRateTooLarge 結束要求（HTTP 狀態碼429）。 它會傳回一個[x 毫秒-重試後](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)端標頭，表示使用者在再次嘗試要求之前必須等待的時間量（以毫秒為單位）。

    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

SDK 全都隱含地攔截這個回應，採用伺服器指定的 retry-after 標頭，並重試此要求。 除非有多個用戶端同時存取您的帳戶，否則下次重試將會成功。

如果您有多個用戶端不斷地以高於要求速率的方式運作，則用戶端目前在內部設定為9的預設重試計數可能不足夠。 在此情況下，用戶端會擲回狀態碼為429的 Documentclientexception (至應用程式。 

您可以藉由`RetryOptions`在`ConnectionPolicy`實例上設定來變更預設重試計數。 根據預設，如果要求繼續以高於要求速率的方式運作，則會在 30 秒的累計等候時間後傳回 DocumentClientException (狀態碼 429)。 即使目前的重試計數小於最大重試計數，此錯誤還是會傳回，不論目前的值是9的預設值還是使用者定義的值。

自動重試行為可協助改善大部分應用程式的復原能力和可用性。 但是當您在進行效能基準測試時，它可能不是最佳的行為，尤其是在測量延遲時。 如果實驗達到伺服器節流並導致用戶端 SDK 以無訊息模式重試，則用戶端觀察到的延遲將會突然增加。 若要避免效能實驗期間的延遲尖峰，測量每個作業所傳回的費用，並確保要求是以低於保留要求速率的方式運作。 如需詳細資訊，請參閱[要求單位](request-units.md)。

**如需更高的輸送量，請設計較小的檔**

給定作業的要求費用（也就是要求-處理成本）與檔案大小直接相互關聯。 大型檔的作業成本高於小型檔的作業。

## <a name="next-steps"></a>後續步驟
如需用來評估一些用戶端電腦上高效能案例 Azure Cosmos DB 的範例應用程式，請參閱[使用 Azure Cosmos DB 的效能和規模測試](performance-testing.md)。

若要深入了解如何針對規模和高效能設計您的應用程式，請參閱 [Azure Cosmos DB 的資料分割與調整規模](partition-data.md)。
