---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: cd7a8c0104a903f49c5ce8ae1e1a561d53c0fbf5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266672"
---
## <a name="prerequisites"></a>Prerequisites

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝語音 SDK，才能執行動作。 根據您的平臺,請按照語音 SDK 文章的<a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">「獲取語音<span class="docon docon-navigate-external x-hidden-focus"></span>SDK」</a>部分下的說明進行操作。

## <a name="import-dependencies"></a>匯入相依性

要執行本文中的範例,請在 python`import`代碼 檔案的頂部包括以下語句。

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>敏感資料與環境變數

本文中的範例原始碼取決於用於儲存敏感資料的環境變數,例如語音資源訂閱密鑰和地區。 python 代碼檔包含從主機環境變數(即`SPEECH__SUBSCRIPTION__KEY`和`SPEECH__SERVICE__REGION`) 分配的兩個值。 這兩個變數都位於全域作用域中,因此可在代碼檔的函數定義中訪問它們。 關於環境變數的詳細資訊,請參閱[環境變數和應用程式設定](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)。

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
```

## <a name="create-a-speech-translation-configuration"></a>建立語音翻譯設定

若要使用語音 SDK 來呼叫語音服務，您必須建立 [`SpeechTranslationConfig`][config]。 此類別包含訂用帳戶的相關資訊，例如您的金鑰和關聯的區域、端點、主機或授權權杖。

> [!TIP]
> 無論您是執行語音辨識、語音合成、翻譯還是意圖辨識，都一定會建立設定。

您可以透過數種方式將 [`SpeechTranslationConfig`][config] 初始化：

* 使用訂用帳戶：傳入金鑰和相關聯的區域。
* 使用端點：傳入語音服務端點。 金鑰或授權權杖是選用項目。
* 使用主機：傳入主機位址。 金鑰或授權權杖是選用項目。
* 使用授權權杖：傳入授權權杖和相關聯的區域。

我們來看看如何使用金鑰和區域建立 [`SpeechTranslationConfig`][config]。 請參閱[區域支援](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)頁以查找區域標識符。

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>變更來源語言

語音轉換的一個常見任務是指定輸入(或源)語言。 我們來看看如何將輸入語言變更為義大利文。 在代碼中,與實體互動[`SpeechTranslationConfig`][config],將分配給屬性`speech_recognition_language`。

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

[`speech_recognition_language`][recognitionlang] 屬性需要語言/地區設定格式字串。 您可以提供支援的[地區設定/語言](../../../language-support.md)清單中位於 [地區設定]  資料行內的任何值。

## <a name="add-translation-language"></a>新增翻譯語言

語音轉換的另一個常見任務是指定目標翻譯語言,至少需要一種,但支援多個語言。 在以下代碼段中,法語和德語作為翻譯語言的目標。

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

每次調用[`add_target_language`][addlang]時,都會指定一種新的目標翻譯語言。 換句話說,當從源語言中識別語音時,每個目標翻譯都可以作為結果翻譯操作的一部分。

## <a name="initialize-a-translation-recognizer"></a>初始化翻譯辨識器

建立 [`SpeechTranslationConfig`][config] 之後，下一步是初始化 [`TranslationRecognizer`][recognizer]。 當您初始化 [`TranslationRecognizer`][recognizer] 時，您必須為其傳入您的 `translation_config`。 配置物件提供語音服務驗證請求所需的認證。

如果您要使用裝置的預設麥克風來辨識語音，則 [`TranslationRecognizer`][recognizer] 應會顯示如下：

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

如果您想要指定音訊輸入裝置，您必須建立 [`AudioConfig`][audioconfig]，並在初始化 [`TranslationRecognizer`][recognizer] 時提供 `audio_config` 參數。

> [!TIP]
> [了解如何取得音訊輸入裝置的裝置識別碼](../../../how-to-select-audio-input-devices.md)。

首先,您將引用物件`AudioConfig`如下:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

如果您想要提供音訊檔案而不使用麥克風，您仍然需要提供 `audioConfig`。 但是,當您創建一[`AudioConfig`][audioconfig]個時,而不是`use_default_microphone=True`使用呼叫,`filename="path-to-file.wav"`您將呼叫`filename`並提供參數。

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>翻譯語音

要翻譯語音,語音 SDK 依賴於麥克風或音訊檔輸入。 語音辨識在語音轉換之前發生。 所有物件都初始化后,調用識別一次函數並獲取結果。

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

關於語音到文字的詳細資訊,請參閱[語音辨識的基礎知識](../../../speech-to-text-basics.md)。

## <a name="synthesize-translations"></a>合成翻譯

在語音辨識和翻譯成功後,結果包含字典中的所有翻譯。 字典[`translations`][translations]鍵是目標翻譯語言,值是翻譯文本。 可識別的語音可以翻譯,然後以不同的語言(語音到語音)合成。

### <a name="event-based-synthesis"></a>基於事件的合成

物件`TranslationRecognizer`公開`Synthesizing`事件 。 該事件多次觸發,並提供從轉換識別結果中檢索合成音訊的機制。 如果要翻譯成多種語言,請參閱[手動合成](#manual-synthesis)。 通過分配分配和[`voice_name`][voicename]`Synthesizing`為事件提供事件處理程式來指定合成語音,獲取音訊。 下面的範例將翻譯的音訊保存為 *.wav*檔案。

> [!IMPORTANT]
> 基於事件的合成僅適用於單個翻譯,**不**添加多個目標翻譯語言。 此外,[`voice_name`][voicename]應與目標翻譯語言的語言相同,例如;`"de"`可以映射到`"de-DE-Hedda"`。

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>手動合成

該[`translations`][translations]詞典可用於從翻譯文本中合成音訊。 反覆運算每個翻譯,並綜合翻譯。 創建`SpeechSynthesizer`實體時,`SpeechConfig`物件需要[`speech_synthesis_voice_name`][speechsynthesisvoicename]將其 屬性設定為所需的語音。 下面的範例轉換為五種語言,然後每個翻譯都合成到相應的神經語言中的音訊檔。

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

有關語音合成的詳細資訊,請參閱[語音合成的基礎知識](../../../text-to-speech-basics.md)。

[config]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python
[audioconfig]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python
[recognizer]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer?view=azure-python
[recognitionlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python
[addlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#add-target-language-language--str-
[translations]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult?view=azure-python#translations
[voicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#voice-name
[speechsynthesisvoicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-synthesis-voice-name
