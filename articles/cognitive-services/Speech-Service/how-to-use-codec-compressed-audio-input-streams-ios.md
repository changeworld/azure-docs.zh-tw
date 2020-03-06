---
title: 使用語音 SDK 在 iOS 上串流編解碼器壓縮的音訊
titleSuffix: Azure Cognitive Services
description: 瞭解如何在 iOS 上使用語音 SDK 將壓縮的音訊串流至語音服務。
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 5d65ac83fc8a41008971108c5b370c1d0416113b
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331514"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>如何：在 iOS 上搭配使用編解碼器壓縮的音訊輸入與語音 SDK

語音 SDK 的**壓縮音訊輸入資料流程**API 可讓您使用提取或推播串流，將壓縮的音訊串流至語音服務。

> [!IMPORTANT]
> 需要語音 SDK 版本1.7.0 或更高版本，才能在 iOS 上串流處理壓縮的音訊。 [ C++Linux 上的C#、和 java （ubuntu 16.04、ubuntu 18.04、Debian 9、RHEL 8、CentOS 8）](how-to-use-codec-compressed-audio-input-streams.md)和[Android 中的 java](how-to-use-codec-compressed-audio-input-streams-android.md)也支援此功能。

針對 wav/PCM，請參閱主線語音檔。 在 wav/PCM 以外，支援下列編解碼器壓縮的輸入格式：

- MP3
- OPUS/OGG
- FLAC
- Wav 容器中的 ALAW
- Wav 容器中的 MULAW

## <a name="prerequisites"></a>必要條件

處理壓縮的音訊會使用[GStreamer](https://gstreamer.freedesktop.org)來執行。 基於授權原因，這些函式不能隨附于 SDK，但包含這些函式的包裝函式程式庫必須由應用程式開發人員建立，並隨附于使用 SDK 的應用程式。

若要建立此包裝函式程式庫，請先下載並安裝[GSTREAMER SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)。 然後，下載包裝函式連結[庫](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)的 Xcode 專案。

在 Xcode 中開啟專案，並為**一般 IOS 裝置**目標建立它，這將無法針對特定目標建立。

組建步驟會針對名稱為 `GStreamerWrapper.framework`的所有必要架構，產生具有動態連結程式庫的動態架構組合。

此架構必須包含在搭配語音服務 SDK 使用壓縮音訊串流的所有應用程式中。

將下列設定套用至您的 Xcode 專案，以完成此動作：

1. 將您剛才建立的 `GStreamerWrapper.framework` 和認知服務語音 SDK 的架構（您可以從[這裡](https://aka.ms/csspeech/iosbinary)下載）複製到包含範例專案的目錄。
1. 調整 [_專案設定_] 中架構的路徑。
   1. 在**內嵌二進位檔案**標題底下的 [**一般**] 索引標籤中，將 SDK 程式庫新增為架構：**新增內嵌的二進位**檔 > **新增其他 ...** > 流覽至您選擇的目錄，並選取這兩個架構。
   1. 移至 [組建設定] 索引標籤，然後啟動 [所有] 設定。
1. 將目錄 `$(SRCROOT)/..` 新增至 [搜尋路徑] 標題下方的 [架構搜尋路徑]。

## <a name="example-code-using-codec-compressed-audio-input"></a>使用編解碼器壓縮音訊輸入的範例程式碼

若要以壓縮的音訊格式串流到語音服務，請建立 `SPXPullAudioInputStream` 或 `SPXPushAudioInputStream`。

下列程式碼片段示範如何從 `SPXPushAudioInputStream`的實例建立 `SPXAudioConfiguration`，並指定 mp3 做為資料流程的壓縮格式。

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

下一個程式碼片段說明如何從檔案中讀取壓縮的音訊資料，並抽出到 `SPXPushAudioInputStream`中。

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>後續步驟

- [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
- [瞭解如何以 JAVA 辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
