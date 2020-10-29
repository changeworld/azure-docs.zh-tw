---
title: 為多個 Azure 資料來源編製索引的 C# 教學課程
titleSuffix: Azure Cognitive Search
description: 了解如何使用索引子將多個資料來源的資料匯入至單一 Azure 認知搜尋服務索引。 本教學課程和範例程式碼採用 C#。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 6a1a7e19e598980b21ee6c41f6984de38d6a6f2b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791608"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>教學課程：使用 .NET SDK 從多個資料來源編製索引

Azure 認知搜尋服務可以將多個資料來源的資料匯入至單一合併搜尋索引，並進行分析和編製索引。 

本教學課程將使用 C# 和 Azure SDK for .NET 中的 [Azure.Search.Documents](/dotnet/api/overview/azure/search) 用戶端程式庫，為 Azure Cosmos DB 中的範例旅館資料編製索引，並將該資料與從 Azure Blob 儲存體文件中擷取的旅館房間詳細資料合併。 結果將會產生一個已合併的旅館搜尋索引，其中包含旅館文件，以及複雜資料類型的房間資料。

在本教學課程中，您將執行下列工作：

> [!div class="checklist"]
> * 上傳範例資料並建立資料來源
> * 識別文件索引鍵
> * 定義並建立索引
> * 為來自 Azure Cosmos DB 的旅館資料編製索引
> * 合併來自 Blob 儲存體的旅館房間資料

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="overview"></a>總覽

