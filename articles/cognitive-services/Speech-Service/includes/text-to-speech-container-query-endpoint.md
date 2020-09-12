---
title: 查詢文字轉換語音容器端點
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 50926074c09ef8f438b0abb53a4eb5f1813871b3
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321004"
---
容器會提供以 [REST 為基礎的端點 api](../rest-text-to-speech.md)。 有許多適用于平臺、架構和語言變化的 [範例原始程式碼專案](https://github.com/Azure-Samples/Cognitive-Speech-TTS) 。

使用標準或類神經文字轉換語音的容器時，您應該依賴所下載影像標記的地區設定和聲音。 例如，如果您已下載標記，則 `latest` 預設地區設定為 `en-US` 和 `AriaRUS` 語音。 `{VOICE_NAME}`引數就會是 [`en-US-AriaRUS`](../language-support.md#standard-voices) 。 請參閱下列 SSML 範例：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

不過，若為*自訂文字轉換語音*，您將需要從[自訂語音入口網站](https://aka.ms/custom-voice-portal)取得**語音/模型**。 自訂模型名稱與語音名稱同義。 流覽至 **定型** 頁面，並複製要作為引數使用的 **語音/模型** `{VOICE_NAME}` 。
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="自訂語音模型-語音名稱":::

請參閱下列 SSML 範例：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

讓我們來建立 HTTP POST 要求，並提供一些標頭和資料裝載。 將 `{VOICE_NAME}` 預留位置取代為您自己的值。

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

此命令：

* 為端點建立 HTTP POST 要求 `speech/synthesize/cognitiveservices/v1` 。
* 指定 `Accept` 標頭 `audio/*`
* 指定的 `Content-Type` 標頭 `application/ssml+xml` ，如需詳細資訊，請參閱 [要求主體](../rest-text-to-speech.md#request-body)。
* 指定的 `X-Microsoft-OutputFormat` 標頭 `riff-16khz-16bit-mono-pcm` ，如需更多選項，請參閱 [音訊輸出](../rest-text-to-speech.md#audio-outputs)。
* 將 [語音合成標記語言 (SSML) ](../speech-synthesis-markup.md) 要求傳送 `{VOICE_NAME}` 給端點。