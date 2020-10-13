---
title: 如何為語音轉換文字指定來源語言
titleSuffix: Azure Cognitive Services
description: 語音 SDK 可讓您在將語音轉換為文字時，指定來源語言。 本文說明如何使用 FromConfig 和 SourceLanguageConfig 方法，讓語音服務知道來源語言並提供自訂模型目標。
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1b134fd3d09eeda340e7323638a36b68336242c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362022"
---
# <a name="specify-source-language-for-speech-to-text"></a>指定語音轉換文字來源語言

在本文中，您將瞭解如何針對傳遞給語音 SDK 以進行語音辨識的音訊輸入，指定來源語言。 此外，也會提供範例程式碼來指定自訂語音模型，以改善辨識。

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>如何以 C 指定來源語言#

在下列範例中，使用結構將來源語言明確提供為參數 `SpeechRecognizer` 。

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

下列範例會使用提供的來源語言 `SourceLanguageConfig` 。 然後， `sourceLanguageConfig` 會以參數形式傳遞至 `SpeechRecognizer` 結構。

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

在下列範例中，會使用提供的來來源語言和自訂端點 `SourceLanguageConfig` 。 然後， `sourceLanguageConfig` 會以參數形式傳遞至 `SpeechRecognizer` 結構。

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage` 和 `EndpointId` set 方法已從 `SpeechConfig` c # 中的類別取代。 不建議使用這些方法，而且不應該在建立時使用 `SpeechRecognizer` 。

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>如何以 c + + 指定來源語言

在下列範例中，使用方法將來源語言明確提供為參數 `FromConfig` 。

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

下列範例會使用提供的來源語言 `SourceLanguageConfig` 。 然後，在 `sourceLanguageConfig` 建立時，會將做為參數傳遞至 `FromConfig` `recognizer` 。

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

在下列範例中，會使用提供的來來源語言和自訂端點 `SourceLanguageConfig` 。 在 `sourceLanguageConfig` 建立時，會以參數的形式傳遞至 `FromConfig` `recognizer` 。

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` 和 `SetEndpointId` 都是 `SpeechConfig` c + + 和 JAVA 類別中的已被取代方法。 不建議使用這些方法，而且不應該在建立時使用 `SpeechRecognizer` 。

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>如何在 JAVA 中指定來源語言

在下列範例中，建立新的時，會明確提供來源語言 `SpeechRecognizer` 。

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

下列範例會使用提供的來源語言 `SourceLanguageConfig` 。 然後，在 `sourceLanguageConfig` 建立新的時，會以參數的形式傳遞 `SpeechRecognizer` 。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

在下列範例中，會使用提供的來來源語言和自訂端點 `SourceLanguageConfig` 。 然後，在 `sourceLanguageConfig` 建立新的時，會以參數的形式傳遞 `SpeechRecognizer` 。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` 和 `setEndpointId` 都是 `SpeechConfig` c + + 和 JAVA 類別中的已被取代方法。 不建議使用這些方法，而且不應該在建立時使用 `SpeechRecognizer` 。

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>如何在 Python 中指定來源語言

在下列範例中，使用結構將來源語言明確提供為參數 `SpeechRecognizer` 。

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

下列範例會使用提供的來源語言 `SourceLanguageConfig` 。 然後， `SourceLanguageConfig` 會以參數形式傳遞至 `SpeechRecognizer` 結構。

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

在下列範例中，會使用提供的來來源語言和自訂端點 `SourceLanguageConfig` 。 然後， `SourceLanguageConfig` 會以參數形式傳遞至 `SpeechRecognizer` 結構。

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language` 和 `endpoint_id` 屬性 `SpeechConfig` 在 Python 中的類別已被取代。 不建議使用這些屬性，而且在建立時不應使用這些屬性 `SpeechRecognizer` 。

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>如何在 JAVAscript 中指定來源語言

第一個步驟是建立 `SpeechConfig` ：

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

接下來，使用下列內容指定音訊的來源語言 `speechRecognitionLanguage` ：

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

如果您要使用自訂模型進行辨識，您可以使用 `endpointId` 下列內容指定端點：

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>如何指定目標中的來源語言-C

在下列範例中，使用結構將來源語言明確提供為參數 `SPXSpeechRecognizer` 。

```Objective-C
SPXSpeechRecognizer* speechRecognizer = \
    [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig language:@"de-DE" audioConfiguration:audioConfig];
```

下列範例會使用提供的來源語言 `SPXSourceLanguageConfiguration` 。 然後， `SPXSourceLanguageConfiguration` 會以參數形式傳遞至 `SPXSpeechRecognizer` 結構。

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"de-DE"];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

在下列範例中，會使用提供的來來源語言和自訂端點 `SPXSourceLanguageConfiguration` 。 然後， `SPXSourceLanguageConfiguration` 會以參數形式傳遞至 `SPXSpeechRecognizer` 結構。

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"de-DE"
                                                     endpointId:@"The Endpoint ID for your custom model."];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

>[!Note]
> `speechRecognitionLanguage` 和 `endpointId` 屬性會 `SPXSpeechConfiguration` 在目標 C 的類別中被取代。 不建議使用這些屬性，而且在建立時不應使用這些屬性 `SPXSpeechRecognizer` 。

::: zone-end

## <a name="see-also"></a>另請參閱

* 如需語音轉換文字支援的語言和地區設定清單，請參閱 [語言支援](language-support.md)。

## <a name="next-steps"></a>後續步驟

* [語音 SDK 參考檔](speech-sdk.md)