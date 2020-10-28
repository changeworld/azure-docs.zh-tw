---
title: 將圖格圖層新增至 Android 地圖 |Microsoft Azure 對應
description: 瞭解如何將圖格圖層新增至地圖。 請參閱使用 Azure 地圖服務 Android SDK 將天氣雷達圖重迭新增至地圖的範例。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: faa5e3e3177e18787a73c6b4c43aa04b6bc760b7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897087"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>使用 Azure 地圖服務 Android SDK 將圖格圖層新增至地圖

本文說明如何使用 Azure 地圖服務 Android SDK，在地圖上呈現圖格圖層。 圖格圖層可讓您在 Azure 地圖服務的地圖底圖上覆蓋影像。 您可以在[縮放層級和圖格格線](zoom-levels-and-tile-grid.md)文件中找到有關 Azure 地圖服務圖格顯示系統的詳細資訊。

圖格圖層會在伺服器的磚中載入。 您可以使用圖格圖層瞭解的命名慣例，在伺服器上預先轉譯和儲存這些映射，就像任何其他影像一樣。 或者，您可以使用動態服務轉譯這些映射，以近乎即時的方式產生影像。 Azure 地圖服務的 >tilelayer 類別支援三種不同的磚服務命名慣例：

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
* `{subdomain}` -子域值的預留位置（如果已指定子域值）。

## <a name="prerequisites"></a>Prerequisites

若要完成本文中的程式，您必須安裝 [Azure 地圖服務 Android SDK](./how-to-use-android-map-control-library.md) 以載入地圖。


## <a name="add-a-tile-layer-to-the-map"></a>將圖格圖層新增至地圖

 這個範例示範如何建立指向一組圖格的圖格圖層。 這些磚會使用「x，y，zoom」圖格系統。 此圖格圖層的來源是天氣雷達覆疊圖，資料來源：[愛荷華州立大學的愛荷華州環境氣象網 (Iowa Environmental Mesonet of Iowa State University)](https://mesonet.agron.iastate.edu/ogc/)。 

您可以遵循下列步驟，將圖格圖層新增至地圖。

1. 編輯 **res > 配置 > activity_main.xml** 讓它看起來如下所示：

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

2. 將下列程式碼片段複製到您類別的 **>oncreate ( # B1** 方法中 `MainActivity.java` 。

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
    
    上述程式碼片段會先使用 **onReady ( # B1** 回呼方法取得 Azure 地圖服務的地圖控制項實例。 然後，它會建立 `TileLayer` 物件，並將格式化的 **XYZ** 磚 URL 傳遞至 `tileUrl` 選項中。 圖層的不透明度設定為 `0.8` ，因為所使用的磚服務磚是256圖元磚，所以會將此資訊傳遞至 `tileSize` 選項。 圖格圖層接著會傳遞至地圖圖層管理員。

    新增上述程式碼片段之後，您 `MainActivity.java` 應該看起來如下所示：
    
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

如果您現在執行應用程式，您應該會在地圖上看到一行，如下所示：

<center>

![Android 地圖線條](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以深入瞭解設定地圖樣式的方式

> [!div class="nextstepaction"]
> [變更 Android 地圖中的地圖樣式](./set-android-map-styles.md)