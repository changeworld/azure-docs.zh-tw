---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421808"
---
要以壓縮音訊格式串流式傳輸到語音服務,請建立`SPXPullAudioInputStream``SPXPushAudioInputStream`或 。

以下代碼段演示如何從 的實`SPXAudioConfiguration`例`SPXPushAudioInputStream`創建 ,指定 MP3 作為流的壓縮格式。

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

下一個程式碼段顯示如何從檔案讀取壓縮音訊資料並將其幫浦入`SPXPushAudioInputStream`。

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
