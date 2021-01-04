---
title: 教學課程 - 遷移 Android 應用程式 | Microsoft Azure 地圖服務
description: 如何將 Android 應用程式從 Google Maps 遷移至 Microsoft Azure 地圖服務的教學課程
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: f4b0642ce54b862b4d4c7b9663cf10e74b206281
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680490"
---
# <a name="tutorial-migrate-an-android-app-from-google-maps"></a>教學課程：從 Google 地圖遷移 Android 應用程式

Azure 地圖服務 Android SDK 具有與 Web SDK 類似的 API 介面。 如果您已使用其中一個 SDK 進行開發，則可套用許多相同的概念、最佳做法和架構。 在本教學課程中，您將學會如何：

> [!div class="checklist"]
> * 載入地圖
> * 將地圖當地語系化
> * 新增標記、聚合線條和多邊形。
> * 覆蓋地圖底圖圖層
> * 顯示流量資料

Azure 地圖服務 Android SDK 支援的最低 Android 版本為 API 21：Android 5.0.0 (Lollipop)。

所有範例均以 Java 提供，但是您可以搭配 Azure 地圖服務 Android SDK 使用 Kotlin。

如需搭配 Azure 地圖服務使用 Android SDK 進行開發的詳細資訊，請參閱 [Azure 地圖服務 Android SDK 的操作指南](how-to-use-android-map-control-library.md)。

## <a name="prerequisites"></a>必要條件

1. 藉由登入 [Azure 入口網站](https://portal.azure.com) 來建立 Azure 地圖服務帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
2. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)
3. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](how-to-manage-authentication.md)。

## <a name="load-a-map"></a>載入地圖

使用 Google Maps 或 Azure 地圖服務在 Android 應用程式中載入地圖的步驟幾乎相同。 使用任一種 SDK 時，您都必須：

* 取得 API 或訂用帳戶金鑰，以存取任一平台。
* 將 XML 新增至活動，以指定應呈現地圖的位置，及其版面配置方式。
* 將包含地圖檢視的活動中所有生命週期方法，覆寫為地圖類別中的對應方法。 特別是，您必須覆寫下列方法：
  * `onCreate(Bundle)`
  * `onStart()`
  * `onResume()`
  * `onPause()`
  * `onStop()`
  * `onDestroy()`
  * `onSaveInstanceState(Bundle)`
  * `onLowMemory()`
* 等到地圖準備就緒後，再嘗試存取及進行程式設計。

### <a name="before-google-maps"></a>之前：Google 地圖

若要使用適用於 Android 的 Google 地圖 SDK 來顯示地圖，應執行下列步驟：

1. 確定已安裝 Google Play 服務。
2. 將 Google Maps 服務的相依性新增至模組的 **gradle.build** 檔案：

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

3. 在 **google\_maps\_api.xml** 檔案的應用程式區段內，新增 Google 地圖 API 金鑰：

    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

4. 將地圖片段新增至主要活動：

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

5. 在 **MainActivity.java** 檔案中，您必須匯入 Google Maps SDK。 將包含地圖檢視的活動中所有的生命週期方法，轉送至地圖類別中的對應方法。 使用 `getMapAsync(OnMapReadyCallback)` 方法，從地圖片段中擷取 `MapView` 執行個體。 `MapView` 會自動初始化地圖系統和檢視。 編輯 **MainActivity.java** 檔案，如下所示：

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

當您執行應用程式時，地圖控制項會以下列影像形式載入。

![簡單的 Google 地圖](media/migrate-google-maps-android-app/simple-google-maps.png)

### <a name="after-azure-maps"></a>之後：Azure 地圖服務

若要使用適用於 Android 的 Azure 地圖服務 SDK 來顯示地圖，必須執行下列步驟：

1. 開啟頂層 **build.gradle** 檔案，並將下列程式碼新增至 [所有專案]區塊區段：

    ```java
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 更新 **app/build.gradle**，並於其中新增下列程式碼：

    1. 確定專案的 **minSdkVersion** 是 API 21 或更新版本。

    2. 將下列程式碼新增至 Android 區段：

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. 更新您的相依性區塊。 為最新的 Azure 地圖服務 Android SDK 新增實作相依性程式行：

        ```Java
        implementation "com.microsoft.azure.maps:mapcontrol:0.6"
        ```

        > [!Note]
        > 您也可以將版本號碼設定為 "0+"，使您的程式碼一律指向最新版本。

    4. 移至工具列中 [檔案]，然後按一下 [同步處理專案與 Gradle 檔案]。

3. 將地圖片段新增至主要活動 (resources pwd\> layout \> activity\_main.xml)：

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

    * 匯入 Azure 地圖服務 SDK
    * 設定您的 Azure 地圖服務驗證資訊
    * 在 **onCreate** 方法中取得地圖控制項執行個體

     使用 `setSubscriptionKey` 或 `setAadProperties` 方法，在 `AzureMaps` 類別中設定驗證資訊。 此全域更新可確保驗證資訊會新增至每個檢視。

    地圖控制項包含其本身用來管理 Android OpenGL 生命週期的生命週期方法。 這些方法必須直接從包含的活動中呼叫。 若要正確地呼叫地圖控制項的生命週期方法，您必須在包含地圖控制項的活動中覆寫下列生命週期方法。 呼叫各自的地圖控制方法。

    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`

    編輯 **MainActivity.java** 檔案，如下所示：

    ```Java
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

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
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
    }}
    ```

