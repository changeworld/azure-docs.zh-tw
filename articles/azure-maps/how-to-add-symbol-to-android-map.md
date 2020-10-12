---
title: 將符號圖層新增至 Android 地圖 |Microsoft Azure 對應
description: 瞭解如何將標記新增至地圖。 請參閱使用 Azure 地圖服務 Android SDK 來新增符號圖層，其中包含來自資料來源之以點為基礎之資料的符號圖層。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 10969e20cd7ae71cade230f6643a27d5d940ceaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311269"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>使用 Azure 地圖服務 Android SDK 將符號圖層新增至地圖

本文說明如何使用 Azure 地圖服務 Android SDK，將資料來源中的點資料呈現為地圖上的符號圖層。

## <a name="prerequisites"></a>必要條件

若要完全遵循這篇文章中的步驟，您必須安裝 [Azure 地圖服務 Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) 以載入對應。

## <a name="add-a-symbol-layer"></a>新增符號圖層

若要使用符號圖層在地圖上新增標記，請遵循下列步驟：

1. 編輯**res**  >  **版面**配置  >  **activity_main.xml** ，使其看起來類似下列 XML：
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. 將下列程式碼片段複製到您類別的 **>oncreate ( # B1 ** 方法中 `MainActivity.java` 。

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    上述程式碼片段會先使用 **onReady ( # B1 ** 回呼方法取得 Azure 地圖服務的地圖控制項實例。 接著，它會使用 **DataSource** 類別建立資料來源物件，並將它加入至對應。 然後，它會將包含點幾何的 **功能** 新增至其中。 接著會將紅色標記影像設定為符號的圖示。 **符號圖層**會使用文字或圖示，將資料來源中包裝的以點為基礎的資料轉譯為地圖上的符號。 接著會建立符號圖層，並將資料來源傳遞給它以轉譯，然後新增至地圖圖層。
    
    新增上述程式碼片段之後，您 `MainActivity.java` 應該看起來如下所示：
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
    public class MainActivity extends AppCompatActivity {
        
        static{
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
            }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
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
    
此時，如果您執行應用程式，您應該會在地圖上看到標記，如下所示：

<center>

![Android 地圖釘選圖示](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>

> [!TIP]
> 根據預設，符號圖層會隱藏重迭的符號來將符號轉譯優化。 當您放大時，隱藏的符號就會變成可見。 若要停用這項功能並隨時轉譯所有符號，請將 `iconAllowOverlap` 選項設定為 `true` 。

## <a name="next-steps"></a>後續步驟

若要在地圖中新增更多東西，請參閱：

> [!div class="nextstepaction"]
> [在 Android 地圖中新增圖形](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [顯示功能資訊](display-feature-information-android.md)