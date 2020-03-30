---
title: 使用影片索引器上傳影片及編製影片索引
titleSuffix: Azure Media Services
description: 本主題將示範如何透過影片索引器使用 API 來上傳影片及編製影片索引。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.openlocfilehash: 245eabdf4d77682c87062c2581239a554112d748
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468757"
---
# <a name="upload-and-index-your-videos"></a>上傳影片及編製影片索引  

使用影片索引器 API 上傳視訊時，您會有下列上傳選項： 

* 從 URL 上傳您的影片 (首選)、
* 將影片檔案當作要求本文中的位元組陣列傳送、
* 通過提供[資產 ID（](https://docs.microsoft.com/azure/media-services/latest/assets-concept)僅在付費帳戶中支援）使用現有的 Azure 媒體服務資產。

上傳視頻後，視頻索引子（可選）對視頻進行編碼（本文中討論）。 建立影片索引器帳戶時，您可以選擇免費試用帳戶 (您可取得特定的免費編製索引分鐘數) 或付費選項 (您不會受限於配額)。 使用免費試用時，影片索引器最多可為網站使用者提供 600 分鐘的免費編製索引，以及為 API 使用者提供 2400 分鐘的免費索引編製。 使用付費選項時，您建立的影片索引器帳戶會[連線到您的 Azure 訂用帳戶和 Azure 媒體服務帳戶](connect-to-azure.md)。 您需支付已編製索引的分鐘數，以及媒體帳戶相關費用。 

本文演示如何使用以下選項上傳和索引視頻：

* [視頻索引子網站](#website) 
* [視頻索引子 API](#apis)

## <a name="uploading-considerations-and-limitations"></a>上傳注意事項和限制
 
- 影片名稱不得超過 80 個字元。
- 根據 URL（首選）上傳視頻時，必須通過 TLS 1.2（或更高版本）保護終結點。
- URL 選項的上傳大小限制為 30GB。
- 請求 URL 長度限制為 6144 個字元，其中查詢字串 URL 長度限制為 4096 個字元。
- 位元組陣列選項的上載大小限制為 2GB。
- 位元組陣列選項在 30 分鐘後超時。
- 需要對參數中`videoURL`提供的 URL 進行編碼。
- 索引媒體服務資產與 URL 索引具有相同的限制。
- 視頻索引子對於單個檔的最大持續時間限制為 4 小時。
- URL 需要可訪問（例如公共 URL）。 

    如果是專用 URL，則需要在請求中提供訪問權杖。
- URL 必須指向有效的媒體檔案，而不是指向網頁，例如指向`www.youtube.com`頁面的連結。
- 在付費帳戶中，您每分鐘最多可以上傳 50 部電影，在試用帳戶中每分鐘最多上傳 5 部電影。

> [!Tip]
> 建議使用 .NET Framework 4.6.2 版。 或更高版本，因為舊版 .NET Framework 不會預設使用 TLS 1.2。
>
> 如果您必須使用舊版 .NET Framework，請在進行 REST API 呼叫之前，在程式碼中新增一行：  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="supported-file-formats-for-video-indexer"></a>影片索引子支援的檔案格式

如需可與影片索引子搭配使用的檔案格式清單，請參閱[輸入容器/檔案格式](../latest/media-encoder-standard-formats.md#input-containerfile-formats)一文。

## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a><a id="website"/>使用視頻索引子網站上傳和索引視頻

> [!NOTE]
> 影片名稱不得超過 80 個字元。

1. 登入 [Video Indexer](https://www.videoindexer.ai/) 網站。
2. 若要上傳影片，請按 [上傳]**** 按鈕或連結。

    ![上傳](./media/video-indexer-get-started/video-indexer-upload.png)

    上傳您的影片後，Video Indexer 會開始編製索引及分析影片。

    ![已上傳](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Video Indexer 完成分析後，您會收到通知，內含您的影片連結以及在影片中找到的簡短描述。 例如：人員、主題、OCR。

## <a name="upload-and-index-with-api"></a><a id="apis"/>使用 API 上載和索引

使用[上傳視頻](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)API 根據 URL 上傳視頻並編制索引視頻。 後面的代碼示例包括注釋出代碼，這些代碼演示如何上載位元組陣列。 

### <a name="configurations-and-params"></a>設定與參數

本節將說明一些選擇性參數及設定這些參數的時機。

#### <a name="externalid"></a>externalID 

此參數可讓您指定要與影片建立關聯的識別碼。 識別碼可以套用至外部的「影片內容管理」(VCM) 系統整合。 您可以使用指定的外部識別碼，搜尋位在影片索引器入口網站中的影片。

#### <a name="callbackurl"></a>callbackUrl

用來通知客戶下列事件的 URL (使用 POST 要求)：

- 索引狀態變更： 
    - 屬性：    
    
        |名稱|描述|
        |---|---|
        |id|視頻 ID|
        |state|影片狀態|  
    - 示例： HTTPs：\//test.com/notifyme?projectName=MyProject&id=1234abcd&狀態=已處理
- 在影片中識別到的人員：
  - 屬性
    
      |名稱|描述|
      |---|---|
      |id| 視頻 ID|
      |faceId|影片索引中出現的臉部識別碼|
      |knownPersonId|臉部模型中唯一的個人識別碼|
      |personName|人員名稱|
        
    - 示例： HTTPs：\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid_12&已知PersonId_CCA84350-89B7-4262-861C-3CAC796542A5&人名_Inigo_Montoya 

##### <a name="notes"></a>注意

- 影片索引器會傳回原始 URL 中提供的任何現有參數。
- 提供的 URL 必須進行編碼。

#### <a name="indexingpreset"></a>indexingPreset

如果原始或外部錄影包含背景雜訊，請使用此參數。 此參數會用來設定索引編製程序。 您可以指定下列值：

- `AudioOnly` – 只使用音訊 (忽略視訊) 來編製索引及擷取深入解析
- `VideoOnly`- 僅使用視頻進行索引和提取見解（忽略音訊）
- `Default` – 使用音訊和視訊來編製索引及擷取深入解析
- `DefaultWithNoiseReduction` – 從音訊及視訊編製索引及擷取深入解析的同時，在音訊串流上套用減少雜訊演算法

> [!NOTE]
> 視頻索引子可覆蓋最多兩個音訊軌道。 如果檔中有更多的音軌，它們將被視為一個軌道。<br/>
如果要單獨索引軌道，則需要提取相關的音訊檔並將其索引為`AudioOnly`。

價格取決於選取的索引編製選項。  

#### <a name="priority"></a>priority

影片索引器會根據影片的優先順序來為其編製索引。 請使用 **priority** 參數來指定索引的優先順序。 下列是有效值：**Low (低)**、**Normal** (一般，預設值) 和 **High (高)**。

僅有付費帳戶可支援 **Priority** 參數。

#### <a name="streamingpreset"></a>streamingPreset

上傳您的影片後，影片索引器會選擇性地對影片進行編碼。 然後，繼續進行索引編製並分析影片。 當影片索引器完成分析時，您會收到包含影片識別碼的通知。  

使用[上傳影片](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)或[重新編製影片索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API 時，其中一個選擇性參數為 `streamingPreset`。 如果您將 `streamingPreset` 設定為 `Default`、`SingleBitrate` 或 `AdaptiveBitrate`，就會觸發編碼程序。 完成編製索引及編碼工作後，影片就會發佈，因此您也可以對影片進行串流處理。 您想要串流影片的串流端點必須處於 [執行中]**** 狀態。

若要執行編製索引及編碼工作，[連線至您影片索引器帳戶的 Azure 媒體服務帳戶](connect-to-azure.md)需要保留單元。 如需詳細資訊，請參閱[調整媒體處理](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview)。 由於這些都是計算密集的作業，強烈建議使用 S3 單元類型。 RU 數量會定義可以平行執行的作業數目上限。 基準建議是 10 個 S3 RU。 

如果您只想要編製影片索引，但不要對影片進行編碼，請將 `streamingPreset` 設定為 `NoStreaming`。

#### <a name="videourl"></a>videoUrl

要對其編製索引的視訊/音訊檔案 URL。 URL 必須指向媒體檔案 (不支援 HTML 網頁)。 作為 URI 一部分提供的存取權杖可以保護檔案，而處理檔案的端點必須透過 TLS 1.2 或更高版本來保護。 URL 必須進行編碼。 

如果未指定 `videoUrl`，影片索引器將預期您會以多部分/表單主體內容的形式傳遞檔案。

### <a name="code-sample"></a>程式碼範例

下列 C# 程式碼片段會示範一起使用所有影片索引器 API 的方式。

#### <a name="instructions-for-running-this-code-sample"></a>運行此代碼示例的說明

將此代碼複製到開發平臺後，您需要提供兩個參數：API 管理身份驗證金鑰和視頻 URL。

* API 金鑰 – API 金鑰是您的個人 API 管理訂閱金鑰，它允許您獲取訪問權杖，以便對視頻索引子帳戶執行操作。 

    要獲取 API 金鑰，請遍通此流：

    * 巡覽到 https://api-portal.videoindexer.ai/
    * 登入
    * 轉到**產品** -> **授權** -> **訂閱**
    * 複製**主鍵**
* 視頻 URL = 要編制索引的視頻/音訊檔的 URL。 URL 必須指向媒體檔案 (不支援 HTML 網頁)。 作為 URI 一部分提供的存取權杖可以保護檔案，而處理檔案的端點必須透過 TLS 1.2 或更高版本來保護。 URL 必須進行編碼。

成功運行代碼示例的結果將包括見解小部件 URL 和播放機小部件 URL，允許您檢查分別上傳的見解和視頻。 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```

### <a name="common-errors"></a>常見錯誤

下表列出上傳作業可能會傳回的狀態碼。

|狀態碼|ErrorType (在回應本文中)|描述|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|指定帳戶中已有正在處理的相同影片。|
|400|VIDEO_ALREADY_FAILED|不到 2 小時前，指定帳戶中有相同的影片處理失敗。 API 用戶端應該等待至少 2 小時，才能重新上傳影片。|
|429||試用帳戶允許每分鐘 5 次上傳。 付費帳戶允許每分鐘 50 次上傳。|

## <a name="next-steps"></a>後續步驟

[檢查 API 生成的 Azure 視頻索引子輸出](video-indexer-output-json-v2.md)
