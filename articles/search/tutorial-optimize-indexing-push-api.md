---
title: C# 教學課程 - 使用推送 API 將索引編製最佳化
titleSuffix: Azure Cognitive Search
description: 了解如何使用 Azure 認知搜尋的推送 API 來有效率地為資料編製索引。 本教學課程和範例程式碼採用 C#。
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: 5cafb7927bb3ec697446b37df8936da65748a9ba
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749470"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>教學課程：使用推送 API 將索引編製最佳化

Azure 認知搜尋支援[兩種基本方法](search-what-is-data-import.md)以供您將資料匯入到搜尋索引中：以程式設計方式將資料*推送*至索引，或將 [Azure 認知搜尋索引子](search-indexer-overview.md)指向受支援的資料來源，以*提取*資料。

本教學課程說明如何使用[推送模型](search-what-is-data-import.md#pushing-data-to-an-index)有效率地為資料編製索引，方法是批次處理要求並使用指數輪詢重試策略。 您可以[下載並執行應用程式](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing)。 本文會說明應用程式的重要層面，以及為資料編製索引時所應考量的因素。

本教學課程將使用 C# 和 [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search) 執行下列工作：

> [!div class="checklist"]
> * 建立索引
> * 測試各種批次大小，以判斷最有效率的大小
> * 以非同步方式為資料編製索引
> * 使用多個執行緒來增加索引編製速度
> * 使用指數輪詢重試策略來重試失敗的項目

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

本教學課程需要下列服務和工具。

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) 的任何版本。 範例程式碼和指示已在免費的 Community 版本上經過測試。

+ [建立 Azure 認知搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

<a name="get-service-info"></a>

## <a name="download-files"></a>下載檔案

本教學課程的原始程式碼位於 [Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) GitHub 存放庫的 [optimzize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) 資料夾中。

## <a name="key-considerations"></a>主要考量

在將資料推送至索引時，有幾個會影響索引編製速度的重要考量。 您可以在[為大型資料集編製索引一文](search-howto-large-index.md)中深入了解這些因素。

需要考量的六個重要因素包括：

+ **服務層級和資料分割/複本的數目** - 新增資料分割和增加層級都會增加索引編製速度。
+ **索引結構描述** - 新增欄位以及在欄位中新增其他屬性 (例如*可搜尋*、*可面向化*或*可篩選*) 都會降低索引編製速度。
+ **批次大小** - 最佳批次大小會根據索引結構描述與資料集而有所不同。
+ **執行緒/背景工作的數目** - 單一執行緒無法充分利用索引編製速度
+ **重試策略** - 請使用指數輪詢重試策略來將索引編製最佳化。
+ **網路資料傳送速度** - 資料傳送速度可能會成為限制因素。 從您的 Azure 環境內為資料編製索引可增加資料傳送速度。


## <a name="1---create-azure-cognitive-search-service"></a>1 - 建立 Azure 認知搜尋服務

若要完成本教學課程，您必須有 Azure 認知搜尋服務 (可[在入口網站中建立](search-create-service-portal.md))。 建議您使用打算在生產環境中使用的相同層級，以便精確地測試索引編製速度並予以最佳化。

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>取得 Azure 認知搜尋的管理員 API 金鑰和 URL

API 呼叫需要用到服務 URL 和存取金鑰。 建立搜尋服務時需要這兩項資料，因此如果您將 Azure 認知搜尋新增至您的訂用帳戶，請依照下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀] 頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定] >  [金鑰] 中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

   ![取得 HTTP 端點和存取金鑰](media/search-get-started-postman/get-url-key.png "取得 HTTP 端點和存取金鑰")

## <a name="2---set-up-your-environment"></a>2 - 設定您的環境

