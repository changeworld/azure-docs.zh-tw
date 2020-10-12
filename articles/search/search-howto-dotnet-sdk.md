---
title: '使用 v10 在 .NET 中搜尋 () '
titleSuffix: Azure Cognitive Search
description: '瞭解如何在 .net 應用程式中使用 c # 和第10版的 .NET SDK 來建立和管理搜尋物件。 程式碼片段會示範如何連接到服務、建立索引和查詢。'
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 394c87bcd3e4580289fbccc6a31b164f914dc8a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020791"
---
# <a name="how-to-use-microsoftazuresearch-v10-in-a-net-application"></a>如何在 .NET 應用程式中使用 Azure 搜尋 (v10) 

本文說明如何使用 c # 和 [Azure 認知搜尋 (v10) .NET SDK](/dotnet/api/overview/azure/search)來建立和管理搜尋物件。 第10版是最新版的 Microsoft Azure. Search 套件。 從 Azure SDK 團隊 [Azure.Search.Documents](/dotnet/api/overview/azure/search.documents-readme) 中，新功能將在未來推出。

如果您有現有或傳遞的開發專案，請繼續使用第10版。 針對新的專案，或使用新功能，您應該將現有的搜尋解決方案轉換至新的程式庫。

## <a name="whats-in-version-10"></a>第10版的功能

SDK 包含數個用戶端程式庫，可讓您管理索引、資料來源、索引子及同義字地圖，以及上傳和管理文件，還可以執行查詢，而且一律不需要處理 HTTP 和 JSON 的細節。 這些用戶端程式庫都以 NuGet 套件來散發。

主要的 NuGet 套件是 `Microsoft.Azure.Search`，這是包含所有其他相依套件的中繼套件。 如果您剛開始使用，或您知道您的應用程式將需要 Azure 認知搜尋的所有功能，請使用此套件。

SDK 中還有其他 NuGet 套件：
 
  - `Microsoft.Azure.Search.Data`：如果您要使用 Azure 認知搜尋開發 .NET 應用程式，且只需要查詢或更新索引中的檔，請使用此封裝。 如果您也需要建立或更新索引、同義字地圖或其他服務層級資源，請改用 `Microsoft.Azure.Search` 套件。
  - `Microsoft.Azure.Search.Service`：如果您要在 .NET 中開發自動化來管理 Azure 認知搜尋的索引、同義字地圖、索引子、資料來源或其他服務層級資源，請使用此封裝。 如果您只需要查詢或更新索引中的文件，請改用 `Microsoft.Azure.Search.Data` 套件。 如果您需要 Azure 認知搜尋的所有功能，請改為使用 `Microsoft.Azure.Search` 套件。
  - `Microsoft.Azure.Search.Common`： Azure 認知搜尋 .NET 程式庫所需的一般類型。 您不需要直接在應用程式中使用此封裝。 它只是用來做為相依性。

各種用戶端程式庫會定義類別，例如 `Index`、`Field` 及 `Document`，以及定義作業，例如 `SearchServiceClient` 和 `SearchIndexClient` 類別上的 `Indexes.Create` 和 `Documents.Search`。 這些類別可編成以下命名空間：

* [Microsoft Azure. 搜尋](/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](/dotnet/api/microsoft.azure.search.models)

