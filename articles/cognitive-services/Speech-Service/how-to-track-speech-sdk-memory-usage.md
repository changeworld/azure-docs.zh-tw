---
title: 如何追蹤語音 SDK 記憶體使用量-語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務 SDK 支援多種程式設計語言，可用於語音轉換文字和文字轉換語音，以及語音翻譯。 本文討論 SDK 內建的記憶體管理工具。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: da5103317a2215aca68cec14ba8a0951258c9b89
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "75462385"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>如何追蹤語音 SDK 記憶體使用量

語音 SDK 是以原生程式碼基底為基礎，透過一系列的互通性層來投射到多種程式設計語言。 每個特定語言的投影都有 idiomatically 正確的功能來管理物件生命週期。 此外，語音 SDK 還包含記憶體管理工具，可利用物件記錄和物件限制來追蹤資源使用量。 

## <a name="how-to-read-object-logs"></a>如何讀取物件記錄

如果[已啟用語音 SDK 記錄](how-to-use-logging.md)功能，則會發出追蹤標記以啟用歷程記錄物件觀察。 這些標記包括： 

* `TrackHandle` 或 `StopTracking` 
* 物件型別
* 目前追蹤物件類型的物件數目，以及目前正在追蹤的數位。

以下是範例記錄： 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>設定警告臨界值

您可以選擇建立警告臨界值，如果超過該臨界值（假設已啟用記錄），就會記錄警告訊息。 警告訊息包含所有物件的傾印及其計數。 這種資訊可以用來進一步瞭解問題。 

若要啟用警告臨界值，必須在`SpeechConfig`物件上指定。 建立新的辨識器時，會檢查此物件。 在下列範例中，假設您已建立名`SpeechConfig` `config`為的實例：

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> 這個屬性的預設值為10000。

## <a name="set-an-error-threshold"></a>設定錯誤閾值 

使用語音 SDK 時，您可以設定在指定時間允許的最大物件數目。 如果啟用此設定，則在達到最大值時，嘗試建立新的辨識器物件將會失敗。 現有的物件將會繼續工作。

以下是範例錯誤：

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

若要啟用錯誤閾值，必須在`SpeechConfig`物件上指定。 建立新的辨識器時，會檢查此物件。 在下列範例中，假設您已建立名`SpeechConfig` `config`為的實例：

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> 這個屬性的預設值是`size_t`資料類型的平臺特定最大值。 一般的辨識會耗用7到10個內建物件。

## <a name="next-steps"></a>後續步驟

* [取得您的語音服務試用版訂用帳戶](get-started.md)
* [瞭解如何使用麥克風辨識語音](quickstarts/speech-to-text-from-microphone.md)