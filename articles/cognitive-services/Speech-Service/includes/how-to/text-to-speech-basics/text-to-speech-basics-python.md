---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: adc40f8c949c50570533a19d46f4b5dafa79325c
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425196"
---
在本快速入門中，您將了解如何使用語音 SDK 進行文字轉換語音合成的常見設計模式。 首先，您會進行基本設定與合成，並繼續處理更多用於自訂應用程式開發的高階範例，包括：

* 以記憶體內部資料流的形式取得回應
* 自訂輸出採樣速率和位元速率
* 使用 SSML 提交合成要求 (語音合成標記語言)
* 使用神經語音

## <a name="skip-to-samples-on-github"></a>跳至 GitHub 上的範例

如果要直接跳到範例程式碼，請參閱 GitHub 上的 [Python 快速入門範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python/text-to-speech)。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝語音 SDK，才能執行動作。

```Python
pip install azure-cognitiveservices-speech
```

如果您在使用 macOS 時遇到安裝問題，您可能需要先執行此命令。

```Python
python3 -m pip install --upgrade pip
```

安裝語音 SDK 之後，請在指令碼的頂端包含下列 import 陳述式。

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>建立語音設定

若要使用語音 SDK 來呼叫語音服務，您必須建立 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python)。 此類別包含訂用帳戶的相關資訊，例如您的金鑰和關聯的區域、端點、主機或授權權杖。

> [!NOTE]
> 無論您是執行語音辨識、語音合成、翻譯還是意圖辨識，都一定會建立設定。

您可以透過數種方式將 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python) 初始化：

* 使用訂用帳戶：傳入金鑰和相關聯的區域。
* 使用端點：傳入語音服務端點。 金鑰或授權權杖是選用項目。
* 使用主機：傳入主機位址。 金鑰或授權權杖是選用項目。
* 使用授權權杖：傳入授權權杖和相關聯的區域。

在此範例中，您會使用訂用帳戶金鑰和區域來建立 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python)。 請依照[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)中的步驟，來取得這些認證。

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>將語音合成至檔案

