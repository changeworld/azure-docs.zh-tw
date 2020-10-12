---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-js
ms.openlocfilehash: f6274769814a6d4aee4648c5e18237438c6c2af5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91377010"
---
語音服務的其中一項核心功能是能夠辨識人類語音，並將它轉譯成其他語言。 在本快速入門中，您將瞭解如何在您的應用程式和產品中使用語音 SDK，以執行高品質的語音翻譯。 本快速入門涵蓋的主題包括：

* 翻譯語音轉換文字
* 將語音翻譯成多種目的語言
* 執行直接語音轉換語音翻譯

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

下載<a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">適用於 JavaScript 的語音 SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a> *microsoft.cognitiveservices.speech.sdk.bundle.js* 檔案並將其解壓縮，放在您的 HTML 檔案可存取的資料夾中。

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> 如果您是以網頁瀏覽器為目標，並使用 `<script>` 標籤，則不需要 `sdk` 前置詞。 `sdk` 前置詞是用來命名 `require` 模組的別名。

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

如需 `import` 的詳細資訊，請參閱<a href="https://javascript.info/import-export" target="_blank">匯出和匯入<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

# <a name="require"></a>[必要項目](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

如需 `require` 的相關資訊，請參閱<a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">什麼是必要項目？<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

---

## <a name="create-a-translation-configuration"></a>建立轉譯設定

若要使用語音 SDK 呼叫轉譯服務，您需要建立 [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest) 。 此類別包含訂用帳戶的相關資訊，例如您的金鑰和關聯的區域、端點、主機或授權權杖。

> [!NOTE]
> 無論您是執行語音辨識、語音合成、翻譯還是意圖辨識，都一定會建立設定。
您可以透過數種方式將 [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest) 初始化：

* 使用訂用帳戶：傳入金鑰和相關聯的區域。
* 使用端點：傳入語音服務端點。 金鑰或授權權杖是選用項目。
* 使用主機：傳入主機位址。 金鑰或授權權杖是選用項目。
* 使用授權權杖：傳入授權權杖和相關聯的區域。

我們來看看如何使用金鑰和區域建立 [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest)。 請參閱[區域支援](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)頁面，以尋找您的區域識別碼。

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>初始化翻譯工具

建立 [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest) 之後，下一步是初始化 [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest)。 當您初始化 [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) 時，您必須為其傳入您的 `speechTranslationConfig`。 這會提供轉譯服務驗證要求所需的認證。

如果您要翻譯透過裝置預設麥克風提供的語音，以下是 [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) 應該看起來的樣子：

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

如果您想要指定音訊輸入裝置，您必須建立 [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest)，並在初始化 [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) 時提供 `audioConfig` 參數。

> [!TIP]
> [了解如何取得音訊輸入裝置的裝置識別碼](../../../how-to-select-audio-input-devices.md)。
參考 `AudioConfig` 物件，如下所示：

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

如果您想要提供音訊檔案而不使用麥克風，您仍然需要提供 `audioConfig`。 不過，這只能在以 **node.js** 為目標時完成，而且當您建立 [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest)，而不是呼叫 `fromDefaultMicrophoneInput`時，您會呼叫 `fromWavFileOutput` 並傳遞 `filename` 參數。

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>翻譯語音

適用于 JavaScript 的語音 SDK [TranslationRecognizer 類別](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) 會公開一些可用於語音翻譯的方法。

* 單次轉譯 (async) -以非封鎖 (非同步) 模式執行轉譯。 這會轉譯單一語句。 單一語句的結尾會藉由聽取結束時的靜默來決定，或是在處理音訊達 15 秒的上限時結束。
* 連續轉譯 (async) -以非同步方式起始連續轉譯作業。 使用者註冊事件並處理各種應用程式狀態。 若要停止非同步連續轉譯，請呼叫 [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync) 。

> [!NOTE]
> 深入了解如何[選擇語音辨識模式](../../../how-to-choose-recognition-mode.md)。
### <a name="specify-a-target-language"></a>指定目的語言

您必須同時指定來源語言和至少一個目的語言，才能轉譯。
您可以使用 [語音翻譯表](../../../language-support.md#speech-translation)中列出的地區設定來選擇來源語言。 在相同的連結中尋找已翻譯語言的選項。 當您想要查看文字，或想要聆聽合成翻譯的語音時，適用于目的語言的選項會有所不同。 若要從英文翻譯為德文，請修改翻譯設定物件：

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>一次性辨識

以下是使用下列各項的非同步一次性轉譯範例 [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#recognizeonceasync) ：

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

您必須撰寫程式碼來處理結果。 此範例會評估 [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult?view=azure-node-latest) 轉譯為德文的：

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

您的程式碼也可以處理轉譯時所提供的更新。
您可以使用這些更新來提供轉譯進度的視覺化意見反應。
請參閱 [此 JavaScript Node.js 範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js) 中的範例程式碼，以顯示在轉譯過程中所提供的更新。 下列程式碼也會顯示在轉譯過程中所產生的詳細資料。

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>連續轉譯

連續轉譯比單次辨識更有其相關。 您必須訂閱 `recognizing`、`recognized` 和 `canceled` 事件，才能取得辨識結果。 若要停止轉譯，您必須呼叫 [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync) 。 以下是如何在音訊輸入檔上執行連續轉譯的範例。

首先，我們要定義輸入並初始化 [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest)：

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

我們會訂閱從 [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) 傳送的事件。

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#recognizing)：包含中繼轉譯結果的事件信號。
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#recognized)：表示包含最終轉譯結果的事件信號， (表示成功的轉譯嘗試) 。
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#sessionstopped)：表示轉譯會話結束 (操作) 的事件信號。
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#canceled)：包含已取消之轉譯結果的事件信號， (表示已取消為結果或直接取消要求的轉譯嘗試，或) 傳輸或通訊協定失敗。

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
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

完成所有設定後，我們可以呼叫 [`startContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#startcontinuousrecognitionasync)。

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>選擇來源語言

語音翻譯的常見工作是指定輸入 (或來源) 語言。 我們來看看如何將輸入語言變更為義大利文。 在您的程式碼中，尋找您的 [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest) ，然後在它的正下方新增下列程式程式碼。

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest#speechrecognitionlanguage) 屬性需要語言/地區設定格式字串。 您可以提供支援的[地區設定/語言](../../../language-support.md)清單中位於 [地區設定]  資料行內的任何值。

## <a name="choose-one-or-more-target-languages"></a>選擇一或多個目的語言

語音 SDK 可以平行轉譯成多個目的語言。 可用的目的語言與來源語言清單有點不同，您可以使用語言代碼（而不是地區設定）來指定目的語言。
在 [[語言支援] 頁面上，查看語音翻譯表](../../../language-support.md#speech-translation)中的文字目的語言代碼清單。 您也可以在該處找到轉譯成合成語言的詳細資料。

下列程式碼會以目的語言的形式新增德文：

```javascript
translationConfig.addTargetLanguage("de");
```

因為可能會有多個目的語言翻譯，所以您的程式碼必須在檢查結果時指定目的語言。 下列程式碼會取得德文的翻譯結果。

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```
