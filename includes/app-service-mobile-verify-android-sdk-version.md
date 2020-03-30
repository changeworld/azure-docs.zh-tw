---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174494"
---
由於持續進行開發，因此 Android Studio 中安裝的 Android SDK 版本可能與程式碼中的版本不相符。 此教學課程參照的 Android SDK 為 26 版，是撰寫本文時的最新版本。 隨著新修訂版 SDK 發行，版本號碼可能會隨之增加，我們建議您使用最新的可用版本。

版本不符合的兩個徵兆為：

- 建置或重新建置專案時，您可能會收到 `Gradle sync failed: Failed to find target with hash string 'android-XX'` 之類的 Gradle 錯誤訊息。
- 錯誤訊息可能是程式碼中必須根據 `import` 陳述式解析的標準 Android 物件所產生。

若出現其中任一項，則 Android Studio 中安裝的 Android SDK 版本可能與下載專案的 SDK 目標不相符。 若要確認版本，請進行下列變更：

1. 在Android工作室中，按一下**工具** > **Android** > SDK**管理器**。 若尚未安裝最新版的 SDK 平台，則按一下以安裝它。 記下版本號碼。

2. 在 [專案總管]**** 索引標籤的 [Gradle 指令碼]**** 下，開啟檔案 **build.gradle (Module: app)**。 確定 **compileSdkVersion** 和 **targetSdkVersion** 已設為最新安裝的 SDK 版本。 `build.gradle` 外觀如下：

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
