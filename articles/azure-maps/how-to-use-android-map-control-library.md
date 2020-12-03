---
title: 開始使用 Azure 地圖服務 Android SDK
description: 熟悉 Microsoft Azure Maps Android SDK。 瞭解如何在 Android Studio 中建立專案、安裝 SDK，以及建立互動式地圖。
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531242"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>開始使用 Azure 地圖服務 Android SDK

Azure 地圖服務 Android SDK 是適用於 Android 的向量地圖庫。 本文會引導您完成安裝 Azure 地圖服務 Android SDK 和載入地圖的程序。

## <a name="prerequisites"></a>Prerequisites

### <a name="create-an-azure-maps-account"></a>建立 Azure 地圖服務帳戶

1. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)
2. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。
如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。
3. [下載並安裝 Google 的 Android Studio](https://developer.android.com/studio/)。

## <a name="create-a-project-in-android-studio"></a>在 Android Studio 中建立專案

完成下列步驟來建立 Android Studio 專案：

1. 啟動 Android Studio。
2. 按一下 [ **+ 建立新專案**]。
3. 在 [ **手機和平板** 電腦] 索引標籤上，按一下 [ **空白活動**]。 按一下 [下一步]  。
4. 在 [設定專案] 底下，選取 `API 21: Android 5.0.0 (Lollipop)` 作為最低版本的 SDK。
5. 選取 `Java` 作為語言。
6. 接受專案的預設值 `Name` 。 按一下 [完成] 。

請參閱 [Android Studio 文件](https://developer.android.com/studio/intro/)，以取得更多如何安裝 Android Studio 並建立新專案的說明。

![在 Android Studio 中建立專案 ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>設定裝置

若要在開發期間測試您的應用程式，您可以使用 Android 手機或 Android 模擬器。

若要深入瞭解如何設定 AVD (Android 虛擬裝置) ，請參閱 [Android Studio 檔](https://developer.android.com/studio/run/managing-avds)。

## <a name="install-the-azure-maps-android-sdk"></a>安裝 Azure 地圖服務 Android SDK

建置應用程式的下一步是安裝 Azure 地圖服務 Android SDK。

完成下列步驟以安裝 SDK：

1. 在 [專案] 索引標籤中，展開 [ **Gradle 腳本**]。 開啟 **gradle (專案： My_Application)**，然後將下列程式碼新增至 [ **所有專案**] `repositories`  區段中：

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 開啟 **gradle (模組： My_Application)**。

3. 請確定區段 **minSdkVersion** 中的 >minsdkversion `defaultConfig` 是在 API 21 或更高版本。

4. 將下列程式碼新增至 Android 區段：

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. 將下列程式碼新增至 `dependencies` 區段：

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. 按一下 **主要工具列上的 [** 檔案]，然後選取 [ **同步處理專案與 Gradle** 檔案]。

7. 開啟 `res > layout > activity_main.xml`。 按一下 `Code` 右上角的 [view]。 在元素內加入下列 XML `<androidx.constraintlayout.widget.ConstraintLayout>` 。
    
    ```XML
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

8. 在檔案中 `java > com.example.myapplication > MainActivity.java` ，您必須：

    * 新增 Azure 地圖服務 SDK 的匯入。
    * 設定您的 Azure 地圖服務驗證資訊。
    * 取得 **>oncreate** 方法中的地圖控制項實例。

    為了避免必須為每個應用程式視圖新增驗證資訊，我們會藉由呼叫來全域設定驗證資訊 `AzureMaps.setSubscriptionKey` 。 `AzureMaps.setAadProperties`如果您想要使用 Azure Active Directory 進行驗證，您也可以呼叫。

    地圖控制項會覆寫 >mainactivity 類別的下列生命週期方法。 這些方法負責管理 Android 的 OpenGL 生命週期。

    * onCreate(Bundle)
    * onStart()
    * onResume()
    * onPause()
    * onStop()
    * onDestroy()
    * onSaveInstanceState(Bundle)
    * onLowMemory()

    編輯檔案，如下所示 `MainActivity.java` ：

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

>[!WARNING]
>Android Studio 可能尚未匯入必要的類別。  因此，程式碼將會有一些無法解析的參考。 若要匯入所需的類別，只要將滑鼠停留在每個無法解析的參考上，然後按 `Alt + Enter` (選項 + 在 Mac) 上返回。

Android Studio 需要幾秒鐘的時間來建置應用程式。 建置完成後，您就可以在模擬的 Android 裝置中測試您的應用程式。 您應該會看到類似下面的地圖：

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Android 應用程式中的 Azure 地圖服務":::

## <a name="localizing-the-map"></a>將地圖當地語系化

Azure 地圖服務 Android SDK 提供三種不同的方式來設定地圖的語言和地區設定。

1. 在 AzureMaps 類別上呼叫靜態方法，以設定語言和地區設定。

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. 定義地圖控制項 XML 中的語言和地區設定。

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. 藉由在地圖控制項上呼叫方法來設定語言和地區設定。 此選項可讓您在執行時間變更設定。

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

以下是將語言設定為的 Azure 地圖服務範例 `fr-FR` 。

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Azure 地圖服務，以法文顯示標籤的地圖影像":::

如需支援的語言和區域檢視的完整清單，請參閱[這裡](supported-languages.md)。

## <a name="navigating-the-map"></a>瀏覽地圖

有數種不同方式可用來對地圖進行縮放、移動瀏覽、旋轉及調整傾斜度。 以下將詳細說明瀏覽地圖的各種不同方式。

**縮放地圖**

- 以兩支手指接觸地圖，捏合即可縮小，張開手指即可放大。
- 在地圖上點兩下可放大一個等級。
- 以兩個手指點一下，將地圖放大一層。
- 點兩下；在點第二下時將手指放在地圖上並向上拖曳可放大，向下拖曳則可縮小。

**移動瀏覽地圖**

- 觸控地圖，然後往任何方向拖曳。

**旋轉地圖**

- 以兩支手指觸控地圖並旋轉。

**調整地圖傾斜度**

- 以兩支手指觸控地圖，然後將其一起向上或向下拖曳。

## <a name="next-steps"></a>後續步驟

了解如何在地圖上新增重疊資料：

> [!div class="nextstepaction"]
> [在 Android 地圖中新增符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [在 Android 地圖中新增圖形](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [變更 Android 地圖中的地圖樣式](./set-android-map-styles.md)