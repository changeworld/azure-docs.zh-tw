---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 3ddd7b1139396a5952d1575ea72b00d5dfa14fab
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95095979"
---
語音服務的核心功能之一，就是能夠辨識並轉譯人類語音 (通常稱為語音轉文字)。 在本快速入門中，您將了解如何在您的應用程式和產品中使用語音 SDK，以執行高品質的語音轉換文字辨識。

## <a name="skip-to-samples-on-github"></a>跳至 GitHub 上的範例

如果要直接跳到範例程式碼，請參閱 GitHub 上的 [C# 快速入門範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet)。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

如果想知道套件名稱以開始使用，請在 NuGet 主控台中執行 `Install-Package Microsoft.CognitiveServices.Speech`。

如需平台特定的安裝指示，請參閱下列連結：

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>建立語音設定

若要使用語音 SDK 來呼叫語音服務，您必須建立 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)。 此類別包含訂用帳戶的相關資訊，例如您的金鑰和關聯的區域、端點、主機或授權權杖。 使用您的金鑰和區域來建立 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)。 請參閱[尋找金鑰和區域](../../../overview.md#find-keys-and-region)頁面，以尋找您的金鑰區域配對。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

您可以透過其他數種方式將 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) 初始化：

* 使用端點：傳入語音服務端點。 金鑰或授權權杖是選用項目。
* 使用主機：傳入主機位址。 金鑰或授權權杖是選用項目。
* 使用授權權杖：傳入授權權杖和相關聯的區域。

> [!NOTE]
> 無論您是執行語音辨識、語音合成、翻譯還是意圖辨識，都一定會建立設定。

## <a name="recognize-from-microphone"></a>從麥克風辨識

若要使用您的裝置麥克風辨識語音，請使用 `FromDefaultMicrophoneInput()` 建立 `AudioConfig`。 然後初始化 [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)，傳遞您的 `audioConfig` 和 `speechConfig`。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromMic(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        Console.WriteLine("Speak into your microphone.");
        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromMic(speechConfig);
    }
}
```

如果要使用「特定」的音訊輸入裝置，您需要在 `AudioConfig` 中指定裝置識別碼。 了解[如何取得音訊輸入裝置的裝置識別碼](../../../how-to-select-audio-input-devices.md)。

## <a name="recognize-from-file"></a>從檔案辨識

如果要辨識來自音訊檔案的語音而不使用麥克風，您仍然需要建立 `AudioConfig`。 不過，建立 [`AudioConfig`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) 時，您不會呼叫 `FromDefaultMicrophoneInput()`，而是會呼叫 `FromWavFileInput()` 並傳遞檔案路徑。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromFile(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromWavFileInput("PathToFile.wav");
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromFile(speechConfig);
    }
}
```

## <a name="recognize-from-in-memory-stream"></a>從記憶體內部資料流辨識

對於許多使用案例，可能是您的音訊資料來自 Blob 儲存體，或已經是記憶體中的 `byte[]` 或類似的原始資料結構。 下列範例會使用 [`PushAudioInputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pushaudioinputstream?view=azure-dotnet) 來辨識語音，這基本上是抽象的記憶體資料流。 範例程式碼會執行下列各項：

* 使用可接受 `byte[]` 的 `Write()` 函式，將未經處理的音訊資料 (PCM) 寫入 `PushAudioInputStream`。
* 使用 `FileReader` 讀取 `.wav` 檔案以供示範之用，但如果您在 `byte[]` 中已經有音訊資料，則可以直接跳到將內容寫入至輸入資料流。
* 預設格式為 16 位元，16khz 單一 PCM。 若要自訂格式，您可以使用靜態函數 `AudioStreamFormat.GetWaveFormatPCM(sampleRate, (byte)bitRate, (byte)channels)`，將 [`AudioStreamFormat`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audiostreamformat?view=azure-dotnet) 物件傳遞至 `CreatePushStream()`。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromStream(SpeechConfig speechConfig)
    {
        var reader = new BinaryReader(File.OpenRead("PathToFile.wav"));
        using var audioInputStream = AudioInputStream.CreatePushStream();
        using var audioConfig = AudioConfig.FromStreamInput(audioInputStream);
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        byte[] readBytes;
        do
        {
            readBytes = reader.ReadBytes(1024);
            audioInputStream.Write(readBytes, readBytes.Length);
        } while (readBytes.Length > 0);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromStream(speechConfig);
    }
}
```

使用推送串流作為輸入時，會假設音訊資料是原始 PCM，例如略過任何標頭。
如果未略過標頭，則此 API 仍然可以在某些情況下運作，但為了獲得最佳結果，請考慮執行邏輯以讀取標頭，讓 `byte[]` 從音訊資料開頭開始。

## <a name="error-handling"></a>錯誤處理

先前的範例只會從 `result.text` 取得已辨識的文字，但是若要處理錯誤和其他回應，您必須撰寫一些程式碼來處理結果。 下列程式碼會評估 [`result.Reason`](/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason?preserve-view=true&view=azure-dotnet) 屬性和：

* 列印辨識結果：`ResultReason.RecognizedSpeech`
* 如果沒有任何相符的辨識，則通知使用者：`ResultReason.NoMatch`
* 如果發生錯誤，則列印錯誤訊息：`ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
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

## <a name="continuous-recognition"></a>連續辨識

先前的範例會使用可辨識單一語句的一次性辨識。 單一語句的結尾會藉由聽取結束時的靜默來決定，或是在處理音訊達 15 秒的上限時結束。

相反地，當您想要 **控制** 何時停止辨識時，可使用連續辨識。 您必須訂閱 `Recognizing`、`Recognized` 和 `Canceled` 事件，才能取得辨識結果。 若要停止辨識，您必須呼叫 [`StopContinuousRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?preserve-view=true&view=azure-dotnet)。 以下範例說明如何對音訊輸入檔執行連續辨識。

