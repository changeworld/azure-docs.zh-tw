---
title: 近乎即時地分析影片 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 了解如何使用電腦視覺 API，針對從即時影片資料流中擷取的畫面，執行近乎即時的分析。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c86b06ff46f1ddc8d22d2ab7ec4bc8620a8c862f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933166"
---
# <a name="analyze-videos-in-near-real-time"></a>近乎即時地分析影片

本文將示範如何使用電腦視覺 API，針對從即時影片資料流中擷取的畫面，執行近乎即時的分析。 這類分析的基本要素如下：

- 從影片來源取得畫面。
- 選取要分析的畫面。
- 將這些畫面提交給 API。
- 取用從 API 呼叫傳回的每一筆分析結果。

本文中的範例會以 C# 撰寫。 若要存取程式碼，請移至 GitHub 上的[影片畫面分析範例](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)頁面。

## <a name="approaches-to-running-near-real-time-analysis"></a>近乎即時的分析執行方式

您可以使用多種方式，解決在視訊資料流上執行近乎即時分析時所遇到的問題。 本文將概述其中三個問題 (依序提高複雜程度)。

### <a name="design-an-infinite-loop"></a>設計無限迴圈

最簡單的近乎即時分析設計就是無限迴圈。 在此迴圈的每個反覆項目中，您會抓取一個畫面、加以分析，然後取用結果：

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

如果您的分析包含輕量型用戶端演算法，這個方法就很合適。 不過，在雲端進行分析時，所發生的延遲表示 API 呼叫可能需要幾秒鐘的時間。 在此期間，您將不能擷取影像，而且您的執行緒基本上不會進行任何動作。 因此，您的畫面播放速率上限將會受限於 API 呼叫的延遲。

### <a name="allow-the-api-calls-to-run-in-parallel"></a>允許以平行方式執行 API 呼叫

雖然簡易的單一執行緒迴圈很適合用於輕量型的用戶端演算法，但在面對雲端 API 呼叫的延遲時，則無法很好地因應。 若要解決這個問題，就是讓長時間執行的 API 呼叫與畫面抓取平行執行。 在 C# 中，您可以使用以工作為基礎的平行處理原則來執行這項作業。 例如，您可以執行下列程式碼：

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

透過這種方法，您可以在個別的工作中啟動每個分析。 當您繼續抓取新的畫面時，此工作會在背景中執行。 當您等候 API 呼叫傳回時，此方法可避免主執行緒遭到封鎖。 不過，此方法可能會帶來一些缺點：
* 您會損失簡單版本提供的一些保證。 也就是說，多個 API 呼叫可能會以平行方式進行，而所傳回的結果可能順序不正確。 
* 這也可能造成多個執行緒同時進入 ConsumeResult() 函式。如果函式不是安全執行緒，此情況就會相當危險。 
* 最後，這個簡易程式碼不會追蹤所建立的工作，因此例外狀況會以無訊息方式消失。 因此，您必須新增「取用者」執行緒，以便追蹤分析工作、引發例外狀況、終止長時間執行的工作，並確保系統會依正確順序一次取用一個結果。

### <a name="design-a-producer-consumer-system"></a>設計生產者-取用者系統

針對您的最後一個方法：設計「生產者-取用者」系統，您可以建立一個看起來類似上述無限迴圈的生產者執行緒。 不過，生產者不會在一有分析結果可用時便立即取用這些結果，而是只會將工作置於佇列中來進行追蹤。

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

您也可以建立取用者執行緒，以從佇列中取出工作、等候這些工作完成，然後顯示結果或引發被擲回的例外狀況。 藉由使用佇列，您就可以確保依正確順序一次取用一個結果，而無須限制系統的最大畫面播放速率。

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implement-the-solution"></a>實作解決方案

### <a name="get-started-quickly"></a>即刻開始使用

