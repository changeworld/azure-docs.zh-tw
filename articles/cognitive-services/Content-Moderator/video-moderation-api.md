---
title: 使用 C# 分析視訊內容中的不當題材 - 內容仲裁
titleSuffix: Azure Cognitive Services
description: 如何使用 Content Moderator SDK for .NET 分析視訊內容中的各種不當題材
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: how-to
ms.date: 05/18/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: cd813c6db9d03b0b7c84497e5b44f6ecdb591437
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912849"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>使用 C# 分析視訊內容中的不當題材

本文提供的資訊和程式碼範例，可協助您開始使用 [Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) \(英文\)，以掃描視訊內容中的成人或露骨內容。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。 

## <a name="prerequisites"></a>Prerequisites
- [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/) 的任何版本

## <a name="set-up-azure-resources"></a>設定 Azure 資源

Content Moderator 的影片仲裁功能是以 Azure 媒體服務 (AMS) 中免費公開預覽 **媒體處理器** 的形式提供。 Azure 媒體服務是適用於整理和串流處理視訊內容的特殊化 Azure 服務。 

### <a name="create-an-azure-media-services-account"></a>建立 Azure 媒體服務帳戶

請依照[使用 Azure 入口網站建立 Azure 媒體服務帳戶](../../media-services/previous/media-services-portal-create-account.md)中的指示，來訂閱 AMS 和建立相關聯的 Azure 儲存體帳戶。 在該儲存體帳戶中，建立新的 Blob 儲存體容器。

### <a name="create-an-azure-active-directory-application"></a>建立 Azure Active Directory 應用程式

在 Azure 入口網站中瀏覽至您新的 AMS 訂用帳戶，然後從側邊功能表選取 **API 存取** 。 選取 [使用服務主體連線到 Azure 媒體服務]  。 記下 [REST API 端點]  欄位中的值，稍後會需要它。

在 [Azure AD 應用程式]  區段中，選取 [新建]  ，並為您新的 Azure AD 應用程式註冊命名 (例如 "VideoModADApp")。 按一下 [儲存]  ，在設定應用程式的同時，請稍候幾分鐘。 然後，您應該會在頁面的 [Azure AD 應用程式]  區段底下看到您新增的應用程式註冊。

選取您的應用程式註冊，然後按一下其下方 [管理應用程式]  按鈕。 記下 [應用程式識別碼]  欄位中的值，稍後會需要它。 選取 [ **設定** 機  >  **碼** ]，然後輸入新金鑰 (的描述，例如 "VideoModKey" ) 。 按一下 [儲存]  ，然後留意到新的金鑰值。 複製這個字串，並將它儲存在安全的位置。

如需上述程序更完整的逐步解說，請參閱[利用 Azure 入口網站開始使用 Azure AD 驗證](../../media-services/previous/media-services-portal-get-started-with-aad.md)。

完成之後，您就有兩種方法可以使用影片仲裁媒體處理器。

## <a name="use-azure-media-services-explorer"></a>使用 Azure 媒體服務總管

Azure 媒體服務總管是方便使用的 AMS 前端。 您可以使用它來瀏覽您的 AMS 帳戶、上傳影片，以及使用 Content Moderator 媒體處理器來掃描內容。 從 [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases) 下載並安裝，如需詳細資訊，請參閱 [Azure 媒體服務總管部落格文章](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) \(英文\)。

