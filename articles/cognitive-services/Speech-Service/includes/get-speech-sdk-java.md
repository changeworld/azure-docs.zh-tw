---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 47cb20f3a23caf586777523e56902af20b747ea1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399936"
---
:::row:::
    :::column span="3":::
        Android 的 Java SDK 打包為<a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR(Android<span class="docon docon-navigate-external x-hidden-focus"></span>庫),</a>其中包括必要的庫和所需的 Android 許可權。 它會以 `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0` 套件的形式裝載在位於 `https://csspeechstorage.blob.core.windows.net/maven/` 的 Maven 存放庫中。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

若要從 Android Studio 專案取用套件，請進行下列變更：

1. 在專案級*產生.gradle*檔中`repository`,向部分新增以下內容:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. 在模組級*build.gradle*檔案中`dependencies`,向 部分新增以下內容:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

Java SDK 也是[語音裝置 SDK](../speech-devices-sdk.md) 的一部分。

#### <a name="additional-resources"></a>其他資源

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android 特定的 Java 快速啟動原始碼<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java 執行時 (JRE) 快速入門源碼<span class="docon docon-navigate-external x-hidden-focus"></span></a>