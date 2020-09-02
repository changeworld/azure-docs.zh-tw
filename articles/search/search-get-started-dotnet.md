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
ms.date: 08/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7901254463ef052f3c13b2c9fc49c31bd8ebc454
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020859"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>快速入門：使用 Azure.Search.Documents 用戶端程式庫建立搜尋索引

使用新的 [Azure.Search.Documents (第 11 版) 用戶端程式庫](/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)在 C# 中建立 .NET Core 主控台應用程式，以建立、載入及查詢搜尋索引。

[下載原始程式碼](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart-v11)以使用已完成的專案來開始，或遵循本文中的步驟來建立您自己的程式碼。

> [!NOTE]
> 在尋找較舊的版本嗎？ 請改為參閱[使用 Microsoft.Azure.Search v10 建立搜尋索引](search-get-started-dotnet-v10.md)。

## <a name="prerequisites"></a>先決條件

開始之前，請先準備下列工具和服務：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/)。

+ Azure 認知搜尋服務。 [建立服務](search-create-service-portal.md)或[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) 的任何版本。 範例程式碼已在 Visual Studio 2019 的免費社群版本上測試過。

<a name="get-service-info"></a>

## <a name="get-a-key-and-endpoint"></a>取得金鑰和端點

在每個對服務發出呼叫的要求上，都需要 URL 端點和存取金鑰。 建立搜尋服務時需要這兩項資料，因此如果您將 Azure 認知搜尋新增至您的訂用帳戶，請依照下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀] 頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