執行應用程式時，地圖控制項會以下列影像的形式載入。

![簡單的 Azure 地圖服務](media/migrate-google-maps-android-app/simple-azure-maps.png)

請注意，Azure 地圖服務控制項支援更高倍數的縮小，而更能提供涵蓋全世界的檢視。

> [!TIP]
> 如果您在 Windows 機器中使用 Android 模擬器，則地圖可能會因為與 OpenGL 和軟體加速圖形轉譯發生衝突，而不會呈現。 下列方法可協助某些人員解決此問題。 開啟 [AVD 管理員]，然後選取要編輯的虛擬裝置。 向下捲動 [確認設定] 面板。 在 [模擬的效能] 區段中，將 [圖形] 選項設定為 [硬體]。

## <a name="localizing-the-map"></a>將地圖當地語系化

如果您的受眾分散在多個國家/區域或使用不同語言，當地語系化便很重要。

### <a name="before-google-maps"></a>之前：Google 地圖

將下列程式碼新增至 `onCreate` 方法，以設定地圖語言。 您必須先加入程式碼，才能設定地圖的內容檢視。 "fr" 語言代碼會將語言限制為法文。

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

以下是語言設定為 "fr" 的 Google 地圖範例。

![Google 地圖當地語系化](media/migrate-google-maps-android-app/google-maps-localization.png)

### <a name="after-azure-maps"></a>之後：Azure 地圖服務

Azure 地圖服務提供三種不同的方式供您設定地圖的語言和區域檢視。 第一個選項是將語言和區域檢視資訊傳入 `AzureMaps` 類別中。 此選項會全域使用靜態 `setLanguage` 和 `setView` 方法。 也就是說，在應用程式中載入的所有 Azure 地圖服務控制項上，都會設定該預設語言和區域檢視。 此範例會使用 "fr-FR" 語言代碼來設定法文。

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

第二個選項是將語言和檢視資訊傳入地圖控制項 XML 程式碼中。

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

第三個選項是使用地圖 `setStyle` 方法，以程式設計方式設定語言和區域檢視。 此選項會在每次程式碼執行時更新語言和區域檢視。

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

以下是語言設定為 "fr-FR" 的 Azure 地圖服務範例。

![Azure 地圖服務當地語系化](media/migrate-google-maps-android-app/azure-maps-localization.png)

請參閱[支援語言](supported-languages.md)的完整清單。

## <a name="setting-the-map-view"></a>設定地圖檢視

您可以藉由呼叫適當的方法，以程式設計方式將 Azure 地圖服務和 Google 地圖中的動態地圖移至新的地理位置。 我們現在要讓地圖顯示衛星空照圖影像，並將地圖置中於某個座標位置，以及變更縮放層級。 在此範例中，我們將使用緯度：35.0272、經度：-111.0225，並將縮放層級設為 15。

### <a name="before-google-maps"></a>之前：Google 地圖

您可以使用 `moveCamera` 方法，以程式設計方式移動 Google Maps 的地圖控制項的相機。 `moveCamera` 方法可讓您指定地圖的中心和縮放層級。 `setMapType` 方法會將地圖的類型變更為 [顯示]。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

![Google 地圖設定的檢視](media/migrate-google-maps-android-app/google-maps-set-view.png)

> [!NOTE]
> Google 地圖所使用的地圖底圖維度為 256 像素，而 Azure 地圖服務則使用較大的 512 像素地圖底圖。 在載入和 Google 地圖相同的地圖區域時，這種設定會讓 Azure 地圖服務減少所需的網路要求數目。 若要達到與 Google Maps 中地圖相同的可檢視區域，您必須在使用 Azure 地圖服務時，將 Google Maps 中使用的縮放層級減一。

### <a name="after-azure-maps"></a>之後：Azure 地圖服務

如前所述，若要在 Azure 地圖服務中達到相同的可檢視區域，請將 Google 地圖中使用的縮放層級減一。 在此案例中，請使用縮放層級 14。

初始地圖檢視可在地圖控制項的 XML 屬性中設定。

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

