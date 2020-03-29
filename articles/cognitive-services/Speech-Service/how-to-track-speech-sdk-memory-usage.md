---
title: 如何跟蹤語音 SDK 記憶體使用方式 - 語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務 SDK 支援許多程式設計語言，用於語音到文本和文本到語音轉換，以及語音轉換。 本文討論 SDK 中內置的記憶體管理工具。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75462385"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>如何跟蹤語音 SDK 記憶體使用方式

語音 SDK 基於本機代碼庫，該庫通過一系列互通性層投影到多個程式設計語言中。 每個特定于語言的投影都有慣用正確的功能來管理物件生命週期。 此外，語音 SDK 還包括記憶體管理工具，用於跟蹤資源使用方式，以及物件日誌記錄和物件限制。 

## <a name="how-to-read-object-logs"></a>如何讀取物件日誌

如果[啟用了語音 SDK 日誌記錄](how-to-use-logging.md)，則發出跟蹤標記以啟用歷史物件觀察。 這些標記包括： 

* `TrackHandle` 或 `StopTracking` 
* 物件型別
* 跟蹤物件類型的當前物件數和要跟蹤的當前編號。

下面是一個示例日誌： 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>設置警告閾值

您可以選擇創建警告閾值，如果超過該閾值（假定啟用了日誌記錄），則會記錄警告訊息。 警告訊息包含存在的所有物件的轉儲及其計數。 此資訊可用於更好地瞭解問題。 

要啟用警告閾值，必須在`SpeechConfig`物件上指定該閾值。 創建新識別器時，將檢查此物件。 在以下示例中，假設您創建了一個調用`SpeechConfig``config`的實例：

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
> 此屬性的預設值為 10，000。

## <a name="set-an-error-threshold"></a>設置錯誤閾值 

使用語音 SDK，可以設置給定時間允許的最大物件數。 如果啟用此設置，則當達到最大數量時，嘗試創建新的識別器物件將失敗。 現有物件將繼續工作。

下面是一個示例錯誤：

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

要啟用錯誤閾值，必須在`SpeechConfig`物件上指定錯誤閾值。 創建新識別器時，將檢查此物件。 在以下示例中，假設您創建了一個調用`SpeechConfig``config`的實例：

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
> 此屬性的預設值是`size_t`資料類型特定于平臺的最大值。 典型的識別將消耗 7 到 10 個內建物件。

## <a name="next-steps"></a>後續步驟

* [獲取語音服務試用訂閱](get-started.md)
* [瞭解如何使用麥克風識別語音](quickstarts/speech-to-text-from-microphone.md)