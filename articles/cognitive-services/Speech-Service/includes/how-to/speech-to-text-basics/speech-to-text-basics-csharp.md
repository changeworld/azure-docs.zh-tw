---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: b1dfbe026b7f30344d49cc2d49881c885227019d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314211"
---
## <a name="prerequisites"></a>Prerequisites

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝語音 SDK，才能執行動作。 根據您的平台，使用下列指示：

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>建立語音設定

若要使用語音 SDK 來呼叫語音服務，您必須建立 [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)。 此類別包含訂用帳戶的相關資訊，例如您的金鑰和關聯的區域、端點、主機或授權權杖。

> [!NOTE]
> 無論您是執行語音辨識、語音合成、翻譯還是意圖辨識，都一定會建立設定。

您可以透過數種方式將 [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) 初始化：

* 使用訂用帳戶：傳入金鑰和相關聯的區域。
* 使用端點：傳入語音服務端點。 金鑰或授權權杖是選用項目。
* 使用主機：傳入主機位址。 金鑰或授權權杖是選用項目。
* 使用授權權杖：傳入授權權杖和相關聯的區域。

我們來看看如何使用金鑰和區域建立 [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)。 請參閱[區域支援](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)頁面，以尋找您的區域識別碼。

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>初始化辨識器

建立 [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) 之後，下一步是初始化 [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)。 當您初始化 [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) 時，您必須為其傳入您的 `speechConfig`。 這會提供語音服務驗證您的要求所需的認證。

如果您要使用裝置的預設麥克風來辨識語音，則 [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) 應會顯示如下：

```csharp
using var recognizer = new SpeechRecognizer(speechConfig);
```

如果您想要指定音訊輸入裝置，您必須建立 [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet)，並在初始化 [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) 時提供 `audioConfig` 參數。

> [!TIP]
> [了解如何取得音訊輸入裝置的裝置識別碼](../../../how-to-select-audio-input-devices.md)。

首先，新增下列 `using` 陳述式。

```csharp
using Microsoft.CognitiveServices.Speech.Audio;
```

接著，您將能夠參考 `AudioConfig` 物件，如下所示：

```csharp
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

如果您想要提供音訊檔案而不使用麥克風，您仍然需要提供 `audioConfig`。 不過，當您建立 [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) 時，您會呼叫 `FromWavFileOutput` 並傳遞 `filename` 參數，而不是呼叫 `FromDefaultMicrophoneInput`。


```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>辨識語音

「適用於 C# 的語音 SDK」的[辨識器類別](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)會公開一些可供您用於語音辨識的方法。

* 一次性辨識 (非同步) -在非封鎖 (非同步) 模式下執行辨識。 這將會辨識單一語句。 單一語句的結尾會藉由聽取結束時的靜默來決定，或是在處理音訊達 15 秒的上限時結束。
* 連續辨識 (非同步) - 以非同步方式起始連續辨識作業。 使用者可註冊事件並處理各種應用程式狀態。 若要停止非同步連續識別，請呼叫 [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet)。

> [!NOTE]
> 深入了解如何[選擇語音辨識模式](../../../how-to-choose-recognition-mode.md)。

### <a name="single-shot-recognition"></a>一次性辨識

以下是使用 [`RecognizeOnceAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet) 進行非同步一次性辨識的範例：

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

您必須撰寫程式碼來處理結果。 此範例會評估 [`result.Reason`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason?view=azure-dotnet)：

* 列印辨識結果：`ResultReason.RecognizedSpeech`
* 如果沒有任何相符的辨識，則通知使用者：`ResultReason.NoMatch`
* 如果發生錯誤，則列印錯誤訊息：`ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
        Console.WriteLine($"    Intent not recognized.");
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

### <a name="continuous-recognition"></a>連續辨識

連續辨識比一次性辨識略為複雜一些。 您必須訂閱 `Recognizing`、`Recognized` 和 `Canceled` 事件，才能取得辨識結果。 若要停止辨識，您必須呼叫 [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet)。 以下範例說明如何對音訊輸入檔執行連續辨識。

首先，我們要定義輸入並初始化 [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)：

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```
接下來，我們要建立一個變數來管理語音辨識的狀態。 為此，我們會在先前的宣告後面宣告 `TaskCompletionSource<int>`。

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

我們會訂閱從 [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) 傳送的事件。

* [`Recognizing`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-dotnet)：包含中繼辨識結果的事件訊號。
* [`Recognized`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-dotnet)：包含最終辨識結果的事件訊號 (表示成功的辨識嘗試)。
* [`SessionStopped`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-dotnet)：表示辨識工作階段 (作業) 結束的事件訊號。
* [`Canceled`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-dotnet)：包含已取消之辨識結果的事件訊號 (表示因直接的取消要求或是傳輸或通訊協定失敗而取消的辨識嘗試)。

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

完成所有設定後，我們可以呼叫 [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet)。

```csharp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// Stops recognition.
await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>聽寫模式

使用連續辨識時，您可以使用對應的「啟用聽寫」功能來啟用聽寫處理。 此模式會使語音設定執行個體解譯句子結構的單字描述，例如標點符號。 例如，"Do you live in town question mark" 語句會解讀為文字 "Do you live in town?"。

若要啟用聽寫模式，請在您的 [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) 上使用 [`EnableDictation`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet) 方法。

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>變更來源語言

語音辨識的常見工作是指定輸入 (或來源) 語言。 我們來看看如何將輸入語言變更為義大利文。 在您的程式碼中，尋找您的 [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)，然後直接在其下方新增以下這一行。

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

[`SpeechRecognitionLanguage`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet) 屬性需要語言/地區設定格式字串。 您可以提供支援的[地區設定/語言](../../../language-support.md)清單中位於 [地區設定]  資料行內的任何值。

## <a name="improve-recognition-accuracy"></a>提高辨識精確度

有數種方式可讓您使用語音 SDK 提高辨識精確度。 我們來看看片語清單。 片語清單可用來識別音訊資料中的已知片語，例如人員的姓名或特定位置。 您可以將單字或完整片語新增至片語清單中。 辨識期間，如果音訊中包含與完整片語完全相符的項目，則會使用片語清單中的項目。 如果找不到與片語完全相符的項目，就不會協助辨識。

> [!IMPORTANT]
> 片語清單功能只能在英文中使用。

若要使用片語清單，請先建立 [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet) 物件，然後使用 [`AddPhrase`](https://docs.microsoft.com//dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-dotnet) 新增特定單字和片語。

[`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet) 的任何變更將會在下一次辨識時或重新連線至語音服務之後生效。

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