地圖檢視可使用地圖的 `setCamera` 和 `setStyle` 方法來進行程式設計。

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-111.0225, 35.0272)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

![Azure 地圖服務設定的檢視](media/migrate-google-maps-android-app/azure-maps-set-view.png)

**其他資源：**

* [支援的地圖樣式](supported-map-styles.md)

## <a name="adding-a-marker"></a>新增標記

點資料常會使用地圖上的影像來呈現。 這些影像稱為標記、圖釘、釘選或符號。 下列範例會將點資料呈現為地圖上的標記，座標為緯度：51.5、經度：-0.2 上。

### <a name="before-google-maps"></a>之前：Google 地圖

使用 Google 地圖時，會使用地圖 `addMarker` 方法來新增標記。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

![Google 地圖標記](media/migrate-google-maps-android-app/google-maps-marker.png)

### <a name="after-azure-maps"></a>之後：Azure 地圖服務

在 Azure 地圖服務中，若要在地圖上呈現位置點資料，須先將點資料新增至資料來源。 然後，將該資料來源連接至符號圖層。 資料來源會在地圖控制項中最佳化空間資料的管理。 符號圖層會指定如何使用影像或文字來呈現位置點資料。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

![Azure 地圖服務的標記](media/migrate-google-maps-android-app/azure-maps-marker.png)

## <a name="adding-a-custom-marker"></a>新增自訂標記

自訂影像可用來代表地圖上的位置點。 下列範例中的地圖會使用自訂影像在地圖上顯示位置點。 位置點在緯度：51.5 及經度：-0.2。 錨點會位移標記位置，讓圖釘圖示的尖端對準地圖上的正確位置。

