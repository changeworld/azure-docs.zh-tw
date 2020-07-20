---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 21c4f88083fab28ae132379eae1b5d8a2afa5d9f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421678"
---
若要以壓縮的音訊格式串流到語音服務，請`PullAudioInputStream`建立`PushAudioInputStream`或。 然後， `AudioConfig`從您的資料流程類別的實例建立，並指定資料流程的壓縮格式。

假設您有一個名`pushStream`為的輸入資料流程類別，而且使用 OPUS/OGG。 您的程式碼看起來可能像這樣：

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

// ... omitted for brevity

var speechConfig =
    SpeechConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");

// Create an audio config specifying the compressed
// audio format and the instance of your input stream class.
var audioFormat =
    AudioStreamFormat.GetCompressedFormat(
        AudioStreamContainerFormat.OGG_OPUS);
var audioConfig =
    AudioConfig.FromStreamInput(
        pushStream,
        audioFormat);

using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
var result = await recognizer.RecognizeOnceAsync();

var text = result.Text;
```