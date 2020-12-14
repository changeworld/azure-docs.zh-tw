---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: 07d118073a73f539df8ea9c5428562a8cdd26db3
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96912267"
---
語音服務的核心功能之一，就是能夠辨識並轉譯人類語音 (通常稱為語音轉文字)。 在本快速入門中，您將了解如何在您的應用程式和產品中使用語音 SDK，以執行高品質的語音轉換文字辨識。

## <a name="skip-to-samples-on-github"></a>跳至 GitHub 上的範例

如果要直接跳到範例程式碼，請參閱 GitHub 上的 [Java 快速入門範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre)。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝語音 SDK，才能執行動作。 根據您的平台，使用下列指示：

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Runtime <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>建立語音設定

若要使用語音 SDK 來呼叫語音服務，您必須建立 [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)。 此類別包含訂用帳戶的相關資訊，例如您的金鑰和關聯的區域、端點、主機或授權權杖。 使用您的金鑰和區域來建立 [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)。 請參閱[尋找金鑰和區域](../../../overview.md#find-keys-and-region)頁面，以尋找您的金鑰區域配對。

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

您可以透過其他數種方式將 [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) 初始化：

* 使用端點：傳入語音服務端點。 金鑰或授權權杖是選用項目。
* 使用主機：傳入主機位址。 金鑰或授權權杖是選用項目。
* 使用授權權杖：傳入授權權杖和相關聯的區域。

> [!NOTE]
> 無論您是執行語音辨識、語音合成、翻譯還是意圖辨識，都一定會建立設定。

## <a name="recognize-from-microphone"></a>從麥克風辨識

若要使用您的裝置麥克風辨識語音，請使用 `fromDefaultMicrophoneInput()` 建立 `AudioConfig`。 然後初始化 [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer)，傳遞您的 `audioConfig` 和 `config`。

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromMic(speechConfig);
    }

    public static void fromMic(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        System.out.println("Speak into your microphone.");
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

如果要使用「特定」的音訊輸入裝置，您需要在 `AudioConfig` 中指定裝置識別碼。 了解[如何取得音訊輸入裝置的裝置識別碼](../../../how-to-select-audio-input-devices.md)。

## <a name="recognize-from-file"></a>從檔案辨識

如果要辨識來自音訊檔案的語音而不使用麥克風，您仍然需要建立 `AudioConfig`。 不過，建立 [`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig) 時，您不會呼叫 `fromDefaultMicrophoneInput()`，而是呼叫 `fromWavFileInput()` 並傳遞檔案路徑。

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromFile(speechConfig);
    }

    public static void fromFile(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);
        
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

## <a name="error-handling"></a>錯誤處理

先前的範例只會使用 `result.getText()` 取得已辨識的文字，但是若要處理錯誤和其他回應，您必須撰寫一些程式碼來處理結果。 下列範例會評估 [`result.getReason()`](/java/api/com.microsoft.cognitiveservices.speech.recognitionresult.getreason) 和：

* 列印辨識結果：`ResultReason.RecognizedSpeech`
* 如果沒有任何相符的辨識，則通知使用者：`ResultReason.NoMatch`
* 如果發生錯誤，則列印錯誤訊息：`ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

## <a name="continuous-recognition"></a>連續辨識

先前的範例會使用可辨識單一語句的一次性辨識。 單一語句的結尾會藉由聽取結束時的靜默來決定，或是在處理音訊達 15 秒的上限時結束。

相反地，當您想要 **控制** 何時停止辨識時，可使用連續辨識。 您必須訂閱 `recognizing`、`recognized` 和 `canceled` 事件，才能取得辨識結果。 若要停止辨識，您必須呼叫 [`stopContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)。 以下範例說明如何對音訊輸入檔執行連續辨識。

首先，我們要定義輸入並初始化 [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer)：

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

接下來，我們要建立一個變數來管理語音辨識的狀態。 為此，我們會在類別範圍宣告 `Semaphore`。

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

我們會訂閱從 [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer) 傳送的事件。

* [`recognizing`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing)：包含中繼辨識結果的事件訊號。
* [`recognized`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized)：包含最終辨識結果的事件訊號 (表示成功的辨識嘗試)。
* [`sessionStopped`](/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped)：表示辨識工作階段 (作業) 結束的事件訊號。
* [`canceled`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled)：包含已取消之辨識結果的事件訊號 (表示因直接的取消要求或是傳輸或通訊協定失敗而取消的辨識嘗試)。

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

完成所有設定後，我們可以呼叫 [`startContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.startcontinuousrecognitionasync)。

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>聽寫模式

使用連續辨識時，您可以使用對應的「啟用聽寫」功能來啟用聽寫處理。 此模式會使語音設定執行個體解譯句子結構的單字描述，例如標點符號。 例如，"Do you live in town question mark" 語句會解讀為文字 "Do you live in town?"。

若要啟用聽寫模式，請在您的 [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) 上使用 [`enableDictation`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation) 方法。

```java
config.enableDictation();
```

## <a name="change-source-language"></a>變更來源語言

語音辨識的常見工作是指定輸入 (或來源) 語言。 我們來看看如何將輸入語言變更為法文。 在您的程式碼中，尋找您的 [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)，然後直接在其下方新增以下這一行。

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage) 是以字串作為引數的參數。 您可以提供支援的[地區設定/語言](../../../language-support.md)清單中的任何值。

## <a name="improve-recognition-accuracy"></a>提高辨識精確度

片語清單可用來識別音訊資料中的已知片語，例如人員的姓名或特定位置。 您可以藉由提供片語清單來改善語音辨識的精確度。

例如，如果您下一個命令 "Move to"，並且可能會說出 "Ward" 作為目的地，則您可以將 "Move to Ward" 新增為輸入項目。 新增片語會增加從音訊中辨識出 "Move to Ward" 的機率，而不是辨識為 "Move toward"

您可以將單字或完整片語新增至片語清單中。 辨識期間，片語清單中的項目都會用來提升清單中的單字和片語辨識度，即使這些項目出現在語句的中間也一樣。 

> [!IMPORTANT]
> 片語清單功能提供下列語言版本：en-US、de-DE、en-AU、en-CA、en-GB、es-ES、es-MX、fr-CA、fr-FR、it-IT、ja-JP、ko-KR、pt-BR、zh-CN

若要使用片語清單，請先建立 [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar) 物件，然後使用 [`AddPhrase`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_) 新增特定單字和片語。

[`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar) 的任何變更將會在下一次辨識時或重新連線至語音服務之後生效。

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

如果您需要清除片語清單： 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>可提高辨識精確度的其他選項

片語清單只是提高辨識精確度的選項之一。 您也可以： 

* [以自訂語音提高精確度](../../../custom-speech-overview.md)
* [以租用戶模型提高精確度](../../../tutorial-tenant-model.md)