---
title: 快速入門：在 .NET 中建立搜尋索引
titleSuffix: Azure Cognitive Search
description: 在此 C# 快速入門中，了解如何使用 Azure.Search.Documents 用戶端程式庫來建立索引、載入資料以及執行查詢。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f0d912d5b14932c43d109f8f955d5f16381cf773
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180093"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>快速入門：使用 Azure.Search.Documents 用戶端程式庫建立搜尋索引

使用新的 [Azure.Search.Documents (第 11 版) 用戶端程式庫](/dotnet/api/overview/azure/search.documents-readme)在 C# 中建立 .NET Core 主控台應用程式，以建立、載入及查詢搜尋索引。

您可以[下載原始程式碼](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11)以使用已完成的專案來開始，或遵循本文中的步驟來建立您自己的程式碼。

> [!NOTE]
> 在尋找較舊的版本嗎？ 請改為參閱[使用 Microsoft.Azure.Search v10 建立搜尋索引](search-get-started-dotnet-v10.md)。

## <a name="prerequisites"></a>先決條件

開始之前，請先準備下列工具和服務：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/)。

+ Azure 認知搜尋服務。 [建立服務](search-create-service-portal.md)或[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) 的任何版本。 範例程式碼已在 Visual Studio 2019 的免費社群版本上測試過。

在設定您的專案時，會下載 [Azure.Search.Documents NuGet 套件](https://www.nuget.org/packages/Azure.Search.Documents/)。

適用於 .NET 的 Azure SDK 符合 [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support)，這表示最低需求為 .NET Framework 4.6.1 和 .NET Core 2.0。

## <a name="set-up-your-project"></a>設定專案

組合服務連線資訊，然後啟動 Visual Studio 來建立可在 .NET Core 上執行的新主控台應用程式專案。

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>複製金鑰和端點

在每個對服務發出呼叫的要求上，都需要 URL 端點和存取金鑰。 第一個步驟是尋找要新增至專案的 API 金鑰和 URL。 您在稍後的步驟中建立用戶端時，將會指定這兩個值。

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀]  頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

2. 在 [設定] > [金鑰] 中，取得管理員金鑰以獲得服務的完整權限 (必須有完整權限才能建立或刪除物件)。 共有兩個可交換的主要和次要金鑰。 您可以使用任一個。

   ![取得 HTTP 端點和存取金鑰](media/search-get-started-rest/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

### <a name="install-the-nuget-package"></a>安裝 NuGet 封裝

專案建立好之後，新增用戶端程式庫。 [Azure.Search.Documents 套件](https://www.nuget.org/packages/Azure.Search.Documents/)包含一個用戶端程式庫，其可提供所有用來與 .NET 中的搜尋服務搭配運作的 API。

1. 啟動 Visual Studio，並建立 .NET Core 主控台應用程式。

1. 在 [工具] > [NuGet 套件管理員] 中，選取 [管理解決方案的 NuGet 套件] 中。 

1. 按一下 **[瀏覽]** 。

1. 搜尋 `Azure.Search.Documents` 並選取 11.0 版或更新版本。

1. 按一下右邊的 [安裝] 以將組件新增到您的r專案與解決方案。

### <a name="create-a-search-client"></a>建立搜尋用戶端

1. 在 **Program.cs** 中，將命名空間變更為 `AzureSearch.SDK.Quickstart.v11`，然後新增下列 `using` 指示詞。

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. 建立兩個用戶端：[SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) 會建立索引，而 [SearchClient](/dotnet/api/azure.search.documents.searchclient) 會載入並查詢現有索引。 兩者都需要服務端點和管理員 API 金鑰，以使用 create/delete 權限進行驗證。

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

        // Create a SearchIndexClient to send create/delete index commands
        Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
        AzureKeyCredential credential = new AzureKeyCredential(apiKey);
        SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);

        // Create a SearchClient to load and query documents
        SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - 建立索引

本快速入門會建置供您隨著飯店資料一起載入並對其執行查詢的 Hotels 索引。 在此步驟中，請定義索引中的欄位。 每個欄位定義都包含名稱、資料類型和屬性，以決定欄位的使用方式。

為求簡化並方便閱讀，此範例會使用 Azure.Search.Documents 程式庫的同步方法。 不過，在生產案例中，則請使用非同步方法，以讓應用程式保持可調整和能回應的狀態。 例如，使用 [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) 而非 [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex)。

1. 將空的類別定義新增至專案中：**Hotel.cs**

1. 將下列程式碼複製到 **Hotel.cs** ，以定義飯店文件的結構。 欄位上的屬性決定它如何在應用程式中使用。 例如，必須將 `IsFilterable` 屬性指派給支援篩選條件運算式的每個欄位。

    ```csharp
    using System;
    using System.Text.Json.Serialization;
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;

    namespace AzureSearch.Quickstart
    {
        public partial class Hotel
        {
            [SimpleField(IsKey = true, IsFilterable = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            [SearchableField]
            public Address Address { get; set; }
        }
    }
    ```

   在 Azure.Search.Documents 用戶端程式庫中，您可以使用 [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) 和 [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) 來簡化欄位定義。 兩者都是 [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) 的衍生項目，而且可能會簡化您的程式碼：

   + `SimpleField` 可以是任何資料類型、一律不可搜尋 (全文檢索搜尋查詢會將其忽略)，以及可擷取 (不會隱藏)。 其他屬性預設為關閉，但可以啟用。 您可以針對僅用於篩選、Facet 或評分設定檔的文件識別碼或欄位使用 `SimpleField`。 若是如此，請務必套用案例所需的任何屬性，例如文件識別碼的 `IsKey = true`。 如需詳細資訊，請參閱原始程式碼中的 [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs)。

   + `SearchableField` 必須是字串，而且一律可搜尋並可擷取。 其他屬性預設為關閉，但可以啟用。 因為此欄位類型是可搜尋，所以其支援同義字和分析器屬性的完整補語。 如需詳細資訊，請參閱原始程式碼中的 [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs)。

   無論您使用的是基本 `SearchField` API 或其中一個 協助程式模型，都必須明確啟用篩選、Facet 和排序屬性。 例如，[IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable)、[IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) 和 [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) 必須明確屬性化，如上述範例所示。 

1. 將第二個空的類別定義新增至專案：**Address.cs**。  將下列程式碼複製到類別中。

   ```csharp
   using Azure.Search.Documents.Indexes;

    namespace AzureSearch.Quickstart
    {
        public partial class Address
        {
            [SearchableField(IsFilterable = true)]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
   ```

1. 另外建立兩個類別：**Hotel.Methods.cs** 和 **Address.Methods.cs**，以執行 ToString() 覆寫。 這些類別是用來在主控台輸出中呈現搜尋結果。  本文並未提供這些類別的內容，但您可以從 [GitHub中的檔案](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11/AzureSearchQuickstart-v11)複製程式碼。

1. 在 **Program.cs** 中，建立 [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) 物件，然後呼叫 [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) 方法來表示搜尋服務中的索引。 索引也包含 [SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester)，可在指定的欄位上啟用自動完成功能。

   ```csharp
    // Create hotels-quickstart index
    private static void CreateIndex(string indexName, SearchIndexClient adminClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));

        var definition = new SearchIndex(indexName, searchFields);

        var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
        definition.Suggesters.Add(suggester);

        adminClient.CreateOrUpdateIndex(definition);
    }
   ```

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 載入文件

Azure 認知搜尋會搜尋服務中儲存的內容。 在此步驟中，您會載入與剛建立的飯店索引相符的 JSON 文件。

在 Azure 認知搜尋中，搜尋文件是同時屬於索引輸入與查詢輸出的資料結構。 如同從外部資料來源所取得的一樣，文件輸入可能是資料庫中的資料列，Blob 儲存體中的 Blob 或磁碟上的 JSON 文件。 在此範例中，我們採用捷徑，並針對程式碼本身中的四家旅館內嵌 JSON 文件。 

上傳文件時，您必須使用 [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) 物件。 `IndexDocumentsBatch` 物件包含[動作](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions)集合，每個物件都包含文件與屬性，後者會告知 Azure 認知搜尋所應執行的動作 ([上傳、合併、刪除及 mergeOrUpload](search-what-is-data-import.md#indexing-actions))。

1. 在 **Program.cs** 中，建立文件與索引動作的索引，然後將該陣列傳遞到 `IndexDocumentsBatch`。 下面的文件符合 hotels-quickstart 索引，如旅館類別所定義。

    ```csharp
    // Upload documents in a single Upload request.
    private static void UploadDocuments(SearchClient searchClient)
    {
        IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.60,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                })
            );

        try
        {
            IndexDocumentsResult result = searchClient.IndexDocuments(batch);
        }
        catch (Exception)
        {
            // If for some reason any documents are dropped during indexing, you can compensate by delaying and
            // retrying. This simple demo just logs the failed document keys and continues.
            Console.WriteLine("Failed to index some of the documents: {0}");
        }
    }
    ```

    一旦將 [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) 物件初始化之後，您就可以將其傳送至索引，方法是在 [SearchClient](/dotnet/api/azure.search.documents.searchclient) 物件上呼叫 [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments)。

1. 將以下幾行新增至 Main()。 載入文件是使用 SearchClient 來完成，但作業也需要服務的管理員權限，這通常與 SearchIndexClient 相關聯。 設定此作業的其中一種方式是透過 SearchIndexClient (在此範例中為 adminClient) 取得 SearchClient。

   ```csharp
    SearchClient ingesterClient = adminClient.GetSearchClient(indexName);

    // Load documents
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(ingesterClient);
   ```

1. 因為這是會循序執行所有命令的主控台應用程式，因此請在索引編製和查詢之間新增 2 秒的等候時間。

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    這 2 秒延的遲是針對索引編製進行補償，這是非同步作業，因此可以在執行查詢之前針對所有文件編製索引。 設計延遲程式碼只有在示範、測試與範例應用程式中才有必要。

## <a name="3---search-an-index"></a>3 - 搜尋索引

您可以在系統為第一個文件編製索引之後立即取得結果，但索引的實際測試應該等到系統為所有文件編製索引之後才進行。

此節新增兩個功能：查詢邏輯，以及結果。 針對查詢，請使用[搜尋](/dotnet/api/azure.search.documents.searchclient.search)方法。 這個方法會接受搜尋文字 (查詢字串) 以及其他[選項](/dotnet/api/azure.search.documents.searchoptions)。

[SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) 類別代表結果。

1. 在 **Program.cs** 中，建立會將結果列印到主控台的 **WriteDocuments** 方法。

    ```csharp
    // Write search results to console
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.GetResults())
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. 建立 **RunQueries** 方法以執行查詢並傳回結果。 結果是 Hotel 物件。 這個範例會顯示方法簽章和第一個查詢。 此查詢會示範可讓您使用文件。中選取的欄位來撰寫結果的 Select 參數。

    ```csharp
    // Run queries, use WriteDocuments to print output
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on empty term '*' to return all documents, showing a subset of fields...\n");

        options = new SearchOptions()
        {
            IncludeTotalCount = true,
            Filter = "",
            OrderBy = { "" }
        };

        options.Select.Add("HotelId");
        options.Select.Add("HotelName");
        options.Select.Add("Address/City");

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    ```

1. 在第二個查詢中搜尋某個字詞，並新增篩選條件來選取 [評等] 大於 4 的文件，然後依評等的遞減順序排序。 篩選則是布林運算式，會針對索引中的 [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) 欄位來進行評估。 篩選會查詢包含或排除值。 因此，沒有與篩選查詢相關聯的相關性分數。 

    ```csharp
    Console.WriteLine("Query #2: Search on 'hotels', filter on 'Rating gt 4', sort by Rating in descending order...\n");

    options = new SearchOptions()
    {
        Filter = "Rating gt 4",
        OrderBy = { "Rating desc" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Rating");

    response = srchclient.Search<Hotel>("hotels", options);
    WriteDocuments(response);
    ```

1. 第三個查詢示範 searchFields，此參數用來將全文檢索搜尋作業的範圍限定為特定欄位。

    ```csharp
    Console.WriteLine("Query #3: Limit search to specific fields (pool in Tags field)...\n");

    options = new SearchOptions()
    {
        SearchFields = { "Tags" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Tags");

    response = srchclient.Search<Hotel>("pool", options);
    WriteDocuments(response);
    ```

1. 第四個查詢示範 Facet，可以用來建立多 Facet 導覽結構。 

   ```csharp
    Console.WriteLine("Query #4: Facet on 'Category'...\n");

    options = new SearchOptions()
    {
        Filter = ""
    };

    options.Facets.Add("Category");

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Category");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
   ```

1. 在第五個查詢中，傳回特定的文件。 文件查閱是結果集中 OnClick 事件的典型回應。

   ```csharp
    Console.WriteLine("Query #5: Look up a specific document...\n");

    Response<Hotel> lookupResponse;
    lookupResponse = srchclient.GetDocument<Hotel>("3");

    Console.WriteLine(lookupResponse.Value.HotelId);
   ```

1. 最後一個查詢會顯示模擬部分使用者輸入 "sa" 的自動完成語法；此輸入在與您在索引中定義的建議工具相關聯之 sourceFields 中可能有兩個相符項目。

   ```csharp
    Console.WriteLine("Query #6: Call Autocomplete on HotelName that starts with 'sa'...\n");

    var autoresponse = srchclient.Autocomplete("sa", "sg");
    WriteDocuments(autoresponse);
   ```

1. 將 **RunQueries** 新增至 Main()。

    ```csharp
    // Call the RunQueries method to invoke a series of queries
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);

    // End the program
    Console.WriteLine("{0}", "Complete. Press any key to end this program...\n");
    Console.ReadKey();
    ```

先前的查詢會顯示[在查詢中比對字詞的多種方式](search-query-overview.md#types-of-queries)：全文檢索搜尋、篩選和自動完成。

全文搜索搜尋和篩選都會使用 [SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search) 方法來執行。 搜尋查詢可以在 `searchText` 字串中傳遞，而篩選運算式則可以在 [SearchOptions](/dotnet/api/azure.search.documents.searchoptions) 類別的[篩選](/dotnet/api/azure.search.documents.searchoptions.filter)屬性中傳遞。 若要篩選而不進行搜尋，只要針對[搜尋](/dotnet/api/azure.search.documents.searchclient.search)方法的 `searchText` 參數傳遞 `"*"` 即可。 若要在不進行篩選的情況下搜尋，則請將 `Filter` 屬性保留在未設定狀態，或完全不要傳入 `SearchOptions` 執行個體。

## <a name="run-the-program"></a>執行程式

按下 F5 以重建應用程式並完整執行程式。 

輸出中包含來自 [Console.WriteLine](/dotnet/api/system.console.writeline) 的訊息，並且會加上查詢資訊和結果。

## <a name="clean-up-resources"></a>清除資源

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 資源若繼續執行，您將必須付費。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。 

## <a name="next-steps"></a>後續步驟

在此 C# 快速入門中，您已執行一組工作來建立索引、使用文件來載入索引，以及執行查詢。 在不同的階段中，我們採用捷徑，以簡化程式碼，讓程式碼更容易閱讀及傳達概念。 若您熟悉已基本概念，我們建議參閱下一篇文章以探索可讓您深入了解的替代方法與概念。 

> [!div class="nextstepaction"]
> [如何以 .NET 開發](search-howto-dotnet-sdk.md) \(部分機器翻譯\)

想要最佳化並節省您的雲端費用嗎？

> [!div class="nextstepaction"]
> [使用成本管理開始分析成本](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
