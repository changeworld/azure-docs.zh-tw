---
title: '舊版 c # 快速入門'
titleSuffix: Azure Cognitive Search
description: '這個 c # 快速入門會使用第10版用戶端程式庫 (Microsoft. Azure. Search) 來建立、載入和查詢搜尋索引。'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 4d8a38666991760fbb133e617984ba23788034ca
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908884"
---
# <a name="quickstart-create-a-search-index-using-the-microsoftazuresearch-v10-client-library"></a>快速入門：使用 Microsoft. Azure 搜尋服務 v10 用戶端程式庫來建立搜尋索引

本文是舊版 Microsoft. Azure 搜尋服務的 c # 快速入門 (版本 10) 用戶端程式庫，現在已由 Azure.Search.Documents (版本 11) 用戶端程式庫取代。 如果您有使用 Microsoft. Azure 搜尋程式庫的現有搜尋解決方案，您可以使用本快速入門來瞭解這些 Api。 

針對新的解決方案，我們建議新的 Azure.Search.Documents 程式庫。 如需簡介，請參閱[快速入門：使用 Azure.Search.Documents 程式庫建立搜尋索引](search-get-started-dotnet.md)。

## <a name="about-this-quickstart"></a>關於本快速入門

在 c # 中建立 .NET Core 主控台應用程式，以使用 Visual Studio 和[Microsoft. Azure 搜尋用戶端程式庫](https://docs.microsoft.com/dotnet/api/overview/azure/search/client10?view=azure-dotnet)來建立、載入和查詢 Azure 認知搜尋索引。 

本文說明如何建立應用程式。 您也可以[下載並執行完整的應用程式](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart-v10)。

> [!NOTE]
> 本文中的示範程式碼會使用 Azure 認知搜尋第10版 .NET SDK 的同步方法來簡化。 不過，針對生產案例，我們建議您在您自己的應用程式中使用非同步方法，讓應用程式保有可擴充性且回應靈敏。 例如，您可以使用 `CreateAsync` 與 `DeleteAsync`，而非 `Create` 與 `Delete`。

## <a name="prerequisites"></a>必要條件

開始之前，您必須具備下列條件：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/)。

+ Azure 認知搜尋服務。 在目前的訂閱下，[建立服務](search-create-service-portal.md) 或 [尋找現有的服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) 的任何版本。 範例程式碼和指示已在免費的 Community 版本上經過測試。

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>取得金鑰和 URL

在每個對服務發出呼叫的要求上，都需要 URL 端點和存取金鑰。 建立搜尋服務時需要這兩項資料，因此如果您將 Azure 認知搜尋新增至您的訂用帳戶，請依照下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀] 頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

2. 在 [設定] >  [金鑰] 中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

   一併取得查詢金鑰。 最佳做法是發出具有唯讀存取權的查詢要求。

