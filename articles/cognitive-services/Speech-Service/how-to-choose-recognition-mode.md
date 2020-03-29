---
title: 使用語音 SDK 選擇語音辨識模式
titleSuffix: Azure Cognitive Services
description: 瞭解如何在使用語音 SDK 時選擇最佳識別模式。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: d997cb592d9d648998f2b44d9f61f465f05faeb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79079819"
---
# <a name="choose-a-speech-recognition-mode"></a>選擇語音辨識模式

在考慮語音到文本識別操作時，[語音 SDK](speech-sdk.md)提供了多種處理語音的模式。 從概念上講，有時稱為*識別模式*。 本文比較了各種識別模式。

## <a name="recognize-once"></a>識別一次

如果要一次處理一個"句子"，請使用"識別一次"功能。 此方法將從檢測到的語音開始，直到下一次暫停，從輸入中檢測識別的說出。 通常，暫停標誌著句子或思路的結束。

在一個可識別的話語結束時，服務將停止處理來自該請求的音訊。 識別的最大限制是 20 秒的句子持續時間。

::: zone pivot="programming-language-csharp"

有關使用 函數`RecognizeOnceAsync`的詳細資訊，請參閱[.NET 語音 SDK 文檔](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync)。

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

有關使用 函數`RecognizeOnceAsync`的詳細資訊，請參閱[C++語音 SDK 文檔](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)。

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

有關使用 函數`recognizeOnceAsync`的詳細資訊，請參閱 JAVA[語音 SDK 文檔](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable)。

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

有關使用 函數`recognize_once`的詳細資訊，請參閱 Python[語音 SDK 文檔](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)。

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

有關其他語言，請參閱[語音 SDK 參考文檔](speech-to-text.md#speech-sdk-reference-docs)。

::: zone-end

## <a name="continuous"></a>連續

如果需要長時間運行的識別，請使用啟動和相應的停止函數進行連續識別。 start 函數將開始並繼續處理所有陳述，直到調用停止函數，或直到超過太多時間在沉默中過去。 使用連續模式時，請確保註冊到發生時將觸發的各種事件。 例如，當發生語音辨識時，將觸發"識別"事件。 您需要有一個事件處理常式來處理識別。

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end
::: zone pivot="programming-language-more"

有關其他語言，請參閱[語音 SDK 參考文檔](speech-to-text.md#speech-sdk-reference-docs)。

::: zone-end

## <a name="dictation"></a>聽寫

使用連續識別時，可以使用相應的"啟用聽寫"功能啟用聽寫處理。 此模式將導致語音配置實例解釋句子結構（如標點符號）的單詞描述。 例如，"你住在城裡問號"的話語將被解釋為"你住在城裡嗎？

::: zone pivot="programming-language-csharp"

有關使用 函數`EnableDictation`的詳細資訊，請參閱[.NET 語音 SDK 文檔](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation)。

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

有關使用 函數`EnableDictation`的詳細資訊，請參閱[C++語音 SDK 文檔](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)。

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

有關使用 函數`enableDictation`的詳細資訊，請參閱 JAVA[語音 SDK 文檔](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable)。

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

有關使用 函數`enable_dictation`的詳細資訊，請參閱 Python[語音 SDK 文檔](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)。

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

有關其他語言，請參閱[語音 SDK 參考文檔](speech-to-text.md#speech-sdk-reference-docs)。

::: zone-end

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 GitHub 上流覽其他語音 SDK 示例](https://aka.ms/csspeech/samples)