接下來，您會建立 [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?preserve-view=true&view=azure-python) 物件，以執行文字轉換語音並輸出至喇叭、檔案或其他輸出串流。 [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?preserve-view=true&view=azure-python) 接受以下物件作為參數：在上一個步驟中建立的 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python) 物件，以及可指定應如何處理輸出結果的 [`AudioOutputConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig?preserve-view=true&view=azure-python) 物件。

若要開始，請建立 `AudioOutputConfig`，以使用 `filename` 建構函式參數，將輸出自動寫入至 `.wav` 檔案。

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

接下來，將 `speech_config` 物件和 `audio_config` 物件當作參數傳遞，以具現化 `SpeechSynthesizer`。 然後，執行語音合成並寫入檔案，就像使用文字字串執行 `speak_text_async()` 一樣簡單。

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

執行程式，而合成的 `.wav` 檔案會寫入至您指定的位置。 這是最基本用法的絕佳範例，但您接著會探討如何自訂輸出，以及如何將輸出回應當作記憶體內部資料流處理，以便處理自訂案例。

## <a name="synthesize-to-speaker-output"></a>合成為喇叭輸出

在某些情況下，您可以直接將合成的語音輸出至喇叭。 若要這麼做，請使用上一節中的範例，但藉由移除 `filename` 參數來變更 `AudioOutputConfig`，然後設定 `use_default_speaker=True`。 這會輸出到目前作用中的輸出裝置。

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>以記憶體內部資料流程的形式取得結果

在語音應用程式開發的許多案例中，您可能需要產生的音訊資料作為記憶體內部資料流，而非直接寫入至檔案。 這可讓您建立自訂行為，包括：

* 將產生的位元組陣列摘要成為自訂下游服務的可搜尋資料流。
* 將結果與其他 API 或服務整合。
* 修改音訊資料、撰寫自訂 `.wav` 標頭等。

從上一個範例進行這項變更很簡單。 首先，移除 `AudioConfig`，因為您會從這個時間點開始手動管理輸出行為，以提高掌控權。 然後在 `SpeechSynthesizer` 建構函式中針對 `AudioConfig` 傳遞 `None`。 

> [!NOTE]
> 針對 `AudioConfig` 傳遞 `None` (而非如同在上述喇叭輸出範例中加以省略)，並不會在目前作用中的輸出裝置上依預設播放音訊。

這次，您會將結果儲存至 [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult?preserve-view=true&view=azure-python) 變數。 `audio_data` 屬性包含輸出資料的 `bytes` 物件。 您可以手動處理此物件，也可以使用 [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?preserve-view=true&view=azure-python) 類別來管理記憶體內部資料流。 在此範例中，您會使用 `AudioDataStream` 建構函式，從結果中取得資料流。

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

在此，您可使用所產生的 `stream` 物件來實作任何自訂行為。

## <a name="customize-audio-format"></a>自訂音訊格式

下一節說明如何自訂音訊輸出屬性，包括：

* 音訊檔案類型
* 採樣速率
* 位元深度

若要變更音訊格式，請在 `SpeechConfig` 物件上使用 `set_speech_synthesis_output_format()` 函式。 此函式應該有 [`SpeechSynthesisOutputFormat`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?preserve-view=true&view=azure-python) 類型的 `enum`，您可使用此類型來選取輸出格式。 如需可用的[音訊格式清單](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?preserve-view=true&view=azure-python)，請參閱參考文件。

根據您的需求而定，有各種選項可供不同的檔案類型使用。 請注意，依照定義，原始格式 (例如 `Raw24Khz16BitMonoPcm`) 不包含音訊標頭。 只有在您知道下游實作可將原始位元資料流解碼，或者打算根據位元深度、採樣速率、通道數目等手動建立標頭時，才會使用原始格式。

在此範例中，您可藉由在 `SpeechConfig` 物件上設定 `SpeechSynthesisOutputFormat`，以指定高精確度的 RIFF 格式 `Riff24Khz16BitMonoPcm`。 與上一節中的範例類似，您可使用 [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?preserve-view=true&view=azure-python) 來取得結果的記憶體內部資料流，然後將其寫入至檔案。


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

再次執行您的程式，會將自訂的 `.wav` 檔案寫入至指定的路徑。

## <a name="use-ssml-to-customize-speech-characteristics"></a>使用 SSML 來自訂語音特性

語音合成標記語言 (SSML) 可讓您從 XML 結構描述提交要求，以微調文字轉換語音輸出的音調、發音、說話速度、音量等等。 本節說明一些實際使用範例，但如需更詳細的指南，請參閱 [SSML 操作說明文章](../../../speech-synthesis-markup.md)。

若要開始使用 SSML 進行自訂，您可進行簡單變更來切換語音。
首先，在根專案目錄中為 SSML 組態建立新的 XML 檔案，在此範例中為 `ssml.xml`。 根元素一律為 `<speak>`，而將文字包裝在 `<voice>` 元素中，可讓您使用 `name` 參數來變更語音。 這個範例會將語音變更為男性英文 (英式) 語音。 請注意，此語音是 **標準** 語音，其定價和可用性與 **神經** 語音不同。 請參閱支援的 **標準** 語音 [完整清單](../../../language-support.md#standard-voices)。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

接下來，您需要變更語音合成要求以參考您的 XML 檔案。 要求大多數相同，但您會使用 `speak_ssml_async()`，而不是使用 `speak_text_async()` 函式。 此函式應該有 XML 字串，因此您會先以字串形式讀取 SSML 組態。 由此看來，結果物件會與先前的範例完全相同。

> [!NOTE]
> 如果 `ssml_string` 在字串開頭包含 `ï»¿`，則必須去除 BOM 格式，否則服務會傳回錯誤。 若要這麼做，請如下所示設定 `encoding` 參數：`open("ssml.xml", "r", encoding="utf-8-sig")`。

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

輸出有作用，但您可進行額外幾項簡單的變更，使其聽起來更自然。 整體說話速度有點太快，因此我們會新增 `<prosody>` 標籤並將速度降低為預設速率的 **90%** 。 此外，句子中逗點之後的暫停有點太短，而且聽起來不自然。 若要修正此問題，請新增 `<break>` 標籤以延遲語音，並將時間參數設定為 **200 毫秒** 。 重新執行合成，以查看這些自訂項目對輸出有何影響。

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

若要切換成神經語音，請將 `name` 變更為其中一個[神經語音選項](../../../language-support.md#neural-voices)。 然後，新增 `mstts` 的 XML 命名空間，並將您的文字包裝在 `<mstts:express-as>` 標籤中。 使用 `style` 參數來自訂說話風格。 此範例會使用 `cheerful`，但請嘗試將其設定為 `customerservice` 或 `chat`，以查看說話風格的差異。

> [!IMPORTANT]
> **只有** 在 [美國東部]、[東南亞] 和 [西歐] 區域中建立的語音資源，才支援神經語音。

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```