---
title: 使用 .NET 來建立影片文字記錄審核項目 - Content Moderator
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用適用于 .NET 的 Azure 認知服務內容仲裁 SDK 來建立影片文字記錄評論。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: e3f7b877818056fc73f10d54b94a6b6c26c605e8
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911268"
---
# <a name="create-video-transcript-reviews-using-net"></a>使用 .NET 來建立影片文字記錄審核項目

本文提供資訊和範例程式碼，可協助您[以 C# 快速開始使用 Content Moderator SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 來執行下列操作：

- 建立給人工仲裁者的影片審核項目
- 將仲裁過的文字記錄新增至審核項目
- 發佈審核項目

## <a name="prerequisites"></a>Prerequisites

- 如果您尚未這麼做，請在內容仲裁者 [審核工具](https://contentmoderator.cognitive.microsoft.com/) 網站上登入或建立帳戶。
- 本文假設您已[仲裁影片](video-moderation-api.md)，並已在審核工具中[建立影片審核項目](video-reviews-quickstart-dotnet.md)以供進行人工決策。 您現在想要在審核工具中新增仲裁過的影片文字記錄。

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>請確定您的 API 金鑰可呼叫審核 API (作業建立)

完成前述步驟後，如果您是從 Azure 入口網站開始作業的，您可能會獲得兩個 Content Moderator 金鑰。

如果您打算在 SDK 範例中使用 Azure 提供的 API 金鑰，請依照[搭配使用 Azure 金鑰與審核 API](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) 一節中說明的步驟操作，以允許應用程式呼叫審核 API 並建立審核項目。

如果您使用審核工具所產生的免費試用版金鑰，則您的審核工具帳戶已知悉金鑰，因此不需執行額外的步驟。

## <a name="prepare-your-video-for-review"></a>準備影片以供審核

將文字記錄新增至影片審核項目。 影片必須在線上發佈。 您需要其串流端點。 串流端點可讓審核工具影片播放程式播放影片。

![影片示範縮圖](images/ams-video-demo-view.PNG)

- 複製這個 [Azure 媒體服務示範](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest)頁面上的 **URL** 作為資訊清單 URL。

## <a name="create-your-visual-studio-project"></a>建立 Visual Studio 專案

1. 將一個新的 [主控台應用程式 (.NET Framework)]  專案新增到您的解決方案。

1. 將專案命名為 **VideoTranscriptReviews** 。

1. 選取此專案作為解決方案的單一啟始專案。

### <a name="install-required-packages"></a>安裝必要的套件

安裝下列適用於 TermLists 專案的 NuGet 套件。

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>更新程式的 using 陳述式

修改程式的 using 陳述式，如下所示。


```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>新增私有屬性

將下列私用屬性新增至命名空間 **videotranscriptreviews、program** 類別 **程式** 。 `AzureEndpoint` `CMSubscriptionKey` 使用您的端點 URL 和訂用帳戶金鑰的值來更新和欄位。 您可以在 Azure 入口網站中資源的 [ **快速入門** ] 索引標籤中找到這些選項。

```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>建立 Content Moderator 用戶端物件

將下列方法定義新增至 VideoTranscriptReviews 命名空間、Program 類別。

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>建立影片審核項目

使用 **ContentModeratorClient.Reviews.CreateVideoReviews** 來建立影片審核項目。 如需詳細資訊，請參閱 [API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) \(英文\)。

**CreateVideoReviews** 具有下列必要參數：
1. 一個包含 MIME 類型的字串，這應該是 "application/json"。 
1. 您的 Content Moderator 小組名稱。
1. 一個 **IList\<CreateVideoReviewsBodyItem>** 物件。 每個 **CreateVideoReviewsBodyItem** 物件皆代表一個影片審核項目。 這個快速入門會一次建立一個審核項目。

**CreateVideoReviewsBodyItem** 具有數個屬性。 您必須至少設定下列屬性：
- **內容** 。 要審核的影片 URL。
- **ContentId** 。 要指派給影片審核項目的識別碼。
- **狀態** 。 請將值設定為 "Unpublished"。 如果您未設定此屬性，則會預設為 "Pending"，意謂著已發佈影片審核項目而正等待人工審核。 發佈影片審核項目之後，您就無法再將影片畫面、文字記錄或文字記錄仲裁結果新增至該審核項目。

> [!NOTE]
> **CreateVideoReviews** 會傳回 IList\<string>。 這些字串中每個都包含影片審核項目的識別碼。 這些識別碼是 GUID 且與 **ContentId** 屬性的值不同。

將下列方法定義新增至 VideoReviews 命名空間、Program 類別。

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> Content Moderator 服務金鑰會有每秒要求數目 (RPS) 的速率限制。 如果您超出此限制，SDK 就會擲回錯誤碼為 429 的例外狀況。
>
> 免費層金鑰有一個 RPS 速率限制。

## <a name="add-transcript-to-video-review"></a>將文字記錄新增至影片審核項目

您可以使用 **ContentModeratorClient.Reviews.AddVideoTranscript** 將文字記錄新增至影片審核項目。 **AddVideoTranscript** 具有下列必要參數：
1. 您的 Content Moderator 小組識別碼。
1. **CreateVideoReviews** 所傳回的影片審核項目識別碼。
1. 一個包含文字記錄的 **Stream** 物件。

文字記錄的格式必須是 WebVTT。 如需詳細資訊，請參閱 [WebVTT：網路影片文字播放軌格式](https://www.w3.org/TR/webvtt1/) \(英文\)。

> [!NOTE]
> 此程式使用 VTT 格式的範例文字記錄。 在真實世界解決方案中，您會使用「Azure 媒體索引器」服務從影片[產生文字記錄](../../media-services/previous/media-services-index-content.md)。

將下列方法定義新增至 VideotranscriptReviews 命名空間、Program 類別。

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>將文字記錄仲裁結果新增至影片審核項目

除了將文字記錄新增至影片審核項目之外，您還需新增該文字記錄的仲裁結果。 您可以使用 **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult** 來達到此目的。 如需詳細資訊，請參閱 [API 參考](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff) \(英文\)。

**AddVideoTranscriptModerationResult** 具有下列必要參數：
1. 一個包含 MIME 類型的字串，這應該是 "application/json"。 
1. 您的 Content Moderator 小組名稱。
1. **CreateVideoReviews** 所傳回的影片審核項目識別碼。
1. 一個 IList\<TranscriptModerationBodyItem>。 **TranscriptModerationBodyItem** 具有下列屬性：
1. **條款** 。 一個 IList\<TranscriptModerationBodyItemTermsItem>。 **TranscriptModerationBodyItemTermsItem** 具有下列屬性：
1. **索引** 。 字詞的從零開始索引。
1. **詞彙** 。 一個包含字詞的字串。
1. **時間戳記** 。 一個包含在文字記錄中找到字詞之時間 (以秒為單位) 的字串。

文字記錄的格式必須是 WebVTT。 如需詳細資訊，請參閱 [WebVTT：網路影片文字播放軌格式](https://www.w3.org/TR/webvtt1/) \(英文\)。

將下列方法定義新增至 VideoTranscriptReviews 命名空間、Program 類別。 此方法會將文字記錄提交給 **ContentModeratorClient.TextModeration.ScreenText** 方法。 此外，也會將會將結果轉譯成 IList\<TranscriptModerationBodyItem>，然後提交給 **AddVideoTranscriptModerationResult** 。

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>發佈影片審核項目

您可以使用 **ContentModeratorClient.Reviews.PublishVideoReview** 來發佈影片審核項目。 **PublishVideoReview** 具有下列必要參數：
1. 您的 Content Moderator 小組名稱。
1. **CreateVideoReviews** 所傳回的影片審核項目識別碼。

將下列方法定義新增至 VideoReviews 命名空間、Program 類別。

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>總整理

將 **Main** 定義新增至 VideoTranscriptReviews 命名空間、Program 類別。 最後，結束 Program 類別和 VideoTranscriptReviews 命名空間。

> [!NOTE]
> 此程式使用 VTT 格式的範例文字記錄。 在真實世界解決方案中，您會使用「Azure 媒體索引器」服務從影片[產生文字記錄](../../media-services/previous/media-services-index-content.md)。

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>執行程式並檢閱輸出

當您執行應用程式時，會看到以下幾行的輸出：

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>瀏覽至您的影片文字記錄審核項目

在 [ **審核** > **影片** 文字 > **記錄** ] 畫面上，移至內容仲裁審核工具中的影片文字記錄。

您會看到下列功能：
- 您新增的兩行文字記錄
- 已找到並由文字仲裁服務醒目提示的粗話字詞
- 選取文字記錄文字會從該時間戳記開始播放影片

![給人工仲裁者的影片文字記錄審核項目](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>下一步

針對這個及其他適用於 .NET 的 Content Moderator 快速入門取得 [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 和 [Visual Studio 解決方案](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)。

了解如何在審核工具中產生[影片審核項目](video-reviews-quickstart-dotnet.md)。

查看有關如何開發[完整影片審查解決方案](video-transcript-moderation-review-tutorial-dotnet.md)的詳細教學課程。