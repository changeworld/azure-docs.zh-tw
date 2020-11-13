---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 52ed0af9d678f93c5b91dbee9a44b754725c2120
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94482700"
---
語音服務的核心功能之一，就是能夠辨識並轉譯人類語音 (通常稱為語音轉文字)。 在本快速入門中，您將了解如何在您的應用程式和產品中使用語音 SDK，以執行高品質的語音轉換文字辨識。

## <a name="skip-to-samples-on-github"></a>跳至 GitHub 上的範例

如果要直接跳到範例程式碼，請參閱 GitHub 上的 [JavaScript 快速入門範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node)。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">適用於 JavaScript 的語音 SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>，才能執行動作。 根據您的平台，使用下列指示：

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">網頁瀏覽器 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

此外，根據目標環境而定，請使用下列其中一項：

# <a name="script"></a>[指令碼](#tab/script)

下載 <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">適用於 JavaScript 的語音 SDK <span class="docon docon-navigate-external x-hidden-focus"></span></a> *microsoft.cognitiveservices.speech.sdk.bundle.js* 檔案並將其解壓縮，放在您的 HTML 檔案可存取的資料夾中。

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> 如果您是以網頁瀏覽器為目標，並使用 `<script>` 標籤，則參考類別不需要 `sdk` 前置詞。 `sdk` 前置詞是用來命名 `require` 模組的別名。

# <a name="require"></a>[必要項目](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

如需 `require` 的相關資訊，請參閱<a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">什麼是必要項目？<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

---

## <a name="create-a-speech-configuration"></a>建立語音設定

若要使用語音 SDK 來呼叫語音服務，您必須建立 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest)。 此類別包含訂用帳戶的相關資訊，例如您的金鑰和關聯的區域、端點、主機或授權權杖。 使用您的金鑰和區域建立 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest)。 請參閱[尋找金鑰和區域](../../../overview.md#find-keys-and-region)頁面，以尋找您的金鑰區域配對。

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

您可以透過其他數種方式將 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) 初始化：

* 使用端點：傳入語音服務端點。 金鑰或授權權杖是選用項目。
* 使用主機：傳入主機位址。 金鑰或授權權杖是選用項目。
* 使用授權權杖：傳入授權權杖和相關聯的區域。

> [!NOTE]
> 無論您是執行語音辨識、語音合成、翻譯還是意圖辨識，都一定會建立設定。

## <a name="recognize-from-microphone-browser-only"></a>從麥克風辨識 (僅限瀏覽器)