首先要定義輸入並初始化 [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?preserve-view=true&view=azure-dotnet)：

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

接下來建立一個 `TaskCompletionSource<int>` 來管理語音辨識的狀態。

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

再來訂閱從 [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) 傳送的事件。

* [`Recognizing`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing?preserve-view=true&view=azure-dotnet)：包含中繼辨識結果的事件訊號。
* [`Recognized`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized?preserve-view=true&view=azure-dotnet)：包含最終辨識結果的事件訊號 (表示成功的辨識嘗試)。
* [`SessionStopped`](/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped?preserve-view=true&view=azure-dotnet)：表示辨識工作階段 (作業) 結束的事件訊號。
* [`Canceled`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled?preserve-view=true&view=azure-dotnet)：包含已取消之辨識結果的事件訊號 (表示因直接的取消要求或是傳輸或通訊協定失敗而取消的辨識嘗試)。

```csharp
recognizer.Recognizing += (s, e) =>
{
    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
};

recognizer.Recognized += (s, e) =>
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
    }
    else if (e.Result.Reason == ResultReason.NoMatch)
    {
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled += (s, e) =>
{
    Console.WriteLine($"CANCELED: Reason={e.Reason}");

    if (e.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }

    stopRecognition.TrySetResult(0);
};

recognizer.SessionStopped += (s, e) =>
{
    Console.WriteLine("\n    Session stopped event.");
    stopRecognition.TrySetResult(0);
};
```

設定好所有項目後，請呼叫 `StartContinuousRecognitionAsync` 以開始辨識。

```csharp
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// make the following call at some point to stop recognition.
// await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>聽寫模式

使用連續辨識時，您可以使用對應的「啟用聽寫」功能來啟用聽寫處理。 此模式會使語音設定執行個體解譯句子結構的單字描述，例如標點符號。 例如，"Do you live in town question mark" 語句會解讀為文字 "Do you live in town?"。

若要啟用聽寫模式，請在您的 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet) 上使用 [`EnableDictation`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?preserve-view=true&view=azure-dotnet) 方法。

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>變更來源語言

語音辨識的常見工作是指定輸入 (或來源) 語言。 我們來看看如何將輸入語言變更為義大利文。 在您的程式碼中，尋找您的 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet)，然後直接在其下方新增以下這一行。

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

[`SpeechRecognitionLanguage`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?preserve-view=true&view=azure-dotnet) 屬性需要語言/地區設定格式字串。 您可以提供支援的[地區設定/語言](../../../language-support.md)清單中位於 [地區設定]  資料行內的任何值。

## <a name="improve-recognition-accuracy"></a>提高辨識精確度

有數種方式可讓您使用語音 SDK 提高辨識精確度。 我們來看看片語清單。 片語清單可用來識別音訊資料中的已知片語，例如人員的姓名或特定位置。 您可以將單字或完整片語新增至片語清單中。 辨識期間，如果音訊中包含與完整片語完全相符的項目，則會使用片語清單中的項目。 如果找不到與片語完全相符的項目，就不會協助辨識。

> [!IMPORTANT]
> 片語清單功能只能在英文中使用。

若要使用片語清單，請先建立 [`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-dotnet) 物件，然後使用 [`AddPhrase`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?preserve-view=true&view=azure-dotnet) 新增特定單字和片語。

[`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-dotnet) 的任何變更將會在下一次辨識時或重新連線至語音服務之後生效。

```csharp
var phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Supercalifragilisticexpialidocious");
```

如果您需要清除片語清單： 

```csharp
phraseList.Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>可提高辨識精確度的其他選項

片語清單只是提高辨識精確度的選項之一。 您也可以： 

* [以自訂語音提高精確度](../../../how-to-custom-speech.md)
* [以租用戶模型提高精確度](../../../tutorial-tenant-model.md)