本教學課程使用新的用戶端程式庫 [Azure.Search.Documents](/dotnet/api/overview/azure/search) 11.x 版，來建立及執行多個索引子。 在本教學課程中，您將設定兩個 Azure 資料來源，以便設定可同時從這兩個來源提取資料以填入單一搜尋索引的索引子。 這兩個資料集必須有共同的值以支援合併。 在此範例中，該欄位是識別碼。 只要有一個共同的欄位可支援對應，索引子就可以合併來自不同資源的資料：來自 Azure SQL 的結構化資料、來自 Blob 儲存體的非結構化資料，或 Azure 上[支援的資料來源](search-indexer-overview.md#supported-data-sources)的任意組合。

您可以在下列專案中找到本教學課程中的最終版本程式碼：

* [multiple-data-sources/v11 (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v11)

本教學課程已更新為使用 Azure.Search.Documents (第 11 版) 套件。 如需舊版的 .NET SDK，請參閱 GitHub 上的 [Microsoft.Azure.Search (第 10 版) 程式碼範例](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v10)。

## <a name="prerequisites"></a>Prerequisites

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure 儲存體](../storage/common/storage-account-create.md)
+ [Visual Studio](https://visualstudio.microsoft.com/)
+ [Azure 認知搜尋 (11.x 版) NuGet 套件](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [建立](search-create-service-portal.md)或[尋找現有的搜尋服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 您可以使用免費服務來進行本教學課程。 免費的搜尋服務會有限制，您只能使用三個索引、三個索引子和三個資料來源。 本教學課程會各建立一個。 開始之前，請確定您的服務有空間可接受新的資源。

## <a name="1---create-services"></a>1 - 建立服務

本教學課程使用 Azure 認知搜尋進行索引編製和查詢、使用 Azure Cosmos DB 存放一個資料集，並使用 Azure Blob 儲存體存放第二個資料集。 

為具備鄰近性和管理方面的優勢，如果可能，請將這些服務全都建立在相同的區域和資源群組中。 在實務上，您的服務可位於任何區域。

此範例會使用兩個小型資料組，其中描述七家虛構的旅館。 一組描述旅館本身，並且將載入至 Azure Cosmos DB 資料庫。 另一組包含旅館房間詳細資料，並以七個不同的 JSON 檔案來提供以上傳至 Azure Blob 儲存體。

### <a name="start-with-cosmos-db"></a>首先處理 Cosmos DB

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後導覽您的 Azure Cosmos DB 帳戶 [概觀] 頁面。

1. 選取 [資料總管]  ，然後選取 [新增資料庫]  。

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="建立新的資料庫" border="false":::

1. 輸入名稱 **hotel-rooms-db** 。 接受其餘設定的預設值。

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="建立新的資料庫" border="false":::

1. 建立新的容器。 使用您剛才建立的現有資料庫。 輸入 **hotels** 作為容器名稱，並使用 **/HotelId** 作為分割區索引鍵。

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="建立新的資料庫" border="false":::

1. 選取 **hotels** 底下的 [項目]  ，然後按一下命令列上的 [上傳項目]  。 瀏覽至專案資料夾中的檔案 **cosmosdb/HotelsDataSubset_CosmosDb.json** ，並加以選取。

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="建立新的資料庫" border="false":::

1. 使用 [重新整理] 按鈕來重新整理您在旅館集合中的項目檢視。 您應該會看到列出七份新的資料庫文件。

1. 將 [金鑰] 頁面中的連接字串複製到 [記事本] 中。 在後續步驟中，您必須將這項資料用於 **appsettings.json** 。 如果您未使用建議的資料庫名稱 "hotel-rooms-db"，請一併複製資料庫名稱。

### <a name="azure-blob-storage"></a>Azure Blob 儲存體

1. 登入 [Azure 入口網站](https://portal.azure.com)、瀏覽至您的 Azure 儲存體帳戶、按一下 [Blob]  ，然後按一下 [+ 容器]  。

1. [建立 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md) (名稱為 **hotel-rooms** ) 來儲存範例旅館房間 JSON 檔案。 您可以將公用存取層級設定為任何有效值。

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="建立新的資料庫" border="false":::

1. 建立容器之後，請加以開啟，然後選取命令列的 [上傳]  。 瀏覽至包含範例檔案的資料夾。 全部選取，然後按一下 [上傳]  。

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="建立新的資料庫" border="false":::

1. 將儲存體帳戶名稱和 [存取金鑰] 頁面中的連接字串複製到 [記事本] 中。 在後續步驟中，您必須將這兩個值用於 **appsettings.json** 。

### <a name="azure-cognitive-search"></a>Azue 認知搜尋

第三個元件是 Azure 認知搜尋，您可以[在入口網站中建立](search-create-service-portal.md)該服務。 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>複製 Azure 認知搜尋的管理員 API 金鑰和 URL

若要向搜尋服務進行驗證，您需要服務 URL 和存取金鑰。

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀]  頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定]   >  [金鑰]  中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="建立新的資料庫" border="false":::

擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="2---set-up-your-environment"></a>2 - 設定您的環境

1. 啟動 Visual Studio，在 [工具] 功能表中選取 [NuGet 套件管理員]，然後選取 [管理解決方案的 NuGet 套件...]。 

1. 在 [瀏覽]索引標籤中，尋找並安裝 **Azure.Search.Documents** (11.0 版或更新版本)。 您必須逐一點選其他對話方塊來完成安裝。

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="建立新的資料庫" border="false":::

1. 搜尋 **Microsoft.Extensions.Configuration** 和 **Microsoft.Extensions.Configuration.Json** NuGet 套件，並加以安裝。

1. 開啟解決方案檔案 **/v11/AzureSearchMultipleDataSources.sln** 。

1. 在方案總管中編輯 **appsettings.json** 檔案，以新增連線資訊。  

    ```json
    {
      "SearchServiceUri": "<YourSearchServiceURL>",
      "SearchServiceAdminApiKey": "<YourSearchServiceAdminApiKey>",
      "BlobStorageAccountName": "<YourBlobStorageAccountName>",
      "BlobStorageConnectionString": "<YourBlobStorageConnectionString>",
      "CosmosDBConnectionString": "<YourCosmosDBConnectionString>",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

前兩個項目是搜尋服務的 URL 和管理金鑰。 請使用完整的端點，例如：`https://mydemo.search.windows.net`。

後續項目會指定帳戶名稱，以及 Azure Blob 儲存體和 Azure Cosmos DB 資料來源的連接字串資訊。

## <a name="3---map-key-fields"></a>3 - 對應索引鍵欄位

合併內容時，兩個資料流必須以搜尋索引中的相同文件作為目標。 

在 Azure 認知搜尋中，索引鍵欄位會唯一識別每份文件。 每個搜尋索引必須確實具有一個 `Edm.String`類型的索引鍵欄位。 在新增至索引的資料來源中，每份文件都必須有該索引鍵欄位。 (實際上它是唯一必要的欄位。)

為多個資料來源的資料編製索引時，請確定每個傳入的資料列或文件都包含通用文件索引鍵，以便將來自兩個不同來源文件的資料合併到合併索引中的新搜尋文件。 

這通常需要一些事先規劃來找出對您的索引有意義的文件索引鍵，並確定該索引鍵存在於兩個資料來源中。 在此示範中，Cosmos DB 中每家旅館的 `HotelId` 索引鍵也都會出現在 Blob 儲存體的房間 JSON Blob 中。

Azure 認知搜尋服務索引子可以使用欄位對應來重新命名，甚至可在編製索引過程中將資料欄位重新格式化，如此一來，便可將該來源資料導向至正確的索引欄位。 例如，在 Cosmos DB 中，旅館識別碼為 **`HotelId`** 。 但在旅館房間的 JSON Blob 檔案中，會將旅館識別碼命名為 **`Id`** 。 程式會藉由將 Blob 的 **`Id`** 欄位對應到索引子中的 **`HotelId`** 索引鍵欄位，來處理此狀況。

> [!NOTE]
> 在大部分情況下，自動產生的文件索引鍵 (例如，預設由某些索引子建立的索引鍵) 無法為合併的索引建立良好的文件索引鍵。 您通常想要使用有意義的唯一索引鍵值，此值已經存在於您的資料來源，或者可輕鬆地新增至您的資料來源。

## <a name="4---explore-the-code"></a>4 - 探索程式碼

當資料和組態設定都就緒之後， **/v11/AzureSearchMultipleDataSources.sln** 中的範例程式應該就已準備好進行建置與執行。

這個簡單的 C#/.NET 主控台應用程式會執行下列工作：

* 根據 C# 旅館類別的資料結構 (也會參考地址和房間類別) 建立新的索引。
* 建立新的資料來源和索引子，以將 Azure Cosmos DB 資料對應到索引欄位。 這是 Azure 認知搜尋中的兩個物件。
* 執行索引子以從 Cosmos DB 載入旅館資料。
* 建立第二個資料來源和索引子，以將 JSON Blob 資料對應到索引欄位。
* 執行第二個索引子，以從 Blob 儲存體載入房間資料。

 執行程式之前，請花一分鐘研讀此範例的程式碼及索引和索引子定義。 相關程式碼位於兩個檔案中：

  + **Hotel.cs** 包含可定義索引的結構描述
  + **Program.cs** 包含函式，可用來建立 Azure 認知搜尋服務索引、資料來源和索引子，並將已合併的結果載入至索引。

### <a name="create-an-index"></a>建立索引

此範例程式會使用 [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) 來定義並建立 Azure 認知搜尋服務索引。 它會利用 [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) \(英文\) 類別，從 C# 資料模型類別產生索引結構。

資料模型會透過旅館類別來定義，其中也包含對地址和房間類別的參考。 FieldBuilder 會向下鑽研多個類別定義，以針對索引產生複雜的資料結構。 中繼資料標記可用來定義每個欄位的屬性，例如，它是可搜尋或可排序的。

假如您想要多次執行此範例，程式將會先刪除任何具有相同名稱的現有索引，再建立新的索引。

下列來自 **Hotel.cs** 檔案的程式碼片段會顯示單一欄位，後面接著另一個資料模型類別 Room[] 的參考，而該類別會定義於 **Room.cs** 檔案中 (未顯示)。

```csharp
. . .
[SimpleField(IsFilterable = true, IsKey = true)]
public string HotelId { get; set; }

[SearchableField(IsFilterable = true, IsSortable = true)]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

在 **Program.cs** 檔案中，會使用 `FieldBuilder.Build` 方法所產生的名稱和欄位集合來定義 [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex)，然後透過如下方式加以建立：

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>建立 Azure Cosmos DB 資料來源和索引子

接下來，主要程式會包含針對旅館資料建立 Azure Cosmos DB 資料來源的邏輯。

首先，它會將 Azure Cosmos DB 資料庫名稱串連至連接字串。 然後，它會定義 [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) 物件。

```csharp
private static async Task CreateAndRunCosmosDbIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    // Append the database name to the connection string
    string cosmosConnectString =
        configuration["CosmosDBConnectionString"]
        + ";Database="
        + configuration["CosmosDBDatabaseName"];

    SearchIndexerDataSourceConnection cosmosDbDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["CosmosDBDatabaseName"],
        type: SearchIndexerDataSourceType.CosmosDb,
        connectionString: cosmosConnectString,
        container: new SearchIndexerDataContainer("hotels"));

    // The Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(cosmosDbDataSource);
```

建立資料來源之後，程式會設定名為 **hotel-rooms-cosmos-indexer** 的 Azure Cosmos DB 索引子。

程式會使用相同的名稱來更新任何現有的索引子，並以上述程式碼的內容覆寫現有的索引子。 如果您想要多次執行此範例，則也會包含重設和執行動作。

下列範例會定義索引子的排程，使其每天執行一次。 如果您未來不想讓索引子再次自動執行，即可從此呼叫中移除排程屬性。

```csharp
SearchIndexer cosmosDbIndexer = new SearchIndexer(
    name: "hotel-rooms-cosmos-indexer",
    dataSourceName: cosmosDbDataSource.Name,
    targetIndexName: indexName)
{
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

// Indexers keep metadata about how much they have already indexed.
// If we already ran the indexer, it "remembers" and does not run again.
// To avoid this, reset the indexer if it exists.
try
{
    await indexerClient.GetIndexerAsync(cosmosDbIndexer.Name);
    // Reset the indexer if it exists.
    await indexerClient.ResetIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404)
{
    // If the indexer does not exist, 404 will be thrown.
}

await indexerClient.CreateOrUpdateIndexerAsync(cosmosDbIndexer);

Console.WriteLine("Running Cosmos DB indexer...\n");

try
{
    // Run the indexer.
    await indexerClient.RunIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 429)
{
    Console.WriteLine("Failed to run indexer: {0}", ex.Message);
}
```

此範例包含簡單的 try-catch 區塊，以報告可能在執行期間發生的任何錯誤。

當 Azure Cosmos DB 索引子執行之後，搜尋索引將包含一組完整的範例旅館文件。 不過，每家旅館的房間欄位將會是空陣列，因為 Azure Cosmos DB 資料來源省略了房間詳細資料。 接下來，程式將從 Blob 儲存體中提取以載入並合併房間資料。

### <a name="create-blob-storage-data-source-and-indexer"></a>建立 Blob 儲存體資料來源和索引子

為了取得房間詳細資料，程式會先設定 Blob 儲存體資料來源，以參考一組個別的 JSON Blob 檔案。

```csharp
private static async Task CreateAndRunBlobIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    SearchIndexerDataSourceConnection blobDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["BlobStorageAccountName"],
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["BlobStorageConnectionString"],
        container: new SearchIndexerDataContainer("hotel-rooms"));

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(blobDataSource);
```

建立資料來源之後，程式會設定名為 **hotel-rooms-blob-indexer** 的 Blob 索引子，如下所示。

JSON Blob 包含名為 **`Id`** (而非 **`HotelId`** ) 的索引鍵欄位。 程式碼會使用 `FieldMapping` 類別，來告知索引子要將 **`Id`** 欄位值導向至索引中的 **`HotelId`** 文件索引鍵。

Blob 儲存體索引子可使用 [IndexingParameters](/dotnet/api/azure.search.documents.indexes.models.indexingparameters) 來指定剖析模式。 您應根據 Blob 是代表單一文件還是相同 Blob 中的多份文件，設定不同的剖析模式。 在此範例中，每個 Blob 均代表單一 JSON 文件，因此程式碼會使用 `json` 剖析模式。 如需用以剖析 JSON Blob 參數的索引子詳細資訊，請參閱[為 JSON Blob 編製索引](search-howto-index-json-blobs.md)。

此範例會定義索引子的排程，如此一來，它將會每天執行一次。 如果您未來不想讓索引子再次自動執行，即可從此呼叫中移除排程屬性。

```csharp
// Map the Id field in the Room documents to the HotelId key field in the index
List<FieldMapping> map = new List<FieldMapping> {
    new FieldMapping("Id")
    {
        TargetFieldName =  "HotelId"
    }
};

IndexingParameters parameters = new IndexingParameters();
parameters.Configuration.Add("parsingMode", "json");

SearchIndexer blobIndexer = new SearchIndexer(
    name: "hotel-rooms-blob-indexer",
    dataSourceName: blobDataSource.Name,
    targetIndexName: indexName)
{
    Parameters = parameters,
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

blobIndexer.FieldMappings.Add(new FieldMapping("Id") { TargetFieldName = "HotelId" });

// Reset the indexer if it already exists
try
{
    await indexerClient.GetIndexerAsync(blobIndexer.Name);
    await indexerClient.ResetIndexerAsync(blobIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404) { }

await indexerClient.CreateOrUpdateIndexerAsync(blobIndexer);

try
{
    // Run the indexer.
    await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
    Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

因為已經使用來自 Azure Cosmos DB 資料庫的旅館資料填入索引，所以 Blob 索引子會更新索引中的現有文件，並新增房間詳細資料。

> [!NOTE]
> 如果您在這兩個資料來源中具有相同的非索引鍵欄位，而且那些欄位內的資料不相符，則索引將包含索引子最近執行過的值。 在此範例中，這兩個資料來源均包含 **HotelName** 欄位。 如果此欄位中的資料基於某些原因而不同，則針對具有相同索引鍵值的文件，最近已編製索引之資料來源的 **HotelName** 資料將會是儲存於索引中的值。

## <a name="5---search"></a>5 - 搜尋

您可以在執行程式之後，使用入口網站中的 [**搜尋總管**](search-explorer.md)來探索已填入的搜尋索引。

在 Azure 入口網站中，開啟搜尋服務 [概觀]  頁面，然後在 [索引]  清單中尋找 **hotel-rooms-sample** 索引。

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="建立新的資料庫" border="false":::

按一下清單中的 hotel-rooms-sample 索引。 您將會看到索引的搜尋總管介面。 輸入 "Luxury" 等字詞的查詢。 您應該會在結果中至少看見一份文件，而這份文件應該會在其房間陣列中顯示房間物件的清單。

## <a name="reset-and-rerun"></a>重設並重新執行

在開發的早期實驗階段中若要設計反覆項目，最實用的方法是從 Azure 認知搜尋中刪除物件，並讓您的程式碼加以重建。 資源名稱是唯一的。 刪除物件可讓您使用相同的名稱加以重新建立。

範例程式碼會檢查是否有現有的物件，並將其刪除或更新，以便您重新執行程式。

您也可以使用入口網站來刪除索引、索引子和資料來源。

## <a name="clean-up-resources"></a>清除資源

如果您使用自己的訂用帳戶，當專案結束時，建議您移除不再需要的資源。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

## <a name="next-steps"></a>後續步驟

現在您已熟悉從多個來源內嵌資料的概念，接下來我們將從 Cosmos DB 開始深入探討索引子設定。

> [!div class="nextstepaction"]
> [設定 Azure Cosmos DB 索引子](search-howto-index-cosmosdb.md)
