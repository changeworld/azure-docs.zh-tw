---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421808"
---
若要以壓縮的音訊格式串流到語音服務，請建立`SPXPullAudioInputStream`或`SPXPushAudioInputStream`。

下列程式碼片段示範如何`SPXAudioConfiguration`從的實例建立`SPXPushAudioInputStream`，並指定 MP3 做為資料流程的壓縮格式。

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

下一個程式碼片段說明如何從檔案中讀取壓縮的音訊資料，並將抽出`SPXPushAudioInputStream`至。

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
