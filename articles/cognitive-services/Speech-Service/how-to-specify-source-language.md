---
title: 如何為語音文本指定來源語言
titleSuffix: Azure Cognitive Services
description: 語音 SDK 允許您在將語音轉換為文本時指定來源語言。 本文介紹如何使用 FromConfig 和 Source語言配置方法讓語音服務瞭解來源語言並提供自訂模型目標。
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f0723534d9d2187593cb73f058ffea62473b80a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235984"
---
# <a name="specify-source-language-for-speech-to-text"></a>指定語音到文本的來源語言

在本文中，您將學習如何為傳遞給語音 SDK 的音訊輸入指定來源語言以進行語音辨識。 此外，還提供了示例代碼來指定自訂語音模型，以提高識別性。

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>如何以 C 方式指定來源語言#

在此示例中，來源語言使用`SpeechRecognizer`構造顯式作為參數提供。

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

在此示例中，使用 提供來源語言`SourceLanguageConfig`。 然後，`sourceLanguageConfig`傳遞為要構造的`SpeechRecognizer`參數.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

在此示例中，使用 提供來源語言和自訂終結點`SourceLanguageConfig`。 然後，`sourceLanguageConfig`傳遞為要構造的`SpeechRecognizer`參數.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`和`EndpointId`設置方法從 C# 中的`SpeechConfig`類中棄用。 不鼓勵使用這些方法，在構造 時不應使用`SpeechRecognizer`。

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>如何在C++中指定來源語言

在此示例中，使用`FromConfig`方法顯式提供來源語言作為參數。

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

在此示例中，使用 提供來源語言`SourceLanguageConfig`。 然後，`sourceLanguageConfig`在創建`FromConfig`時，將作為參數傳遞給 。 `recognizer`

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

在此示例中，使用 提供來源語言和自訂終結點`SourceLanguageConfig`。 在`sourceLanguageConfig`創建`FromConfig`時`recognizer`，將作為參數傳遞給 。

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`並且`SetEndpointId`從C++和JAVA中的`SpeechConfig`類中棄用的方法。 不鼓勵使用這些方法，在構造 時不應使用`SpeechRecognizer`。

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>如何在 JAVA 中指定來源語言

在此示例中，在創建新`SpeechRecognizer`的 時顯式提供來源語言。

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

在此示例中，使用 提供來源語言`SourceLanguageConfig`。 然後，`sourceLanguageConfig`在創建新`SpeechRecognizer`的 時，將作為參數傳遞 。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

在此示例中，使用 提供來源語言和自訂終結點`SourceLanguageConfig`。 然後，`sourceLanguageConfig`在創建新`SpeechRecognizer`的 時，將作為參數傳遞 。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`並且`setEndpointId`從C++和JAVA中的`SpeechConfig`類中棄用的方法。 不鼓勵使用這些方法，在構造 時不應使用`SpeechRecognizer`。

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>如何在 Python 中指定來源語言

在此示例中，來源語言使用`SpeechRecognizer`構造顯式作為參數提供。

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

在此示例中，使用 提供來源語言`SourceLanguageConfig`。 然後，`SourceLanguageConfig`傳遞為要構造的`SpeechRecognizer`參數.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

在此示例中，使用 提供來源語言和自訂終結點`SourceLanguageConfig`。 然後，`SourceLanguageConfig`傳遞為要構造的`SpeechRecognizer`參數.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`屬性`endpoint_id`從 Python 中的`SpeechConfig`類中棄用。 不鼓勵使用這些屬性，並且不應在構造 時`SpeechRecognizer`使用 。

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>如何在 JAVAscript 中指定來源語言

第一步是創建 ： `SpeechConfig`

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

接下來，使用 指定音訊的來源語言： `speechRecognitionLanguage`

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

如果使用自訂模型進行識別，則可以使用 指定終結點`endpointId`：

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>如何在目標 C 中指定來源語言

第一步是創建 ： `speechConfig`

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

接下來，使用 指定音訊的來源語言： `speechRecognitionLanguage`

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

如果使用自訂模型進行識別，則可以使用 指定終結點`endpointId`：

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>另請參閱

* 有關語音到文本的支援語言和地區設定的清單，請參閱[語言支援](language-support.md)。

## <a name="next-steps"></a>後續步驟

* [語音 SDK 參考文檔](speech-sdk.md)