1. 啟動 Visual Studio 並開啟 **OptimizeDataIndexing.sln**。
1. 在方案總管中開啟 **appsettings.json**，以提供連線資訊。
1. 就 `searchServiceName` 而言，如果完整 URL 是 "https://my-demo-service.search.windows.net"，則應提供的服務名稱就是 "my-demo-service"。

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3 - 探索程式碼

在更新 *appsettings.json* 後，**OptimizeDataIndexing.sln** 中的範例程式應該便準備好開始建置並執行。

此程式碼衍生自 [C# 快速入門](search-get-started-dotnet.md)。 您可以在該文章中找到如何使用 .NET SDK 的基本概念詳細資訊。

這個簡單的 C#/.NET 主控台應用程式會執行下列工作：

+ 根據 C# 旅館類別的資料結構 (也會參考地址類別) 建立新的索引。
+ 測試各種批次大小，以判斷最有效率的大小
+ 以非同步方式為資料編製索引
    + 使用多個執行緒來增加索引編製速度
    + 使用指數輪詢重試策略來重試失敗的項目

 執行程式之前，請花一分鐘研讀此範例的程式碼及索引定義。 相關程式碼位於幾個檔案中：

  + **Hotel.cs** 和 **Address.cs** 包含可定義索引的結構描述
  + **DataGenerator.cs** 包含一個簡單的類別，可讓您輕鬆地建立大量飯店資料
  + **ExponentialBackoff.cs** 包含程式碼而可將編製索引的程序最佳化，如下所述
  + **Program.cs** 包含的函式可建立和刪除 Azure 認知搜尋索引、為資料的批次編製索引，以及測試不同的批次大小

### <a name="creating-the-index"></a>建立索引

此範例程式會使用 .NET SDK 來定義並建立 Azure 認知搜尋服務索引。 它會利用 [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) \(英文\) 類別，從 C# 資料模型類別產生索引結構。

資料模型會透過旅館類別來定義，其中也包含對地址類別的參考。 FieldBuilder 會向下鑽研多個類別定義，以針對索引產生複雜的資料結構。 中繼資料標記可用來定義每個欄位的屬性，例如，其是否可搜尋或可排序。

下列來自 **Hotel.cs** 檔案的程式碼片段示範如何指定單一欄位 (以及對另一個資料模型類別的參考)。

```csharp
. . .
[IsSearchable, IsSortable]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

在 **Program.cs** 檔案中，使用 `FieldBuilder.BuildForType<Hotel>()` 方法所產生的名稱和欄位集合來定義索引，然後以如下方式來建立：

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="generating-data"></a>產生資料

**DataGenerator.cs** 檔案中會實作簡單的類別，以產生用於測試的資料。 此類別的唯一目的是要讓您輕鬆地產生具有唯一識別碼的大量文件以供編製索引。

若要取得有唯一識別碼的 100,000 家飯店清單，請執行下列兩行程式碼：

```csharp
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(100000, "large");
```

此範例中有兩種飯店大小可供進行測試：**小型**和**大型**。

您索引的結構描述會顯著影響索引編製的速度。 由於此一影響，因此在完成本教學課程之後，請轉換此類別以產生符合所需索引結構描述的資料。

## <a name="4---test-batch-sizes"></a>4 - 測試批次大小

Azure 認知搜尋支援使用下列 API 來將單一或多個文件載入至索引：

+ [新增、更新或刪除文件 (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) 或 [indexBatch class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet)

以批次方式為文件編製索引會大幅提升索引編製效能。 這些批次最多可達 1000 個文件，或每批次最多 16 MB 左右。

為您的資料決定最佳批次大小是將索引編製速度最佳化的關鍵要素。 影響最佳批次大小的兩個主要因素如下：

+ 索引的結構描述
+ 資料的大小

由於最佳批次大小取決於索引和資料，因此最好的方法是測試不同批次大小，以判斷在您所處的情況下，能達到最快索引編製速度的大小。

下列函式示範用來測試批次大小的簡單方法。

```csharp
public static async Task TestBatchSizes(ISearchIndexClient indexClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocuments(indexClient, hotels);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }
}
```

由於並非所有文件的大小都相同 (但此範例中確實是如此)，因此我們必須估計要傳送到搜尋服務的資料大小。 我們使用下列函式來執行這項操作：先將物件轉換為 json，然後判斷其大小 (以位元組為單位)。 這項技術可讓我們根據 MB/s 的索引編製速度，來判斷哪些批次大小最有效率。

```csharp
public static double EstimateObjectSize(object data)
{
    // converting data to json for more accurate sizing
    var json = JsonConvert.SerializeObject(data);

    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

此函式需要 `ISearchIndexClient`，以及您想要針對每個批次大小進行測試的嘗試次數。 因為每個批次的索引編製時間可能會有一些變化，所以我們預設會針對每個批次進行三次嘗試，以讓結果更具統計意義。

```csharp
await TestBatchSizes(indexClient, numTries: 3);
```

在執行此函式時，您應該會在主控台中看到如下的輸出：

   ![測試批次大小函數的輸出](media/tutorial-optimize-data-indexing/test-batch-sizes.png "測試批次大小函數的輸出")

請找出最有效率的批次大小，然後在本教學課程的下一個步驟中使用該批次大小。 您可能會在不同的批次大小中看到 MB/s 峰值。

## <a name="5---index-data"></a>5 - 為資料編製索引

由於我們已找出所要使用的批次大小，下一步就是開始為資料編製索引。 為了有效率地為資料編製索引，此範例會：

* 使用多個執行緒/背景工作。
* 實作指數輪詢重試策略。

### <a name="use-multiple-threadsworkers"></a>使用多個執行緒/背景工作

為了充分利用 Azure 認知搜尋的索引編製速度，您很可能必須使用多個執行緒，以將批次索引要求同時傳送給服務。  

在上述重要考量中，有好幾個會影響最佳的執行緒數目。 您可以修改此範例，並使用不同的執行緒計數進行測試，以判斷您所處情況的最佳執行緒計數。 不過，只要您有數個同時執行的執行緒，應該就能利用絕大多數的效率增益。

當抵達搜尋服務的要求增加時，您可能會遇到 [HTTP 狀態碼](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)指出要求並未完全成功。 在索引編製期間，兩個常見的 HTTP 狀態碼如下：

+ **503 服務無法使用** - 此錯誤表示系統負載過重，因此目前無法處理要求。
+ **207多重狀態** - 此錯誤表示有些文件成功，但至少有一個文件失敗。

### <a name="implement-an-exponential-backoff-retry-strategy"></a>實作指數輪詢重試策略

如果發生失敗，系統應該就會使用[指數輪詢重試策略](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff)來重試要求。

Azure 認知搜尋的 .NET SDK 會自動重試 503 和其他失敗的要求，但您必須實作自己的邏輯才能重試 207。 您也可以使用 [Polly](https://github.com/App-vNext/Polly) 等開放原始碼工具來實作重試策略。 

在此範例中，我們會實行自己的指數輪詢重試策略。 為了實作此策略，我們一開始會先定義一些變數，包括失敗要求的 `maxRetryAttempts` 和初始 `delay`：

```csharp
// Create batch of documents for indexing
IndexBatch<Hotel> batch = IndexBatch.Upload(hotels);

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

請務必攔截 [IndexBatchException](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception?view=azure-dotnet)，因為這些例外狀況表示索引編製作業只部分成功 (207)。 請使用 `FindFailedActionsToRetry` 方法來重試失敗項目，以便能輕鬆地建立只包含失敗項目的新批次。

此外，`IndexBatchException` 以外的例外狀況也請攔截下來，這些例外狀況表示要求徹底失敗。 這些例外狀況較不常見，特別是在 .NET SDK 中，因為其會自動重試 503。

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        var response = await indexClient.Documents.IndexAsync(batch);
        break;
    }
    catch (IndexBatchException ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2}", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        // Find the failed items and create a new batch to retry
        batch = ex.FindFailedActionsToRetry(batch, x => x.HotelId);
        Console.WriteLine("Retrying failed documents using exponential backoff...\n");

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
    catch (Exception ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2} \n", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

從這裡開始，我們會將指數輪詢程式碼包裝到函式中，讓其可供您輕鬆呼叫。

接著建立另一個函式來管理作用中的執行緒。 為求簡化，這裡並未包含該函式，但您可以在 [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/OptimizeDataIndexing/ExponentialBackoff.cs) 中找到。 您可以使用下列命令來呼叫該函數，其中 `hotels` 是我們想要上傳的資料、`1000` 是批次大小，而 `8` 則是並行執行緒的數目：

```csharp
ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8).Wait();
```

在執行此函式時，您應該會看到如下輸出：

![資料索引編製函式的輸出](media/tutorial-optimize-data-indexing/index-data-start.png "資料索引編製函式的輸出")

當一批文件失敗時，系統會顯示錯誤以指出失敗以及批次正在重試：

![來自資料索引編製函式的錯誤](media/tutorial-optimize-data-indexing/index-data-error.png "測試批次大小函數的輸出")

函式執行完成後，您就可以確認所有文件都已新增至索引中。

## <a name="6---explore-index"></a>6 - 探索索引

您可以在執行程式之後，以程式設計方式或使用入口網站中的[**搜尋總管**](search-explorer.md)來探索已填入的搜尋索引。

### <a name="programatically"></a>程式設計方式

有兩個主要選項可用來檢查索引中的文件數目：[文件計數 API](https://docs.microsoft.com/rest/api/searchservice/count-documents) 和[取得索引統計資料 API](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics)。 這兩種途徑可能需要一些額外時間來進行更新，因此，如果所傳回的文件數目低於您原本的預期，請不要驚慌。

#### <a name="count-documents"></a>文件計數

文件計數操作可在搜尋索引中抓取文件數目的計數：

```csharp
long indexDocCount = indexClient.Documents.Count();
```

#### <a name="get-index-statistics"></a>取得索引統計資料

取得索引統計資料操作會傳回目前索引的文件計數，以及儲存體使用量。 索引統計資料所需的更新時間會超過文件計數。

```csharp
IndexGetStatisticsResult indexStats = serviceClient.Indexes.GetStatistics(configuration["SearchIndexName"]);
```

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中，開啟搜尋服務 [概觀] 頁面，然後在 [索引] 清單中尋找 **optimize-indexing** 索引。

  ![Azure 認知搜尋索引的清單](media/tutorial-optimize-data-indexing/portal-output.png "Azure 認知搜尋索引的清單")

*文件計數*和*儲存體大小*的根據是[取得索引統計資料 API](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics)，而且可能需要花幾分鐘的時間才會更新。

## <a name="reset-and-rerun"></a>重設並重新執行

在開發的早期實驗階段中若要設計反覆項目，最實用的方法是從 Azure 認知搜尋中刪除物件，並讓您的程式碼加以重建。 資源名稱是唯一的。 刪除物件可讓您使用相同的名稱加以重新建立。

本教學課程的範例程式碼會檢查是否有現有的索引，並將其刪除，以便您重新執行程式碼。

您也可以使用入口網站來刪除索引。

## <a name="clean-up-resources"></a>清除資源

如果您使用自己的訂用帳戶，當專案結束時，建議您移除不再需要的資源。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

## <a name="next-steps"></a>後續步驟

您已熟悉有效率擷取資料的概念，接下來讓我們深入了解 Lucene 查詢架構，以及全文檢索搜尋在 Azure 認知搜尋中的運作方式。

> [!div class="nextstepaction"]
> [全文檢索搜尋如何在 Azure 認知搜尋中運作](search-lucene-query-architecture.md)
