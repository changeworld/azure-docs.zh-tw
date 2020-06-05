---
title: 查詢文字轉換語音容器端點
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81275324"
---
容器提供以[REST 為基礎的端點 api](../rest-text-to-speech.md)。 有許多適用于平臺、架構和語言變化的[範例原始程式碼專案](https://github.com/Azure-Samples/Cognitive-Speech-TTS)。

使用*標準的文字轉換語音*容器，您應該依賴所下載影像標籤的地區設定和語音。 例如，如果您下載標記，則 `latest` 預設地區設定為 `en-US` 和 `JessaRUS` 語音。 `{VOICE_NAME}`接著，引數會是 [`en-US-JessaRUS`](../language-support.md#standard-voices) 。 請參閱以下的範例 SSML：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

不過，針對*自訂文字轉換語音*，您必須從[自訂語音入口網站](https://aka.ms/custom-voice-portal)取得**語音/型號**。 自訂模型名稱與語音名稱同義。 流覽至 [**定型**] 頁面，並複製要當做引數使用的**語音/模型** `{VOICE_NAME}` 。
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="自訂語音模型-語音名稱":::

請參閱以下的範例 SSML：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

讓我們來建立 HTTP POST 要求，並提供一些標頭和資料裝載。 `{VOICE_NAME}`以您自己的值取代預留位置。

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

此命令：

* 為端點建立 HTTP POST 要求 `speech/synthesize/cognitiveservices/v1` 。
* 指定 `Accept` 的標頭`audio/*`
* 指定的 `Content-Type` 標頭 `application/ssml+xml` ，如需詳細資訊，請參閱[要求主體](../rest-text-to-speech.md#request-body)。
* 指定的 `X-Microsoft-OutputFormat` 標頭 `riff-16khz-16bit-mono-pcm` ，如需更多選項，請參閱[音訊輸出](../rest-text-to-speech.md#audio-outputs)。
* 將指定的[語音合成標記語言（SSML）](../speech-synthesis-markup.md)要求傳送 `{VOICE_NAME}` 至端點。