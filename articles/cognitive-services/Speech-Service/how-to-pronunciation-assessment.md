---
title: 如何使用語音 SDK 進行發音評定
titleSuffix: Azure Cognitive Services
description: 語音 SDK 支援發音評定，可評估語音輸入的發音品質，以及精確度、流暢度、完整性等指標。
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 8602d43113f4ce21cdb430e1fa3e83f006c64753
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185558"
---
# <a name="pronunciation-assessment"></a>發音評定

發音評量會評估語音發音，並對說話音訊的精確度和流暢度提供說話者的意見反應。
使用發音評定，語言學習工具可實務、取得即時意見反應，並改善其發音，讓他們可以安心地說話並提供意見。
教育工作者可以使用此功能來即時評估多個喇叭的發音。

在本文中，您將瞭解如何 `PronunciationAssessmentConfig` 使用語音 SDK 來設定和取得 `PronunciationAssessmentResult` 。

> [!NOTE]
> 「發音評定」功能目前僅適用于區域 `westus` ， `eastasia` 而且 `centralindia` 僅支援語言 `en-US` 。

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>語音 SDK 的發音評量

在下列範例中，您將建立 `PronunciationAssessmentConfig` ，然後將它套用至 `SpeechRecognizer` 。

下列程式碼片段說明如何在您的應用程式中使用自動語言偵測：

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var pronunciationAssessmentConfig = new SpeechSDK.PronunciationAssessmentConfig("reference text",
    PronunciationAssessmentGradingSystem.HundredMark,
    PronunciationAssessmentGranularity.Word, true);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig);
// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(speechRecognizer);

speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var pronunciationAssessmentResult = SpeechSDK.PronunciationAssessmentResult.fromResult(result);
        var pronunciationScore = pronResult.pronunciationScore;
        var wordLevelResult = pronResult.detailResult.Words;
},
{});
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>發音評定設定參數

下表列出發音評定的設定參數。

| 參數 | Description | 必要？ |
|-----------|-------------|---------------------|
| ReferenceText | 用來評估發音的文字。 | 必要 |
| GradingSystem | 評分校正的點系統。 `FivePoint`系統會提供0-5 浮點分數，並 `HundredMark` 提供0-100 浮點分數。 預設：`FivePoint`。 | 選擇性 |
| 細微度 | 評估細微性。 接受的值為，顯示全文檢索文字和 `Phoneme` 音素層級的分數，其中顯示全文檢索和單字層級的分數，其中 `Word` 只會 `FullText` 顯示全文檢索層級的分數。 預設：`Phoneme`。 | 選擇性 |
| EnableMiscue | 啟用 miscue 計算。 啟用此功能之後，會將發音的單字與參考文字進行比較，並根據比較來標示省略/插入。 接受的值為 `False` 和 `True`。 預設：`False`。 | 選擇性 |
| ScenarioId | 表示自訂點系統的 GUID。 | 選擇性 |

### <a name="pronunciation-assessment-result-parameters"></a>發音評定結果參數

下表列出發音評量的結果參數。

| 參數 | Description |
|-----------|-------------|
| `AccuracyScore` | 語音的發音精確度。 精確度表示音素與原生喇叭的發音相符程度。 文字和全文檢索層級精確度分數是從音素層級精確度分數匯總而來。 |
| `FluencyScore` | 指定語音的流暢度。 流暢度指出語音符合原生說話者在單字之間使用無訊息中斷的程度。 |
| `CompletenessScore` | 語音的完整性，藉由計算發音文字與參考文字輸入的比例來決定。 |
| `PronunciationScore` | 表示指定語音之發音品質的整體分數。 這是從 `AccuracyScore` `FluencyScore` 和加權匯總而來 `CompletenessScore` 。 |
| `ErrorType` | 此值會指出文字是否省略、插入或不太明顯，相較于 `ReferenceText` 。 可能的值為 `None` (表示這個字組沒有錯誤) `Omission` 、 `Insertion` 和 `Mispronunciation` 。 |

## <a name="next-steps"></a>後續步驟

<!-- TODO: update JavaScript sample links after release -->

::: zone pivot="programming-language-csharp"
* 請參閱 GitHub 上的 [範例程式碼](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L949) ，以取得發音評量。
::: zone-end

::: zone pivot="programming-language-cpp"
* 請參閱 GitHub 上的 [範例程式碼](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L633) ，以取得發音評量。
::: zone-end

::: zone pivot="programming-language-java"
* 請參閱 GitHub 上的 [範例程式碼](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L697) ，以取得發音評量。
::: zone-end

::: zone pivot="programming-language-python"
* 請參閱 GitHub 上的 [範例程式碼](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L576) ，以取得發音評量。
::: zone-end

::: zone pivot="programming-language-objectivec"
* 請參閱 GitHub 上的 [範例程式碼](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L642) ，以取得發音評量。
::: zone-end

* [語音 SDK 參考檔](speech-sdk.md)
