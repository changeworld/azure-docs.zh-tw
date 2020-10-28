---
title: '在 .NET 中使用 Azure.Search.Documents (v11) '
titleSuffix: Azure Cognitive Search
description: '瞭解如何使用 c # 和 Azure.Search.Documents (v11) 用戶端程式庫，在 .NET 應用程式中建立和管理搜尋物件。 程式碼片段會示範如何連接到服務、建立索引和查詢。'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 47cface4efbf38f2cec630745d498db674c936d3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791948"
---
# <a name="how-to-use-azuresearchdocuments-in-a-c-net-application"></a>如何在 c # .NET 應用程式中使用 Azure.Search.Documents

本文說明如何使用 c # 和 [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) (第11版) 用戶端程式庫來建立和管理搜尋物件。

## <a name="about-version-11"></a>關於第11版

Azure SDK for .NET 會從 Azure SDK 小組新增新的 [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) 用戶端程式庫，其功能等同于 [Microsoft。搜尋](/dotnet/api/overview/azure/search/client10) 用戶端程式庫，但在適用的情況下使用常見的方法和慣例。 部分範例包括 [`AzureKeyCredential`](/dotnet/api/azure.azurekeycredential) 金鑰驗證，以及 [System.Text.Js。](/dotnet/api/system.text.json.serialization) JSON 序列化的序列化。

如同先前的版本，您可以使用此程式庫來：

+ 建立及管理搜尋索引、資料來源、索引子、技能集和同義字地圖
+ 載入和管理索引中的搜尋檔
+ 執行查詢，而不需要處理 HTTP 和 JSON 的詳細資料

程式庫是以單一 [Azure.Search.Doc>ument NuGet 套件](https://www.nuget.org/packages/Azure.Search.Documents/)的形式散發，其中包含用來以程式設計方式存取搜尋服務的所有 api。

用戶端程式庫會定義類別，例如 `SearchIndex`、`SearchField` 及 `SearchDocument`，以及定義作業，例如 `SearchIndexClient` 和 `SearchClient` 類別上的 `SearchIndexClient.CreateIndex` 和 `SearchClient.Search`。 這些類別可編成以下命名空間：

+ [`Azure.Search.Documents`](/dotnet/api/azure.search.documents)
+ [`Azure.Search.Documents.Indexes`](/dotnet/api/azure.search.documents.indexes)
+ [`Azure.Search.Documents.Indexes.Models`](/dotnet/api/azure.search.documents.indexes.models)
+ [`Azure.Search.Documents.Models`](/dotnet/api/azure.search.documents.models)

Azure.Search.Documents (第11版) 目標版本[ `2020-06-30` 的 Azure 認知搜尋 REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Azure.Search/preview/2020-06-30)。 

用戶端程式庫不會提供 [服務管理作業](/rest/api/searchmanagement/)，例如建立和調整搜尋服務和管理 API 金鑰。 如果您需要從 .NET 應用程式管理搜尋資源，請使用 Azure SDK for .NET 中的 [ [搜尋](/dotnet/api/overview/azure/search/management) ] 程式庫。

## <a name="upgrade-to-v11"></a>升級至 v11

如果您已經使用舊版 .NET SDK，而且想要升級至目前的正式推出版本，請參閱 [升級至 Azure 認知搜尋 .NET sdk 第11版](search-dotnet-sdk-migration-version-11.md)

## <a name="sdk-requirements"></a>SDK 需求

+ Visual Studio 2019 或更新版本。

+ 您自己的 Azure 認知搜尋服務。 為了使用 SDK，您需要為服務命名，還需要一或多個 API 金鑰。 [在入口網站中建立服務](search-create-service-portal.md) （如果沒有的話）。

+ 使用 **工具** NuGet 封裝管理員管理 Visual Studio 中 [Azure.Search.Documents package](https://www.nuget.org/packages/Azure.Search.Documents)  >  **NuGet Package Manager**  >  **解決方案的 nuget 套件** ，以下載Azure.Search.Documents 套件。 搜尋套件名稱 `Azure.Search.Documents` 。

適用于 .NET 的 Azure SDK 符合 [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support)，這表示 .NET Framework 4.6.1 和 .net Core 2.0 作為最低需求。

## <a name="example-application"></a>範例應用程式

本文是以 GitHub 上的 >dotnethowto 程式碼範例為例，它會以 GitHub 上的[DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo/v11)程式碼範例為例，說明如何建立、載入和查詢搜尋索引的基本 Azure 認知搜尋概念。

針對本文的其餘部分，假設有一個名為「飯店」的新索引，並填入幾份檔，其中有數個符合結果的查詢。

以下是主要程式，顯示整體流程：

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchIndexClient indexClient = CreateSearchIndexClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, indexClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, indexClient);

    SearchClient searchClient = indexClient.GetSearchClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(searchClient);

    SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

    Console.WriteLine("{0}", "Run queries...\n");
    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

接下來是輸出的部分螢幕擷取畫面，假設您使用有效的服務名稱和 API 金鑰執行此應用程式：

:::image type="content" source="media/search-howto-dotnet-sdk/console-output.png" alt-text="Console. WriteLine 範例程式的輸出":::

### <a name="client-types"></a>用戶端類型

用戶端程式庫會針對各種作業使用三種用戶端類型： [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) 建立、更新或刪除索引、 [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) 載入或查詢索引，以及使用索引 [`SearchIndexerClient`](/dotnet/api/azure.search.documents.indexes.searchindexerclient) 器和技能集。 本文著重于前兩個專案。 

所有用戶端至少都需要服務名稱或端點，以及 API 金鑰。 通常會在設定檔中提供這項資訊，類似于您在 `appsettings.json` [>dotnethowto 範例應用程式](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)的檔案中找到的內容。 若要從設定檔讀取，請新增 `using Microsoft.Extensions.Configuration;` 至您的程式。

下列語句會建立索引用戶端，用來建立、更新或刪除索引。 它會採用搜尋端點和管理 API 金鑰。

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceEndPoint), new AzureKeyCredential(adminApiKey));
    return indexClient;
}
```

下一個語句會建立用來載入檔或執行查詢的搜尋用戶端。 `SearchClient` 需要索引。 您將需要系統管理員 API 金鑰才能載入檔，但您可以使用查詢 API 金鑰來執行查詢。 

```csharp
string indexName = configuration["SearchIndexName"];

