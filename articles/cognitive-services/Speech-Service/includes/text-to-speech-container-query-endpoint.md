---
title: 查詢文字到語音轉換容器終結點
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275324"
---
該容器提供[基於 REST 的終結點 API。](../rest-text-to-speech.md) 有許多可用於平臺、框架和語言變體[的範例碼專案](https://github.com/Azure-Samples/Cognitive-Speech-TTS)。

使用 *「標準文字到語音轉換」* 容器時,應依賴於下載的圖像標記的區域設置和語音。 例如,如果您下載了標記,`latest`則預設區域設置`en-US`是`JessaRUS`和語音。 然後`{VOICE_NAME}`,論點[`en-US-JessaRUS`](../language-support.md#standard-voices)將是 。 請參考下面的範例 SSML:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

但是,對於*自訂文字到語音,* 您需要從[自訂語音門戶](https://aka.ms/custom-voice-portal)獲取**語音/模型**。 自定義模型名稱是語音名稱的同義詞。 導航到 **「訓練」** 頁面,並複製 **「語音/模型**」`{VOICE_NAME}`以用作 參數。
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="自訂語音模型 - 語音名稱":::

請參考下面的範例 SSML:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

讓我們構造一個 HTTP POST 請求,提供幾個標頭和數據負載。 將`{VOICE_NAME}`佔位符替換為您自己的值。

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
* 關於詳細資訊,`Content-Type`請`application/ssml+xml`指定標頭,請參考[要求正文](../rest-text-to-speech.md#request-body)。
* 指定`X-Microsoft-OutputFormat`標`riff-16khz-16bit-mono-pcm`頭的更多選項,請參閱[音訊輸出](../rest-text-to-speech.md#audio-outputs)。
* 向終結點發送語音`{VOICE_NAME}`[合成標記語言 (SSML)](../speech-synthesis-markup.md)請求。