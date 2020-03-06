---
title: 在 Android 上使用語音 SDK 的串流編解碼器壓縮音訊
titleSuffix: Azure Cognitive Services
description: 瞭解如何在 Android 上使用語音 SDK 將壓縮的音訊串流至語音服務。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 1539ca9aa18892f617f278e67c8b6141f5f6d880
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331122"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-android"></a>如何：在 Android 上搭配使用編解碼器壓縮的音訊輸入與語音 SDK

語音 SDK 的**壓縮音訊輸入資料流程**API 提供了使用 PullStream 或 PushStream 將壓縮的音訊串流至語音服務的方式。

> [!IMPORTANT]
> [ C++在 Linux （ubuntu 16.04、ubuntu 18.04、 C#Debian 9、RHEL 8、CentOS 8）上，目前支援、和 JAVA](how-to-use-codec-compressed-audio-input-streams.md)的串流處理壓縮輸入音訊。 Android 中的 JAVA 和 iOS 平臺的[目標-C](how-to-use-codec-compressed-audio-input-streams-ios.md)也支援此功能。
> 需要1.7.0 或更高版本的語音 SDK。

針對 wav/PCM，請參閱主線語音檔。 在 wav/PCM 以外，支援下列編解碼器壓縮的輸入格式：

- MP3
- OPUS/OGG
- FLAC
- Wav 容器中的 ALAW
- Wav 容器中的 MULAW

## <a name="prerequisites-to-using-codec-compressed-audio-input-on-android"></a>在 Android 上使用編解碼器壓縮音訊輸入的必要條件

編解碼器壓縮的音訊會使用[GStreamer](https://gstreamer.freedesktop.org)來執行。 基於授權原因，Gstreamer 二進位檔不會以 SDK 編譯。 您將需要使用預建的適用于 Android 的二進位檔。 若要下載預先建立的程式庫，請參閱[安裝 Android 開發](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c)。

需要 `libgstreamer_android.so`。 請確定您的 GStreamer 外掛程式已連結 `libgstreamer_android.so`。

```make
GSTREAMER_PLUGINS := coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
```

以下提供範例 `Android.mk` 和 `Application.mk` 檔案。 請遵循下列步驟來建立 gstreamer 共用物件： `libgstreamer_android.so`。

```make
# Android.mk
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := dummy
LOCAL_SHARED_LIBRARIES := gstreamer_android
LOCAL_LDLIBS := -llog
include $(BUILD_SHARED_LIBRARY)


ifndef GSTREAMER_ROOT_ANDROID
$(error GSTREAMER_ROOT_ANDROID is not defined!)
endif

ifndef APP_BUILD_SCRIPT
$(error APP_BUILD_SCRIPT is not defined!)
endif

ifndef TARGET_ARCH_ABI
$(error TARGET_ARCH_ABI is not defined!)
endif

ifeq ($(TARGET_ARCH_ABI),armeabi)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm
else ifeq ($(TARGET_ARCH_ABI),armeabi-v7a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/armv7
else ifeq ($(TARGET_ARCH_ABI),arm64-v8a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm64
else ifeq ($(TARGET_ARCH_ABI),x86)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86
else ifeq ($(TARGET_ARCH_ABI),x86_64)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86_64
else
$(error Target arch ABI not supported: $(TARGET_ARCH_ABI))
endif

GSTREAMER_NDK_BUILD_PATH  := $(GSTREAMER_ROOT)/share/gst-android/ndk-build/
include $(GSTREAMER_NDK_BUILD_PATH)/plugins.mk
GSTREAMER_PLUGINS         :=  coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
GSTREAMER_EXTRA_LIBS      := -liconv
include $(GSTREAMER_NDK_BUILD_PATH)/gstreamer-1.0.mk
```

```make
# Application.mk
APP_STL = c++_shared
APP_PLATFORM = android-21
APP_BUILD_SCRIPT = Android.mk
```

您可以使用下列命令在 Ubuntu 16.04 或18.04 上建立 `libgstreamer_android.so`。 下列命令列只有針對使用[ANDROID NDK b16b](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip)的[Gstreamer android 版本 1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2)進行測試。

```sh
# assuming wget and unzip already installed on the system
mkdir buildLibGstreamer
cd buildLibGstreamer
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip -q -o android-ndk-r16b-linux-x86_64.zip
export PATH=$PATH:$(pwd)/android-ndk-r16b
export NDK_PROJECT_PATH=$(pwd)/android-ndk-r16b
wget https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2
mkdir gstreamer_android
tar -xjf gstreamer-1.0-android-universal-1.14.4.tar.bz2 -C $(pwd)/gstreamer_android/
export GSTREAMER_ROOT_ANDROID=$(pwd)/gstreamer_android

mkdir gstreamer
# Copy the Application.mk and Android.mk from the documentation above and put it inside $(pwd)/gstreamer

# Enable only one of the following at one time to create the shared object for the targeted ABI
echo "building for armeabi-v7a. libgstreamer_android.so will be placed in $(pwd)/armeabi-v7a"
ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=armeabi-v7a NDK_LIBS_OUT=$(pwd)

#echo "building for arm64-v8a. libgstreamer_android.so will be placed in $(pwd)/arm64-v8a"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=arm64-v8a NDK_LIBS_OUT=$(pwd)

#echo "building for x86_64. libgstreamer_android.so will be placed in $(pwd)/x86_64"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86_64 NDK_LIBS_OUT=$(pwd)

#echo "building for x86. libgstreamer_android.so will be placed in $(pwd)/x86"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86 NDK_LIBS_OUT=$(pwd)
```

一旦共用物件（libgstreamer_android. so）建立後，應用程式開發人員就必須將共用物件放在 Android 應用程式中，讓語音 SDK 可以載入它。

## <a name="example-code-using-codec-compressed-audio-input"></a>使用編解碼器壓縮音訊輸入的範例程式碼

若要以壓縮的音訊格式串流到語音服務，請建立 `PullAudioInputStream` 或 `PushAudioInputStream`。 然後，從您的資料流程類別的實例建立 `AudioConfig`，並指定資料流程的壓縮格式。

假設您有一個稱為 `myPullStream` 的輸入資料流程類別，而且使用 OPUS/OGG。 您的程式碼看起來可能像這樣：

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = AudioStreamFormat.getCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig = AudioConfig.fromStreamInput(myPullStream, audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get()

String text = result.getText();
```

## <a name="next-steps"></a>後續步驟

- [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
- [瞭解如何以 JAVA 辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
