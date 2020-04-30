---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: d9bc744292922fd127e983392199fa21554d3c62
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400446"
---
## <a name="prerequisites"></a>Prerequisites

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中開啟您的專案

第一個步驟是確定您已在 Visual Studio 中開啟專案。

1. 啟動 Visual Studio 2019。
2. 載入您的專案，並開啟 `Program.cs`。
3. 下載<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a>，並將其新增至您的專案。
    - 儲存 `Program.cs` 檔案旁邊的 whatstheweatherlike.wav  檔案。
    - 在 [方案總管]  中，以滑鼠右鍵按一下專案，然後選取 [新增] > [現有項目]  。
    - 選取 whatstheweatherlike.wav  檔案，然後選取 [新增]  按鈕。
    - 以滑鼠右鍵按一下新增的檔案，然後選取 [屬性]  。
    - 將 [複製到輸出目錄]  變更為 [永遠複製]  。

## <a name="start-with-some-boilerplate-code"></a>從重複使用程式碼開始著手

我們將新增程式碼，作為專案的基本架構。 請注意，您已建立名為 `RecognizeSpeechAsync()` 的非同步方法。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
        }
    }
}
```

## <a name="create-a-speech-configuration"></a>建立語音設定

您必須先建立使用訂用帳戶金鑰和訂用帳戶區域的設定，才可以初始化 `SpeechRecognizer` 物件。 在 `RecognizeSpeechAsync()` 方法中插入此程式碼。

> [!NOTE]
> 此範例會使用 `FromSubscription()` 方法來建置 `SpeechConfig`。 如需可用方法的完整清單，請參閱 [SpeechConfig 類別](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) \(英文\)。
> 語音 SDK 會預設為使用 en-us 來辨識語言，如需選擇來源語言的詳細資訊，請參閱[指定語音轉換文字的來源語言](../../../../how-to-specify-source-language.md)。

```csharp
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="create-an-audio-configuration"></a>建立音訊設定

現在，您需要建立指向音訊檔案的 `AudioConfig` 物件。 這個物件是在 using 陳述式內建立的，可確保適當釋放未受控資源。 將此程式碼插入您的語音設定下方的 `RecognizeSpeechAsync()` 方法中。

```csharp
using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
{
}
```

## <a name="initialize-a-speechrecognizer"></a>初始化 SpeechRecognizer

現在，讓我們使用稍早建立的 `SpeechConfig` 和 `AudioConfig` 物件來建立 `SpeechRecognizer` 物件。 這個物件也是在 using 陳述式內建立的，可確保適當釋放未受控資源。 在 `RecognizeSpeechAsync()` 方法中插入此程式碼，在包裝 ```AudioConfig``` 物件的 using 陳述式內。

```csharp
using (var recognizer = new SpeechRecognizer(config, audioInput))
{
}
```

## <a name="recognize-a-phrase"></a>辨識片語

從 `SpeechRecognizer` 物件，您將呼叫 `RecognizeOnceAsync()` 方法。 此方法可讓語音服務知道您要傳送單一片語以進行辨識，且一旦識別出該片語即停止辨識語音。

在 using 陳述式中，新增下列程式碼：

```csharp
Console.WriteLine("Recognizing first result...");
var result = await recognizer.RecognizeOnceAsync();
```

## <a name="display-the-recognition-results-or-errors"></a>顯示辨識結果 (或錯誤)

當語音服務傳回辨識結果時，建議您對其執行一些動作。 為了簡單起見，我們將結果列印到主控台。

在 using 陳述式中的 `RecognizeOnceAsync()` 下方，新增此程式碼：

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"We recognized: {result.Text}");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

## <a name="check-your-code"></a>檢查您的程式碼

此時，您的程式碼應會如下所示：

```csharp
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
            using (var recognizer = new SpeechRecognizer(config, audioInput))
            {
                Console.WriteLine("Recognizing first result...");
                var result = await recognizer.RecognizeOnceAsync();

                switch (result.Reason)
                {
                    case ResultReason.RecognizedSpeech:
                        Console.WriteLine($"We recognized: {result.Text}");
                        break;
                    case ResultReason.NoMatch:
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        break;
                    case ResultReason.Canceled:
                        var cancellation = CancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
                
                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                        break;
                }
            }
        }
    }
}
```

## <a name="build-and-run-your-app"></a>建置並執行您的應用程式

現在您已準備好使用語音服務來建立應用程式，並測試我們的語音辨識。

1. 編譯程式碼：從 Visual Studio  的功能表列中，選擇 [建置]   > [建置解決方案]  。
2. 啟動您的應用程式：從功能表列中，選擇 [偵錯]   > [開始偵錯]  ，或按 **F5**。
3. 開始辨識：您的音訊檔會傳送至語音服務、轉譯為文字，並在主控台中轉譯。

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