如果您想要針對未來的 SDK 更新提供意見反應，請參閱我們的 [意見反應頁面](https://feedback.azure.com/forums/263029-azure-search/) 或在 [GitHub](https://github.com/azure/azure-sdk-for-net/issues) 上建立問題，並在問題標題中提及「Azure 認知搜尋」。

.NET SDK 的目標版本是 `2019-05-06` [Azure 認知搜尋 REST API](/rest/api/searchservice/)。 此版本包含對 Azure Blob 編制索引時的 [複雜類型](search-howto-complex-data-types.md)、 [AI 擴充](cognitive-search-concept-intro.md)、 [自動完成](/rest/api/searchservice/autocomplete)和 [JsonLines 剖析模式](search-howto-index-json-blobs.md) 的支援。 

此 SDK 不支援[管理作業](/rest/api/searchmanagement/)，例如建立及調整搜尋服務和管理 API 金鑰。 如果您需要從 .NET 應用程式管理搜尋資源，您可以使用 [Azure 認知搜尋 .Net 管理 SDK](https://aka.ms/search-mgmt-sdk)。

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>升級到最新版本的 SDK
如果您已經使用舊版的 Azure 認知搜尋 .NET SDK，而且想要升級至最新的正式推出版本， [本文](search-dotnet-sdk-migration-version-9.md) 將說明如何進行。

## <a name="requirements-for-the-sdk"></a>SDK 的需求
1. Visual Studio 2017 或更新版本。
2. 您自己的 Azure 認知搜尋服務。 為了使用 SDK，您需要為服務命名，還需要一或多個 API 金鑰。 [在入口網站建立服務](search-create-service-portal.md) 可協助您執行這些步驟。
3. 使用 Visual Studio 中的 [管理 NuGet 封裝] 來下載 Azure 認知搜尋 .NET SDK [NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Search) 。 只要在 NuGet.org 搜尋套件名稱 `Microsoft.Azure.Search` (或者，如果您只需要某個功能子集，則搜尋上述其中一個其他套件名稱)。

Azure 認知搜尋 .NET SDK 支援以 .NET Framework 4.5.2 和更新版本，以及 .NET Core 2.0 和更新版本為目標的應用程式。

## <a name="core-scenarios"></a>核心案例
您必須在搜尋應用程式中執行一些作業。 本教學課程中涵蓋了這些主要情節：

* 建立索引
* 透過文件填入索引
* 使用全文檢索搜尋和篩選來搜尋文件

下列範例程式碼說明每個案例。 歡迎在您的應用程式中使用這些程式碼片段。

### <a name="overview"></a>概觀
我們將探索的範例應用程式，會建立名為 "hotels" 的新索引，並透過一些文件填入索引，然後執行一些搜尋查詢。 以下是主要程式，該程式顯示了整體流程：

```csharp
// This sample shows how to delete, create, upload documents and query an index
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

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> 您可以在 [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 上尋找本逐步解說中所用範例應用程式的完整原始程式碼。
> 
>

我們會逐步說明， 首先必須建立新的 `SearchServiceClient`。 此物件可讓您管理索引。 您必須提供您的 Azure 認知搜尋服務名稱以及管理 API 金鑰，才能建立一個。 您可以在[範例應用程式](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)的 `appsettings.json` 檔案中輸入這項資訊。

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> 如果提供不正確的金鑰 (例如，需要系統管理金鑰卻提供查詢金鑰)，則 `SearchServiceClient` 會在您第一次用它呼叫作業方法時 (例如 `Indexes.Create`) 擲回 `CloudException`，並附上「禁止」的錯誤訊息。 如果遇到此情況，請按兩下我們的 API 金鑰。
> 
> 

以下幾行程式碼會呼叫建立名為 "hotels" 索引的方法，如果索引已存在，請加以刪除。 稍後我們會逐項執行這些方法。

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

接著必須填入索引。 若要填入索引，我們將需要 `SearchIndexClient` 。 取得的方式有兩種：建構該項目，或用 `SearchServiceClient` 呼叫 `Indexes.GetClient`。 為方便起見，我們使用後者。

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> 在一般搜尋應用程式中，索引管理和擴展可能會由搜尋查詢中的個別元件處理。 `Indexes.GetClient` 很方便用來擴展索引，因為它可讓您節省額外的麻煩 `SearchCredentials` 。 執行方法是將用於建立 `SearchServiceClient` 的系統管理金鑰傳遞至新的 `SearchIndexClient`。 不過，在執行查詢的應用程式中，最好是直接建立， `SearchIndexClient` 讓您可以傳入查詢金鑰，而這只允許您讀取資料，而不是管理金鑰。 這不但符合最低權限的準則，也可以讓您的應用程式更安全。 您可以 [在這裡](/rest/api/searchservice/#authentication-and-authorization)深入了解系統管理金鑰和查詢金鑰。
> 
> 

現在我們有 `SearchIndexClient`，可以開始填入索引。 索引擴展是由我們稍後將逐步解說的另一種方法來完成。

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

最後，我們會執行一些搜尋查詢並顯示結果。 這次我們使用不同的 `SearchIndexClient`：

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

我們稍後會仔細查看 `RunQueries` 方法。 以下是可建立新 `SearchIndexClient` 的程式碼：

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

這次我們使用查詢金鑰，因為我們不需要索引的寫入存取權。 您可以在[範例應用程式](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)的 `appsettings.json` 檔案中輸入這項資訊。

如果您使用有效的服務名稱和 API 金鑰執行此應用程式，輸出應該看起來像此範例： (某些主控台輸出已取代為 "..."供說明之用。 ) 

```output

Deleting index...

Creating index...

Uploading documents...

Waiting for documents to be indexed...

Search the entire index for the term 'motel' and return only the HotelName field:

Name: Secret Point Motel

Name: Twin Dome Motel


Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

HotelId: 1
Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

HotelId: 2
Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Triple Landscape Hotel
Last renovated on: 9/20/2015 12:00:00 AM +00:00

Name: Twin Dome Motel
Last renovated on: 2/18/1979 12:00:00 AM +00:00


Search the hotel names for the term 'hotel':

HotelId: 3
Name: Triple Landscape Hotel
...

Complete.  Press any key to end application... 
```

本文結尾會提供完整的應用程式原始程式碼。

接著，我們要進一步了解 `Main`所呼叫的每個方法。

### <a name="creating-an-index"></a>建立索引
建立之後 `SearchServiceClient` ， `Main` 會刪除「飯店」索引（如果已存在）。 這項刪除作業是透過下列方法來完成：

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

此方法使用指定的 `SearchServiceClient` 查看索引是否存在，如果存在，就加以刪除。

> [!NOTE]
> 為了簡單起見，本文中的範例程式碼會使用 Azure 認知搜尋 .NET SDK 的同步方法。 我們建議您在應用程式中使用非同步方法，讓應用程式保有可擴充性且回應靈敏。 例如，您可以在以上方法中使用 `ExistsAsync` 和 `DeleteAsync`，而非 `Exists` 和 `Delete`。
> 
> 

接著，`Main` 會呼叫此方法建立新的 "hotels" 索引：

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

此方法會以定義新索引結構描述的 `Field` 物件清單，建立新的 `Index` 物件。 每個欄位均有一個名稱、資料類型和一些屬性，以用於定義欄位的搜尋行為。 `FieldBuilder` 類別會檢查指定 `Hotel` 模型類別的公用屬性 (property) 和屬性 (attribute)，進而使用反映來建立索引的 `Field` 物件清單。 我們稍後會仔細查看 `Hotel` 類別。

> [!NOTE]
> 如有需要，您永遠可以直接建立 `Field` 物件清單，而不是使用`FieldBuilder`。 例如，您可能不想使用模型類別，或您可能需要使用不想藉由新增屬性來修改的現有模型類別。
>
> 

除了欄位之外，您也可以將評分設定檔、建議工具或 CORS 選項新增至索引 (這些參數會在範例中省略，以求簡潔) 。 您可以在 [SDK 參考](/dotnet/api/microsoft.azure.search.models.index)及 [Azure 認知搜尋 REST API 參考](/rest/api/searchservice/)中，找到更多關於 Index 物件和其組成部分的資訊。

### <a name="populating-the-index"></a>填入索引
中的下一個步驟會 `Main` 填入新建立的索引。 此索引擴展是以下列方法完成： (某些程式碼取代為 "..."供說明之用。  如需完整的資料填入程式碼，請參閱完整的範例解決方案。 ) 

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
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
        },
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
        },
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

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

此方法分四個部分。 第一個會建立3個物件的陣列， `Hotel` 每個物件都有3個 `Room` 物件，這些物件將作為要上傳至索引的輸入資料。 為簡單起見，此資料採硬式編碼。 在您的應用程式中，資料可能會來自像是 SQL 資料庫的外部資料來源。

第二個部分會建立包含文件的 `IndexBatch` 。 您在建立批次時 (在此案例中，是藉由呼叫 `IndexBatch.Upload`)，指定要套用至該批次的作業。 然後，此批次會由方法上傳至 Azure 認知搜尋索引 `Documents.Index` 。

> [!NOTE]
> 在此範例中，我們只上傳文件。 如果您想要將變更合併至現有的文件，或是刪除文件，您可以改為呼叫 `IndexBatch.Merge`、`IndexBatch.MergeOrUpload` 或 `IndexBatch.Delete` 來建立批次。 您也可以在單一批次中混合不同的作業，方法是呼叫 `IndexBatch.New` ，這會取得物件的集合 `IndexAction` ，每個物件都會告訴 Azure 認知搜尋對檔執行特定作業。 您可以建立每個擁有自己作業的 `IndexAction`，方法是呼叫對應的方法，例如 `IndexAction.Merge`、`IndexAction.Upload` 等等。
> 
> 

此方法的第三部分是擷取區塊，該區塊會為編制索引處理重要錯誤情況。 如果您的 Azure 認知搜尋服務無法對批次中的某些檔編制索引， `IndexBatchException` 則會擲回 `Documents.Index` 。 如果您在服務負載過重時編制檔索引，就會發生此例外狀況。 **我們強烈建議您在程式碼中明確處理此情況。**  您可以延遲，然後重新嘗試將失敗的文件編制索引，或像範例一樣加以記錄並繼續，或是根據您應用程式的資料一致性需求執行其他操作。

> [!NOTE]
> 您可以使用 [`FindFailedActionsToRetry`](/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) 方法來建立新的批次，其中只包含先前呼叫中失敗的動作 `Index` 。 [StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry) 上有關於如何適當地使用此方法的討論。
>
>

最後，`UploadDocuments` 方法會延遲兩秒。 索引會在 Azure 認知搜尋服務中以非同步方式執行，因此範例應用程式必須等候一小段時間，以確保檔可以搜尋。 通常只有在示範、測試和範例應用程式中，才需要這類延遲。

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>.NET SDK 如何處理文件
您可能會想知道 Azure 認知搜尋 .NET SDK 如何能夠將使用者定義類別的實例上傳 `Hotel` 至索引。 為了回答這問題，我們來看一下 `Hotel` 類別：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
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

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

首先要注意的是，類別中的每個公用屬性名稱 `Hotel` 都會對應到索引定義中具有相同名稱的欄位。 如果您希望每個欄位以小寫字母開頭 ( 「camel 大小寫」 ) ，您可以告知 SDK 使用類別上的屬性，自動將屬性名稱對應至 camel 大小寫 `[SerializePropertyNamesAsCamelCase]` 。 此案例在執行資料系結的 .NET 應用程式中很常見，因為目標架構不在應用程式開發人員的控制範圍內，而不需要違反 .NET 中的「Pascal 案例」命名方針。

> [!NOTE]
> Azure 認知搜尋 .NET SDK 會使用 [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) 程式庫，將您的自訂模型物件序列化和還原序列化為 JSON。 如有需要，您可以自訂這個序列化的過程。 如需詳細資訊，請參閱 [使用 JSON.NET 的自訂序列化](#JsonDotNet)。
> 
> 

第二個要注意的是，每個屬性都是利用 `IsFilterable` 、 `IsSearchable` 、和等屬性裝飾 `Key` `Analyzer` 。 這些屬性會直接對應至 [Azure 認知搜尋索引中的對應欄位屬性](/rest/api/searchservice/create-index)。 `FieldBuilder`類別會使用這些屬性來建立索引的欄位定義。

類別的第三個重要事項 `Hotel` 是公用屬性的資料類型。 這些屬性的 .NET 類型會對應至索引定義中，與其相當的欄位類型。 例如，`Category` 字串屬性會對應至 `category` 欄位 (此欄位屬於 `Edm.String` 類型)。 、、和等之間有類似的類型對應 `bool?` `Edm.Boolean` `DateTimeOffset?` `Edm.DateTimeOffset` 。 類型對應的特定規則會與 `Documents.Get` [AZURE 認知搜尋 .net SDK 參考](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get)中的方法一起記載。 `FieldBuilder` 類別會為您處理這項對應，但它仍有助您了解您需要對任何序列化問題進行疑難排解的情況。

您是否已注意到 `SmokingAllowed` 屬性？

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

`JsonIgnore`這個屬性（attribute）上的屬性（attribute）會告知， `FieldBuilder` 不要將它序列化為索引（欄位）。  這是建立用戶端計算屬性的絕佳方法，可供您在應用程式中用來做為協助程式。  在此情況下， `SmokingAllowed` 屬性會反映集合中的任何是否 `Room` `Rooms` 允許吸煙。  如果全部都是 false，則表示整個旅館不允許吸煙。

某些屬性（例如 `Address` 和） `Rooms` 是 .net 類別的實例。  這些屬性代表更複雜的資料結構，因此需要索引中具有 [複雜資料類型](./search-howto-complex-data-types.md) 的欄位。

`Address`屬性代表類別中的一組多個值 `Address` ，定義如下：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
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

這個類別包含用來描述美國或加拿大位址的標準值。 您可以使用像這樣的類型，將邏輯欄位一起分組在索引中。

`Rooms`屬性代表物件的陣列 `Room` ：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

您在 .NET 中的資料模型及其對應的索引架構，應設計成支援您想要提供給使用者的搜尋體驗。 .NET 中的每個最上層物件（索引中的 ie 檔）會對應至您在使用者介面中顯示的搜尋結果。 例如，在旅館搜尋應用程式中，您的終端使用者可能想要依旅館名稱、旅館的功能，或特定房間的特性來搜尋。 我們稍後會討論一些查詢範例。

這項功能可讓您使用自己的類別與索引中的檔互動;您也可以取出搜尋結果，然後讓 SDK 自動將其還原序列化為您選擇的類型，如下一節所示。

> [!NOTE]
> Azure 認知搜尋 .NET SDK 也支援使用類別的動態類型檔 `Document` ，這是功能變數名稱與域值的索引鍵/值對應。 當您在設計階段卻不知道索引的結構描述時，這很實用，否則要繫結到特定模型類別會很麻煩。 SDK 中所有處理文件的方法，都有可搭配 `Document` 類別使用的多載，以及使用泛型類型參數的強類型多載。 本教學課程的範例程式碼只使用了後者。 [ `Document` 類別](/dotnet/api/microsoft.azure.search.models.document)繼承自 `Dictionary<string, object>` 。
> 
>

**為什麼您應該使用 Nullable 資料類型**

當您設計自己的模型類別以對應至 Azure 認知搜尋索引時，建議您將實數值型別的屬性（例如和）宣告為可 `bool` `int` 為 null (例如， `bool?` 而不是 `bool`) 。 如果您使用不可為 Null 的屬性，則必須保證 **** 索引中沒有任何文件的對應欄位包含 Null 值。 SDK 和 Azure 認知搜尋服務都不會協助您強制執行此工作。

這不只是假設性的問題：如果您在類型為 `Edm.Int32` 的現有索引中新增欄位， 更新索引定義之後，新欄位的所有檔都會有 null 值 (因為所有類型在 Azure 認知搜尋) 中都可為 null。 如果您接著對該欄位使用 `int` 屬性不可為 Null 的模型類別，就會在嘗試擷取文件時得到類似這樣的 `JsonSerializationException`：

```output
Error converting value {null} to type 'System.Int32'. Path 'IntValue'.
```

因此，我們建議您在模型類別中使用可為 Null 的類型，來做為最佳作法。

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>使用 JSON.NET 自訂序列化
SDK 會使用 JSON.NET 序列化和還原序列化文件。 您可以視需要自訂序列化和還原序列化，方法是定義您自己的 `JsonConverter` 或 `IContractResolver` 。 如需詳細資訊，請參閱 [JSON.NET 檔](https://www.newtonsoft.com/json/help/html/Introduction.htm)。 當您想要在應用程式中調整現有的模型類別以搭配 Azure 認知搜尋使用，以及其他更先進的案例時，這會很有用。 例如，使用自訂序列化，您可以：

* 包含或排除模型類別的特定屬性儲存為文件欄位。
* 在程式碼的屬性名稱和索引的欄位名稱之間進行對應。
* 建立可用來將屬性對應至文件欄位的自訂屬性。

您可以在 GitHub 上的 Azure 認知搜尋 .NET SDK 的單元測試中，找到執行自訂序列化的範例。 [這個資料夾](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models)是好的起點。 它包含自訂序列化測試使用的類別。

### <a name="searching-for-documents-in-the-index"></a>搜尋索引中的文件
範例應用程式的最後一個步驟是搜尋索引中的某些檔：

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

每次執行查詢時，這個方法都會先建立新的 `SearchParameters` 物件。 此物件可用來指定查詢的其他選項，例如排序、篩選、分頁和 facet。 在此方法中，我們會針對不同查詢設定 `Filter`、`Select`、`OrderBy` 和 `Top` 屬性。 所有 `SearchParameters` 屬性都記載於[這裡](/dotnet/api/microsoft.azure.search.models.searchparameters)。

下一步是實際執行搜尋查詢， 您可以使用方法來執行搜尋 `Documents.Search` 。 針對每次查詢，我們會傳遞搜尋文字做為字串 (如果沒有搜尋文字，則傳遞 `"*"`)，並再加上先前建立的搜尋參數。 我們也指定了 `Hotel` 做為 `Documents.Search` 的類型參數，藉此告訴 SDK 將搜尋結果中的文件還原序列化為 `Hotel` 類型的物件。

> [!NOTE]
> 如需搜尋查詢運算式語法的詳細資訊，請參閱 [這裡](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)。
> 
> 

最後，在每次查詢之後，此方法會反覆查詢搜尋結果中的所有相符項，然後將每個文件列印至主控台：

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

讓我們依序仔細查看每個查詢。 以下是執行第一個查詢的程式碼︰

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

在此情況下，我們會在任何可搜尋的欄位中搜尋 "motel" 一字的整個索引，而我們只想要取得該參數所指定的旅館名稱 `Select` 。 以下是結果：

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

下一個查詢稍微有趣一點。  我們想要找出具有每晚費率低於 $100 的旅館，然後只傳回旅館識別碼和描述：

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

此查詢會使用 OData `$filter` 運算式 (`Rooms/any(r: r/BaseRate lt 100)`)，以篩選索引中的文件。 這會使用 [any 運算子](./search-query-odata-collection-operators.md) 將 ' BaseRate lt 100 ' 套用至房間集合中的每個專案。 您可以在 [這裡](./query-odata-filter-orderby-syntax.md)找到 Azure 認知搜尋支援之 OData 語法的詳細資訊。

查詢的結果如下：

```output
HotelId: 1
Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

HotelId: 2
Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...
```

接下來，我們要尋找最近重新裝修的前兩間旅館，並顯示旅館名稱和上次重新裝修日期。 程式碼如下： 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

在此情況下，我們再次使用 OData 語法，將 `OrderBy` 參數指定為 `lastRenovationDate desc`。 我們也會將 `Top` 設定為 2，以確保只取得前兩份文件。 如同前面，我們會設定 `Select` 來指定應傳回哪些欄位。

以下是結果：

```output
Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00
```

最後，我們想要尋找符合「飯店」一字的所有旅館名稱：

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

結果如下，其中包含所有欄位，因為我們並未指定 `Select` 屬性︰

```output
    HotelId: 3
    Name: Triple Landscape Hotel
    ...
```

此步驟已完成本教學課程，但別就此結束。 * * 接下來的步驟會提供進一步的資源，讓您深入瞭解 Azure 認知搜尋。

## <a name="next-steps"></a>後續步驟
* 瀏覽 [.NET SDK](/dotnet/api/microsoft.azure.search) 及 [REST API](/rest/api/searchservice/) 參考文章。
* 檢閱 [命名規則](/rest/api/searchservice/Naming-rules) ，了解命名各種物件的規則。
* 請參閱 Azure 認知搜尋中 [支援的資料類型](/rest/api/searchservice/Supported-data-types) 。