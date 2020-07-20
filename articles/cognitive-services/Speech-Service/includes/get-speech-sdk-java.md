---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 64a6bf1fec61871d2787966a5b0af24d4f012032
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637407"
---
:::row:::
    :::column span="3":::
        適用于 Android 的 JAVA SDK 會封裝成<a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR （Android 程式庫 <span class="docon docon-navigate-external x-hidden-focus"></span> ） </a>，其中包含必要的程式庫和所需的 Android 許可權。 它會以 `com.microsoft.cognitiveservices.speech:client-sdk:1.12.1` 套件的形式裝載在位於 `https://csspeechstorage.blob.core.windows.net/maven/` 的 Maven 存放庫中。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

若要從 Android Studio 專案取用套件，請進行下列變更：

1. 在專案層級*gradle*檔案中，將下列內容新增至 `repository` 區段：
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. 在模組層級的*gradle*檔案中，將下列內容新增至 `dependencies` 區段：
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.12.1'
  ```

Java SDK 也是[語音裝置 SDK](../speech-devices-sdk.md) 的一部分。

#### <a name="additional-resources"></a>其他資源

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android 特定 JAVA 快速入門原始程式碼<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">JAVA Runtime （JRE）快速入門原始程式碼<span class="docon docon-navigate-external x-hidden-focus"></span></a>