---
title: 使用 Azure 地圖服務 Android SDK 將符號圖層新增至地圖
description: 瞭解如何將標記新增至地圖。 請參閱使用 Microsoft Azure Maps Android SDK 來新增符號圖層，其中包含來自資料來源之以點為基礎之資料的符號圖層。
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/24/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 300a7968b2072459d6d7709e4d89388e1bcf59f3
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531202"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>使用 Azure 地圖服務 Android SDK 將符號圖層新增至地圖

本文說明如何使用 Azure 地圖服務 Android SDK，將資料來源中的點資料呈現為地圖上的符號圖層。

## <a name="prerequisites"></a>Prerequisites

1. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)
2. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。
3. 下載並安裝 [Azure 地圖服務 Android SDK](./how-to-use-android-map-control-library.md)。

## <a name="add-a-symbol-layer"></a>新增符號圖層

若要使用符號圖層在地圖上新增標記，請遵循下列步驟：

1. 編輯 **res**  >  **版面** 配置  >  **activity_main.xml** ，使其看起來類似下列 XML：
    
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

2. 將下列程式碼片段複製到您類別的 **>oncreate ( # B1** 方法中 `MainActivity.java` 。

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a red custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
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

當您執行應用程式時，您應該會在地圖上看到標記，如下所示：

![Android 地圖釘選圖示](./media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> 根據預設，符號圖層會隱藏重迭的符號來將符號轉譯優化。 當您放大時，隱藏的符號就會變成可見。 若要停用這項功能並隨時轉譯所有符號，請將 `iconAllowOverlap` 選項設定為 `true` 。

## <a name="next-steps"></a>後續步驟

若要將更多資料新增至您的地圖，請參閱：

> [!div class="nextstepaction"]
> [在 Android 地圖中新增圖形](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [顯示功能資訊](display-feature-information-android.md)