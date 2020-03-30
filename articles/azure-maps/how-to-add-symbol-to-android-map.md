---
title: 向 Android 地圖添加符號圖層 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Microsoft Azure 地圖 Android SDK 向地圖添加符號圖層來呈現地圖上的點資料。
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0eca73b5acae715283c05125181e12729ed5a772
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335579"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>使用 Azure 地圖 Android SDK 將符號圖層添加到地圖

本文演示如何使用 Azure 地圖 Android SDK 將資料來源中的點資料呈現為地圖上的符號圖層。

## <a name="prerequisites"></a>Prerequisites

要完全按照本文中的步驟操作，您需要安裝[Azure 地圖 Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library)才能載入地圖。

## <a name="add-a-symbol-layer"></a>新增符號圖層

要使用符號圖層在地圖上委任標記，請按照以下步驟操作：

1. 編輯 > **activity_main.xml** ** **  > **佈局**，以便它看起來像以下 XML：
    
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

2. 將以下程式碼片段複製到類`MainActivity.java`的**onCreate（）** 方法中。

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
    
    上面的程式碼片段首先使用**onReady（）** 回檔方法獲取 Azure 地圖映射控制項實例。 然後，它使用**DataSource**類創建資料來源物件並將其添加到地圖中。 然後，它將包含點幾何體的**要素**添加到其中。 然後，紅色標記圖像設置為符號的圖示。 **符號圖層**使用文本或圖示將資料來源中包裝為地圖上符號的基於點的資料。 然後創建一個符號圖層，並將資料來源傳遞給它以進行渲染，然後添加到地圖的圖層中。
    
    添加上面的程式碼片段後，應`MainActivity.java`如下所示：
    
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
    
此時，如果您運行應用程式，您應該在地圖上看到一個標記，如下所示：

<center>

![Android 地圖釘選圖示](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>後續步驟

要向地圖添加更多內容，請參閱：

> [!div class="nextstepaction"]
> [將形狀添加到 Android 地圖](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [顯示功能資訊](display-feature-information-android.md)