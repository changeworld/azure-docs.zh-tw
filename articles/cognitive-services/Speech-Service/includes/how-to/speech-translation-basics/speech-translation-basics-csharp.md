---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: c8392770d7edb18f776f7664657c6985b9f7fb63
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424953"
---
語音服務的其中一項核心功能是能夠辨識人類語音，並將它轉譯成其他語言。 在本快速入門中，您將瞭解如何在您的應用程式和產品中使用語音 SDK，以執行高品質的語音翻譯。 本快速入門涵蓋的主題包括：

* 翻譯語音轉換文字
* 將語音翻譯成多種目的語言
* 執行直接語音轉換語音翻譯

## <a name="skip-to-samples-on-github"></a>跳至 GitHub 上的範例

如果要直接跳到範例程式碼，請參閱 GitHub 上的 [C# 快速入門範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/translate-speech-to-text)。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝語音 SDK，才能執行動作。 視您的平臺而定，請依照「 _關於語音 sdk_ 」一文的「 <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> 取得語音 sdk</a> 」一節中的指示進行。

## <a name="import-dependencies"></a>匯入相依性

若要執行本文中的範例，請 `using` 在 *Program.cs* 檔案的頂端加入下列語句。

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

本文中的範例原始程式碼相依于儲存機密資料的環境變數，例如語音資源訂用帳戶金鑰和區域。 `Program`類別包含兩個 `static readonly string` 從主機電腦環境變數（即和）指派的值 `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION` 。 這兩個欄位都在類別範圍內，使其可在類別的方法主體內進行存取。 如需環境變數的詳細資訊，請參閱 [環境變數和應用程式](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)設定。

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

我們來看看如何使用金鑰和區域建立 [`SpeechTranslationConfig`][config]。 請依照[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)中的步驟，來取得這些認證。

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

語音翻譯的一個常見工作是指定輸入 (或來源) 語言。 我們來看看如何將輸入語言變更為義大利文。 在您的程式碼中，與 [`SpeechTranslationConfig`][config] 實例互動，並指派給 `SpeechRecognitionLanguage` 屬性。

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

語音翻譯的另一項常見工作是指定目標翻譯語言，至少需要一個，但支援倍數。 下列程式碼片段會將法文和德文設定為翻譯語言目標。

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

每次呼叫時 [`AddTargetLanguage`][addlang] ，會指定新的目標翻譯語言。 換句話說，從來源語言辨識語音時，每個目標翻譯都可作為產生之轉譯作業的一部分。

## <a name="initialize-a-translation-recognizer"></a>初始化翻譯辨識器

建立 [`SpeechTranslationConfig`][config] 之後，下一步是初始化 [`TranslationRecognizer`][recognizer]。 當您初始化 [`TranslationRecognizer`][recognizer] 時，您必須為其傳入您的 `translationConfig`。 設定物件會提供語音服務驗證要求所需的認證。

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

首先，您將參考物件，如下所示 `AudioConfig` ：

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

為了翻譯語音，語音 SDK 會依賴麥克風或音訊檔案輸入。 語音轉換之前會進行語音辨識。 所有物件都已初始化之後，請呼叫「辨識一次」函數並取得結果。

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

如需語音轉換文字的詳細資訊，請參閱 [語音辨識的基本概念](../../../get-started-speech-to-text.md)。

## <a name="synthesize-translations"></a>合成翻譯

成功進行語音辨識和轉譯之後，結果會包含字典中的所有翻譯。 [`Translations`][translations]字典索引鍵是目標翻譯語言，而值是翻譯的文字。 辨識的語音可以轉譯，然後以不同的語言合成 (語音轉換語音) 。

### <a name="event-based-synthesis"></a>以事件為基礎的合成

`TranslationRecognizer`物件會公開 `Synthesizing` 事件。 事件會引發數次，並提供從轉譯辨識結果取出合成音訊的機制。 如果您要翻譯成多種語言，請參閱 [手動合成](#manual-synthesis)。 藉由指派來指定合成語音 [`VoiceName`][voicename] ，並提供事件的事件處理常式 `Synthesizing` ，取得音訊。 下列範例會將翻譯的音訊儲存為 *.wav* 檔。

> [!IMPORTANT]
> 以事件為基礎的合成僅適用于單一轉譯， **請勿** 新增多個目標翻譯語言。 此外，也 [`VoiceName`][voicename] 應該與目標翻譯語言的語言相同，例如， `"de"` 可能會對應到 `"de-DE-Hedda"` 。

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

[`Translations`][translations]字典可以用來從翻譯文字合成音訊。 逐一查看每個翻譯，併合成翻譯。 建立實例時 `SpeechSynthesizer` ，物件必須 `SpeechConfig` [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] 將其屬性設定為所需的聲音。 下列範例會轉譯成五種語言，然後每個轉譯都會合成對應的類神經語言中的音訊檔案。

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

如需語音合成的詳細資訊，請參閱 [語音合成的基本概念](../../../get-started-text-to-speech.md)。

[config]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: /dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet