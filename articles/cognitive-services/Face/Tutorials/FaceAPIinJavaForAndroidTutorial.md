---
title: 教學課程：使用 Android SDK 偵測並框出影像中的人臉
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您會建立簡單的 Android 應用程式，以使用臉部辨識服務來偵測並框出影像中的臉部。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 8d5bef141f83eedaa996bb63c1fb814aeb6af197
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76165973"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>教學課程：建立 Android 應用程式來偵測並框出影像中的臉部

在本教學課程中，您會透過 Java SDK 建立 Android 應用程式，以使用 Azure 臉部辨識服務來偵測影像中的人臉。 此應用程式會顯示選取的影像，並在每個偵測到的人臉周圍繪製框架。

本教學課程說明如何：

> [!div class="checklist"]
> - 建立 Android 應用程式
> - 安裝臉部用戶端程式庫
> - 使用用戶端程式庫來偵測影像中的臉部
> - 在所偵測到的每個臉部周圍繪出邊框

![相片中的臉部以紅色矩形框住的 Android 螢幕擷取畫面](../Images/android_getstarted2.1.PNG)

GitHub 上的[認知服務臉部 Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) 存放庫會有完整的程式碼範例。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 

## <a name="prerequisites"></a>Prerequisites

- 臉部訂用帳戶金鑰。 您可以從[試用認知服務](https://azure.microsoft.com/try/cognitive-services/?api=face-api)取得免費的試用訂用帳戶金鑰。 或是，依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱臉部辨識服務並取得金鑰。 然後，分別為名為 [ 和 ](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) 的金鑰和服務端點字串`FACE_SUBSCRIPTION_KEY`建立環境變數`FACE_ENDPOINT`。
- 任何 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/) 版本。
- [Android Studio](https://developer.android.com/studio/)，API 層級為 22 以上 (臉部用戶端程式庫所需)。

## <a name="create-the-android-studio-project"></a>建立 Android Studio 專案

請遵循下列步驟以建立新的 Android 應用程式專案。

1. 在 Android Studio 中，選取 [啟動新的 Android Studio 專案]  。
1. 在 [建立 Android 專案]  畫面上，如有必要可修改預設欄位，然後按 [下一步]  。
1. 在 [目標 Android 裝置]  畫面上，使用下拉式選取器選擇 [API 22]  或更高版本，然後按 [下一步]  。
1. 選取 [空白活動]  ，然後按 [下一步]  。
1. 取消核取 [回溯相容性]  ，然後按一下 [完成]  。

## <a name="add-the-initial-code"></a>新增初始程式碼

### <a name="create-the-ui"></a>建立 UI

開啟 activity_main.xml  。 在配置編輯器中，選取 [文字]  索引標籤，然後將其中的內容取代為下列程式碼。

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>建立主類別

開啟 MainActivity.java  ，然後以下列程式碼取代現有 `import` 陳述式。

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

然後，將 **MainActivity** 類別的內容取代為下列程式碼。 這會對 **Button** 建立事件處理常式，以啟動新的活動讓使用者可以選取圖片。 它會在 **ImageView** 中顯示圖片。

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>試用應用程式

將 **onActivityResult** 方法中 **detectAndFrame** 的呼叫註解。 然後，按下功能表上的 [執行]  來測試應用程式。 當應用程式開啟時，在模擬器或連線的裝置中，按一下底部的 [瀏覽]  。 裝置的檔案選取對話方塊應該會隨即出現。 選擇影像，並確認影像有顯示在視窗中。 然後，關閉應用程式，並進入下一個步驟。

![相片中具有臉部的 Android 螢幕擷取畫面](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>新增臉部 SDK

### <a name="add-the-gradle-dependency"></a>新增 Gradle 相依性

在 [專案]  窗格中，使用下拉式選取器來選取 [Android]  。 展開 [Gradle 指令碼]  ，然後開啟 build.gradle (模組：應用程式)  。 為臉部用戶端程式庫 `com.microsoft.projectoxford:face:1.4.3` 新增相依性 (如以下螢幕擷取畫面所示)，然後按一下 [立即同步]  。

![build.gradle 應用程式檔案的 Android Studio 螢幕擷取畫面](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>新增臉部相關專案的程式碼

返回 **MainActivity.java** 並新增下列 `import` 陳述式：

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

然後，在 **MainActivity** 類別中，於 **onCreate** 方法之上插入下列程式碼：

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

在 [專案]  窗格中，依序展開 [應用程式]  和 [資訊清單]  ，然後開啟 AndroidManifest.xml  。 將下列元素插入為 `manifest` 元素的直接子系：

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>上傳影像和偵測臉部

應用程式會藉由呼叫 **faceClient.Face.DetectWithStreamAsync** 方法來偵測臉部，此方法會包裝[偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API，並傳回**臉部**執行個體的清單。

每個傳回的**臉部**都會包含矩形來指出其位置，以及一系列的選擇性臉部屬性。 在此範例中，我們只會要求臉部矩形。

將下列兩種方法插入到 **MainActivity** 類別。 當臉部偵測完成時，應用程式會呼叫 **drawFaceRectanglesOnBitmap** 方法來修改 **ImageView**。 您接下來便會定義這個方法。

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>繪製臉部矩形

將下列協助程式方法插入 **MainActivity** 類別中。 此方法會使用每個**臉部**執行個體的矩形座標，在所偵測到的每個臉部周圍繪製矩形。

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

最後，在 **onActivityResult** 中將 **detectAndFrame** 方法的呼叫取消註解。

## <a name="run-the-app"></a>執行應用程式

執行此應用程式，然後瀏覽具有臉部的影像。 請等候幾秒，讓臉部服務回應。 您應該會看到影像中的每個臉部上有一個紅色矩形。

![Android 螢幕擷取畫面，臉部且周圍繪有紅色矩形](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解臉部 Java SDK 的基本使用程序，並建立了應用程式來偵測並框出影像中的臉部。 接下來，請深入了解臉部偵測的詳細資料。

> [!div class="nextstepaction"]
> [如何偵測影像中的人臉](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
