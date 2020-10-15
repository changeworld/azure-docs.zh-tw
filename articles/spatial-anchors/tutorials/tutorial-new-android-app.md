---
title: 教學課程：建立新的 Android 應用程式
description: 在本教學課程中，您將了解如何使用 Azure Spatial Anchors 建立新的 Android 應用程式。
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 06/22/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3ef24e29e5dde90aa829c46d789256e6e5f3233b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85296197"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>教學課程：使用 Azure Spatial Anchors 新建 Android 應用程式的逐步指示

本教學課程將說明如何建立可整合 ARCore 功能與 Azure Spatial Anchors 的新 Android 應用程式。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定您具有下列項目︰

- 具有 <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a> 的 Windows 或 macOS 機器。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">由開發人員啟用</a>且<a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">具備 ARCore 功能</a>的 Android 裝置。

## <a name="getting-started"></a>開始使用

啟動 Android Studio。 在 [歡迎使用 Android Studio] 視窗中，按一下 [開始新的 Android Studio 專案]。 或者，如果您有已開啟的專案，請選取 [檔案]->[新增專案]。

在 [建立新的專案] 視窗的 [手機和平板電腦] 區段下方，選擇 [空白活動]，然後按 [下一步]。 然後，在 [最低 API 層級] 下方選擇 `API 26: Android 8.0 (Oreo)`，並確定 [語言] 設為 `Java`。 您可以變更專案名稱和位置，以及套件名稱。 請將其他選項保留為原狀。 按一下 [完成] 。 **元件安裝程式**將會執行。 完成之後，請按一下 [完成]。 完成某些處理後，Android Studio 會開啟 IDE。

## <a name="trying-it-out"></a>立即試用

若要測試新的應用程式，請使用 USB 纜線，將已啟用開發人員功能的裝置連線至開發電腦。 按一下 [執行]->[執行「應用程式」]。 在 [選取部署目標] 視窗中選取您的裝置，然後按一下 [確定]。 Android Studio 會在您連線的裝置上安裝應用程式，並加以啟動。 此時您應該會看到 "Hello World!" 顯示於您的裝置所執行的應用程式中。 按一下 [執行]->[停止「應用程式」]。

## <a name="integrating-_arcore_"></a>整合 _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> 是 Google 的平台，用以建置擴增實境體驗，讓您的裝置能夠在移動時追蹤本身的位置，並產生它對於實際環境的了解。

請修改 `app\manifests\AndroidManifest.xml` 以包含根 `<manifest>` 節點內的以下項目。 此程式碼片段會執行幾個動作：

- 它會讓您的應用程式存取您的裝置相機。
- 它也會確保只有支援 ARCore 的裝置才能夠在 Google Play 商店中看見您的應用程式。
- 它會設定 Google Play 商店，以在安裝您的應用程式時下載並安裝 ARCore (如果尚未安裝)。

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

請修改 `Gradle Scripts\build.gradle (Module: app)` 以包含下列項目。 此程式碼會確保您的應用程式將以 ARCore 1.8 版作為目標。 進行此變更後，您可能會收到 Gradle 詢問是否要同步的通知：按一下 [立即同步]。

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>整合 _Sceneform_