![Azure 媒體服務總管與內容仲裁](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>建立 Visual Studio 專案

1. 在 Visual Studio 中建立新的 **主控台應用程式 (.NET Framework)** 專案，並將它命名為 **VideoModeration** 。 
1. 如果您的解決方案中有其他專案，請選取此專案作為單一啟始專案。
1. 取得必要的 NuGet 套件。 以滑鼠右鍵按一下 [方案總管] 中的專案，並選取 [管理 NuGet 套件]  ，然後尋找並安裝下列套件：
    - windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>新增影片仲裁程式碼

接下來，您應將本指南中的程式碼複製並貼到您的專案中，以實作基本內容仲裁案例。

### <a name="update-the-programs-using-statements"></a>更新程式的 using 陳述式

在 _Program.cs_ 檔案的頂端新增下列 `using` 陳述式。

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="set-up-resource-references"></a>設定資源參考

將下列靜態欄位新增至 _Program.cs_ 中的 **Program** 類別。 這些欄位持有連線到您的 AMS 訂用帳戶的必要資訊。 使用您在上述步驟中取得的值填入這些欄位。 請注意，`CLIENT_ID` 是您 Azure AD 應用程式的 **應用程式識別碼** 值，而 `CLIENT_SECRET` 是您為該應用程式建立的 "VideoModKey"。

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

如果您想要使用本機的視訊檔案 (最簡單的情況)，請將它新增至專案，並將其路徑輸入為 `INPUT_FILE` 值 (相對路徑是相對於執行目錄)。

您也需要在目前的目錄中建立 _preset.json_ 檔案，並使用它來指定版本號碼。 例如：

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>載入輸入影片

**Program** 類別的 **Main** 方法會建立「Azure 媒體內容」，然後建立「Azure 儲存體內容」(以防您的視訊在 Blob 儲存體中)。 其餘程式碼會從本機資料夾、blob 或 Azure 儲存體容器中的多個 blob 掃描影片。 您可以透過將其他程式碼行註解化，來嘗試所有選項。

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>建立 Azure 媒體內容

將下列方法新增至 **Program** 類別。 這會使用您的 AMS 認證來允許對 AMS 的通訊。

```csharp
// Creates a media context from azure credentials
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>新增程式碼以建立 Azure 儲存體內容

將下列方法新增至 **Program** 類別。 您會使用儲存體內容 (從您的儲存體認證建立)，來存取您的 Blob 儲存體。

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>新增程式碼以從本機檔案和 blob 建立 Azure 媒體資產

內容仲裁媒體處理器會針對 Azure 媒體服務平台中的 **資產** 執行工作。
這些方法可從本機檔案或相關聯的 blob 建立資產。

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>新增程式碼以掃描容器中的影片集合 (以 blob 表示)

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

// Get all blobs in your container
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>新增方法以執行內容仲裁工作

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>新增協助程式函式

這些方法會從 Azure 媒體服務資產下載內容仲裁輸出檔案 (JSON)，並協助追蹤仲裁工作的狀態，讓程式可將執行中狀態記錄至主控台。

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>執行程式並檢閱輸出

內容仲裁工作完成後，分析 JSON 回應。 其中包含下列項目：

- 影片資訊摘要
- 以 " **fragments** " 表示的 **畫面**
- 以 " **events** " 表示 **主要畫面格** ，其 **reviewRecommended" (= true 或 false)"** 旗標視 **成人** 和 **猥褻** 分數而定
- **start** 、 **duration** 、 **totalDuration** 和 **timestamp** 是以「刻度」為單位。 除以 **timescale** 以取得秒數。
 
> [!NOTE]
> - `adultScore` 代表可能有在特定情況下被視為明顯色情或成人內容存在，以及其預測分數。
> - `racyScore` 代表可能有在特定情況下被視為具性暗示或成人內容存在，以及其預測分數。
> - `adultScore` 和 `racyScore` 介於 0 到 1 之間。 分數越高，模型預測為適用該類別的可能性就越高。 此預覽版功能倚賴統計模型，而不是手動編碼的結果。 建議您使用自己的內容進行測試，以判斷每個類別如何符合您的需求。
> - `reviewRecommended` 會是 true 或 false，視內部分數閾值而定。 客戶應該評估是要使用此值，還是根據其內容原則決定自訂閾值。

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>下一步

了解如何從仲裁輸出產生[影片檢閱](video-reviews-quickstart-dotnet.md)。

將[文字記錄仲裁](video-transcript-moderation-review-tutorial-dotnet.md)新增至您的影片檢閱。

查看有關如何建置[完整影片和文字記錄仲裁解決方案](video-transcript-moderation-review-tutorial-dotnet.md)的詳細教學課程。

[下載 Visual Studio 解決方案](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)，以取得這個及其他適用於 .NET 的內容仲裁快速入門。