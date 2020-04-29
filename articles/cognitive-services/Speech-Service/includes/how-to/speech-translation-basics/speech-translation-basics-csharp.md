---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: bac2ed447c9055f095e604725591c487378f5091
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399586"
---
## <a name="prerequisites"></a>Prerequisites

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝語音 SDK，才能執行動作。 視您的平臺而定，請依照語音 sdk 文章的<a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">取得<span class="docon docon-navigate-external x-hidden-focus"></span>語音 sdk</a>一節中的指示進行。

## <a name="import-dependencies"></a>匯入相依性

若要執行本文中的範例，請在`using` *Program.cs*檔案的頂端加入下列語句。

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>敏感性資料和環境變數

本文中的範例原始程式碼取決於用來儲存敏感性資料的環境變數，例如語音資源訂用帳戶金鑰和區域。 `Program`類別包含兩個`static readonly string`指派自主機電腦環境變數的值，即`SPEECH__SUBSCRIPTION__KEY`和`SPEECH__SERVICE__REGION`。 這兩個欄位都在類別範圍內，讓它們可在類別的方法主體中存取。 如需環境變數的詳細資訊，請參閱[環境變數和應用程式](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)設定。

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
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

我們來看看如何使用金鑰和區域建立 [`SpeechTranslationConfig`][config]。 請參閱[區域支援](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)頁面，以尋找您的區域識別碼。

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>變更來源語言

語音翻譯的一個常見工作是指定輸入（或來源）語言。 我們來看看如何將輸入語言變更為義大利文。 在您的程式碼中， [`SpeechTranslationConfig`][config]與實例進行互動， `SpeechRecognitionLanguage`並將指派給屬性。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

[`SpeechRecognitionLanguage`][recognitionlang] 屬性需要語言/地區設定格式字串。 您可以提供支援的[地區設定/語言](../../../language-support.md)清單中位於 [地區設定]  資料行內的任何值。

## <a name="add-translation-language"></a>新增翻譯語言

語音翻譯的另一個常見工作是指定目標轉譯語言，至少需要一個，但支援多個。 在下列程式碼片段中，以法文和德文做為翻譯語言目標。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

每次呼叫時[`AddTargetLanguage`][addlang]，會指定新的目標轉譯語言。 換句話說，從來源語言辨識出語音時，每個目標轉譯都可做為所產生轉譯作業的一部分。

## <a name="initialize-a-translation-recognizer"></a>初始化翻譯辨識器

建立 [`SpeechTranslationConfig`][config] 之後，下一步是初始化 [`TranslationRecognizer`][recognizer]。 當您初始化 [`TranslationRecognizer`][recognizer] 時，您必須為其傳入您的 `translationConfig`。 Configuration 物件會提供語音服務驗證要求所需的認證。

如果您要使用裝置的預設麥克風來辨識語音，則 [`TranslationRecognizer`][recognizer] 應會顯示如下：

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

如果您想要指定音訊輸入裝置，您必須建立 [`AudioConfig`][audioconfig]，並在初始化 [`TranslationRecognizer`][recognizer] 時提供 `audioConfig` 參數。

> [!TIP]
> [了解如何取得音訊輸入裝置的裝置識別碼](../../../how-to-select-audio-input-devices.md)。

首先，您將參考`AudioConfig`物件，如下所示：

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

如果您想要提供音訊檔案而不使用麥克風，您仍然需要提供 `audioConfig`。 不過，當您建立 [`AudioConfig`][audioconfig] 時，您會呼叫 `FromWavFileInput` 並傳遞 `filename` 參數，而不是呼叫 `FromDefaultMicrophoneInput`。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>翻譯語音

若要翻譯語音，語音 SDK 會依賴麥克風或音訊檔案輸入。 語音辨識會在語音翻譯之前進行。 在初始化所有物件之後，呼叫辨識一次函式並取得結果。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

如需有關語音轉換文字的詳細資訊，請參閱[語音辨識的基本概念](../../../speech-to-text-basics.md)。

## <a name="synthesize-translations"></a>合成翻譯

在成功的語音辨識和轉譯之後，結果會包含字典中的所有翻譯。 [`Translations`][translations]字典索引鍵是目標轉譯語言，而值則是翻譯的文字。 可以翻譯辨識的語音，然後以不同的語言（語音轉換語音）合成。

### <a name="event-based-synthesis"></a>以事件為基礎的合成

`TranslationRecognizer`物件會公開`Synthesizing`事件。 事件會引發數次，並提供從轉譯辨識結果抓取合成音訊的機制。 如果您要翻譯成多種語言，請參閱[手動合成](#manual-synthesis)。 藉由指派[`VoiceName`][voicename]並提供`Synthesizing`事件的事件處理常式來指定合成語音，以取得音訊。 下列範例會將轉譯的音訊儲存為 *.wav*檔案。

> [!IMPORTANT]
> 以事件為基礎的合成僅適用于單一轉譯，**請勿**新增多個目標轉譯語言。 此外， [`VoiceName`][voicename]應該與目標轉譯語言的語言相同，例如：`"de"`可以對應至`"de-DE-Hedda"`。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>手動合成

[`Translations`][translations]字典可以用來從翻譯文字合成音訊。 逐一查看每個翻譯，併合成翻譯。 建立`SpeechSynthesizer`實例時， `SpeechConfig`物件必須將其[`SpeechSynthesisVoiceName`][speechsynthesisvoicename]屬性設定為所需的聲音。 下列範例會轉譯成五種語言，然後每個翻譯都會合成對應的類神經語言中的音訊檔案。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

如需有關語音合成的詳細資訊，請參閱[語音合成的基本概念](../../../text-to-speech-basics.md)。

[config]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet
