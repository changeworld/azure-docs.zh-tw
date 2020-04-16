---
title: 如何對語音文字使用自動語言偵測
titleSuffix: Azure Cognitive Services
description: 語音 SDK 支援語音到文字的自動語言檢測。 使用此功能時,提供的音訊與提供的語言清單進行比較,並確定最有可能的匹配。 然後,返回的值可用於選擇用於語音到文本的語言模型。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: fefbe793fa4a6b90ba9bf8d468d42dcbd315759c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402201"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>語音到文字的自動語言偵測

與提供的語言清單進行比較時,自動語言檢測用於確定傳遞給語音 SDK 的音訊的最可能匹配項。 然後,自動語言檢測返回的值用於選擇語音到文本的語言模型,從而為您提供更準確的轉錄。 要檢視哪些語言可用,請參考[語言支援](language-support.md)。

在本文中,您將學習如何使用`AutoDetectSourceLanguageConfig`來構`SpeechRecognizer`造 物件並檢索檢測到的語言。

> [!IMPORTANT]
> 此功能僅適用於 C#、C++、Java 和 Python 的語音 SDK。

## <a name="automatic-language-detection-with-the-speech-sdk"></a>使用語音 SDK 自動偵測語言

自動語言檢測目前具有每次檢測兩種語言的服務側限制。 構造`AudoDetectSourceLanguageConfig`物件時,請記住此限制。 在下面的範例中,您將建立一個`AutoDetectSourceLanguageConfig`,然後使用它建構 一`SpeechRecognizer`個 。

> [!TIP]
> 還可以指定在將語音執行文本時使用的自定義模型。 有關詳細資訊,請參閱[使用自訂模型進行自動語言偵測](#use-a-custom-model-for-automatic-language-detection)。

以下代碼段說明瞭如何在應用中使用自動語言檢測:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>使用自訂模型進行自動語言偵測

除了使用語音服務模型進行語言檢測外,您還可以指定自定義模型以增強識別。 如果未提供自定義模型,該服務將使用默認語言模型。

以下代碼段說明瞭如何在對語音服務的調用中指定自定義模型。 如果檢測到的語言為`en-US`,則使用預設模型。 如果檢測到的語言為`fr-FR`,則使用自定義模型的終結點:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

## <a name="next-steps"></a>後續步驟

- [語音 SDK 文件](speech-sdk.md)