![取得 HTTP 端點和存取金鑰](media/search-get-started-postman/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="set-up-your-environment"></a>設定您的環境

一開始請開啟 Visual Studio，並建立可在 .NET Core 上執行的新主控台應用程式專案。

### <a name="install-nuget-packages"></a>安裝 NuGet 套件

「 [Microsoft Azure 搜尋套件](https://www.nuget.org/packages/Microsoft.Azure.Search/)」是由幾個以 NuGet 套件形式散發的用戶端程式庫所組成。

針對此專案，請使用版本10的 `Microsoft.Azure.Search` nuget 套件和最新的 `Microsoft.Extensions.Configuration.Json` nuget 套件。

1. 在 [工具] > [NuGet 套件管理員] 中，選取 [管理解決方案的 NuGet 套件] 中。 

1. 按一下 **[瀏覽]** 。

1. 搜尋 `Microsoft.Azure.Search` 並選取 [第10版]。

1. 按一下右邊的 [安裝] 以將組件新增到您的r專案與解決方案。

1. 針對 `Microsoft.Extensions.Configuration.Json` 重複此程序，但選取 2.2.0 版或更新版本。


### <a name="add-azure-cognitive-search-service-information"></a>新增 Azure 認知搜尋服務資訊

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後選取 [新增] > [新項目]。 

1. 在 [新增項目] 中，搜尋 "JSON" 以傳回 JSON 相關項目類型清單。

1. 選擇 [JSON 檔案]、將該檔案命名為 "appsettings.json"，然後按一下 [新增]。 

1. 將檔案新增到您的輸出目錄。 以滑鼠右鍵按一下 appsettings.json，然後選取 [屬性]。 在 [複製到輸出目錄] 中，選取 [有更新時才複製]。

1. 將下列 JSON 複製到您的新 JSON 檔案中。 

    ```json
    {
      "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
      "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
      "SearchIndexName": "hotels-quickstart"
    }
    ```

1. 將搜尋服務名稱 (YOUR-SEARCH-SERVICE-NAME) 與系統管理 API 金鑰 (YOUR-ADMIN-API-KEY) 取代為有效的值。 如果您的服務端點是 `https://mydemo.search.windows.net` ，服務名稱會是 " `mydemo` "。

### <a name="add-class-method-files-to-your-project"></a>新增類別 ".Method" 檔案到您的專案

需要執行此步驟，才能在主控台中產生有意義的輸出。 當您將結果列印到主控台視窗時，來自 Hotel 專案的個別欄位必須以字串形式傳回。 此步驟會執行 [ToString()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) 來執行這項工作，您可以將必要的程式碼複製到兩個新的檔案來完成這項工作。

1. 新增兩個空類別定義到您的專案：Address.Methods.cs、Hotel.Methods.cs

1. 在 Address.Methods.cs 中，藉由下列程式碼來覆寫預設內容，[lines 1-25](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L25)。

1. 在 Hotel.Methods.cs 中，複製 [lines 1-68](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L68)。

## <a name="1---create-index"></a>1 - 建立索引

Hotels 索引由簡單與複雜欄位組成，其中簡單欄位是 "HotelName" 或 "Description"，而複雜欄位是具有子欄位的地址，或房間集合。 當索引包括複雜欄位時，請將複雜欄位定義隔離在個別類別中。

1. 新增兩個空類別定義到您的專案：Address.cs、Hotel.cs

1. 在 Address.cs 中，使用下列程式碼覆寫預設內容：

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

1. 在 Hotel.cs 中，類別會定義索引的整體結構，包括對地址類別的參考。

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    欄位上的屬性決定它如何在應用程式中使用。 例如，`IsSearchable` 屬性必須指派給應該包括在全文檢索搜尋中的每個欄位。 
    
    > [!NOTE]
    > 在 .NET SDK 中，欄位必須明確地屬性化為 [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet)、[`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet)、[`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet) 與 [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet)。 此行為與 REST API 形成對比，後者隱含啟用以資料類型為基礎的屬性 (例如，簡單的字串欄位可自動搜尋)。

    只有類型為 `string` 索引中的一個欄位才必須是「索引鍵」欄位，它會識別每個文件。 在此結構描述中，索引鍵是 `HotelId`。

    在此索引中，描述欄位使用選擇性的 [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) 屬性，這是在當您想要覆寫預設標準 Lucene 分析器時指定的。 `description_fr` 欄位使用法文 Lucene 分析器 ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet))，因為它會儲存法文文字。 `description` 是使用選擇性的 Microsoft 語言分析器 ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet))。

