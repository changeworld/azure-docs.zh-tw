---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: 9c9ac75be5eba41d5625a092ac48166e24fcac79
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425519"
---
語音服務的其中一項核心功能是能夠辨識人類語音，並將它轉譯成其他語言。 在本快速入門中，您將瞭解如何在您的應用程式和產品中使用語音 SDK，以執行高品質的語音翻譯。 本快速入門涵蓋的主題包括：

* 翻譯語音轉換文字
* 將語音翻譯成多種目的語言
* 執行直接語音轉換語音翻譯

## <a name="skip-to-samples-on-github"></a>跳至 GitHub 上的範例

如果要直接跳到範例程式碼，請參閱 GitHub 上的 [Java 快速入門範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/translate-speech-to-text)。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝語音 SDK，才能執行動作。 視您的平臺而定，請依照「 _關於語音 sdk_ 」一文的「 <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> 取得語音 sdk</a> 」一節中的指示進行。

## <a name="import-dependencies"></a>匯入相依性

若要執行本文中的範例，請 `import` 在 * 的最上方加入下列語句 *。JAVA* 程式碼檔案。

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>敏感性資料和環境變數

本文中的範例原始程式碼相依于儲存機密資料的環境變數，例如語音資源訂用帳戶金鑰和區域。 JAVA 程式碼檔案包含 `static final String` 從主機電腦環境變數（即和）指派的兩個 `SPEECH__SUBSCRIPTION__KEY` 值 `SPEECH__SERVICE__REGION` 。 這兩個欄位都在類別範圍內，使其可在類別的方法主體內進行存取。 如需環境變數的詳細資訊，請參閱 [環境變數和應用程式](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)設定。

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
}
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

我們來看看如何使用金鑰和區域建立 [`SpeechTranslationConfig`][config]。 請依照[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)中的步驟，來取得這些認證。

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>變更來源語言

語音翻譯的一個常見工作是指定輸入 (或來源) 語言。 我們來看看如何將輸入語言變更為義大利文。 在您的程式碼中，與 [`SpeechTranslationConfig`][config] 實例互動，呼叫 `setSpeechRecognitionLanguage` 方法。

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

[`setSpeechRecognitionLanguage`][recognitionlang]函數需要語言地區設定格式字串。 您可以提供支援的[地區設定/語言](../../../language-support.md)清單中位於 [地區設定]  資料行內的任何值。

## <a name="add-translation-language"></a>新增翻譯語言

語音翻譯的另一項常見工作是指定目標翻譯語言，至少需要一個，但支援倍數。 下列程式碼片段會將法文和德文設定為翻譯語言目標。

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

每次呼叫時 [`addTargetLanguage`][addlang] ，會指定新的目標翻譯語言。 換句話說，從來源語言辨識語音時，每個目標翻譯都可作為產生之轉譯作業的一部分。

## <a name="initialize-a-translation-recognizer"></a>初始化翻譯辨識器

建立 [`SpeechTranslationConfig`][config] 之後，下一步是初始化 [`TranslationRecognizer`][recognizer]。 當您初始化 [`TranslationRecognizer`][recognizer] 時，您必須為其傳入您的 `translationConfig`。 設定物件會提供語音服務驗證要求所需的認證。

如果您要使用裝置的預設麥克風來辨識語音，則 [`TranslationRecognizer`][recognizer] 應會顯示如下：

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

如果您想要指定音訊輸入裝置，您必須建立 [`AudioConfig`][audioconfig]，並在初始化 [`TranslationRecognizer`][recognizer] 時提供 `audioConfig` 參數。

> [!TIP]
> [了解如何取得音訊輸入裝置的裝置識別碼](../../../how-to-select-audio-input-devices.md)。

首先，您將參考物件，如下所示 `AudioConfig` ：

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

如果您想要提供音訊檔案而不使用麥克風，您仍然需要提供 `audioConfig`。 不過，當您建立 [`AudioConfig`][audioconfig] 時，您會呼叫 `fromWavFileInput` 並傳遞 `filename` 參數，而不是呼叫 `fromDefaultMicrophoneInput`。

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>翻譯語音

為了翻譯語音，語音 SDK 會依賴麥克風或音訊檔案輸入。 語音轉換之前會進行語音辨識。 所有物件都已初始化之後，請呼叫「辨識一次」函數並取得結果。

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

如需語音轉換文字的詳細資訊，請參閱 [語音辨識的基本概念](../../../get-started-speech-to-text.md)。

## <a name="synthesize-translations"></a>合成翻譯

成功進行語音辨識和轉譯之後，結果會包含字典中的所有翻譯。 函式 [`getTranslations`][translations] 會傳回字典，並以索引鍵做為目標翻譯語言，而值則是翻譯的文字。 辨識的語音可以轉譯，然後以不同的語言合成 (語音轉換語音) 。

### <a name="event-based-synthesis"></a>以事件為基礎的合成

`TranslationRecognizer`物件會公開 `synthesizing` 事件。 事件會引發數次，並提供從轉譯辨識結果取出合成音訊的機制。 如果您要翻譯成多種語言，請參閱 [手動合成](#manual-synthesis)。 藉由指派來指定合成語音 [`setVoiceName`][voicename] ，並提供事件的事件處理常式 `synthesizing` ，取得音訊。 下列範例會將翻譯的音訊儲存為 *.wav* 檔。

> [!IMPORTANT]
> 以事件為基礎的合成僅適用于單一轉譯， **請勿** 新增多個目標翻譯語言。 此外，也 [`setVoiceName`][voicename] 應該與目標翻譯語言的語言相同，例如， `"de"` 可能會對應到 `"de-DE-Hedda"` 。

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>手動合成

[`getTranslations`][translations]函數會傳回可用來從翻譯文字合成音訊的字典。 逐一查看每個翻譯，併合成翻譯。 建立實例時 `SpeechSynthesizer` ，物件必須 `SpeechConfig` [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] 將其屬性設定為所需的聲音。 下列範例會轉譯成五種語言，然後每個轉譯都會合成對應的類神經語言中的音訊檔案。

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

如需語音合成的詳細資訊，請參閱 [語音合成的基本概念](../../../get-started-text-to-speech.md)。

[config]: /java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig?view=azure-java-stable
[audioconfig]: /java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig?view=azure-java-stable
[recognizer]: /java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer?view=azure-java-stable
[recognitionlang]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable
[addlang]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage?view=azure-java-stable
[translations]: /java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations?view=azure-java-stable
[voicename]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename?view=azure-java-stable
[speechsynthesisvoicename]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename?view=azure-java-stable