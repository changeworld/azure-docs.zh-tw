---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: d91082ae3c0ae5e501675a06b02e2f55f20ce236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399542"
---
## <a name="prerequisites"></a>Prerequisites

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝 <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript Speech SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>，才能執行動作。 根據您的平台，使用下列指示：
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">網頁瀏覽器 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

此外，根據目標環境而定，請使用下列其中一項：

# <a name="import"></a>[import](#tab/import)

```javascript
import { readFileSync } from "fs";
import {
    AudioConfig,
    SpeechConfig,
    SpeechSynthesisOutputFormat,
    SpeechSynthesizer 
} from "microsoft-cognitiveservices-speech-sdk";
```

如需 `import` 的詳細資訊，請參閱<a href="https://javascript.info/import-export" target="_blank">匯出和匯入<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

# <a name="require"></a>[require](#tab/require)

```javascript
const readFileSync = require("fs").readFileSync;
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

如需 `require` 的相關資訊，請參閱<a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">什麼是必要項目？<span class="docon docon-navigate-external x-hidden-focus"></span></a>。


# <a name="script"></a>[script](#tab/script)

下載並將 <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span></a> *microsoft.cognitiveservices.speech.sdk.bundle.js* 檔案解壓縮，並放在您的 HTML 檔案可存取的資料夾中。

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> 如果您是以網頁瀏覽器為目標，並使用 `<script>` 標籤，則不需要 `sdk` 前置詞。 `sdk` 前置詞是用來命名 `require` 模組的別名。

---

## <a name="create-a-speech-configuration"></a>建立語音設定

若要使用語音 SDK 來呼叫語音服務，您必須建立 [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)。 此類別包含訂用帳戶的相關資訊，例如您的金鑰和關聯的區域、端點、主機或授權權杖。

> [!NOTE]
> 無論您是執行語音辨識、語音合成、翻譯還是意圖辨識，都一定會建立設定。

您可以透過數種方式將 [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) 初始化：

* 使用訂用帳戶：傳入金鑰和相關聯的區域。
* 使用端點：傳入語音服務端點。 金鑰或授權權杖是選用項目。
* 使用主機：傳入主機位址。 金鑰或授權權杖是選用項目。
* 使用授權權杖：傳入授權權杖和相關聯的區域。

在此範例中，您會使用訂用帳戶金鑰和區域來建立 [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)。 請參閱[區域支援](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)頁面，以尋找您的區域識別碼。 您也會建立一些基本的重複使用程式碼，用於本文的其餘部分，而您可針對不同的自訂項目進行修改。

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>將語音合成至檔案

接下來，您會建立 [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) 物件，以執行文字轉換語音並輸出至喇叭、檔案或其他輸出串流。 [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) 接受以下物件作為參數：在上一個步驟中建立的 [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) 物件，以及可指定應如何處理輸出結果的 [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) 物件。

若要開始，請建立 `AudioConfig`，以使用 `fromAudioFileOutput()` 靜態函式將輸出自動寫入至 `.wav` 檔案。

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

接下來，將 `speechConfig` 物件和 `audioConfig` 物件當作參數傳遞，以具現化 `SpeechSynthesizer`。 然後，執行語音合成並寫入檔案，就像使用文字字串執行 `speakTextAsync()` 一樣簡單。 結果回呼是呼叫 `synthesizer.close()` 的絕佳位置，事實上，為了讓合成正常運作，您需要進行此呼叫。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        }
    },
    error => {
        console.log(error);
        synthesizer.close();
    });
}
```

執行程式，而合成的 `.wav` 檔案會寫入至您指定的位置。 這是最基本用法的絕佳範例，但您接著會探討如何自訂輸出，以及如何將輸出回應當作記憶體內部資料流處理，以便處理自訂案例。

## <a name="synthesize-to-speaker-output"></a>合成為喇叭輸出

在某些情況下，您可以直接將合成的語音輸出至喇叭。 若要這麼做，請使用 `fromDefaultSpeakerOutput()` 靜態函式來具現化 `AudioConfig`。 這會輸出到目前作用中的輸出裝置。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>以記憶體內部資料流程的形式取得結果

在語音應用程式開發的許多案例中，您可能需要產生的音訊資料作為記憶體內部資料流，而非直接寫入至檔案。 這可讓您建立自訂行為，包括：

* 將產生的位元組陣列摘要成為自訂下游服務的可搜尋資料流。
* 將結果與其他 API 或服務整合。
* 修改音訊資料、撰寫自訂 `.wav` 標頭等。

從上一個範例進行這項變更很簡單。 首先，移除 `AudioConfig` 區塊，因為您會從這個時間點開始手動管理輸出行為，以提高掌控權。 然後在 `SpeechSynthesizer` 建構函式中針對 `AudioConfig` 傳遞 `undefined`。 