1. 在Program.cs 中，建立 [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) 類別的執行個體以使用儲存在應用程式設定檔 (appsettings.json) 中的值連線到該服務。 

   `SearchServiceClient` 具有 [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) 屬性，它提供建立、列出、更新或刪除 Azure 認知搜尋索引所需的所有方法。 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
            static void Main(string[] args)
            {
                IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
                IConfigurationRoot configuration = builder.Build();

                SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

                string indexName = configuration["SearchIndexName"];

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteIndexIfExists(indexName, serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateIndex(indexName, serviceClient);

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Microsoft.Azure.Search.Models.Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    如果可能的話，在您的應用程式中共用 `SearchServiceClient` 的單一執行個體以避免開啟太多連線。 類別方法是是安全執行緒，可啟用此類共用。

   該類別有數個建構函式。 您所需的建構函式會取得您的搜尋服務名稱和 `SearchCredentials` 物件作為參數。 `SearchCredentials` 會包裝您的 API 金鑰。

    在索引定義中，建立 `Field` 物件最簡單的方式是透過呼叫 `FieldBuilder.BuildForType` 方法，傳遞類型參數的模型類別。 模型類別具有會對應至您索引欄位的屬性。 此對應可讓您將來自您搜尋索引的文件繫結至模型類別的執行個體。

    > [!NOTE]
    > 如果您不打算使用模型類別，仍然可以藉由直接建立 `Field` 物件來定義您的索引。 您可以為建構函式提供欄位的名稱，以及資料類型 (或字串欄位的分析器)。 您也可以設定其他屬性，例如 `IsSearchable`、`IsFilterable` 等等。
    >

1. 按下 F5 以建置應用程式並建立索引。 

    若專案成功地建置，會開啟主控台視窗，而且刪除及建立索引的狀態訊息會寫入到畫面上。 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 載入文件

在 Azure 認知搜尋中，文件是同時屬於索引輸入與查詢輸出的資料結構。 如同從外部資料來源所取得的一樣，文件輸入可能是資料庫中的資料列，Blob 儲存體中的 Blob 或磁碟上的 JSON 文件。 在此範例中，我們採用捷徑，並針對程式碼本身中的四家旅館內嵌 JSON 文件。 

當上傳文件時，您必須使用 [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 物件。 `IndexBatch` 包含 [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) 物件的集合，每個物件都包含文件與屬性，後者會告知 Azure 認知搜尋所應執行的動作 ([上傳、合併、刪除及 mergeOrUpload](search-what-is-data-import.md#indexing-actions))。

1. 在 Program.cs 中，建立文件與索引動作的索引，然後將該陣列傳遞到 `IndexBatch`。 下面的文件符合旅館快速入門索引，如旅館與地址類別所定義。

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
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
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    初始化 `IndexBatch` 物件之後，您可以將它傳送到索引，方式是呼叫您 [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) 物件上的 [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet)。 `Documents` 是 `SearchIndexClient` 的屬性，此物件提供的方法可用來新增、修改、刪除或查詢您索引中的文件。

    對 `Index` 方法之呼叫周圍的 `try`/`catch` 會追補索引失敗，當您的服務負載很高時可能會發生索引失敗。 在生產程式碼中，您可以延遲，然後重新重新為失敗的文件編製索引，或像範例一樣記錄失敗狀況並繼續，或是根據您應用程式的資料一致性需求處理失敗狀況。

    這 2 秒延的遲是針對索引編製進行補償，這是非同步作業，因此可以在執行查詢之前針對所有文件編製索引。 設計延遲程式碼只有在示範、測試與範例應用程式中才有必要。

1. 在 Program.cc 中，在 main 中，將「2 - 載入文件」該行取消註解。 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. 按下 F5 以重建應用程式。 

    若專案成功地建置，會開啟主控台視窗並寫入狀態訊息，但這次使用文件上傳相關訊息。 在 Azure 入口網站中的搜尋服務 [概觀] 頁面中，旅館快速入門索引現在應該有 4 個文件。

如需有關文件處理的詳細資訊，請參閱[「.NET SDK 如何處理文件」](search-howto-dotnet-sdk.md#how-dotnet-handles-documents)。

## <a name="3---search-an-index"></a>3 - 搜尋索引

您可以在系統為第一個文件編製索引之後立即取得結果，但索引的實際測試應該等到系統為所有文件編製索引之後才進行。 

此節新增兩個功能：查詢邏輯，以及結果。 針對查詢，請使用 [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) 方法。 此方法接受查詢文件與其他[參數](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet) \(英文\)。 

[`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) 類別代表結果。


1. 在 Program.cs 中，建立會將結果列印到主控台的 WriteDocuments 方法。

    ```csharp
    private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.Results)
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. 建立 RunQueries 方法以執行查詢並傳回結果。 結果是 Hotel 物件。 您可以使用 select 參數來呈現個別欄位。 若欄位不包括在 select 參數中，其對應 Hotel 屬性將會是 Null。

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    有[兩種在查詢中比對字詞的方式](search-query-overview.md#types-of-queries)：全文檢索搜尋，以及篩選。 全文檢索搜尋查詢會在您的索引中搜尋 `IsSearchable` 欄位中的一或多個字詞。 篩選是布林值運算式，系統會針對索引中的 `IsFilterable` 欄位評估運算式。 您可以一起使用全文檢索搜尋與篩選或個別使用它們。

    搜尋和篩選均使用 `Documents.Search` 方法執行。 搜尋查詢可在 `searchText` 參數中傳遞，而篩選運算式可在 `SearchParameters` 類別的 `Filter` 屬性中傳遞。 若要篩選而不進行搜尋，只要為 `searchText` 參數傳遞 `"*"` 即可。 若要在不進行篩選的情況下搜尋，則只要將 `Filter` 屬性保留在未設定狀態，或完全不要傳入 `SearchParameters` 執行個體。

1. 在 Program.cs 中，在 main 中，將「3 - 搜尋」該行取消註解。 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. 解決方案現在已完成。 按下 F5 以重建應用程式並完整執行程式。 

    輸出包括與之前相同的訊息，加上查詢資訊與結果。

## <a name="clean-up-resources"></a>清除資源

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。 

## <a name="next-steps"></a>後續步驟

在此 C# 快速入門中，您已執行一系列的工作來建立索引、使用文件來載入索引，以及執行查詢。 在不同的階段中，我們採用捷徑，以簡化程式碼，讓程式碼更容易閱讀及傳達概念。 若您熟悉已基本概念，我們建議參閱下一篇文章以探索可讓您深入了解的替代方法與概念。 

相同的程式碼與索引是此文章中程式碼的進階版本。 下一個範例會新增 Rooms 集合、使用不同的類別與動作，並深入檢視程序運作方式。

> [!div class="nextstepaction"]
> [如何以 .NET 開發](search-howto-dotnet-sdk.md) \(部分機器翻譯\)

想要最佳化並節省您的雲端費用嗎？

> [!div class="nextstepaction"]
> [使用成本管理開始分析成本](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)