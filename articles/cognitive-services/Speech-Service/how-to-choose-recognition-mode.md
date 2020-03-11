---
title: 使用語音 SDK 選擇語音辨識模式
titleSuffix: Azure Cognitive Services
description: 瞭解如何在使用語音 SDK 時選擇最佳辨識模式。
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
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79079819"
---
# <a name="choose-a-speech-recognition-mode"></a>選擇語音辨識模式

當您考慮語音轉換文字辨識作業時，[語音 SDK](speech-sdk.md)會提供多種模式來處理語音。 在概念上，有時稱為辨識*模式*。 本文會比較各種辨識模式。

## <a name="recognize-once"></a>辨識一次

如果您想要一次處理一個「句子」的語句，請使用「辨識一次」函式。 這個方法會偵測到從偵測到的語音開頭開始的輸入所識別的語句，直到下一次暫停為止。 通常，暫停會標示句子的結尾或預定的行。

在一個可辨識的語句結束時，服務會停止處理該要求的音訊。 辨識的最大限制為20秒的句子持續時間。

::: zone pivot="programming-language-csharp"

如需使用 `RecognizeOnceAsync` 函式的詳細資訊，請參閱[.Net 語音 SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync)檔。

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

如需使用 `RecognizeOnceAsync` 函式的詳細資訊，請參閱[ C++語音 SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)檔。

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

如需使用 `recognizeOnceAsync` 函式的詳細資訊，請參閱[JAVA 語音 SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable)檔。

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

如需使用 `recognize_once` 函式的詳細資訊，請參閱[Python 語音 SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)檔。

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

如需其他語言，請參閱[語音 SDK 參考](speech-to-text.md#speech-sdk-reference-docs)檔。

::: zone-end

## <a name="continuous"></a>連續

如果您需要長時間執行的辨識，請使用啟動和對應的 stop 函式來進行連續辨識。 啟動函式會啟動並繼續處理所有的語句，直到您叫用 stop 函式為止，或直到超過過多時間為止。 使用連續模式時，請務必註冊將在發生時引發的各種事件。 例如，當語音辨識發生時，就會引發「已辨識」事件。 您需要有事件處理常式，才能處理辨識。

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

如需其他語言，請參閱[語音 SDK 參考](speech-to-text.md#speech-sdk-reference-docs)檔。

::: zone-end

## <a name="dictation"></a>聽寫

使用連續辨識時，您可以使用對應的「啟用聽寫」功能來啟用聽寫處理。 此模式會使語音設定實例解讀句子結構的單字描述，例如標點符號。 例如，「您居住在城鎮問號中」的語句會解讀為「您居住在城鎮嗎？」的文字。

::: zone pivot="programming-language-csharp"

如需使用 `EnableDictation` 函式的詳細資訊，請參閱[.Net 語音 SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation)檔。

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

如需使用 `EnableDictation` 函式的詳細資訊，請參閱[ C++語音 SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)檔。

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

如需使用 `enableDictation` 函式的詳細資訊，請參閱[JAVA 語音 SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable)檔。

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

如需使用 `enable_dictation` 函式的詳細資訊，請參閱[Python 語音 SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)檔。

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

如需其他語言，請參閱[語音 SDK 參考](speech-to-text.md#speech-sdk-reference-docs)檔。

::: zone-end

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的其他語音 SDK 範例](https://aka.ms/csspeech/samples)
