---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: travisw
ms.openlocfilehash: 2efe17bdf597b7e3ba7d0277f06d4b24e9b51db3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187551"
---
1. 啟動 Android Studio，然後在 [歡迎使用] 視窗中選取 [開始新的 Android Studio 專案]。

    ![Android Studio 歡迎使用視窗的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. [選擇專案] 精靈會隨即顯示。 請選取活動選取方塊中的 [手機和平板電腦] 和 [空白活動]。 選取 [下一步] 。

   ![選擇專案精靈的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. 在 [設定您的專案] 畫面中，輸入 *Quickstart* 作為 **名稱**，並輸入 *samples.speech.cognitiveservices.microsoft.com* 作為 **套件名稱**。 然後選擇專案目錄。 針對 [最低 API 層級]，選取 **[API 23：Android 6.0 (Marshmallow)]** 。 所有其他核取方塊保持在不勾選的狀態，然後選取 [完成]。

   ![設定專案精靈的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studio 需要一些時間來準備您新的 Android 專案。 接著，請設定專案以了解 Azure 認知服務語音 SDK 並使用 Java 8。

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

認知服務語音 SDK 最新的版本為 1.13.0。

適用於 Android 的語音 SDK 會封裝成 [AAR (Android 程式庫)](https://developer.android.com/studio/projects/android-library) \(英文\)，其中包含必要的程式庫及所需的 Android 權限。
它會裝載在位於 https:\//csspeechstorage.blob.core.windows.net/maven/ 的 Maven 存放庫中。

將您的專案設定為使用語音 SDK。 從 Android Studio 功能表列中選取 [檔案] > [專案結構]，以開啟 [專案結構] 視窗。 在 [專案結構] 視窗中，進行下列變更：

1. 在位於視窗左側的清單中，選取 [專案]。 藉由附加逗號並將 Maven 存放庫 URL 放在單引號中：'https:\//csspeechstorage.blob.core.windows.net/maven/'，以編輯 [預設程式庫存放庫]

   ![專案結構視窗的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. 在相同畫面中的左側，選取 [應用程式]。 然後，選取視窗頂端的 [相依性] 索引標籤。 選取綠色加號 ( **+** )，然後從下拉式功能表中選取 [程式庫相依性]。

   ![程式庫相依性的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. 在出現的視窗中，輸入適用於 Android 之的語音 SDK 的名稱與版本：com.microsoft.cognitiveservices.speech:client-sdk:1.13.0。 然後選取 [確定]。
   語音 SDK 現在應該會加入至相依性的清單，如下所示：

   ![相依性清單中的語音 SDK 螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. 選取 [屬性] 索引標籤。針對 [來源相容性] 和 [目標相容性] 兩者，都選取 [1.9]。

   ![來源相容性和目標相容性的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. 選取 [確定] 以關閉 [專案結構] 視窗，並將您的變更套用至專案。
