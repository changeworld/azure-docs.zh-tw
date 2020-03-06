---
title: 使用語音 SDK 的串流編解碼器壓縮音訊-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用語音 SDK 將壓縮的音訊串流至語音服務。 適用于C++Linux C#的、和 java、Android 中的 java 和 iOS 中的目標-C。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 2b530da06b02091ce66ff7c116f3e17ddcc22497
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331105"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>搭配使用編解碼器壓縮的音訊輸入與語音 SDK

語音 SDK 的**壓縮音訊輸入資料流程**API 提供了使用 PullStream 或 PushStream 將壓縮的音訊串流至語音服務的方式。

> [!IMPORTANT]
> 在 Linux （ubuntu 16.04、ubuntu 18.04 C++、 C#Debian 9、RHEL 8、CentOS 8）上，目前支援、和 JAVA 的串流處理壓縮輸入音訊。 [Android 中的 JAVA](how-to-use-codec-compressed-audio-input-streams-android.md)和 iOS 平臺的[目標-C](how-to-use-codec-compressed-audio-input-streams-ios.md)也支援此功能。
> 需要1.7.0 或更新版本的語音 SDK （RHEL 8、CentOS 8 的1.10.0 或更高版本）。

針對 wav/PCM，請參閱主線語音檔。  在 wav/PCM 以外，支援下列編解碼器壓縮的輸入格式：

- MP3
- OPUS/OGG
- FLAC
- Wav 容器中的 ALAW
- Wav 容器中的 MULAW

## <a name="prerequisites"></a>必要條件

處理壓縮的音訊會使用[GStreamer](https://gstreamer.freedesktop.org)來執行。 基於授權原因，Gstreamer 二進位檔不會使用語音 SDK 進行編譯和連結。 因此，應用程式開發人員必須在18.04、16.04 和 Debian 9 上安裝下列各項，才能使用壓縮的輸入音訊。

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

在 RHEL/CentOS 8 上：

```sh
sudo yum install gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

> [!NOTE]
> 在 RHEL/CentOS 8 上，請遵循[如何設定 OpenSSL For Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)的指示。

## <a name="example-code-using-codec-compressed-audio-input"></a>使用編解碼器壓縮音訊輸入的範例程式碼

若要以壓縮的音訊格式串流到語音服務，請建立 `PullAudioInputStream` 或 `PushAudioInputStream`。 然後，從您的資料流程類別的實例建立 `AudioConfig`，並指定資料流程的壓縮格式。

假設您有一個稱為 `myPushStream` 的輸入資料流程類別，而且使用 OPUS/OGG。 您的程式碼看起來可能像這樣：

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>後續步驟

- [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
* [瞭解如何以 JAVA 辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
