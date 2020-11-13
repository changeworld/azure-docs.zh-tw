---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 44980977f366bc40ceff9c7b5751d5657c79ccc2
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94482760"
---
語音服務的核心功能之一，就是能夠辨識並轉譯人類語音 (通常稱為語音轉文字)。 在本快速入門中，您將了解如何在您的應用程式和產品中使用語音 SDK，以執行高品質的語音轉換文字辨識。

## <a name="skip-to-samples-on-github"></a>跳至 GitHub 上的範例

如果要直接跳到範例程式碼，請參閱 GitHub 上的 [Python 快速入門範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python)。

## <a name="prerequisites"></a>Prerequisites

本文假設：

* 您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-and-import-the-speech-sdk"></a>安裝和匯入語音 SDK

您必須先安裝語音 SDK，才能執行動作。

```Python
pip install azure-cognitiveservices-speech
```

如果您在使用 macOS 時遇到安裝問題，您可能需要先執行此命令。

```Python
python3 -m pip install --upgrade pip
```

安裝語音 SDK 之後，請將其匯入您的 Python 專案中。

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>建立語音設定

若要使用語音 SDK 來呼叫語音服務，您必須建立 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)。 此類別包含訂用帳戶的相關資訊，例如您的金鑰和關聯的區域、端點、主機或授權權杖。 使用您的金鑰和區域建立 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)。 請參閱[尋找金鑰和區域](../../../overview.md#find-keys-and-region)頁面，以尋找您的金鑰區域配對。

```Python
speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
```

您可以透過其他數種方式將 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) 初始化：

* 使用端點：傳入語音服務端點。 金鑰或授權權杖是選用項目。
* 使用主機：傳入主機位址。 金鑰或授權權杖是選用項目。
* 使用授權權杖：傳入授權權杖和相關聯的區域。

> [!NOTE]
> 無論您是執行語音辨識、語音合成、翻譯還是意圖辨識，都一定會建立設定。

## <a name="recognize-from-microphone"></a>從麥克風辨識

若要使用您的裝置麥克風辨識語音，只需建立 `SpeechRecognizer` 並傳遞 `speech_config`，無需傳遞 `AudioConfig`。

```Python
import azure.cognitiveservices.speech as speechsdk

def from_mic():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
    
    print("Speak into your microphone.")
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_mic()
```

如果要使用「特定」的音訊輸入裝置，您需要在 `AudioConfig` 中指定裝置識別碼，並傳遞至 `SpeechRecognizer` 建構函式的 `audio_config` 參數。 了解 [如何取得音訊輸入裝置的裝置識別碼](../../../how-to-select-audio-input-devices.md)。

## <a name="recognize-from-file"></a>從檔案辨識

如果要辨識來自音訊檔案的語音而不使用麥克風，請建立 [`AudioConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?preserve-view=true&view=azure-python) 並使用 `filename` 參數。

```Python
import azure.cognitiveservices.speech as speechsdk

def from_file():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
    audio_input = speechsdk.AudioConfig(filename="your_file_name.wav")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
    
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_file()
```

## <a name="error-handling"></a>錯誤處理

先前的範例只會從 `result.text` 取得已辨識的文字，但是若要處理錯誤和其他回應，您必須撰寫一些程式碼來處理結果。 下列程式碼會評估 [`result.reason`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python) 屬性和：

* 列印辨識結果：`speechsdk.ResultReason.RecognizedSpeech`
* 如果沒有任何相符的辨識，則通知使用者：`speechsdk.ResultReason.NoMatch `
* 如果發生錯誤，則列印錯誤訊息：`speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

## <a name="continuous-recognition"></a>連續辨識

先前的範例會使用可辨識單一語句的一次性辨識。 單一語句的結尾會藉由聽取結束時的靜默來決定，或是在處理音訊達 15 秒的上限時結束。

相反地，當您想要 **控制** 何時停止辨識時，可使用連續辨識。 您必須連線至 `EventSignal` 才能取得辨識結果，且若要停止辨識，您必須呼叫 [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) 或 [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--)。 以下範例說明如何對音訊輸入檔執行連續辨識。

首先，我們要定義輸入並初始化 [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?preserve-view=true&view=azure-python)：

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

接下來，我們要建立一個變數來管理語音辨識的狀態。 為此，我們會將其設定為 `False`，因為在辨識開始時我們可以安全地假設辨識尚未完成。

```Python
done = False
```

現在，我們將建立一個回呼，以在接收到 `evt` 時停止連續識別。 有幾件事要牢記在心。

* 接收到 `evt` 時，會列印 `evt` 訊息。
* 接收到 `evt` 後，會呼叫 [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) 以停止辨識。
* 辨識狀態會變更為 `True`。

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

此程式碼範例說明如何將回呼連線至從 [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--) 傳送的事件。

* [`recognizing`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing)：包含中繼辨識結果的事件訊號。
* [`recognized`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized)：包含最終辨識結果的事件訊號 (表示成功的辨識嘗試)。
* [`session_started`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started)：表示辨識工作階段 (作業) 開始的事件訊號。
* [`session_stopped`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped)：表示辨識工作階段 (作業) 結束的事件訊號。
* [`canceled`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled)：包含已取消之辨識結果的事件訊號 (表示因直接的取消要求或是傳輸或通訊協定失敗而取消的辨識嘗試)。

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

完成所有設定後，我們可以呼叫 [start_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped)。

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>聽寫模式

使用連續辨識時，您可以使用對應的「啟用聽寫」功能來啟用聽寫處理。 此模式會使語音設定執行個體解譯句子結構的單字描述，例如標點符號。 例如，"Do you live in town question mark" 語句會解讀為文字 "Do you live in town?"。

若要啟用聽寫模式，請在您的 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python) 上使用 [`enable_dictation()`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) 方法。

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>變更來源語言

語音辨識的常見工作是指定輸入 (或來源) 語言。 我們來看看如何將輸入語言變更為德文。 在您的程式碼中，尋找您的 SpeechConfig，然後直接在其下方新增以下這一行。

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language) 是以字串作為引數的參數。 您可以提供支援的[地區設定/語言](../../../language-support.md)清單中的任何值。

## <a name="improve-recognition-accuracy"></a>提高辨識精確度

有數種方式可讓您使用語音 SDK 提高辨識精確度。 我們來看看片語清單。 片語清單可用來識別音訊資料中的已知片語，例如人員的姓名或特定位置。 您可以將單字或完整片語新增至片語清單中。 辨識期間，如果音訊中包含與完整片語完全相符的項目，則會使用片語清單中的項目。 如果找不到與片語完全相符的項目，就不會協助辨識。

> [!IMPORTANT]
> 片語清單功能只能在英文中使用。

若要使用片語清單，請先建立 [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-python) 物件，然後使用 [`addPhrase`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-) 新增特定單字和片語。

[`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-python) 的任何變更將會在下一次辨識時或重新連線至語音服務之後生效。

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

如果您需要清除片語清單： 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>可提高辨識精確度的其他選項

片語清單只是提高辨識精確度的選項之一。 您也可以： 

* [以自訂語音提高精確度](../../../how-to-custom-speech.md)
* [以租用戶模型提高精確度](../../../tutorial-tenant-model.md)