> [!NOTE]
> 針對 `AudioConfig` 傳遞 `undefined` (而非如同在上述喇叭輸出範例中加以省略)，並不會在目前作用中的輸出裝置上依預設播放音訊。

這次，您會將結果儲存至 [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest) 變數。 `SpeechSynthesisResult.audioData` 屬性會傳回輸出資料的 `ArrayBuffer`。 您可以手動使用此 `ArrayBuffer`。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

在此，您可使用所產生的 `ArrayBuffer` 物件來實作任何自訂行為。

## <a name="customize-audio-format"></a>自訂音訊格式

下一節說明如何自訂音訊輸出屬性，包括：

* 音訊檔案類型
* 採樣速率
* 位元深度

若要變更音訊格式，請在 `SpeechConfig` 物件上使用 `speechSynthesisOutputFormat` 屬性。 此屬性應該有 [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) 類型的 `enum`，您可使用此類型來選取輸出格式。 如需可用的[音訊格式清單](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest)，請參閱參考文件。

根據您的需求而定，有各種選項可供不同的檔案類型使用。 請注意，依照定義，原始格式 (例如 `Raw24Khz16BitMonoPcm`) 不包含音訊標頭。 只有在您知道下游實作可將原始位元資料流解碼，或者打算根據位元深度、採樣速率、通道數目等手動建立標頭時，才會使用原始格式。

在此範例中，您可藉由在 `SpeechConfig` 物件上設定 `speechSynthesisOutputFormat`，以指定高精確度的 RIFF 格式 `Riff24Khz16BitMonoPcm`。 與上一節的範例類似，請取得音訊 `ArrayBuffer` 資料並與其互動。

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

再次執行您的程式，會將 `.wav` 檔案寫入至指定的路徑。

## <a name="use-ssml-to-customize-speech-characteristics"></a>使用 SSML 來自訂語音特性

語音合成標記語言 (SSML) 可讓您從 XML 結構描述提交要求，以微調文字轉換語音輸出的音調、發音、說話速度、音量等等。 本節說明一些實際使用範例，但如需更詳細的指南，請參閱 [SSML 操作說明文章](../../../speech-synthesis-markup.md)。

若要開始使用 SSML 進行自訂，您可進行簡單變更來切換語音。
首先，在根專案目錄中為 SSML 組態建立新的 XML 檔案，在此範例中為 `ssml.xml`。 根元素一律為 `<speak>`，而將文字包裝在 `<voice>` 元素中，可讓您使用 `name` 參數來變更語音。 這個範例會將語音變更為男性英文 (英式) 語音。 請注意，此語音是**標準**語音，其定價和可用性與**神經**語音不同。 請參閱支援的**標準**語音[完整清單](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices)。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

接下來，您需要變更語音合成要求以參考您的 XML 檔案。 要求大多數相同，但您會使用 `speakSsmlAsync()`，而不是使用 `speakTextAsync()` 函式。 此函式應該有 XML 字串，因此您會先建立函式來載入 XML 檔案並以字串形式將其傳回。

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

如需 `readFileSync` 的詳細資訊，請參閱 <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js 檔案系統<span class="docon docon-navigate-external x-hidden-focus"></span></a>。 由此看來，結果物件會與先前的範例完全相同。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

輸出有作用，但您可進行額外幾項簡單的變更，使其聽起來更自然。 整體說話速度有點太快，因此我們會新增 `<prosody>` 標籤並將速度降低為預設速率的 **90%** 。 此外，句子中逗點之後的暫停有點太短，而且聽起來不自然。 若要修正此問題，請新增 `<break>` 標籤以延遲語音，並將時間參數設定為 **200 毫秒**。 重新執行合成，以查看這些自訂項目對輸出有何影響。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>神經語音

神經語音是由深度神經網路提供技術支援的語音合成演算法。 使用神經語音時，合成語音與人類錄音幾乎無法區分。 具有類似人類的自然韻律和清楚的文字清晰度，神經語音大幅降低使用者與 AI 系統互動時的聆聽疲勞。

若要切換成神經語音，請將 `name` 變更為其中一個[神經語音選項](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices)。 然後，新增 `mstts` 的 XML 命名空間，並將您的文字包裝在 `<mstts:express-as>` 標籤中。 使用 `style` 參數來自訂說話風格。 此範例會使用 `cheerful`，但請嘗試將其設定為 `customerservice` 或 `chat`，以查看說話風格的差異。

> [!IMPORTANT]
> **只有**在 [美國東部]  、[東南亞]  和 [西歐]  區域中建立的語音資源，才支援神經語音。

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
