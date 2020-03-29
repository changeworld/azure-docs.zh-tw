---
title: 查詢文本到語音轉換容器終結點
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73491072"
---
該容器提供[基於 REST 的終結點 API。](../rest-text-to-speech.md) 有許多可用於平臺、框架和語言變體[的示例原始程式碼專案](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/)。

使用 *"標準文本到語音轉換"* 容器時，應依賴于下載的圖像標記的地區設定和語音。 例如，如果您下載了標記，`latest`則預設區域設置是`en-US`和`JessaRUS`語音。 然後`{VOICE_NAME}`，論點將是[`en-US-JessaRUS`](../language-support.md#standard-voices)。 請參閱下面的示例 SSML：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

但是，對於*自訂文本到語音，* 您需要從[自訂語音門戶](https://aka.ms/custom-voice-portal)獲取**語音/模型**。 自訂模型名稱是語音名稱的同義字。 導航到 **"訓練"** 頁面，並複製 **"語音/模型**"以用作`{VOICE_NAME}`參數。
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="自訂語音模型 - 語音名稱":::

請參閱下面的示例 SSML：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

讓我們構造一個 HTTP POST 請求，提供幾個標頭和資料負載。 將`{VOICE_NAME}`預留位置替換為您自己的值。

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

此命令：

* 為`speech/synthesize/cognitiveservices/v1`終結點構造 HTTP POST 請求。
* 指定`Accept``audio/*`
* 有關詳細資訊，`Content-Type`請指定`application/ssml+xml`標頭，請參閱[請求正文](../rest-text-to-speech.md#request-body)。
* 指定`X-Microsoft-OutputFormat`標頭`riff-16khz-16bit-mono-pcm`的更多選項，請參閱[音訊輸出](../rest-text-to-speech.md#audio-outputs)。
* 向終結點發送語音`{VOICE_NAME}`[合成標記語言 （SSML）](../speech-synthesis-markup.md)請求。