---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: 4ec9c847158c9b8a1160cb6f4510912ed83a9fae
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947121"
---
首先，使用靜態函式載入關鍵字模型檔案，此函式會傳回 `FromFile()` `KeywordRecognitionModel` 。 使用 `.table` 您從 Speech Studio 下載之檔案的路徑。 此外，您可以 `AudioConfig` 使用預設的麥克風建立，然後使用音訊設定來具現化新的 `KeywordRecognizer` 。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

接下來，藉 `RecognizeOnceAsync()` 由傳遞您的模型物件，透過一次呼叫來執行關鍵字識別。 這會啟動一個持續的關鍵字辨識會話，直到辨識關鍵字為止。 因此，您通常會在多執行緒應用程式中使用此設計模式，或在可能會無限期等候喚醒字的使用案例中使用此設計模式。

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> 此處所示的範例會使用本機關鍵詞辨識，因為它不需要 `SpeechConfig` 驗證內容的物件，也不會與後端連線。 不過，您可以 [使用直接後端連接](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword)來執行關鍵字辨識和驗證。

### <a name="continuous-recognition"></a>連續辨識

語音 SDK 中的其他類別可針對語音和意圖辨識) 使用關鍵字辨識來支援持續辨識 (。 這可讓您使用通常用於連續辨識的相同程式碼，且能夠參考 `.table` 關鍵字模型的檔案。

針對語音轉換文字，請依照 [快速入門](../../../get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=script%2cbrowser%2cwindowsinstall#continuous-recognition) 中所示的相同設計模式來設定連續辨識。 然後，將的呼叫取代 `recognizer.StartContinuousRecognitionAsync()` 為 `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` ，並傳遞您的 `KeywordRecognitionModel` 物件。 若要使用關鍵字找出停止持續辨識，請使用 `recognizer.StopKeywordRecognitionAsync()` 而不是 `recognizer.StopContinuousRecognitionAsync()` 。

意圖辨識搭配和函式使用相同的模式 [`StartKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) [`StopKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) 。
