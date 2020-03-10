---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943762"
---
若要以壓縮的音訊格式串流到語音服務，請建立 `SPXPullAudioInputStream` 或 `SPXPushAudioInputStream`。

下列程式碼片段示範如何從 `SPXPushAudioInputStream`的實例建立 `SPXAudioConfiguration`，並將 MP3 指定為數據流的壓縮格式。

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

下一個程式碼片段說明如何從檔案中讀取壓縮的音訊資料，並抽出到 `SPXPushAudioInputStream`中。

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
