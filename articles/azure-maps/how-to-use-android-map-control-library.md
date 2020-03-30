---
title: 開始使用 Android 地圖控制項 |微軟 Azure 地圖
description: 在本文中，您將學習如何使用 Microsoft Azure 地圖 Android SDK 開始使用 Android 地圖控制項。
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 55bfb5030d0a168e7556240212fcd5f3be30a289
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335355"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>開始使用 Azure 地圖 Android SDK

Azure 地圖 Android SDK 是 Android 的向量映射庫。 本文將指導您完成安裝 Azure 地圖 Android SDK 和載入地圖的過程。

## <a name="prerequisites"></a>Prerequisites

### <a name="create-an-azure-maps-account"></a>建立 Azure 地圖服務帳戶

要完成本文中的過程，首先需要在 S1 定價層中[創建 Azure 地圖帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)，並[獲取帳戶的主金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

### <a name="download-android-studio"></a>下載安卓工作室

在安裝 Azure 地圖 Android SDK 之前，下載 Android Studio 並創建活動為空的專案。 你可以從谷歌免費[下載Android工作室](https://developer.android.com/studio/)。 

## <a name="create-a-project-in-android-studio"></a>在 Android Studio 中建立專案

首先，創建一個活動為空的新專案。 完成這些步驟以創建 Android 工作室專案：

1. 在 **"選擇您的專案**"下，選擇 **"手機"和"平板電腦**"。 您的應用程式將在此外形上運行。
2. 在 **"電話和平板電腦"** 選項卡上，選擇 **"空活動**"，然後選擇 **"下一步**"。
3. 在 [設定專案]**** 底下，選取 `API 21: Android 5.0.0 (Lollipop)` 作為最低版本的 SDK。 這是 Azure 地圖 Android SDK 支援的最早版本。
4. 接受預設值`Activity Name`並`Layout Name`選擇 **"完成**"。

有關安裝 Android Studio 和創建新專案的更多説明，請參閱[Android Studio 文檔](https://developer.android.com/studio/intro/)。

![在 Android 工作室中創建專案 ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>設定虛擬裝置

Android Studio 可讓您在電腦上設定虛擬 Android 裝置。 這樣做可以説明您在開發期間測試應用程式。 要設置虛擬裝置，請在專案螢幕右上角選擇 Android 虛擬裝置 （AVD） 管理器圖示，然後選擇"**創建虛擬裝置**"。 您還可以通過從工具列中選擇**工具** > **Android** > **AVD 管理器**來訪問 AVD 管理器。 在 **"電話"** 類別中，選擇**Nexus 5X**，然後選擇 **"下一步**"。

你可以瞭解更多關於在[Android工作室文檔中](https://developer.android.com/studio/run/managing-avds)設置AVD。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>安裝 Azure 地圖 Android SDK

構建應用程式的下一步是安裝 Azure 映射 Android SDK。 完成這些步驟以安裝 SDK：

1. 開啟頂層 **build.gradle** 檔案，並將下列程式碼新增至 [所有專案]****、[存放庫區塊]**** 區段：

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 更新 **app/build.gradle**，並於其中新增下列程式碼：
    
    1. 確定專案的 **minSdkVersion** 是 API 21 或更新版本。

    2. 將下列程式碼新增至 Android 區段：

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. 更新您的相依性區塊，並且為最新的 Azure 地圖服務 Android SDK 新增實作相依性程式行：

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```
    
    4. 移至工具列中 [檔案]****，然後按一下 [同步處理專案與 Gradle 檔案]****。
3. 將地圖片段新增至主要活動 (res \> layout \> activity\_main.xml)：
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. 在 **MainActivity.java** 檔案中，您將必須：
    
    * 新增 Azure 地圖服務 SDK 的匯入
    * 設定您的 Azure 地圖服務驗證資訊
    * 獲取**onCreate**方法中的地圖控制實例

    使用 `setSubscriptionKey` 或 `setAadProperties` 方法全域設定 `AzureMaps` 類別的驗證資訊，而無須在每個檢視上新增您的驗證資訊。 

    地圖控制項包含其本身用來管理 Android OpenGL 生命週期的生命週期方法。 這些生命週期方法必須直接從包含活動調用。 要使應用正確調用地圖控制項的生命週期方法，必須覆蓋包含地圖控制項的"活動"中的以下生命週期方法。 而且，您必須調用相應的地圖控制項方法。 

    * 打開創建（捆綁） 
    * 開始（） 
    * 上簡歷（） 
    * 上暫停（） 
    * 上停止（） 
    * 上銷毀（） 
    * 在保存實例狀態（捆綁） 
    * 在低記憶體（） 

    編輯 **MainActivity.java** 檔案，如下所示：
    
    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

## <a name="import-classes"></a>導入類

完成上述步驟後，您可能會收到來自 Android Studio 有關某些代碼的警告。 要解決這些警告，導入 中引用的`MainActivity.java`類。

您可以通過選擇 Alt_Enter（選項+在 Mac 上返回）自動導入這些類。

選擇運行按鈕（如下圖所示（或在 Mac 上按 Control+R）來構建應用程式。

![按一下 [執行]](./media/how-to-use-android-map-control-library/run-app.png)

Android工作室將需要幾秒鐘來構建應用程式。 生成完成後，您可以在類比的 Android 設備中測試應用程式。 您應該會看到如下所示的地圖：

<center>

![Android 應用程式中的 Azure 映射](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>將地圖當地語系化

Azure 地圖 Android SDK 提供了三種不同的方法來設置地圖的語言和區域視圖。 以下代碼演示如何將語言設置為法語（"fr-FR"），區域視圖設置為"自動"。 

第一個選項是使用靜態`AzureMaps``setLanguage`和方法`setView`全域傳遞語言並將區域資訊傳遞到類中。 這將在應用中載入的所有 Azure 地圖控制項中設置預設語言和區域視圖。

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

第二個選項是將語言和檢視資訊傳入地圖控制項 XML 中。

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

第三個選項是使用地圖 `setStyle` 方法，以程式設計方式設定地圖的語言和區域檢視。 此方法可隨時執行，以變更地圖的語言和區域檢視。

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

下面是 Azure 地圖的示例，其語言設置為"fr-FR"，區域視圖設置為"自動"。

<center>

![Azure 地圖，以法語顯示標籤的地圖圖像](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

如需支援的語言和區域檢視的完整清單，請參閱[這裡](supported-languages.md)。

## <a name="next-steps"></a>後續步驟

瞭解如何在地圖上添加疊加資料：

> [!div class="nextstepaction"]
> [向 Android 地圖添加符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [將形狀添加到 Android 地圖](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [更改 Android 地圖中的地圖樣式](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
