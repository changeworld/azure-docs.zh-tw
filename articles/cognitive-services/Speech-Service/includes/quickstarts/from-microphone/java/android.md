---
title: 快速入門：從麥克風辨識語音，Java (Android) - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 Android 上以 Java 辨識語音
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: 2641464e52b80c59b6c6b5a123ad1605c1d41495
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275470"
---
## <a name="prerequisites"></a>Prerequisites

開始之前：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定開發環境](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * 確定您可以存取麥克風以擷取音訊

## <a name="create-a-user-interface"></a>建立使用者介面

現在，我們將建立應用程式的基本使用者介面。 編輯您主要活動的版面配置 `activity_main.xml`。 最初，版面配置會包含具有您應用程式名稱的標題列，以及包含 "Hello World!" 文字的 TextView。

* 選取 TextView 元素。 在右上角將其 ID 屬性變更為 `hello`。

* 從 `activity_main.xml` 視窗左上方的 [調色盤] 中，將按鈕拖曳至文字上方的空白處。

* 在右側的按鈕屬性中，針對 `onClick` 屬性的值，輸入 `onSpeechButtonClicked`。 我們將以此名稱撰寫用來處理按鈕事件的方法。 在右上角將其 ID 屬性變更為 `button`。

* 使用設計工具頂端的魔術棒圖示，推斷版面配置條件約束。

  ![魔術棒圖示的螢幕擷取畫面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

UI 的文字表現方式和畫面現在應會如下：

![使用者介面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟來源檔案 `MainActivity.java`。 以下列程式碼取代此檔案中的所有程式碼：

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` 方法包含要求麥克風和網際網路權限，以及初始化原生平台繫結的程式碼。 原生平台繫結只需要設定一次。 此設定應該在應用程式初始化初期完成。

   * 如先前所述，方法 `onSpeechButtonClicked` 是按鈕點擊處理常式。 按下按鈕就會觸發語音轉換文字。

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 同時將 `YourServiceRegion` 字串取代為訂用帳戶所關聯[區域](https://aka.ms/speech/sdkregion)中的 [區域識別碼]  。 例如，針對免費試用訂用帳戶使用 `westus`。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 將 Android 裝置連接到開發電腦。 請確定您已在裝置上啟用[開發模式和 USB 偵錯](https://developer.android.com/studio/debug/dev-options)。

1. 若要建置應用程式，請選取 Ctrl+F9，或從功能表列中選取 [建置]   > [建立專案]  。

1. 若要啟動應用程式，請選取 Shift+F10，或選取 [執行]   > [執行應用程式]  。

1. 在出現的 [部署目標] 視窗中，選取您的 Android 裝置。

   ![選取部署目標視窗的螢幕擷取畫面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

選取應用程式中的按鈕，開始進行一段語音辨識。 接下來 15 秒的英文語音會傳送到語音服務，並進行轉譯。 結果會出現在 Android 應用程式中，以及 Android Studio 的 Logcat 視窗中。

![Android 應用程式的螢幕擷取畫面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
