---
title: 使用影片索引器 API
titleSuffix: Azure Media Services
description: 本文介紹如何開始使用 Azure 媒體服務視頻索引器 API。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/13/2020
ms.author: juliako
ms.openlocfilehash: 82bdb177cf4d9c400d1b13ba7178658089950557
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314342"
---
# <a name="tutorial-use-the-video-indexer-api"></a>教學:使用視訊索引器 API

視頻索引器將Microsoft提供的各種音訊和視訊人工智慧 (AI) 技術整合為一個整合服務,使開發更簡單。 API 旨在使開發人員能夠專注於使用媒體 AI 技術,而不必擔心雲端平臺的規模、全球覆蓋範圍、可用性和可靠性。 您可以使用 API 上傳檔、獲取詳細的影片見解、獲取可嵌入的見解和播放器小部件的 URL 等。

創建視頻索引器帳戶時,您可以選擇免費試用帳戶(在其中獲得一定數量的免費索引分鐘數)或付費選項(不受配額限制)。 通過免費試用,視頻索引器為網站使用者提供長達 600 分鐘的免費索引,為 API 使用者提供長達 2400 分鐘的免費索引。 使用付費選項,可以建立[連接到 Azure 訂閱的視訊索引器帳戶和 Azure 媒體服務帳戶](connect-to-azure.md)。 您需支付已編製索引的分鐘數，以及 Azure 媒體服務帳戶相關費用。

本文說明開發人員可以如何善用[影片索引器 API](https://api-portal.videoindexer.ai/)。

## <a name="subscribe-to-the-api"></a>訂閱 API

1. 登入[影片索引器開發人員入口網站](https://api-portal.videoindexer.ai/)。
    
    ![登入影片索引器開發人員門戶](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * 您必須使用註冊影片索引器時所使用的提供者。
   > * 個人 Google 和 Microsoft(Outlook/即時)帳戶只能用於試用帳戶。 連線至 Azure 的帳戶需要 Azure AD。
   > * 每封電子郵件只能有一個活動帳戶。 如果用戶嘗試登錄user@gmail.comLinkedIn,稍後user@gmail.com使用 Google 登錄,則後者將顯示一個錯誤頁面,表示使用者已存在。

2. 訂閱。

    選擇「[產品](https://api-portal.videoindexer.ai/products)」選項卡。然後,選擇"授權"並訂閱。
    
    ![視訊索引器開發人員門戶中的產品選項卡](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > 系統會自動完成新使用者的授權訂閱。
    
    訂閱後,可以看到訂閱以及主密鑰和輔助密鑰。 應妥善保護金鑰。 這些金鑰應只供伺服器程式碼使用。 它們不應在用戶端(.js、.html 等)上可用。

    ![視訊索引器開發人員門戶中的訂閱和密鑰](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> 影片索引器使用者可以使用單一訂用帳戶金鑰來連線到多個影片索引器帳戶。 接著，您可以將這些影片索引器帳戶連結到不同媒體服務帳戶。

## <a name="obtain-access-token-using-the-authorization-api"></a>使用授權 API 取得存取權杖

訂閱授權 API 後,即可獲取訪問權杖。 這些存取權杖可用來對作業 API 進行驗證。

作業 API 的每次呼叫皆應與存取權杖相關聯，以符合呼叫的授權範圍。

- 使用者級別:使用者級訪問權杖允許您在**使用者**級別執行操作。 例如，取得相關聯的帳戶。
- 帳戶級別:帳戶級別訪問權杖允許您在**帳戶**級別或**視頻**級別執行操作。 例如,上傳視頻、列出所有視頻、獲取視頻見解等。
- 視頻級別:視頻級訪問權杖允許您對特定**視頻**執行操作。 例如,獲取視頻見解、下載字幕、獲取小部件等。

您可以通過指定**allowEdit_true/false**來控制這些權杖是唯讀的,還是允許編輯。

對於大多數伺服器到伺服器方案,您可能使用相同的**帳戶**令牌,因為它同時涵蓋**帳戶**操作和**視頻**操作。 但是,如果您計劃對視頻索引器(例如,從 JAvaScript)進行用戶端呼叫,則需要使用**影片**存取權杖來阻止用戶端存取整個帳戶。 這也是在用戶端中嵌入視頻索引器客戶端碼(例如,使用**獲取見解小工具**或**獲取播放機小部件**)時必須提供**視頻**訪問權杖的原因。

為了方便起見，您可以使用**授權** API > **GetAccounts** 來取得您的帳戶，而不需先取得使用者權杖。 您也可以透過有效權杖來要求取得帳戶，以略過用於取得帳戶權杖的額外呼叫。

存取權杖會在 1 小時後過期。 請先確定您的存取權杖有效，再使用作業 API。 如果過期,請再次調用授權 API 以獲取新的訪問權杖。

您已準備好開始與 API 整合。 請探索[每個影片索引器 REST API 的詳細描述](https://api-portal.videoindexer.ai/)。

## <a name="account-id"></a>帳戶識別碼

帳戶識別碼參數是所有作業 API 呼叫的必要項目。 帳戶識別碼是 GUID，可使用下列其中一個方法取得：

* 使用**影片索引器網站**來取得帳戶識別碼：

    1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。
    2. 瀏覽至 [設定]**** 頁面。
    3. 複製帳戶識別碼。

        ![視訊索引器設定和帳戶 ID](./media/video-indexer-use-apis/account-id.png)

* 使用**影片索引器開發人員入口網站**，以程式設計方式取得帳戶識別碼。

    使用[獲取帳戶](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?)API。

    > [!TIP]
    > 您可以藉由定義 `generateAccessTokens=true` 來產生帳戶的存取權杖。

* 從帳戶中的播放程式頁面 URL 取得帳戶識別碼。

    當您觀看影片時，識別碼會出現在 `accounts` 區段之後及 `videos` 區段之前。

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>建議

本節會列出使用影片索引器 API 時的一些建議。

- 如果您計劃上傳影片,建議將檔案放在某些公共網路位置(例如 OneDrive)。 取得影片的連結，並將此 URL 作為上傳檔案參數。

    提供給影片索引器的 URL 必須指向媒體 (音訊或視訊) 檔案。 有些 OneDrive 所產生的連結是包含檔案的 HTML 網頁。 對 URL 的一個簡單驗證是將其貼上到瀏覽器中— 如果檔案開始下載,則可能是一個不錯的 URL。 如果瀏覽器正在呈現一些可視化效果,則它可能不是指向檔的連結,而是指向 HTML 頁面的連結。

- 當您呼叫 API 以取得指定影片的影片深入解析時，取得的回應內容會是詳細的 JSON 輸出。 [請在本主題中參閱傳回的 JSON 詳細資料](video-indexer-output-json-v2.md)。

## <a name="code-sample"></a>程式碼範例

下列 C# 程式碼片段會示範一起使用所有影片索引器 API 的方式。

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="see-also"></a>另請參閱

- [影片索引子概觀](video-indexer-overview.md)
- [區域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>後續步驟

- [檢查輸出 JSON 的詳細資訊](video-indexer-output-json-v2.md)
- 檢視影像上傳與索引的重要的主[式程式碼](https://github.com/Azure-Samples/media-services-video-indexer)。 遵循代碼將讓您瞭解如何將我們的 API 用於基本功能。 請務必閱讀內聯評論,並注意我們的最佳實踐建議。

