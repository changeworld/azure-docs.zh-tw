---
title: 在 Android 地圖中設定地圖樣式 |Microsoft Azure 對應
description: 瞭解設定地圖樣式的兩種方式。 瞭解如何在版面配置檔案或活動類別中使用 Azure 地圖服務 Android SDK 來調整樣式。
author: rbrundritt
ms.author: richbrun
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1cce355c8ffbcd4704bd32b0e4d1739c77c2b623
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678454"
---
# <a name="set-map-style-android-sdk"></a>設定地圖樣式 (Android SDK) 

本文說明使用 Azure 地圖服務 Android SDK 設定地圖樣式的兩種方式。 Azure 地圖服務有六種不同的地圖樣式可供選擇。 如需支援的地圖樣式的詳細資訊，請參閱 [Azure 地圖服務中支援的地圖樣式](supported-map-styles.md)。

## <a name="prerequisites"></a>必要條件

請務必完成 [快速入門：建立 Android 應用程式](quick-android-map.md) 檔中的步驟。

## <a name="set-map-style-in-the-layout"></a>在版面配置中設定地圖樣式

新增地圖控制項時，您可以在活動類別的配置檔案中設定地圖樣式。 下列程式碼會設定中心位置、縮放層級和地圖樣式。

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_centerLat="47.602806"
    app:mapcontrol_centerLng="-122.329330"
    app:mapcontrol_zoom="12"
    app:mapcontrol_style="grayscale_dark"
    />
```

下列螢幕擷取畫面顯示上面的程式碼顯示具有灰階深色樣式的道路地圖。

![具有灰階深色道路地圖樣式的地圖](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>在程式碼中設定地圖樣式

您可以使用對應的方法，以程式設計方式在程式碼中設定地圖樣式 `setStyle` 。 下列程式碼會使用 maps 方法和地圖樣式，將中心位置和縮放層級設定 `setCamera` 為 `SATELLITE_ROAD_LABELS` 。

```java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS));
});
```

下列螢幕擷取畫面顯示上述程式碼顯示具有附屬道路標籤樣式的地圖。

![具有附屬道路標籤樣式的地圖](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>設定地圖攝影機

地圖攝影機可控制地圖中顯示地圖的哪個部分。 攝影機可以在程式碼中以程式設計的方式進行版面配置。 在程式碼中進行設定時，有兩個主要的方法可以設定地圖的位置;使用置中和縮放，或傳入周框方塊。 下列程式碼示範如何在使用和時設定所有選用的相機選項 `center` `zoom` 。

```java
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

通常最好將地圖放在一組資料上。 您可以使用方法從特徵計算周框方塊 `MapMath.fromData` ，並將其傳遞至 `bounds` 地圖攝影機的選項。 根據周框方塊設定地圖視圖時，指定一個 `padding` 值來考慮要轉譯成氣泡或符號的圖元大小，通常會很有用。 下列程式碼示範如何在使用周框方塊來設定相機的位置時，設定所有選用的相機選項。

```java
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

請注意，周框方塊的外觀比例可能與地圖的外觀比例不同，因此地圖通常會顯示完整的周框方塊區域，但通常只會以垂直或水準方式緊密地顯示。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [新增符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer-android.md)
