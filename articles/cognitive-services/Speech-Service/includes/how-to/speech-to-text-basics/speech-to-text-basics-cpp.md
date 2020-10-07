---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: trbye
ms.openlocfilehash: 65340bfcab76bd35901d1b3a9f3b4a8736205706
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377121"
---
語音服務的核心功能之一，就是能夠辨識並轉譯人類語音 (通常稱為語音轉文字)。 在本快速入門中，您將了解如何在您的應用程式和產品中使用語音 SDK，以執行高品質的語音轉換文字辨識。

## <a name="skip-to-samples-on-github"></a>跳至 GitHub 上的範例

如果要直接跳到範例程式碼，請參閱 GitHub 上的 [C++ 快速入門範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows)。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝語音 SDK，才能執行動作。 根據您的平台，使用下列指示：

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>建立語音設定

若要使用語音 SDK 來呼叫語音服務，您必須建立 [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)。 此類別包含訂用帳戶的相關資訊，例如您的金鑰和關聯的區域、端點、主機或授權權杖。

> [!NOTE]
> 無論您是執行語音辨識、語音合成、翻譯還是意圖辨識，都一定會建立設定。

您可以透過數種方式將 [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) 初始化：

* 使用訂用帳戶：傳入金鑰和相關聯的區域。
* 使用端點：傳入語音服務端點。 金鑰或授權權杖是選用項目。
* 使用主機：傳入主機位址。 金鑰或授權權杖是選用項目。
* 使用授權權杖：傳入授權權杖和相關聯的區域。

我們來看看如何使用金鑰和區域建立 [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)。 請參閱[區域支援](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)頁面，以尋找您的區域識別碼。

```cpp
auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>初始化辨識器

建立 [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) 之後，下一步是初始化 [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)。 當您初始化 [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) 時，您必須為其傳入您的 `speech_config`。 這會提供語音服務驗證您的要求所需的認證。

```cpp
auto recognizer = SpeechRecognizer::FromConfig(config);
```

## <a name="recognize-from-microphone-or-file"></a>從麥克風或檔案辨識

如果要指定音訊輸入裝置，必須建立 [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig)，並在初始化 [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) 時提供參數。

若要使用裝置麥克風辨識語音，請使用 `FromDefaultMicrophoneInput()` 建立 `AudioConfig`，然後在建立 `SpeechRecognizer` 物件時傳遞音訊設定。

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
```

> [!TIP]
> [了解如何取得音訊輸入裝置的裝置識別碼](../../../how-to-select-audio-input-devices.md)。

如果要辨識來自音訊檔案的語音而不使用麥克風，您仍然需要建立 `AudioConfig`。 不過，建立 [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) 時，您不會呼叫 `FromDefaultMicrophoneInput()`，而是會呼叫 `FromWavFileInput()` 並傳遞 `filename` 參數。

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

## <a name="recognize-speech"></a>辨識語音

「適用於 C++ 的語音 SDK」的[辨識器類別](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)會公開一些可供您用於語音辨識的方法。

* 一次性辨識 (非同步) -在非封鎖 (非同步) 模式下執行辨識。 這將會辨識單一語句。 單一語句的結尾會藉由聽取結束時的靜默來決定，或是在處理音訊達 15 秒的上限時結束。
* 連續辨識 (非同步) - 以非同步方式起始連續辨識作業。 使用者必須連線至處理事件，才能接收辨識結果。 若要停止非同步連續識別，請呼叫 [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync)。

> [!NOTE]
> 深入了解如何[選擇語音辨識模式](../../../how-to-choose-recognition-mode.md)。

### <a name="single-shot-recognition"></a>一次性辨識

以下是使用 [`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) 進行非同步一次性辨識的範例：

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

您必須撰寫程式碼來處理結果。 此範例會評估 [`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason)：

* 列印辨識結果：`ResultReason::RecognizedSpeech`
* 如果沒有任何相符的辨識，則通知使用者：`ResultReason::NoMatch`
* 如果發生錯誤，則列印錯誤訊息：`ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>連續辨識

連續辨識比一次性辨識略為複雜一些。 您必須訂閱 `Recognizing`、`Recognized` 和 `Canceled` 事件，才能取得辨識結果。 若要停止辨識，您必須呼叫 [StopContinuousRecognitionAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync)。 以下範例說明如何對音訊輸入檔執行連續辨識。

首先，我們要定義輸入並初始化 [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)：

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

接下來，我們要建立一個變數來管理語音辨識的狀態。 為此，我們將宣告 `promise<void>`，因為在辨識開始時我們可以安全地假設辨識尚未完成。

```cpp
promise<void> recognitionEnd;
```

我們會訂閱從 [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) 傳送的事件。

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing)：包含中繼辨識結果的事件訊號。
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized)：包含最終辨識結果的事件訊號 (表示成功的辨識嘗試)。
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped)：表示辨識工作階段 (作業) 結束的事件訊號。
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled)：包含已取消之辨識結果的事件訊號 (表示因直接的取消要求或是傳輸或通訊協定失敗而取消的辨識嘗試)。

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

完成所有設定後，我們可以呼叫 [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync)。

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>聽寫模式

使用連續辨識時，您可以使用對應的「啟用聽寫」功能來啟用聽寫處理。 此模式會使語音設定執行個體解譯句子結構的單字描述，例如標點符號。 例如，"Do you live in town question mark" 語句會解讀為文字 "Do you live in town?"。

若要啟用聽寫模式，請在您的 [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) 上使用 [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) 方法。

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>變更來源語言

語音辨識的常見工作是指定輸入 (或來源) 語言。 我們來看看如何將輸入語言變更為德文。 在您的程式碼中，尋找您的 [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)，然後直接在其下方新增以下這一行。

```cpp
config->SetSpeechRecognitionLanguage("de-DE");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) 是以字串作為引數的參數。 您可以提供支援的[地區設定/語言](../../../language-support.md)清單中的任何值。

## <a name="improve-recognition-accuracy"></a>提高辨識精確度

有數種方式可讓您使用語音 SDK 提高辨識精確度。 我們來看看片語清單。 片語清單可用來識別音訊資料中的已知片語，例如人員的姓名或特定位置。 您可以將單字或完整片語新增至片語清單中。 辨識期間，如果音訊中包含與完整片語完全相符的項目，則會使用片語清單中的項目。 如果找不到與片語完全相符的項目，就不會協助辨識。

> [!IMPORTANT]
> 片語清單功能只能在英文中使用。

若要使用片語清單，請先建立 [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) 物件，然後使用 [`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase) 新增特定單字和片語。

[`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) 的任何變更將會在下一次辨識時或重新連線至語音服務之後生效。

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

如果您需要清除片語清單： 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>可提高辨識精確度的其他選項

片語清單只是提高辨識精確度的選項之一。 您也可以： 

* [以自訂語音提高精確度](../../../how-to-custom-speech.md)
* [以租用戶模型提高精確度](../../../tutorial-tenant-model.md)
