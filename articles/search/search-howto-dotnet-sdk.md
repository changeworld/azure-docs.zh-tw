---
title: 在 .NET 中使用 Azure 認知搜索
titleSuffix: Azure Cognitive Search
description: 瞭解如何使用 C# 和 .NET SDK 在 .NET 應用程式中使用 Azure 認知搜索。 以程式碼為基礎的工作包括連線至服務、為內容編製索引，以及查詢索引。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b31a4e40c1e9095499faf265673ab4213ad6bde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283065"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>如何使用 .NET 應用程式中的 Azure 認知搜索

本文是一個演練，説明您啟動和運行 Azure[認知搜索 .NET SDK](https://aka.ms/search-sdk)。 可以使用 .NET SDK 在應用程式中使用 Azure 認知搜索實現豐富的搜索體驗。

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>Azure 認知搜索 SDK 中的內容
SDK 包含數個用戶端程式庫，可讓您管理索引、資料來源、索引子及同義字地圖，以及上傳和管理文件，還可以執行查詢，而且一律不需要處理 HTTP 和 JSON 的細節。 這些用戶端程式庫都以 NuGet 套件來散發。

主要的 NuGet 套件是 `Microsoft.Azure.Search`，這是包含所有其他相依套件的中繼套件。 如果您剛剛入門，或者知道應用程式將需要 Azure 認知搜索的所有功能，請使用此包。

SDK 中還有其他 NuGet 套件：
 
  - `Microsoft.Azure.Search.Data`： 如果使用 Azure 認知搜索開發 .NET 應用程式，並且只需要查詢或更新索引中的文檔，請使用此包。 如果您也需要建立或更新索引、同義字地圖或其他服務層級資源，請改用 `Microsoft.Azure.Search` 套件。
  - `Microsoft.Azure.Search.Service`： 如果在 .NET 中開發自動化來管理 Azure 認知搜索索引、同義字映射、索引子、資料來源或其他服務等級資源，請使用此包。 如果您只需要查詢或更新索引中的文件，請改用 `Microsoft.Azure.Search.Data` 套件。 如果需要 Azure 認知搜索的所有功能，`Microsoft.Azure.Search`請使用包。
  - `Microsoft.Azure.Search.Common`：Azure 認知搜索 .NET 庫所需的常見類型。 您無需直接在應用程式中使用此包。 它僅用於作為依賴項。

各種用戶端程式庫會定義類別，例如 `Index`、`Field` 及 `Document`，以及定義作業，例如 `SearchServiceClient` 和 `SearchIndexClient` 類別上的 `Indexes.Create` 和 `Documents.Search`。 這些類別可編成以下命名空間：

* [微軟.Azure.搜索](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

如果您想為 SDK 的未來更新提供回饋，請參閱我們[的回饋頁面](https://feedback.azure.com/forums/263029-azure-search/)或在[GitHub](https://github.com/azure/azure-sdk-for-net/issues)上創建問題，並在問題標題中提及"Azure 認知搜索"。

.NET SDK 支援`2019-05-06` [Azure 認知搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)的版本。 此版本包括對[複雜類型](search-howto-complex-data-types.md)[、AI 擴充](cognitive-search-concept-intro.md)、[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)和[JonLines 解析模式](search-howto-index-json-blobs.md)的支援，用於索引 Azure Blob。 

此 SDK 不支援[管理作業](https://docs.microsoft.com/rest/api/searchmanagement/)，例如建立及調整搜尋服務和管理 API 金鑰。 如果需要從 .NET 應用程式管理搜索資源，可以使用[Azure 認知搜索 .NET 管理 SDK](https://aka.ms/search-mgmt-sdk)。

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>升級到最新版本的 SDK
如果您已經在使用舊版本的 Azure 認知搜索 .NET SDK，並且希望升級到最新的一般可用版本，[本文](search-dotnet-sdk-migration-version-9.md)將說明如何升級。

## <a name="requirements-for-the-sdk"></a>SDK 的需求
1. 視覺工作室 2017 或更高版本。
2. 您自己的 Azure 認知搜索服務。 為了使用 SDK，您需要為服務命名，還需要一或多個 API 金鑰。 [在入口網站建立服務](search-create-service-portal.md) 可協助您執行這些步驟。
3. 在視覺化工作室中使用"管理 NuGet 包"下載 Azure 認知搜索 .NET SDK [NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Search)。 只要在 NuGet.org 搜尋套件名稱 `Microsoft.Azure.Search` (或者，如果您只需要某個功能子集，則搜尋上述其中一個其他套件名稱)。

Azure 認知搜索 .NET SDK 支援針對 .NET 框架 4.5.2 及以上以及 .NET Core 2.0 及以上的應用程式。

## <a name="core-scenarios"></a>核心案例
您必須在搜尋應用程式中執行一些作業。 本教學課程中涵蓋了這些主要情節：

* 建立索引
* 透過文件填入索引
* 使用全文檢索搜尋和篩選來搜尋文件

以下示例代碼說明了每種方案。 歡迎在您的應用程式中使用這些程式碼片段。

### <a name="overview"></a>總覽
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

我們會逐步說明， 首先必須建立新的 `SearchServiceClient`。 此物件可讓您管理索引。 為了構造一個，您需要提供 Azure 認知搜索服務名稱以及管理員 API 金鑰。 您可以在[範例應用程式](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)的 `appsettings.json` 檔案中輸入這項資訊。

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

接著必須填入索引。 要填充索引，我們需要一個`SearchIndexClient`。 取得的方式有兩種：建構該項目，或用 `SearchServiceClient` 呼叫 `Indexes.GetClient`。 為方便起見，我們使用後者。

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> 在典型的搜索應用程式中，索引管理和填充可能由與搜索查詢的單獨元件處理。 `Indexes.GetClient`填充索引很方便，因為它節省了您提供額外`SearchCredentials`麻煩的麻煩。 執行方法是將用於建立 `SearchServiceClient` 的系統管理金鑰傳遞至新的 `SearchIndexClient`。 但是，在執行查詢的應用程式部分，最好直接創建 ，`SearchIndexClient`以便可以傳遞查詢金鑰，該金鑰僅允許您讀取資料，而不是管理金鑰。 這不但符合最低權限的準則，也可以讓您的應用程式更安全。 您可以 [在這裡](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization)深入了解系統管理金鑰和查詢金鑰。
> 
> 

現在我們有 `SearchIndexClient`，可以開始填入索引。 索引填充由另一種方法完成，我們將在稍後演練。

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

如果使用有效的服務名稱和 API 金鑰運行此應用程式，則輸出應如下所示：（某些主控台輸出已替換為"..."用於說明目的。

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

本文結尾會提供完整的應用程式原始程式碼。

接著，我們要進一步了解 `Main`所呼叫的每個方法。

### <a name="creating-an-index"></a>建立索引
創建 後`SearchServiceClient`，`Main`如果"酒店"索引已存在，則刪除該索引。 該刪除通過以下方法完成：

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
> 為了簡單起見，本文中的示例代碼使用 Azure 認知搜索 .NET SDK 的同步方法。 我們建議您在應用程式中使用非同步方法，讓應用程式保有可擴充性且回應靈敏。 例如，您可以在以上方法中使用 `ExistsAsync` 和 `DeleteAsync`，而非 `Exists` 和 `Delete`。
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

除了欄位之外，您還可以向索引添加評分設定檔、建議器或 CORS 選項（為了簡潔起見，這些參數在示例中省略）。 您可以在[SDK 引用](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)中以及[Azure 認知搜索 REST API 引用](https://docs.microsoft.com/rest/api/searchservice/)中找到有關索引物件及其組成部分的詳細資訊。

### <a name="populating-the-index"></a>填入索引
中的下一`Main`步將填充新創建的索引。 此索引填充以以下方法完成：（某些代碼替換為"..."用於說明目的。  有關完整的資料填充代碼，請參閱完整的示例解決方案。

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

此方法分四個部分。 第一個創建一個包含`Hotel`3 個`Room`物件的陣列，每個物件包含 3 個物件，這些物件將作為要上載到索引的輸入資料。 為簡單起見，此資料採硬式編碼。 在您的應用程式中，資料可能會來自像是 SQL 資料庫的外部資料來源。

第二個部分會建立包含文件的 `IndexBatch` 。 您在建立批次時 (在此案例中，是藉由呼叫 `IndexBatch.Upload`)，指定要套用至該批次的作業。 然後，`Documents.Index`批次處理將按方法上載到 Azure 認知搜索索引。

> [!NOTE]
> 在此範例中，我們只上傳文件。 如果您想要將變更合併至現有的文件，或是刪除文件，您可以改為呼叫 `IndexBatch.Merge`、`IndexBatch.MergeOrUpload` 或 `IndexBatch.Delete` 來建立批次。 還可以通過調用`IndexBatch.New`在單個批次處理中混合不同的操作，調用 它獲取`IndexAction`物件的集合，每個物件都告訴 Azure 認知搜索對文檔執行特定操作。 您可以建立每個擁有自己作業的 `IndexAction`，方法是呼叫對應的方法，例如 `IndexAction.Merge`、`IndexAction.Upload` 等等。
> 
> 

此方法的第三部分是擷取區塊，該區塊會為編制索引處理重要錯誤情況。 如果 Azure 認知搜索服務無法索引批次處理中的某些文檔，則 由`IndexBatchException``Documents.Index`引發 。 如果在服務負載嚴重時對文檔進行索引，則可能發生此異常。 **我們強烈建議您在程式碼中明確處理此情況。**  您可以延遲，然後重新嘗試將失敗的文件編制索引，或像範例一樣加以記錄並繼續，或是根據您應用程式的資料一致性需求執行其他操作。

> [!NOTE]
> 可以使用 方法[`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry)構造一個新批次處理，其中僅包含對 的上一次調用 中失敗的`Index`操作。 [StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry) 上有關於如何適當地使用此方法的討論。
>
>

最後，`UploadDocuments` 方法會延遲兩秒。 索引在 Azure 認知搜索服務中非同步進行，因此應用程式範例需要等待很短的時間，以確保文檔可供搜索。 通常只有在示範、測試和範例應用程式中，才需要這類延遲。

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>.NET SDK 如何處理文件
您可能想知道 Azure 認知搜索 .NET SDK 如何將使用者定義的類的實例（如索引）`Hotel`上載。 為了回答這問題，我們來看一下 `Hotel` 類別：

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

需要注意的第一件事是，`Hotel`類中的每個公共屬性的名稱將映射到索引定義中具有相同名稱的欄位。 如果希望每個欄位都以小寫字母（"camel case"）開頭，則可以告訴 SDK 將屬性名稱自動映射到駱駝大小寫，並帶有類上`[SerializePropertyNamesAsCamelCase]`的屬性。 此方案在 .NET 應用程式中很常見，這些應用程式執行資料繫結，其中目標架構不在應用程式開發人員的控制範圍之外，而不必違反 .NET 中的"Pascal case"命名準則。

> [!NOTE]
> Azure 認知搜索 .NET SDK 使用[NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)庫來序列化和從 JSON 序列化自訂模型物件。 如有需要，您可以自訂這個序列化的過程。 有關詳細資訊，請參閱使用[JSON.NET 的自訂序列化](#JsonDotNet)。
> 
> 

需要注意的第`IsFilterable`二件事是每個屬性都用 屬性（如 、`IsSearchable``Key`和`Analyzer`） 進行修飾。 這些屬性直接映射到[Azure 認知搜索索引中的相應欄位屬性](/rest/api/searchservice/create-index)。 類`FieldBuilder`使用這些屬性為索引構造欄位定義。

類的第`Hotel`三個重要之處在于公共屬性的資料類型。 這些屬性的 .NET 類型會對應至索引定義中，與其相當的欄位類型。 例如，`Category` 字串屬性會對應至 `category` 欄位 (此欄位屬於 `Edm.String` 類型)。 `Edm.Boolean`、、`DateTimeOffset?``bool?``Edm.DateTimeOffset`等 之間存在類似的類型映射。 類型映射的特定規則使用[Azure 認知搜索 .NET SDK 引用](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get)中`Documents.Get`的方法進行記錄。 `FieldBuilder` 類別會為您處理這項對應，但它仍有助您了解您需要對任何序列化問題進行疑難排解的情況。

你碰巧注意到了這處房產嗎`SmokingAllowed`？

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

此屬性`JsonIgnore`上的屬性告訴 不`FieldBuilder`將其序列化為欄位的索引。  這是創建用戶端計算屬性的好方法，您可以在應用程式中用作説明器。  在這種情況下，`SmokingAllowed`該屬性反映收藏中`Room``Rooms`是否有任何允許吸煙。  如果一切都是虛假的，則表示整個酒店不允許吸煙。

某些屬性（如`Address``Rooms`和） 是 .NET 類的實例。  這些屬性工作表示更複雜的資料結構，因此需要索引中具有[複雜資料類型](https://docs.microsoft.com/azure/search/search-howto-complex-data-types)的欄位。

屬性`Address`表示類中的`Address`一組多個值，定義如下：

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

此類包含用於描述美國或加拿大位址的標準值。 可以使用此類類型將邏輯欄位分組到索引中。

屬性`Rooms`表示物件陣列`Room`：

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

.NET 中的資料模型及其相應的索引架構應設計為支援您希望向最終使用者提供搜索體驗。 .NET 中的每個頂級物件（即索引中的文檔）對應于將在使用者介面中呈現的搜尋結果。 例如，在酒店搜索應用程式中，最終使用者可能希望按酒店名稱、酒店功能或特定房間的特徵進行搜索。 稍後我們將介紹一些查詢示例。

這種使用您自己的類與索引中的文檔交互的能力在兩個方向上工作;您還可以檢索搜尋結果，並使 SDK 自動將它們序列化為您選擇的類型，我們將在下一節中看到。

> [!NOTE]
> Azure 認知搜索 .NET SDK 還支援使用`Document`類動態鍵入的文檔，該類是欄位名稱的關鍵/值對應到欄位值。 當您在設計階段卻不知道索引的結構描述時，這很實用，否則要繫結到特定模型類別會很麻煩。 SDK 中所有處理文件的方法，都有可搭配 `Document` 類別使用的多載，以及使用泛型類型參數的強類型多載。 本教學課程的範例程式碼只使用了後者。 [ `Document`類](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document)從`Dictionary<string, object>`繼承。
> 
>

**為什麼您應該使用 Nullable 資料類型**

在設計`bool`自己的模型類以映射到 Azure 認知搜索索引時，我們建議聲明數值型別的屬性，如 和`int`是 null（例如，`bool?`而不是`bool`）。 如果您使用不可為 Null 的屬性，則必須保證 **** 索引中沒有任何文件的對應欄位包含 Null 值。 SDK 和 Azure 認知搜索服務都無法説明您實施此功能。

這不只是假設性的問題：如果您在類型為 `Edm.Int32` 的現有索引中新增欄位， 更新索引定義後，所有文檔都將具有該新欄位的 null 值（因為所有類型在 Azure 認知搜索中都是空的）。 如果您接著對該欄位使用 `int` 屬性不可為 Null 的模型類別，就會在嘗試擷取文件時得到類似這樣的 `JsonSerializationException`：

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

因此，我們建議您在模型類別中使用可為 Null 的類型，來做為最佳作法。

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>使用 JSON.NET 自訂序列化
SDK 會使用 JSON.NET 序列化和還原序列化文件。 如果需要，可以通過定義自己的`JsonConverter`或`IContractResolver`來自訂序列化和反序列化。 有關詳細資訊，請參閱[JSON.NET文檔](https://www.newtonsoft.com/json/help/html/Introduction.htm)。 當您希望從應用程式中調整現有模型類以用於 Azure 認知搜索和其他更高級的方案時，這非常有用。 例如，使用自訂序列化，您可以：

* 包含或排除模型類別的特定屬性儲存為文件欄位。
* 在程式碼的屬性名稱和索引的欄位名稱之間進行對應。
* 建立可用來將屬性對應至文件欄位的自訂屬性。

您可以在 GitHub 上的 Azure 認知搜索 .NET SDK 的單位測試中找到實現自訂序列化的示例。 [這個資料夾](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models)是好的起點。 它包含自訂序列化測試使用的類別。

### <a name="searching-for-documents-in-the-index"></a>搜尋索引中的文件
應用程式範例中的最後一步是在索引中搜索某些文檔：

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

每次執行查詢時，這個方法都會先建立新的 `SearchParameters` 物件。 此物件用於為查詢指定其他選項，如排序、篩選、分頁和分面。 在此方法中，我們會針對不同查詢設定 `Filter`、`Select`、`OrderBy` 和 `Top` 屬性。 所有 `SearchParameters` 屬性都記載於[這裡](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters)。

下一步是實際執行搜尋查詢， 運行搜索使用 方法`Documents.Search`完成。 針對每次查詢，我們會傳遞搜尋文字做為字串 (如果沒有搜尋文字，則傳遞 `"*"`)，並再加上先前建立的搜尋參數。 我們也指定了 `Hotel` 做為 `Documents.Search` 的類型參數，藉此告訴 SDK 將搜尋結果中的文件還原序列化為 `Hotel` 類型的物件。

> [!NOTE]
> 如需搜尋查詢運算式語法的詳細資訊，請參閱 [這裡](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)。
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

在這種情況下，我們將在任何可搜索欄位中搜索單詞"motel"一詞的整個索引，並且只想檢索`Select`參數指定的酒店名稱。 以下是結果：

    Name: Secret Point Motel

    Name: Twin Dome Motel

下一個查詢更有趣一些。  我們希望找到任何客房每晚低於100美元，並且僅返回酒店 ID 和說明的酒店：

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

此查詢會使用 OData `$filter` 運算式 (`Rooms/any(r: r/BaseRate lt 100)`)，以篩選索引中的文件。 這使用[任何運算子](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators)將"BaseRate lt 100"應用於"房間"集合中的每個專案。 您可以在此處瞭解有關 Azure 認知搜索支援的 OData 語法[的更多資訊](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax)。

查詢的結果如下：

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

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

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

最後，我們要查找與"酒店"一詞匹配的所有酒店名稱：

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

結果如下，其中包含所有欄位，因為我們並未指定 `Select` 屬性︰

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

此步驟已完成本教學課程，但別就此結束。 *後續步驟提供了其他資源，用於瞭解有關 Azure 認知搜索的更多資訊。

## <a name="next-steps"></a>後續步驟
* 瀏覽 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) 及 [REST API](https://docs.microsoft.com/rest/api/searchservice/) 參考文章。
* 檢閱 [命名規則](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) ，了解命名各種物件的規則。
* 查看 Azure 認知搜索中[支援的資料類型](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)。