private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

> [!NOTE]
> 如果您為匯入作業提供了不正確索引鍵 (例如，需要系統管理金鑰的查詢索引鍵) ，則 `SearchClient` 會在 `CloudException` 您第一次對其呼叫作業方法時擲回，並顯示錯誤訊息「禁止」。 如果發生這種情況，請再次檢查 API 金鑰。
>

### <a name="deleting-the-index"></a>正在刪除索引

在開發的初期階段，您可能會想要包含 [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) 語句來刪除進行中的工作，讓您可以使用更新的定義來重新建立。 Azure 認知搜尋的範例程式碼通常包含刪除步驟，讓您可以重新執行範例。

下列程式程式碼呼叫 `DeleteIndexIfExists` ：

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, indexClient);
```

此方法會使用指定的 `SearchIndexClient` 來檢查索引是否存在，如果有的話，會將它刪除：

```csharp
private static void DeleteIndexIfExists(string indexName, SearchIndexClient indexClient)
{
    try
    {
        if (indexClient.GetIndex(indexName) != null)
        {
            indexClient.DeleteIndex(indexName);
        }
    }
    catch (RequestFailedException e) when (e.Status == 404)
    {
        // Throw an exception if the index name isn't found
        Console.WriteLine("The index doesn't exist. No deletion occurred.");
```

> [!NOTE]
> 為了簡單起見，本文中的範例程式碼會使用同步方法，但您應該在自己的應用程式中使用非同步方法，讓它們保持可擴充和回應性。 例如，在上述方法中，您可以使用 [`DeleteIndexAsync`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindexasync) 而不是 [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) 。
>

## <a name="create-an-index"></a>建立索引

您可以使用 [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) 來建立索引。 

下列方法 [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) 會使用定義新索引架構的物件清單，建立新的物件。 每個欄位均有一個名稱、資料類型和一些屬性，以用於定義欄位的搜尋行為。 

您可以使用從模型類別定義欄位 [`FieldBuilder`](/dotnet/api/azure.search.documents.indexes.fieldbuilder) 。 `FieldBuilder` 類別會檢查指定 `Hotel` 模型類別的公用屬性 (property) 和屬性 (attribute)，進而使用反映來建立索引的 `SearchField` 物件清單。 我們稍後會仔細查看 `Hotel` 類別。

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    indexClient.CreateOrUpdateIndex(definition);
}
```

除了欄位之外，您也可以將評分設定檔、建議工具或 CORS 選項新增至索引 (這些參數會在範例中省略，以求簡潔) 。 您可以在 [ [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) 屬性] 清單以及 [REST API 參考](/rest/api/searchservice/)中，找到有關 SearchIndex 物件及其組成部分的詳細資訊。

> [!NOTE]
> 如有需要，您永遠可以直接建立 `Field` 物件清單，而不是使用`FieldBuilder`。 例如，您可能不想使用模型類別，或您可能需要使用不想藉由新增屬性來修改的現有模型類別。
>

### <a name="call-createindex-in-main"></a>在主要 ( # A1 中呼叫 CreateIndex

`Main` 藉由呼叫上述方法，建立新的「飯店」索引：

```csharp
Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, indexClient);
```

## <a name="use-a-model-class-for-data-representation"></a>使用模型類別來表示資料

>dotnethowto 範例會使用 [旅館](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Hotel.cs)、 [位址](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Address.cs)和 [房間](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Room.cs) 資料結構的模型類別。 `Hotel` 參考 `Address` 、單一層級複雜型別 (多部分欄位) ，以及 `Room` (多部分欄位的集合) 。

您可以使用這些類型來建立和載入索引，以及從查詢中建立回應的結構：

```csharp
// Use-case: <Hotel> in a field definition
FieldBuilder fieldBuilder = new FieldBuilder();
var searchFields = fieldBuilder.Build(typeof(Hotel));

// Use-case: <Hotel> in a response
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="field-definitions"></a>欄位定義

您在 .NET 中的資料模型及其對應的索引架構，都應該支援您想要提供給使用者的搜尋體驗。 .NET 中的每個最上層物件（例如搜尋索引中的搜尋檔）會對應至您在使用者介面中顯示的搜尋結果。 例如，在旅館搜尋應用程式中，您的終端使用者可能想要依旅館名稱、旅館的功能，或特定房間的特性來搜尋。 

在每個類別中，欄位會以決定其使用方式的資料類型和屬性來定義。 每個類別中的每個公用屬性名稱會對應至索引定義中具有相同名稱的欄位。 

請看看下列程式碼片段，從飯店類別提取數個欄位定義。 請注意，位址和房間是具有自己的類別定義的 c # 型別 (請參閱範例程式碼，如果您想要) 加以查看。 兩者都是複雜類型。 如需詳細資訊，請參閱 [如何建立複雜類型的模型](search-howto-complex-data-types.md)。

```csharp
public partial class Hotel
{
    [SimpleField(IsKey = true, IsFilterable = true)]
    public string HotelId { get; set; }

    [SearchableField(IsSortable = true)]
    public string HotelName { get; set; }

    [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
    public string Description { get; set; }

    [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
    public string Category { get; set; }

    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [SearchableField]
    public Address Address { get; set; }

    public Room[] Rooms { get; set; }
```

#### <a name="choosing-a-field-class"></a>選擇欄位類別

在定義欄位時，您可以使用基類 [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) ，也可以使用做為「範本」的衍生協助程式模型搭配預先設定的屬性。

索引中只有一個欄位必須做為檔索引鍵 (`IsKey = true`) 。 它必須是字串，且必須可唯一識別每份檔。 它也必須有 `IsHidden = true` ，這表示它不會顯示在搜尋結果中。

| 欄位類型 | 描述和使用方式 |
|------------|-----------------------|
| [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) | 基類，其中大部分的屬性都設定為 null、excepting `Name` 是必要的，而且 `AnalyzerName` 預設為標準 Lucene。 |
| [`SimpleField`](/dotnet/api/azure.search.documents.indexes.models.simplefield) | Helper 模型。 可以是任何資料類型，永遠無法搜尋 (在) 的全文檢索搜尋查詢中會忽略該資料，而且 (不會隱藏) ，則會將其忽略。 其他屬性預設為關閉，但可以啟用。 您可以針對僅用於篩選、Facet 或評分設定檔的文件識別碼或欄位使用 `SimpleField`。 若是如此，請務必套用案例所需的任何屬性，例如文件識別碼的 `IsKey = true`。 如需詳細資訊，請參閱原始程式碼中的 [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs)。 |
| [`SearchableField`](/dotnet/api/azure.search.documents.indexes.models.searchablefield) | Helper 模型。 必須是字串，而且一律可搜尋及可供使用。 其他屬性預設為關閉，但可以啟用。 因為此欄位類型是可搜尋，所以其支援同義字和分析器屬性的完整補語。 如需詳細資訊，請參閱原始程式碼中的 [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs)。 |

無論您使用的是基本 `SearchField` API 或其中一個 協助程式模型，都必須明確啟用篩選、Facet 和排序屬性。 例如，[IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable)、[IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) 和 [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) 必須明確屬性化，如上述範例所示。

#### <a name="adding-field-attributes"></a>加入欄位屬性

請注意，每個欄位如何使用 `IsFilterable` 、、和等屬性裝飾 `IsSortable` `IsKey` `AnalyzerName` 。 這些屬性會直接對應至 [Azure 認知搜尋索引中的對應欄位屬性](/rest/api/searchservice/create-index)。 `FieldBuilder`類別會使用這些屬性來建立索引的欄位定義。

#### <a name="field-type-mapping"></a>欄位類型對應

在索引定義中，屬性的 .NET 類型會對應至其對等的欄位類型。 例如，`Category` 字串屬性會對應至 `category` 欄位 (此欄位屬於 `Edm.String` 類型)。 、、和等之間有類似的類型對應 `bool?` `Edm.Boolean` `DateTimeOffset?` `Edm.DateTimeOffset` 。 

您是否已注意到 `SmokingAllowed` 屬性？

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

`JsonIgnore`這個屬性（attribute）上的屬性（attribute）會告知， `FieldBuilder` 不要將它序列化為索引（欄位）。  這是建立用戶端計算屬性的絕佳方法，可供您在應用程式中用來做為協助程式。  在此情況下， `SmokingAllowed` 屬性會反映集合中的任何是否 `Room` `Rooms` 允許吸煙。 如果全部都是 false，則表示整個旅館不允許吸煙。

## <a name="load-an-index"></a>載入索引

中的下一個步驟會 `Main` 填入新建立的「飯店」索引。 此索引擴展是以下列方法完成： (某些程式碼取代為 "..."供說明之用。 如需完整的資料填入程式碼，請參閱完整的範例解決方案。 ) 

```csharp
private static void UploadDocuments(SearchClient searchClient)
{
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                HotelName = "Secret Point Motel",
                ...
                Address = new Address()
                {
                    StreetAddress = "677 5th Ave",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Budget Room, 1 Queen Bed (Cityside)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (City View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                HotelName = "Twin Dome Motel",
                ...
                {
                    StreetAddress = "140 University Town Center Dr",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Suite, 2 Double Beds (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Standard Room, 1 Queen Bed (City View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Waterfront View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "3",
                HotelName = "Triple Landscape Hotel",
                ...
                Address = new Address()
                {
                    StreetAddress = "3393 Peachtree Rd",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Standard Room, 2 Queen Beds (Amenities)",
                        ...
                    },
                    new Room ()
                    {
                        Description = "Standard Room, 2 Double Beds (Waterfront View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (Cityside)",
                        ...
                    }
                }
            }
        };

    try
    {
        IndexDocumentsResult result = searchClient.IndexDocuments(batch);
    }
    catch (Exception)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine("Failed to index some of the documents: {0}");
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
```

此方法分四個部分。 第一個會建立3個物件的陣列， `Hotel` 每個物件都有3個 `Room` 物件，這些物件將作為要上傳至索引的輸入資料。 為簡單起見，此資料採硬式編碼。 在實際的應用程式中，資料可能來自于外部資料源，例如 SQL 資料庫。

第二個部分會建立 [`IndexDocumentsBatch`](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) 包含檔的。 您可以在建立批次時，指定要套用至批次的作業（在此案例中，藉由呼叫） [`IndexDocumentsAction.Upload`](/dotnet/api/azure.search.documents.models.indexdocumentsaction.upload) 。 然後，此批次會由方法上傳至 Azure 認知搜尋索引 [`IndexDocuments`](/dotnet/api/azure.search.documents.searchclient.indexdocuments) 。

> [!NOTE]
> 在此範例中，我們只上傳文件。 如果您想要將變更合併至現有的文件，或是刪除文件，您可以改為呼叫 `IndexDocumentsAction.Merge`、`IndexDocumentsAction.MergeOrUpload` 或 `IndexDocumentsAction.Delete` 來建立批次。 您也可以在單一批次中混合不同的作業，方法是呼叫 `IndexBatch.New` ，這會取得物件的集合 `IndexDocumentsAction` ，每個物件都會告訴 Azure 認知搜尋對檔執行特定作業。 您可以建立每個擁有自己作業的 `IndexDocumentsAction`，方法是呼叫對應的方法，例如 `IndexDocumentsAction.Merge`、`IndexAction.Upload` 等等。
> 

此方法的第三部分是擷取區塊，該區塊會為編制索引處理重要錯誤情況。 如果您的搜尋服務無法為批次中的某些檔編制索引， `IndexBatchException` 則會擲回 `IndexDocuments` 。 如果您在服務負載過重時編制檔索引，就會發生此例外狀況。 **我們強烈建議您在程式碼中明確處理此情況。**  您可以延遲，然後重新嘗試將失敗的文件編制索引，或像範例一樣加以記錄並繼續，或是根據您應用程式的資料一致性需求執行其他操作。

最後，`UploadDocuments` 方法會延遲兩秒。 索引會在搜尋服務中以非同步方式執行，因此範例應用程式必須等候一小段時間，以確保檔可以搜尋。 通常只有在示範、測試和範例應用程式中，才需要這類延遲。

### <a name="call-uploaddocuments-in-main"></a>在主要 ( # A1 中呼叫 UploadDocuments

下列程式碼片段會使用 indexClient 的方法來設定的實例 [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) [`GetSearchClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient.getsearchclient) 。 IndexClient 會在其要求上使用管理 API 金鑰，這是載入或重新整理檔所需的金鑰。

替代方法是 `SearchClient` 直接呼叫，在上傳入管理 API 金鑰 `AzureKeyCredential` 。

```csharp
SearchClient searchClient = indexClient.GetSearchClient(indexName);

Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(searchClient);
```

## <a name="run-queries"></a>執行查詢

首先，請設定 `SearchClient` ，從 **appsettings.js** 開始讀取搜尋端點和查詢 API 金鑰：

```csharp
private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

其次，定義傳送查詢要求的方法。 

每次方法執行查詢時，都會建立新的 [`SearchOptions`](/dotnet/api/azure.search.documents.searchoptions) 物件。 此物件可用來指定查詢的其他選項，例如排序、篩選、分頁和 facet。 在這個方法中，我們要 `Filter` `Select` 為不同的查詢設定、和 `OrderBy` 屬性。 如需搜尋查詢運算式語法的詳細資訊，請 [簡單查詢語法](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)。

下一步是實際執行搜尋查詢， 您可以使用方法來執行搜尋 `SearchClient.Search` 。 針對每個查詢，傳遞搜尋文字做為字串 (或者， `"*"` 如果沒有搜尋文字) ，再加上先前建立的搜尋選項。 我們也指定了 `Hotel` 做為 `SearchClient.Search` 的類型參數，藉此告訴 SDK 將搜尋結果中的文件還原序列化為 `Hotel` 類型的物件。

```csharp
private static void RunQueries(SearchClient searchClient)
{
    SearchOptions options;
    SearchResults<Hotel> results;

    Console.WriteLine("Query 1: Search for 'motel'. Return only the HotelName in results:\n");

    options = new SearchOptions();
    options.Select.Add("HotelName");

    results = searchClient.Search<Hotel>("motel", options);

    WriteDocuments(results);

    Console.Write("Query 2: Apply a filter to find hotels with rooms cheaper than $100 per night, ");
    Console.WriteLine("returning the HotelId and Description:\n");

    options = new SearchOptions()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)"
    };
    options.Select.Add("HotelId");
    options.Select.Add("Description");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.Write("Query 3: Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    options =
        new SearchOptions()
        {
            Size = 2
        };
    options.OrderBy.Add("LastRenovationDate desc");
    options.Select.Add("HotelName");
    options.Select.Add("LastRenovationDate");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.WriteLine("Query 4: Search the HotelName field for the term 'hotel':\n");

    options = new SearchOptions();
    options.SearchFields.Add("HotelName");

    //Adding details to select, because "Location" is not supported yet when deserialize search result to "Hotel"
    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Category");
    options.Select.Add("Tags");
    options.Select.Add("ParkingIncluded");
    options.Select.Add("LastRenovationDate");
    options.Select.Add("Rating");
    options.Select.Add("Address");
    options.Select.Add("Rooms");

    results = searchClient.Search<Hotel>("hotel", options);

    WriteDocuments(results);
}
```

第三，定義一個寫入回應的方法，將每個檔列印到主控台：

```csharp
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="call-runqueries-in-main"></a>在主要 ( # A1 中呼叫 RunQueries

```csharp
SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

Console.WriteLine("{0}", "Running queries...\n");
RunQueries(indexClientForQueries);
```

### <a name="explore-query-constructs"></a>探索查詢結構

讓我們依序仔細查看每個查詢。 以下是執行第一個查詢的程式碼︰

```csharp
options = new SearchOptions();
options.Select.Add("HotelName");

results = searchClient.Search<Hotel>("motel", options);

WriteDocuments(results);
```

在此情況下，我們會在任何可搜尋的欄位中搜尋 "motel" 一字的整個索引，而我們只會根據選項的指定來取出旅館名稱 `Select` 。 以下是結果：

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

在第二個查詢中，使用篩選準則來選取每晚速率小於 $100 的房間。 只傳回結果中的旅館識別碼和描述：

```csharp
options = new SearchOptions()
{
    Filter = "Rooms/any(r: r/BaseRate lt 100)"
};
options.Select.Add("HotelId");
options.Select.Add("Description");

results = searchClient.Search<Hotel>("*", options);
```

上述查詢會使用 OData `$filter` 運算式 `Rooms/any(r: r/BaseRate lt 100)` 來篩選索引中的檔。 這會使用 [any 運算子](./search-query-odata-collection-operators.md) 將 ' BaseRate lt 100 ' 套用至房間集合中的每個專案。 如需詳細資訊，請參閱 [OData 篩選語法](./query-odata-filter-orderby-syntax.md)。

在第三個查詢中，尋找最近整修的前兩個旅館，並顯示旅館名稱和上次整修日期。 程式碼如下： 

```csharp
options =
    new SearchOptions()
    {
        Size = 2
    };
options.OrderBy.Add("LastRenovationDate desc");
options.Select.Add("HotelName");
options.Select.Add("LastRenovationDate");

results = searchClient.Search<Hotel>("*", options);

WriteDocuments(results);
```

在最後一個查詢中，尋找符合「飯店」一字的所有旅館名稱：

```csharp
options.Select.Add("HotelId");
options.Select.Add("HotelName");
options.Select.Add("Description");
options.Select.Add("Category");
options.Select.Add("Tags");
options.Select.Add("ParkingIncluded");
options.Select.Add("LastRenovationDate");
options.Select.Add("Rating");
options.Select.Add("Address");
options.Select.Add("Rooms");

results = searchClient.Search<Hotel>("hotel", options);

WriteDocuments(results);
```

本節將介紹 .NET SDK 的簡介，但不要在這裡停止。 下一節建議其他資源，以深入瞭解如何使用 Azure 認知搜尋進行程式設計。

## <a name="next-steps"></a>後續步驟

+ 流覽[Azure.Search.Documents](/dotnet/api/azure.search.documents)和[REST API](/rest/api/searchservice/)的 API 參考檔

+ 根據 azure 中的 Azure.Search.Documents 流覽其他程式碼範例 [-搜尋-dotnet-範例](https://github.com/Azure-Samples/azure-search-dotnet-samples) 和 [搜尋-入門-dotnet](https://github.com/Azure-Samples/search-dotnet-getting-started)

+ 複習 [命名慣例](/rest/api/searchservice/Naming-rules) ，以瞭解命名各種物件的規則

+ 檢查 [支援的資料類型](/rest/api/searchservice/Supported-data-types)