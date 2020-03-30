---
title: 向 Android 地圖添加切片圖層 |微軟 Azure 地圖
description: 在本文中，您將學習如何使用 Microsoft Azure 地圖 Android SDK 在地圖上呈現切片圖層。
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f98598bd1307bb1b46ff23814780c5f809b9ac90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335569"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>使用 Azure 地圖 Android SDK 將切片圖層添加到地圖

本文演示如何使用 Azure 地圖 Android SDK 在地圖上呈現切片圖層。 圖格圖層可讓您在 Azure 地圖服務的地圖底圖上覆蓋影像。 您可以在[縮放層級和圖格格線](zoom-levels-and-tile-grid.md)文件中找到有關 Azure 地圖服務圖格顯示系統的詳細資訊。

磁貼圖層從伺服器載入切片。 這些圖像可以像伺服器上的任何其他圖像一樣，使用切片層可以理解的命名約定進行預渲染和存儲。 或者，這些圖像可以使用動態服務進行渲染，該服務可近乎即時地生成圖像。 Azure 地圖切片層類支援三種不同的切片服務命名約定：

* X、Y、縮放標記法 - 以縮放層級為基礎，在圖格格線中的圖格上，x 是資料行位置，而 y 是資料列位置。
* Quadkey 標記法 - 將 x、y、縮放資訊結合成單一字串值，以作為圖格的唯一識別碼。
* 週框方塊 - 週框方塊座標可用來以 `{west},{south},{east},{north}` 格式指定影像，[Web 地圖服務 (WMS)](https://www.opengeospatial.org/standards/wms) 常使用此格式。

> [!TIP]
> TileLayer 是視覺化地圖上大型資料集的好方法。 不只可以從影像產生圖格圖層，向量資料也可轉譯為圖格圖層。 藉由將向量資料轉譯為圖格圖層，地圖控制項就只需載入圖層，而這可能比向量資料所表示的檔案大小小很多。 許多人因為需要轉譯地圖上數百萬個資料列，而選擇使用這項技術。

傳遞至圖格圖層的圖格 URL 必須是 TileJSON 資源的 http/https URL，或是使用下列參數的圖格 URL 範本： 

* `{x}` - 圖格的 X 位置。 也需要 `{y}` 和 `{z}`。
* `{y}` - 圖格的 Y 位置。 也需要 `{x}` 和 `{z}`。
* `{z}` 圖格的縮放層級。 也需要 `{x}` 和 `{y}`。
* `{quadkey}` -圖格 quadkey 識別碼，以 Bing Maps 圖格系統的命名慣例為基礎。
* `{bbox-epsg-3857}` - 使用 `{west},{south},{east},{north}` 格式的週框方塊字串，位在 EPSG 3857 空間參考系統中。
* `{subdomain}`- 如果指定子域值，則子域值的預留位置。

## <a name="prerequisites"></a>Prerequisites

要完成本文中的過程，您需要安裝[Azure 地圖 Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library)來載入地圖。


## <a name="add-a-tile-layer-to-the-map"></a>向地圖添加切片圖層

 此示例演示如何創建指向一組切片的切片圖層。 這些切片使用"x、y、縮放"切片系統。 此圖格圖層的來源是天氣雷達覆疊圖，資料來源：[愛荷華州立大學的愛荷華州環境氣象網 (Iowa Environmental Mesonet of Iowa State University)](https://mesonet.agron.iastate.edu/ogc/)。 

您可以按照以下步驟向地圖添加切片圖層。

1. **>activity_main.xml 編輯>佈局**，以便它看起來像下面的佈局：

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
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. 將以下程式碼片段複製到類`MainActivity.java`的**onCreate（）** 方法中。

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    上面的程式碼片段首先使用**onReady（）** 回檔方法獲取 Azure 地圖映射控制項實例。 然後，它創建`TileLayer`一個物件，並將格式化的**xyz**磁貼`tileUrl`URL 傳遞到該選項中。 圖層的不極性設置為`0.8`，並且由於正在使用的磁貼服務的切片為 256 圖元切片，因此此資訊將傳遞到選項中。 `tileSize` 然後，切片圖層將傳遞到地圖圖層管理器中。

    添加上面的程式碼片段後，應`MainActivity.java`如下所示：
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
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

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
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

如果現在運行應用程式，您應該在地圖上看到一條線，如下所示：

<center>

![安卓地圖線](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>後續步驟

請參閱以下文章，瞭解有關設置地圖樣式的方法

> [!div class="nextstepaction"]
> [更改 Android 地圖中的地圖樣式](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)