2. 在 [設定] > [金鑰] 中，取得管理員金鑰以獲得服務的完整權限 (必須有完整權限才能建立或刪除物件)。 共有兩個可交換的主要和次要金鑰。 您可以使用任一個。

   ![取得 HTTP 端點和存取金鑰](media/search-get-started-postman/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="set-up-your-project"></a>設定專案

啟動 Visual Studio，並建立可在 .NET Core 上執行的新主控台應用程式專案。 

### <a name="install-the-nuget-package"></a>安裝 NuGet 封裝

專案建立好之後，新增用戶端程式庫。 [Azure.Search.Documents 套件](https://www.nuget.org/packages/Azure.Search.Documents/)包含一個用戶端程式庫，其可提供所有用來與 .NET 中的搜尋服務搭配運作的 API。

1. 在 [工具] > [NuGet 套件管理員] 中，選取 [管理解決方案的 NuGet 套件] 中。 

1. 按一下 **[瀏覽]** 。

1. 搜尋 `Azure.Search.Documents` 並選取 11.0.0 版。

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

1. 建立兩個用戶端：[SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) 負責建立索引，而 [SearchClient](/dotnet/api/azure.search.documents.searchclient) 則用來處理現有索引。 兩者都需要服務端點和管理員 API 金鑰，以使用 create/delete 權限進行驗證。

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart-v11";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

       // Create a SearchIndexClient to send create/delete index commands
       Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
       AzureKeyCredential credential = new AzureKeyCredential(apiKey);
       SearchIndexClient idxclient = new SearchIndexClient(serviceEndpoint, credential);

       // Create a SearchClient to load and query documents
       SearchClient qryclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - 建立索引

本快速入門會建置供您隨著飯店資料一起載入並對其執行查詢的 Hotels 索引。 在此步驟中，請定義索引中的欄位。 每個欄位定義都包含名稱、資料類型和屬性，以決定欄位的使用方式。

為求簡化並方便閱讀，此範例會使用 Azure.Search.Documents 程式庫的同步方法。 不過，在生產案例中，則請使用非同步方法，以讓應用程式保持可調整和能回應的狀態。 例如，使用 [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) 而非 [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex)。

1. 將空的類別定義新增至專案中：**Hotel.cs**

1. 在 **Hotel.cs** 中，定義飯店文件的結構。

    ```csharp
    using System;
    using System.Text.Json.Serialization;

    namespace AzureSearch.SDK.Quickstart.v11
    {
        public class Hotel
        {
            [JsonPropertyName("hotelId")]
            public string Id { get; set; }

            [JsonPropertyName("hotelName")]
            public string Name { get; set; }

            [JsonPropertyName("hotelCategory")]
            public string Category { get; set; }

            [JsonPropertyName("baseRate")]
            public Int32 Rate { get; set; }

            [JsonPropertyName("lastRenovationDate")]
            public DateTime Updated { get; set; }
        }
    }
    ```

1. 在 **Program.cs** 中，指定欄位和屬性。 使用 [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) 和 [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) 來建立索引。

   ```csharp
    // Define an index schema using SearchIndex
    // Create the index using SearchIndexClient
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };

    Console.WriteLine("{0}", "Creating index...\n");
    idxclient.CreateIndex(index);
   ```

欄位上的屬性決定它如何在應用程式中使用。 例如，必須將 `IsFilterable` 屬性指派給支援篩選條件運算式的每個欄位。

相較於可搜尋字串欄位上需要 [IsSearchable](/dotnet/api/microsoft.azure.search.models.field.issearchable) 的舊版 .NET SDK，您可以使用 [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) 和 [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) 來簡化欄位定義。

與之前的版本類似，定義本身仍需要其他屬性。 例如，[IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable)、[IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) 和 [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) 必須明確屬性化，如上述範例所示。 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 載入文件

Azure 認知搜尋會搜尋服務中儲存的內容。 在此步驟中，您會載入與剛建立的飯店索引相符的 JSON 文件。

在 Azure 認知搜尋中，文件是同時屬於索引輸入與查詢輸出的資料結構。 如同從外部資料來源所取得的一樣，文件輸入可能是資料庫中的資料列，Blob 儲存體中的 Blob 或磁碟上的 JSON 文件。 在此範例中，我們採用捷徑，並針對程式碼本身中的五家飯店內嵌 JSON 文件。 

上傳文件時，您必須使用 [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) 物件。 IndexDocumentsBatch 包含[動作](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions)的集合，每個動作都包含文件與屬性，以告知 Azure 認知搜尋所應執行的動作 ([上傳、合併、刪除及 mergeOrUpload](search-what-is-data-import.md#indexing-actions))。

1. 在 **Program.cs** 中，建立文件和索引動作的陣列，然後將陣列傳遞到 `ndexDocumentsBatch`。下列文件符合飯店類別所定義的 hotels-quickstart-v11 索引。

    ```csharp
    // Load documents (using a subset of fields for brevity)
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(new Hotel { Id = "78", Name = "Upload Inn", Category = "hotel", Rate = 279, Updated = new DateTime(2018, 3, 1, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "54", Name = "Breakpoint by the Sea", Category = "motel", Rate = 162, Updated = new DateTime(2015, 9, 12, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "39", Name = "Debug Motel", Category = "motel", Rate = 159, Updated = new DateTime(2016, 11, 11, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "48", Name = "NuGet Hotel", Category = "hotel", Rate = 238, Updated = new DateTime(2016, 5, 30, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "12", Name = "Renovated Ranch", Category = "motel", Rate = 149, Updated = new DateTime(2020, 1, 24, 7, 0, 0) }));

    IndexDocumentsOptions idxoptions = new IndexDocumentsOptions { ThrowOnAnyError = true };

    Console.WriteLine("{0}", "Loading index...\n");
    qryclient.IndexDocuments(batch, idxoptions);
    ```

    一旦將 [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) 物件初始化之後，您就可以將其傳送至索引，方法是在 [SearchClient](/dotnet/api/azure.search.documents.searchclient) 物件上呼叫 [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments)。

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

1. 在 **Program.cs** 中，建立會將結果列印到主控台的 WriteDocuments 方法。

    ```csharp
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> response in searchResults.GetResults())
        {
            Hotel doc = response.Document;
            var score = response.Score;
            Console.WriteLine($"Name: {doc.Name}, Type: {doc.Category}, Rate: {doc.Rate}, Last-update: {doc.Updated}, Score: {score}");
        }

        Console.WriteLine();
    }
    ```

1. 建立 RunQueries 方法以執行查詢並傳回結果。 結果是 Hotel 物件。

    ```csharp
    private static void RunQueries(SearchClient qryclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on the term 'motel' and list the relevance score for each match...\n");

        options = new SearchOptions()
        {
            Filter = "",
            OrderBy = { "" }
        };

        response = qryclient.Search<Hotel>("motel", options);
        WriteDocuments(response);

        Console.WriteLine("Query #2: Find hotels where 'type' equals hotel...\n");

        options = new SearchOptions()
        {
            Filter = "hotelCategory eq 'hotel'",
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);

        Console.WriteLine("Query #3: Filter on rates less than $200 and sort by when the hotel was last updated...\n");

        options = new SearchOptions()
        {
            Filter = "baseRate lt 200",
            OrderBy = { "lastRenovationDate desc" }
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    }
    ```

此範例會顯示兩種[在查詢中比對字詞的方式](search-query-overview.md#types-of-queries)：全文檢索搜尋，以及篩選：

+ 全文檢索搜尋會查詢索引中可搜尋欄位內的一或多個字詞。 第一個查詢會是全文檢索搜尋。 全文檢索搜尋會產生用來排名結果的相關性分數。

+ 篩選則是布林運算式，會針對索引中的 [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) 欄位來進行評估。 篩選會查詢包含或排除值。 因此，沒有與篩選查詢相關聯的相關性分數。 最後兩個查詢會示範篩選搜尋。

您可以一起使用全文檢索搜尋與篩選或個別使用它們。

搜尋和篩選都會使用 [SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search) 方法來執行。 搜尋查詢可以在 `searchText` 字串中傳遞，而篩選運算式則可以在 [SearchOptions](/dotnet/api/azure.search.documents.searchoptions) 類別的[篩選](/dotnet/api/azure.search.documents.searchoptions.filter)屬性中傳遞。 若要篩選而不進行搜尋，只要針對[搜尋](/dotnet/api/azure.search.documents.searchclient.search)方法的 `searchText` 參數傳遞 `"*"` 即可。 若要在不進行篩選的情況下搜尋，則請將 `Filter` 屬性保留在未設定狀態，或完全不要傳入 `SearchOptions` 執行個體。

## <a name="run-the-program"></a>執行程式

按下 F5 以重建應用程式並完整執行程式。 

輸出中包含來自 [Console.WriteLIne](/dotnet/api/system.console.writeline) 的訊息，並且會加上查詢資訊和結果。

## <a name="clean-up-resources"></a>清除資源

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。 

## <a name="next-steps"></a>後續步驟

在此 C# 快速入門中，您已執行一系列的工作來建立索引、使用文件來載入索引，以及執行查詢。 在不同的階段中，我們採用捷徑，以簡化程式碼，讓程式碼更容易閱讀及傳達概念。 若您熟悉已基本概念，我們建議參閱下一篇文章以探索可讓您深入了解的替代方法與概念。 

> [!div class="nextstepaction"]
> [如何以 .NET 開發](search-howto-dotnet-sdk.md) \(部分機器翻譯\)

想要最佳化並節省您的雲端費用嗎？

> [!div class="nextstepaction"]
> [使用成本管理開始分析成本](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)