[_Sceneform_](https://developers.google.com/sceneform/develop/) 可讓您輕鬆地在擴增實境應用程式中呈現逼真的 3D 場景，而不需要學習 OpenGL。

請修改 `Gradle Scripts\build.gradle (Module: app)` 以包含下列項目。 此程式碼可讓您的應用程式使用 Java 8 的語言建構，這是 `Sceneform` 不可或缺的。 它也會確保您的應用程式將以 `Sceneform` 1.8 版為目標，因為其版本必須符合您的應用程式所使用的 ARCore 版本。 進行此變更後，您可能會收到 Gradle 詢問是否要同步的通知：按一下 [立即同步]。

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.11.0'
    ...
}
```

開啟您的 `app\res\layout\activity_main.xml`，並將現有的 Hello Wolrd `<TextView>` 元素取代為下列 ArFragment。 此程式碼會使觀景窗饋送顯示在您的螢幕上，讓 ARCore 能夠在您的裝置移動時追蹤其位置。

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

將您的應用程式[重新部署](#trying-it-out)至裝置，以再次加以驗證。 這次，系統應該會要求您提供相機權限。 獲得核准後，您應該會看到景觀窗饋送呈現在您的螢幕上。

## <a name="place-an-object-in-the-real-world"></a>將物件放在實際環境中

我們將使用您的應用程式建立及放置物件。 首先，請在您的 `app\java\<PackageName>\MainActivity` 中新增下列匯入項目：

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

然後，在您的 `MainActivity` 類別中新增下列成員變數：

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

接著，在 `app\java\<PackageName>\MainActivity` `onCreate()` 方法中新增下列程式碼。 此程式碼將會連接名為 `handleTap()` 的接聽程式，而此接聽程式將會在使用者點選裝置上的螢幕時加以偵測。 如果點選的是 ARCore 的追蹤已辨識的實際介面，接聽程式就會執行。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

最後，新增將會結合所有項目的下列 `handleTap()` 方法。 它會建立圓球，並將其置於點選的位置上。 此圓球一開始會是黑色的，因為 `this.recommendedSessionProgress` 目前設為零。 此值將於稍後調整。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-172,175-183,199-200)]

將您的應用程式[重新部署](#trying-it-out)至裝置，以再次加以驗證。 這次，您可以移動裝置，使 ARCore 開始辨識您的環境。 然後，點選螢幕以建立您的黑色圓球，並將其置於您選擇的介面上。

## <a name="attach-a-local-azure-spatial-anchor"></a>連結本機 Azure Spatial Anchor

請修改 `Gradle Scripts\build.gradle (Module: app)` 以包含下列項目。 此程式碼會確保您的應用程式將以 Azure Spatial Anchors 2.2.0 版為目標。 雖然如此，參考任何最新版的 Azure Spatial Anchors 應該也是可行的。 您可以在[這裡](https://github.com/Azure/azure-spatial-anchors-samples/releases)找到版本資訊。

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

以滑鼠右鍵按一下 [新增]`app\java\<PackageName>`-> **[Java 類別]** ->。 將 [名稱] 設為 MyFirstApp，並將 [Superclass] 設為 android.app.Application。 請將其他選項保留為原狀。 按一下 [確定]。 此時會建立名為 `MyFirstApp.java` 的檔案。 請為其新增下列匯入項目：

```java
import com.microsoft.CloudServices;
```

然後，在新的 `MyFirstApp` 類別內新增下列程式碼內，這將可確保 Azure Spatial Anchors 會使用您的應用程式內容進行初始化。

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

現在，請修改 `app\manifests\AndroidManifest.xml` 以包含根 `<application>` 節點內的以下項目。 此程式碼會將您建立的應用程式類別連接到您的應用程式中。

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

回到 `app\java\<PackageName>\MainActivity`，並在其中新增下列匯入項目：

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

然後，在您的 `MainActivity` 類別中新增下列成員變數：

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

接著，我們將在 `mainActivity` 類別內新增下列 `initializeSession()` 方法。 經呼叫後，它將確保在您的應用程式啟動期間會建立 Azure Spatial Anchors 工作階段，並正確地初始化。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,147)]

現在，我們要將您的 `initializeSession()` 方法連接到 `onCreate()` 方法中。 此外，我們也將確保景觀窗饋送的畫面格會傳送至 Azure Spatial Anchors SDK 進行處理。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

最後，在 `handleTap()` 方法中新增下列程式碼。 它會將本機 Azure Spatial Anchor 連結至我們放在實際環境中的黑色圓球。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-183,199-200&highlight=12-13)]

再次[重新部署](#trying-it-out)您的應用程式。 移動您的裝置、點選螢幕，並放置黑色圓球。 但這次程式碼會建立本機 Azure Spatial Anchor，並將其連結至您的圓球。

在進一步操作之前，您必須先建立 Azure Spatial Anchors 帳戶以取得帳戶識別碼、金鑰和網域 (如果您還沒有這些項目的話)。 請依照下一節的指示加以取得。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>將本機錨點上傳至雲端

取得 Azure Spatial Anchors 帳戶識別碼、金鑰和網域之後，我們可以回到 `app\java\<PackageName>\MainActivity`，並在其中新增下列匯入項目：

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

然後，在您的 `MainActivity` 類別中新增下列成員變數：

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

接著，在 `initializeSession()` 方法中新增下列程式碼。 首先，此程式碼可讓您的應用程式監視 Azure Spatial Anchors SDK 從景觀窗饋送收集畫面格時的進度。 進行收集時，圓球的色彩會開始從原本的黑色變成灰色。 然後，在收集到足夠的畫面格可將錨點提交至雲端時，圓球將變成白色。 其次，此程式碼會提供與雲端後端進行通訊所需的認證。 您將在此處設定應用程式，以使用您的帳戶識別碼、金鑰和網域。 [設定空間錨點資源](#create-a-spatial-anchors-resource)時，將它們複製到文字編輯器中。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-148&highlight=11-37)]

接著，在 `mainActivity` 類別內新增下列 `uploadCloudAnchorAsync()` 方法。 經呼叫後，此方法將以非同步方式等待，直到從您的裝置收集到足夠的畫面格。 一旦達到此條件，它就會將圓球的色彩切換為黃色，然後開始將您的本機 Azure Spatial Anchor 上傳至雲端。 上傳完成後，此程式碼會傳回錨點識別碼。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

最後，我們要將所有項目連結在一起。 在您的 `handleTap()` 方法中新增下列程式碼。 它會在您的圓球建立時隨即叫用 `uploadCloudAnchorAsync()` 方法。 此方法傳回後，下列程式碼會對您的圓球執行最後的更新，將其色彩變更為藍色。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-200&highlight=24-37)]

再次[重新部署](#trying-it-out)您的應用程式。 移動您的裝置、點選螢幕，並放置圓球。 但這次，隨著相機畫面格的收集，圓球的色彩將從黑色變成白色。 一旦有足夠的畫面格時，圓球就會變成黃色，並開始進行雲端上傳。 上傳完成後，圓球就會變成藍色。 (選擇性) 您也可以使用 Android Studio 內的 `Logcat` 視窗來監視應用程式傳送的記錄訊息。 例如，畫面格擷取期間的工作階段進度，和雲端在上傳完成時傳回的錨點識別碼。

## <a name="locate-your-cloud-spatial-anchor"></a>找出您的雲端空間錨點

在錨點上傳至雲端後，我們即可嘗試再次將其找出。 首先，我們將在程式碼中新增下列匯入項目。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

接著，我們在 `handleTap()` 方法中新增下列程式碼。 此程式碼將會：

- 從螢幕中移除現有的藍色圓球。
- 重新初始化 Azure Spatial Anchors 工作階段。 此動作可確保我們要找出的錨點會來自雲端，而不是我們所建立的本機錨點。
- 針對我們上傳至雲端的錨點發出查詢。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

現在，我們將連結在尋找所要查詢的錨點時所將叫用的程式碼。 在您的 `initializeSession()` 方法中新增下列程式碼。 此程式碼片段會在找到雲端空間錨點時建立並放置綠色圓球。 此外也會再次啟用螢幕點選，讓您能夠再次重複整個案例：建立另一個本機錨點、加以上傳，並再次將其找出。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

就這麼簡單！ 最後一次[重新部署](#trying-it-out)您的應用程式，並嘗試進行整個端對端案例。 移動您的裝置，並放置黑色圓球。 然後，繼續移動您的裝置以擷取相機畫面格，直到圓球變成黃色。 您的本機錨點將會上傳，且圓球會變成藍色。 最後，再次點選螢幕，以移除本機錨點，接著我們將查詢其雲端對應項目。 繼續移動您的裝置，直到找到雲端空間錨點為止。 綠色圓球應會出現在正確的位置，而您可以清除並再次重複整個案例。

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