若要使用您的裝置麥克風辨識語音，請使用 `fromDefaultMicrophoneInput()` 建立 `AudioConfig`。 然後初始化 [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest)，傳遞您的 `speechConfig` 和 `audioConfig`。

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromMic() {
    let audioConfig = sdk.AudioConfig.fromDefaultMicrophoneInput();
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    console.log('Speak into your microphone.');
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromMic();
```

如果要使用「特定」的音訊輸入裝置，您需要在 `AudioConfig` 中指定裝置識別碼。 了解 [如何取得音訊輸入裝置的裝置識別碼](../../../how-to-select-audio-input-devices.md)。

## <a name="recognize-from-file"></a>從檔案辨識 

# <a name="browser"></a>[瀏覽器](#tab/browser)

若要從以瀏覽器為基礎的 JavaScript 環境中的音訊檔案辨識語音，請使用 `fromWavFileInput()` 函式來建立 [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest)。 函式 `fromWavFileInput()` 預期會使用 JavaScript [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) 物件作為參數。

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    // wavByteContent should be a byte array of the raw wav content
    let file = new File([wavByteContent]);
    let audioConfig = sdk.AudioConfig.fromWavFileInput(file);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromFile();
```

# <a name="nodejs"></a>[Node.js](#tab/node)

若要從 Node.js 中的音訊檔案辨識語音，必須使用推送資料流的替代設計模式，因為 JavaScript [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) 物件無法用於 Node.js 執行階段。 下列程式碼：

* 使用 `createPushStream()` 建立推送資料流
* 藉由建立讀取資料流並寫入推送資料流來開啟 `.wav` 檔案
* 使用推送資料流建立音訊設定

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

使用推送資料流作為輸入時，會假設音訊資料是原始 PCM，例如略過任何標頭。
如果未略過標頭，則此 API 仍然可以在某些情況下運作，但為了獲得最佳結果，請考慮執行邏輯以讀取標頭，讓 `fs` 從音訊資料開頭開始。

---

## <a name="error-handling"></a>錯誤處理

先前的範例只會從 `result.text` 取得已辨識的文字，但是若要處理錯誤和其他回應，您必須撰寫一些程式碼來處理結果。 下列程式碼會評估 [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?preserve-view=true&view=azure-node-latest#reason) 屬性和：

* 列印辨識結果：`ResultReason.RecognizedSpeech`
* 如果沒有任何相符的辨識，則通知使用者：`ResultReason.NoMatch`
* 如果發生錯誤，則列印錯誤訊息：`ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>連續辨識

先前的範例會使用可辨識單一語句的一次性辨識。 單一語句的結尾會藉由聽取結束時的靜默來決定，或是在處理音訊達 15 秒的上限時結束。

相反地，當您想要 **控制** 何時停止辨識時，可使用連續辨識。 您必須訂閱 `Recognizing`、`Recognized` 和 `Canceled` 事件，才能取得辨識結果。 若要停止辨識，您必須呼叫 [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#stopcontinuousrecognitionasync)。 以下範例說明如何對音訊輸入檔執行連續辨識。

首先要定義輸入並初始化 [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest)：

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig);
```

再來訂閱從 [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest) 傳送的事件。

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognizing)：包含中繼辨識結果的事件訊號。
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognized)：包含最終辨識結果的事件訊號 (表示成功的辨識嘗試)。
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#sessionstopped)：表示辨識工作階段 (作業) 結束的事件訊號。
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#canceled)：包含已取消之辨識結果的事件訊號 (表示因直接的取消要求或是傳輸或通訊協定失敗而取消的辨識嘗試)。

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

設定好所有項目後，請呼叫 [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#startcontinuousrecognitionasync) 以開始辨識。

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>聽寫模式

使用連續辨識時，您可以使用對應的「啟用聽寫」功能來啟用聽寫處理。 此模式會使語音設定執行個體解譯句子結構的單字描述，例如標點符號。 例如，"Do you live in town question mark" 語句會解讀為文字 "Do you live in town?"。

若要啟用聽寫模式，請在您的 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) 上使用 [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest#enabledictation--) 方法。

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>變更來源語言

語音辨識的常見工作是指定輸入 (或來源) 語言。 我們來看看如何將輸入語言變更為義大利文。 在您的程式碼中，尋找您的 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest)，然後直接在其下方新增以下這一行。

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest#speechrecognitionlanguage) 屬性需要語言/地區設定格式字串。 您可以提供支援的[地區設定/語言](../../../language-support.md)清單中位於 [地區設定]  資料行內的任何值。

## <a name="improve-recognition-accuracy"></a>提高辨識精確度

有數種方式可讓您使用語音提高辨識正確性 讓我們看看片語清單。 片語清單可用來識別音訊資料中的已知片語，例如人員的姓名或特定位置。 您可以將單字或完整片語新增至片語清單中。 辨識期間，如果音訊中包含與完整片語完全相符的項目，則會使用片語清單中的項目。 如果找不到與片語完全相符的項目，就不會協助辨識。

> [!IMPORTANT]
> 片語清單功能只能在英文中使用。

若要使用片語清單，請先建立 [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest) 物件，然後使用 [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest#addphrase-string-) 新增特定單字和片語。

[`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest) 的任何變更將會在下一次辨識時或重新連線至語音服務之後生效。

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

如果您需要清除片語清單：

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>可提高辨識精確度的其他選項

片語清單只是提高辨識精確度的選項之一。 您也可以： 

* [以自訂語音提高精確度](../../../how-to-custom-speech.md)
* [以租用戶模型提高精確度](../../../tutorial-tenant-model.md)