---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: 040ffea69f76255dcb1bfc6787cad45a95baa904
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305852"
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
> 此處所示的範例會使用本機關鍵詞辨識，因為它不需要 `SpeechConfig` 驗證內容的物件，也不會與後端連線。 不過，您可以 [使用連續的後端連接](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword)來執行關鍵字辨識和驗證。