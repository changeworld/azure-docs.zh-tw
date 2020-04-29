---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 09f08e314a634de13a683440ad9fead97ad8a260
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399649"
---
## <a name="prerequisites"></a>Prerequisites

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝語音 SDK，才能執行動作。 視您的平臺而定，請依照語音 sdk 文章的<a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">取得<span class="docon docon-navigate-external x-hidden-focus"></span>語音 sdk</a>一節中的指示進行。

## <a name="import-dependencies"></a>匯入相依性

若要執行本文中的範例，請在 c `#include` + `using` + 程式碼檔案的頂端包含下列和語句。

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>敏感性資料和環境變數

本文中的範例原始程式碼取決於用來儲存敏感性資料的環境變數，例如語音資源訂用帳戶金鑰和區域。 C + + 程式碼檔案包含兩個從主機電腦環境變數指派的字串值， `SPEECH__SUBSCRIPTION__KEY`亦`SPEECH__SERVICE__REGION`即和。 這兩個欄位都在類別範圍內，讓它們可在類別的方法主體中存取。 如需環境變數的詳細資訊，請參閱[環境變數和應用程式](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)設定。

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
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

我們來看看如何使用金鑰和區域建立 [`SpeechTranslationConfig`][config]。 請參閱[區域支援](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)頁面，以尋找您的區域識別碼。

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>變更來源語言

語音翻譯的一個常見工作是指定輸入（或來源）語言。 我們來看看如何將輸入語言變更為義大利文。 在您的程式碼中， [`SpeechTranslationConfig`][config]與實例進行互動`SetSpeechRecognitionLanguage` ，並呼叫方法。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

[`SpeechRecognitionLanguage`][recognitionlang] 屬性需要語言/地區設定格式字串。 您可以提供支援的[地區設定/語言](../../../language-support.md)清單中位於 [地區設定]  資料行內的任何值。

## <a name="add-translation-language"></a>新增翻譯語言

語音翻譯的另一個常見工作是指定目標轉譯語言，至少需要一個，但支援多個。 在下列程式碼片段中，以法文和德文做為翻譯語言目標。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

每次呼叫時[`AddTargetLanguage`][addlang]，會指定新的目標轉譯語言。 換句話說，從來源語言辨識出語音時，每個目標轉譯都可做為所產生轉譯作業的一部分。

## <a name="initialize-a-translation-recognizer"></a>初始化翻譯辨識器

建立 [`SpeechTranslationConfig`][config] 之後，下一步是初始化 [`TranslationRecognizer`][recognizer]。 當您初始化 [`TranslationRecognizer`][recognizer] 時，您必須為其傳入您的 `translationConfig`。 Configuration 物件會提供語音服務驗證要求所需的認證。

如果您要使用裝置的預設麥克風來辨識語音，則 [`TranslationRecognizer`][recognizer] 應會顯示如下：

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

如果您想要指定音訊輸入裝置，您必須建立 [`AudioConfig`][audioconfig]，並在初始化 [`TranslationRecognizer`][recognizer] 時提供 `audioConfig` 參數。

> [!TIP]
> [了解如何取得音訊輸入裝置的裝置識別碼](../../../how-to-select-audio-input-devices.md)。

首先，您將參考`AudioConfig`物件，如下所示：

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

如果您想要提供音訊檔案而不使用麥克風，您仍然需要提供 `audioConfig`。 不過，當您建立 [`AudioConfig`][audioconfig] 時，您會呼叫 `FromWavFileInput` 並傳遞 `filename` 參數，而不是呼叫 `FromDefaultMicrophoneInput`。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>翻譯語音

若要翻譯語音，語音 SDK 會依賴麥克風或音訊檔案輸入。 語音辨識會在語音翻譯之前進行。 在初始化所有物件之後，呼叫辨識一次函式並取得結果。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

如需有關語音轉換文字的詳細資訊，請參閱[語音辨識的基本概念](../../../speech-to-text-basics.md)。

## <a name="synthesize-translations"></a>合成翻譯

在成功的語音辨識和轉譯之後，結果會包含字典中的所有翻譯。 [`Translations`][translations]字典索引鍵是目標轉譯語言，而值則是翻譯的文字。 可以翻譯辨識的語音，然後以不同的語言（語音轉換語音）合成。

### <a name="event-based-synthesis"></a>以事件為基礎的合成

`TranslationRecognizer`物件會公開`Synthesizing`事件。 事件會引發數次，並提供從轉譯辨識結果抓取合成音訊的機制。 如果您要翻譯成多種語言，請參閱[手動合成](#manual-synthesis)。 藉由指派[`SetVoiceName`][voicename]並提供`Synthesizing`事件的事件處理常式來指定合成語音，以取得音訊。 下列範例會將轉譯的音訊儲存為 *.wav*檔案。

> [!IMPORTANT]
> 以事件為基礎的合成僅適用于單一轉譯，**請勿**新增多個目標轉譯語言。 此外， [`SetVoiceName`][voicename]應該與目標轉譯語言的語言相同，例如：`"de"`可以對應至`"de-DE-Hedda"`。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>手動合成

[`Translations`][translations]字典可以用來從翻譯文字合成音訊。 逐一查看每個翻譯，併合成翻譯。 建立`SpeechSynthesizer`實例時， `SpeechConfig`物件必須將其[`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename]屬性設定為所需的聲音。 下列範例會轉譯成五種語言，然後每個翻譯都會合成對應的類神經語言中的音訊檔案。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

如需有關語音合成的詳細資訊，請參閱[語音合成的基本概念](../../../text-to-speech-basics.md)。

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
