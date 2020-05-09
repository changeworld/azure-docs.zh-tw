---
title: 如何指定語音轉換文字的來來源語言
titleSuffix: Azure Cognitive Services
description: 語音 SDK 可讓您在將語音轉換成文字時，指定來源語言。 本文說明如何使用 FromConfig 和 SourceLanguageConfig 方法，讓語音服務知道來源語言，並提供自訂模型目標。
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 07ca4c519a04910e7e126d3481afb17d2b5dc350
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977680"
---
# <a name="specify-source-language-for-speech-to-text"></a>指定語音轉換文字的來來源語言

在本文中，您將瞭解如何針對傳遞至語音 SDK 以進行語音辨識的音訊輸入，指定來源語言。 此外，也會提供範例程式碼來指定自訂語音模型，以改善辨識。

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>如何在 C 中指定來源語言#

在此範例中，會使用`SpeechRecognizer`結構明確地提供來來源語言做為參數。

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

在此範例中，會使用來`SourceLanguageConfig`提供來來源語言。 然後， `sourceLanguageConfig`會傳遞做為參數以進行`SpeechRecognizer`結構。

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

在此範例中，會使用`SourceLanguageConfig`來提供來來源語言和自訂端點。 然後， `sourceLanguageConfig`會傳遞做為參數以進行`SpeechRecognizer`結構。

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`和`EndpointId` set 方法已從 c # `SpeechConfig`中的類別取代。 不建議使用這些方法，而不應在建立時使用`SpeechRecognizer`。

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>如何在 c + + 中指定來源語言

在此範例中，會使用`FromConfig`方法，將原始語言明確地提供給參數。

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

在此範例中，會使用來`SourceLanguageConfig`提供來來源語言。 然後，在`sourceLanguageConfig`建立時，會將做`FromConfig`為參數傳遞`recognizer`至。

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

在此範例中，會使用`SourceLanguageConfig`來提供來來源語言和自訂端點。 在`sourceLanguageConfig`建立時，會將當做`FromConfig`參數傳遞至`recognizer`。

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`和`SetEndpointId`已被取代為 c `SpeechConfig` + + 和 JAVA 中類別的方法。 不建議使用這些方法，而不應在建立時使用`SpeechRecognizer`。

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>如何在 JAVA 中指定來源語言

在此範例中，建立新`SpeechRecognizer`的時，會明確地提供來來源語言。

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

在此範例中，會使用來`SourceLanguageConfig`提供來來源語言。 然後，在`sourceLanguageConfig`建立新`SpeechRecognizer`的時，會傳遞做為參數。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

在此範例中，會使用`SourceLanguageConfig`來提供來來源語言和自訂端點。 然後，在`sourceLanguageConfig`建立新`SpeechRecognizer`的時，會傳遞做為參數。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`和`setEndpointId`已被取代為 c `SpeechConfig` + + 和 JAVA 中類別的方法。 不建議使用這些方法，而不應在建立時使用`SpeechRecognizer`。

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>如何在 Python 中指定來源語言

在此範例中，會使用`SpeechRecognizer`結構明確地提供來來源語言做為參數。

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

在此範例中，會使用來`SourceLanguageConfig`提供來來源語言。 然後， `SourceLanguageConfig`會傳遞做為參數以進行`SpeechRecognizer`結構。

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

在此範例中，會使用`SourceLanguageConfig`來提供來來源語言和自訂端點。 然後， `SourceLanguageConfig`會傳遞做為參數以進行`SpeechRecognizer`結構。

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`和`endpoint_id`屬性已從 Python 中`SpeechConfig`的類別取代。 不建議使用這些屬性，而且不應在建立時使用`SpeechRecognizer`。

::: zone-end

::: zone pivot="programming-language-javascript"

## <a name="how-to-specify-source-language-in-javascript"></a>如何在 JavaScript 中指定來源語言

第一個步驟是建立`SpeechConfig`：

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

接下來，使用下列方式`speechRecognitionLanguage`指定音訊的來來源語言：

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

如果您使用自訂模型進行辨識，則可以使用`endpointId`來指定端點：

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```
::: zone-end

::: zone pivot="programming-language-objectivec"

## <a name="how-to-specify-source-language-in-objective-c"></a>如何指定目標中的來源語言-C

第一個步驟是建立`speechConfig`：

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

接下來，使用下列方式`speechRecognitionLanguage`指定音訊的來來源語言：

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

如果您使用自訂模型進行辨識，則可以使用`endpointId`來指定端點：

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>另請參閱

* 如需支援的語言和語音轉換文字的地區設定清單，請參閱[語言支援](language-support.md)。

## <a name="next-steps"></a>後續步驟

* [語音 SDK 參考檔](speech-sdk.md)
