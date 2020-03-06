---
title: Android 地圖控制項使用者入門 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Android SDK 開始使用 Android 地圖控制項。
author: farah-alyasari
ms.author: v-faalya
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ca4051e56f7d51ac7ac5fbe534cb8fc536d64a68
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78368680"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure 地圖服務 Android SDK 入門

Azure 地圖服務 Android SDK 是適用于 Android 的向量地圖程式庫。 本文會引導您完成安裝 Azure 地圖服務 Android SDK 和載入對應的程式。

## <a name="prerequisites"></a>Prerequisites

### <a name="create-an-azure-maps-account"></a>建立 Azure 地圖服務帳戶

若要完成本文中的程式，您必須先在 S1 定價層中[建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)，並取得帳戶的[主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

### <a name="download-android-studio"></a>下載 Android Studio

在安裝 Azure 地圖服務 Android SDK 之前，請先下載 Android Studio 並建立具有空白活動的專案。 您可以從 Google 免費[下載 Android Studio](https://developer.android.com/studio/) 。 

## <a name="create-a-project-in-android-studio"></a>在 Android Studio 中建立專案

首先，建立具有空白活動的新專案。 請完成下列步驟來建立 Android Studio 專案：

1. 在 **[選擇您的專案**] 底下，選取 [**手機和平板**電腦]。 您的應用程式將以此外型規格執行。
2. 在 [**電話和平板**電腦] 索引標籤上選取 [**空白活動**]，然後選取 **[下一步]** 。
3. 在 [設定專案] 底下，選取 `API 21: Android 5.0.0 (Lollipop)` 作為最低版本的 SDK。 這是 Azure 地圖服務 Android SDK 所支援的最早版本。
4. 接受預設 `Activity Name` 並 `Layout Name`，然後選取 **[完成]** 。

如需安裝 Android Studio 和建立新專案的詳細說明，請參閱[Android Studio 檔](https://developer.android.com/studio/intro/)。

![在 Android studio 中建立專案 ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>設定虛擬裝置

Android Studio 可讓您在電腦上設定虛擬 Android 裝置。 這麼做可以協助您在開發期間測試應用程式。 若要設定虛擬裝置，請選取專案畫面右上角的 [Android 虛擬裝置（AVD）管理員] 圖示，然後選取 [**建立虛擬裝置**]。 您也可以從工具列中選取 [**工具**] > [ **Android** > **AVD 管理員**] 來取得 AVD 管理員。 在 [**電話**] 類別中，選取 [**結點 5x**]，然後選取 **[下一步]** 。

您可以在[Android Studio 檔](https://developer.android.com/studio/run/managing-avds)中深入瞭解如何設定 AVD。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>安裝 Azure 地圖服務 Android SDK

建立應用程式的下一步是安裝 Azure 地圖服務 Android SDK。 完成下列步驟以安裝 SDK：

1. 開啟頂層 **build.gradle** 檔案，並將下列程式碼新增至 [所有專案]、[存放庫區塊] 區段：

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
    
    4. 移至工具列中 [檔案]，然後按一下 [同步處理專案與 Gradle 檔案]。
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
    * 在 **onCreate** 方法中取得地圖控制項執行個體

    使用 `setSubscriptionKey` 或 `setAadProperties` 方法，全域設定 `AzureMaps` 類別的驗證資訊，讓您不需要在每個視圖上新增驗證資訊。 

    地圖控制項包含其本身用來管理 Android OpenGL 生命週期的生命週期方法。 這些生命週期方法必須直接從包含活動呼叫。 若要讓您的應用程式正確地呼叫地圖控制項的生命週期方法，您必須覆寫活動中包含地圖控制項的下列生命週期方法。 而且，您必須呼叫個別的地圖控制方法。 

    * onCreate （配套） 
    * onStart （） 
    * onResume （） 
    * onPause （） 
    * onStop （） 
    * onDestroy() 
    * onSaveInstanceState （配套） 
    * onLowMemory() 

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

## <a name="import-classes"></a>匯入類別

完成上述步驟之後，您可能會收到關於部分程式碼 Android Studio 的警告。 若要解決這些警告，請匯入 `MainActivity.java`中參考的類別。

您可以選取 [Alt + Enter] （Mac 上的 Option + Return）來自動匯入這些類別。

選取 [執行] 按鈕，如下圖所示（或在 Mac 上按 Control + R），以建立您的應用程式。

![按一下 [執行]](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio 需要幾秒鐘的時間來建立應用程式。 組建完成之後，您可以在模擬的 Android 裝置中測試您的應用程式。 您應該會看到類似下面的地圖：

<center>

Android 應用程式中的 ![Azure 地圖服務](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>將地圖當地語系化

Azure 地圖服務 Android SDK 提供三種不同的方式來設定地圖的語言和地區視圖。 下列程式碼示範如何將語言設定為法文（"fr-fr"）和地區視圖，以「自動」的方式顯示。 

第一個選項是使用靜態的 `setLanguage` 和 `setView` 方法，將語言和區域資訊以全域方式傳遞至 `AzureMaps` 類別。 這會在應用程式中載入的所有 Azure 地圖服務控制項之間設定預設語言和地區視圖。

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

以下是語言設為 "fr-fr" 且地區視圖設為 "auto" 的 Azure 地圖服務範例。

<center>

![Azure 地圖服務，以法文](./media/how-to-use-android-map-control-library/android-localization.png)
顯示標籤的地圖影像 </center>

如需支援的語言和區域檢視的完整清單，請參閱[這裡](supported-languages.md)。

## <a name="next-steps"></a>後續步驟

瞭解如何在地圖上新增重迭資料：

> [!div class="nextstepaction"]
> [將符號圖層新增至 Android 地圖](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [將圖形新增至 Android 地圖](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [變更 Android 地圖中的地圖樣式](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