![黃色圖釘影像](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png

在這兩個範例中，上述影像均新增至應用程式資源的可繪製資料夾。

### <a name="before-google-maps"></a>之前：Google 地圖

使用 Google 地圖時，自訂影像可用來作為標記。 使用標記的 `icon` 選項載入自訂影像。 若要將影像點對齊座標，請使用 `anchor` 選項。 錨點會相對於影像的維度。 在此案例中，錨點為 0.2 個單位寬及 1 個單位高。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

![Google 地圖的自訂標記](media/migrate-google-maps-android-app/google-maps-custom-marker.png)

### <a name="after-azure-maps"></a>之後：Azure 地圖服務

Azure 地圖服務中的符號圖層支援自訂影像，但影像必須先載入至地圖資源，並獲派唯一識別碼。 接著，符號圖層必須參考此識別碼。 您可以使用 `iconOffset` 選項來位移符號，使其對齊影像上的正確位置點。 圖示位移以像素為單位。 根據預設，位移會相對於影像的底部中央，但您可以使用 `iconAnchor` 選項來調整此位移值。 此範例會將 `iconAnchor` 選項設定為 `"center"`。 並且使用圖示位移將影像向右移動五個像素，同時向上移動 15 個像素，以對齊圖釘影像的點。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

![Azure 地圖服務的自訂標記](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)

## <a name="adding-a-polyline"></a>新增聚合線條

聚合線條可用來代表地圖上的一條線或路徑。 下列範例說明如何在地圖上建立虛線聚合線條。

### <a name="before-google-maps"></a>之前：Google 地圖

使用 Google Maps 時，呈現聚合線條的方式是使用 `PolylineOptions` 類別。 使用 `addPolyline` 方法，將聚合線條新增至地圖。 使用 `color` 選項設定筆觸色彩。 使用 `width` 選項設定筆觸寬度。 使用 `pattern` 選項新增筆觸虛線陣列。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

![Google 地圖的聚合線條](media/migrate-google-maps-android-app/google-maps-polyline.png)

### <a name="after-azure-maps"></a>之後：Azure 地圖服務

在 Azure 地圖服務中，聚合線條稱為 `LineString` 或 `MultiLineString` 物件。 將這些物件新增至資料來源，並使用線條圖層來加以呈現。 使用 `strokeWidth` 選項設定筆觸寬度。 使用 `strokeDashArray` 選項新增筆觸虛線陣列。

Azure 地圖服務 Web SDK 中的筆觸寬度和虛線陣列「像素」單位與 Google Maps 服務相同。 此兩者都接受相同的值，因此可產生相同結果。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

![Azure 地圖服務的聚合線條](media/migrate-google-maps-android-app/azure-maps-polyline.png)

## <a name="adding-a-polygon"></a>新增多邊形

多邊形可用來代表地圖上的區域。 下一個範例會示範如何建立多邊形。 此多邊形會根據地圖的中心座標來形成三角形。

### <a name="before-google-maps"></a>之前：Google 地圖

使用 Google Maps 時，呈現多邊形的方式是使用 `PolygonOptions` 類別。 使用 `addPolygon` 方法，將多邊形新增至地圖。 分別使用 `fillColor` 和 `strokeColor` 選項來設定「填滿」和「筆觸」色彩。 使用 `strokeWidth` 選項設定筆觸寬度。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

![Google 地圖的多邊形](media/migrate-google-maps-android-app/google-maps-polygon.png)

### <a name="after-azure-maps"></a>之後：Azure 地圖服務

在 Azure 地圖服務中，`Polygon` 和 `MultiPolygon` 物件會新增至資料來源，並以圖層在地圖上加以呈現。 在多邊形圖層中呈現多邊形的區域。 使用線條圖層來呈現多邊形的外框。 使用 `strokeColor` 和 `strokeWidth` 選項來設定筆觸色彩和寬度。

Azure 地圖服務 Web SDK 中的筆觸寬度和虛線陣列「像素」單位分別與 Google Maps 中的單位一致。 此兩者都接受相同的值，可產生相同結果。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

![Azure 地圖服務的多邊形](media/migrate-google-maps-android-app/azure-maps-polygon.png)

## <a name="overlay-a-tile-layer"></a>覆蓋地圖底圖圖層

 使用地圖底圖圖層將已分解成較小地圖底圖影像、並與地圖底圖系統契合的圖層影像覆蓋在一起。 覆蓋圖層影像或大型資料集時，常會使用這種方式。 地圖底圖圖層在 Google 地圖中稱為影像覆蓋。

下列範例會覆蓋來自愛荷華州立大學 Iowa Environmental Mesonet 的氣象雷達地圖底圖圖層。 地圖底圖的大小為 256 像素。

### <a name="before-google-maps"></a>之前：Google 地圖

使用 Google Maps 時，地圖底圖圖層可以重疊在地圖上方。 使用 `TileOverlayOptions` 類別。 使用 `addTileLauer` 方法，將地圖底圖圖層新增至地圖。 若要讓地圖底圖變成半透明狀，可將 `transparency` 選項設定為 0.2 或 20% 透明。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

![Google 地圖的地圖底圖圖層](media/migrate-google-maps-android-app/google-maps-tile-layer.png)

### <a name="after-azure-maps"></a>之後：Azure 地圖服務

您可以將地圖底圖圖層新增至地圖中，其方式與任何其他圖層大致相同。 具有 x、y、縮放預留位置 (分別為 `{x}`、`{y}`、`{z}`) 的格式化 URL 可用來讓圖層知道要於何處存取地圖底圖。 此外，Azure 地圖服務中的地圖底圖圖層也支援 `{quadkey}`、`{bbox-epsg-3857}` 和 `{subdomain}` 預留位置。 若要讓地圖底圖圖層變成半透明狀，可使用不透明度值 0.8。 不透明度和透明度雖然類似，但應使用倒數。 若要在這兩個選項之間轉換，將一減去其值即可。

> [!TIP]
> 在 Azure 地圖服務中，您可以輕鬆地在其他圖層下呈現圖層，包括基底地圖圖層。 此外，地圖底圖圖層通常最好是放到地圖標籤下面來呈現，以方便辨識。 `map.layers.add` 方法會採用第二個參數，也就是要在其下面插入新圖層的目標圖層所具有的識別碼。 若要在地圖標籤下面插入地圖底圖圖層，您可以使用下列程式碼：`map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

![Azure 地圖服務的地圖底圖圖層](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)

## <a name="show-traffic"></a>顯示流量

Azure 地圖服務和 Google Maps 都具有重疊交通資料的選項。

### <a name="before-google-maps"></a>之前：Google 地圖

使用 Google 地圖時，可以藉由將 true 傳入地圖的 `setTrafficEnabled` 方法中，將交通流量資料重疊在地圖之上。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

![Google 地圖的交通](media/migrate-google-maps-android-app/google-maps-traffic.png)

### <a name="after-azure-maps"></a>之後：Azure 地圖服務

Azure 地圖服務提供數個不同的選項來顯示交通。 交通事件 (例如道路封閉和事故) 可在地圖上顯示為圖示。 交通流量和色彩編碼的道路可以在地圖上重疊。 色彩可以相對於正常預期的延遲或絕對延遲來修改，使其相對於發佈的速度限制來顯示。 Azure 地圖服務中的事件資料會每分鐘更新一次，車流資料則會每兩分鐘更新一次。

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

![Azure 地圖服務的交通](media/migrate-google-maps-android-app/azure-maps-traffic.png)

## <a name="clean-up-resources"></a>清除資源

沒有要清除的資源。

## <a name="next-steps"></a>後續步驟

深入了解遷移至 Azure 地圖服務：

> [!div class="nextstepaction"]
> [遷移 Android 應用程式](migrate-from-google-maps-android-app.md)