為了協助您的應用程式儘快啟動並執行，我們已實作上一節中所述的系統。 其目的在於有足夠的彈性來處理許多案例，而且容易使用。 若要存取程式碼，請移至 GitHub 上的[影片畫面分析範例](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)頁面。

此程式庫包含 `FrameGrabber` 類別，可實作上述的「生產者-取用者」系統，以處理來自網路攝影機的影片畫面。 使用者可以指定 API 呼叫的確切形式，而該類別則會使用事件，在取得新畫面或有新分析結果可供使用時，通知呼叫程式碼。

為了說明一些可能性，我們提供了兩個使用此程式庫的範例應用程式。 

第一個範例應用程式是簡單的主控台應用程式，可從預設網路攝影機抓取畫面，然後將其提交至臉部辨識服務以進行臉部偵測。 應用程式的簡化版本會在下列程式碼中重現：

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static async Task Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face service.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            await grabber.StartProcessingCameraAsync();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            await grabber.StopProcessingAsync();
        }
    }
}
```

第二個範例應用程式比較有趣。 該應用程式可讓您選擇要對影片畫面呼叫的 API。 在左側，應用程式會顯示即時影片的預覽。 在右側，應用程式會將最新的 API 結果重疊在對應的畫面上。

在大多數模式中，左側即時影片與右側視覺化分析之間會有明顯的延遲。 此延遲是進行 API 呼叫所需的時間。 在 "EmotionsWithClientFaceDetect" 模式中會有例外，此模式會先使用 OpenCV 在用戶端電腦本機上執行臉部偵測，然後才將任何影像提交給「Azure 認知服務」。 

藉由使用這種方法，您可以立即將偵測到的臉部視覺化。 然後，您可以在 API 呼叫傳回之後，再更新情緒。 這示範了「混合式」方法的可能性。 也就是說，您可以在用戶端上執行一些簡單的處理，然後再視需要使用「認知服務 API」，以更進階的分析加強這個處理。

![LiveCameraSample 應用程式顯示具有標記的影像](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>將範例整合至您的程式碼基底

若要開始使用此範例，請執行下列動作：

1. 建立 [Azure 帳戶](https://azure.microsoft.com/free/cognitive-services/)。 如果您已經有 Azure 帳戶，您可以跳到下一個步驟。
2. 在 Azure 入口網站中建立電腦視覺和 Face 的資源，以取得您的金鑰和端點。 請務必在設定期間選取免費層 (F0)。
   - [電腦視覺](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Face](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) 部署資源之後，按一下 [移至資源]，以收集每個資源的金鑰和端點。 
3. 複製 [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) \(英文\) GitHub 存放庫。
4. 在 Visual Studio 2015 或更新版本中開啟範例，然後建置並執行範例應用程式：
    - 就 BasicConsoleSample 而言，臉部金鑰會直接以硬式編碼編寫在 [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) 中。
    - 針對 LiveCameraSample，在應用程式的 [設定] 窗格中輸入金鑰。 這些金鑰會以使用者資料的形式跨工作階段保存。

當您做好整合範例的準備時，請從自己的專案參考 VideoFrameAnalyzer 程式庫。

VideoFrameAnalyzer 的影像、語音、影片和文字理解功能會使用「Azure 認知服務」。 Microsoft 會收到您透過此應用程式上傳的影像、聲音、影片和其他資料，而且可能使用它們來改善服務。 當您的應用程式將使用者的資料傳送給「Azure 認知服務」時，請務必協助保護這些使用者。

## <a name="summary"></a>摘要

在本文中，您已了解如何使用臉部辨識和電腦視覺服務，對即時影片串流執行近乎即時的分析。 您也了解如何使用我們的範例程式碼來開始著手。

歡迎您在 [GitHub 存放庫](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)中提供意見反應和建議。 若要提供更廣泛的 API 意見反應，請前往我們的 [UserVoice 網站](https://cognitive.uservoice.com/)。

