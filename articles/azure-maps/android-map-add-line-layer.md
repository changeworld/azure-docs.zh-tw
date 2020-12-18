---
title: 將線條圖層新增至 Android 地圖 |Microsoft Azure 對應
description: 瞭解如何將線條新增至地圖。 請參閱使用 Azure 地圖服務 Android SDK 將線條圖層新增至地圖，以及自訂具有符號和色彩漸層之線條的範例。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 3e68be79a4405af103512a9009187857a0d9af39
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681495"
---
# <a name="add-a-line-layer-to-the-map-android-sdk"></a>將線條圖層新增至地圖 (Android SDK) 

線條圖層可用來將 `LineString` 和 `MultiLineString` 功能轉譯為地圖上的路徑或路線。 線條圖層也可用來呈現 `Polygon` 和 `MultiPolygon` 功能的外框。 資料來源會連線到線條圖層，以為其提供要呈現的資料。

> [!TIP]
> 根據預設，線條圖層會轉譯資料來源中的多邊形座標及線條。 若要限制圖層，使其只呈現 LineString 幾何特徵，請將 `filter` 圖層的選項設定為 `eq(geometryType(), "LineString")` 。 如果您也想要包含 MultiLineString 功能，請將 `filter` 圖層的選項設定為 `any(eq(geometryType(), "LineString"), eq(geometryType(), "MultiLineString"))` 。

## <a name="prerequisites"></a>必要條件

請務必完成 [快速入門：建立 Android 應用程式](quick-android-map.md) 檔中的步驟。 本文中的程式碼區塊可以插入 maps `onReady` 事件處理常式中。

## <a name="add-a-line-layer"></a>新增線條圖層

下列程式碼示範如何建立線條。 將這一行加入至資料來源，然後使用類別以線條圖層呈現 `LineLayer` 。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a list of points.
List<Point> points = Arrays.asList(
    Point.fromLngLat(-73.972340, 40.743270),
    Point.fromLngLat(-74.004420, 40.756800));

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points));

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor("blue"),
    strokeWidth(5f)
);

map.layers.add(layer);
```

下列螢幕擷取畫面顯示上述程式碼線上條圖層中呈現線條。

![使用線條圖層呈現的線條對應](media/android-map-add-line-layer/android-line-layer.png)

## <a name="data-drive-line-style"></a>資料磁片磁碟機線條樣式

下列程式碼會建立兩個線條功能，並將速度限制值新增為每一行的屬性。 線條圖層會使用資料磁片磁碟機樣式表達式，並根據速度限制值來建立線條的色彩。 由於直線資料重迭沿著道路，下列程式碼會將線條圖層新增至標籤圖層下方，讓道路標籤仍能清楚地讀取。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a line feature.
Feature feature = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.131821, 47.704033),
        Point.fromLngLat(-122.099919, 47.703678))));

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35);

//Add the feature to the data source.
source.add(feature);

//Create a second line feature.
Feature feature2 = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.126662, 47.708265),
        Point.fromLngLat(-122.126877, 47.703980))));

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15);

//Add the second feature to the data source.
source.add(feature2);

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
);

map.layers.add(layer, "labels");
```

下列螢幕擷取畫面顯示線上條圖層中轉譯兩行的程式碼，其色彩會根據線條特徵中的屬性從資料驅動樣式表達式抓取。

![以線條圖層轉譯的資料磁片磁碟機樣式線條對應](media/android-map-add-line-layer/android-line-layer-data-drive-style.png)

## <a name="add-symbols-along-a-line"></a>沿著線條新增符號

這個範例會示範如何沿著地圖上的線條新增箭號圖示。 使用符號圖層時，請將 `symbolPlacement` 選項設定為 `SymbolPlacement.LINE` 。 此選項會沿著線條呈現符號，並旋轉圖示 (0 度 = 右方)。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple-arrow-right);

//Create and add a line to the data source.
source.add(LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.18822, 47.63208),
        Point.fromLngLat(-122.18204, 47.63196),
        Point.fromLngLat(-122.17243, 47.62976),
        Point.fromLngLat(-122.16419, 47.63023),
        Point.fromLngLat(-122.15852, 47.62942),
        Point.fromLngLat(-122.15183, 47.62988),
        Point.fromLngLat(-122.14256, 47.63451),
        Point.fromLngLat(-122.13483, 47.64041),
        Point.fromLngLat(-122.13466, 47.64422),
        Point.fromLngLat(-122.13844, 47.65440),
        Point.fromLngLat(-122.13277, 47.66515),
        Point.fromLngLat(-122.12779, 47.66712),
        Point.fromLngLat(-122.11595, 47.66712),
        Point.fromLngLat(-122.11063, 47.66735),
        Point.fromLngLat(-122.10668, 47.67035),
        Point.fromLngLat(-122.10565, 47.67498))));

//Create a line layer and add it to the map.
map.layers.add(new LineLayer(source,
    strokeColor("DarkOrchid"),
    strokeWidth(5f)
));

//Create a symbol layer and add it to the map.
map.layers.add(new SymbolLayer(source,
    //Space symbols out along line.
    symbolPlacement(SymbolPlacement.LINE),

    //Spread the symbols out 100 pixels apart.
    symbolSpacing(100f),
    
    //Use the arrow icon as the symbol.
    iconImage("arrow-icon"),

    //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
    iconAllowOverlap(true),

    //Center the symbol icon.
    iconAnchor(AnchorType.CENTER),

    //Scale the icon size.
    iconSize(0.8f)
));
```

在此範例中，下圖已載入至應用程式的可繪製資料夾。

| ![紫色箭號圖示影像](media/android-map-add-line-layer/purple-arrow-right.png)|
|:-----------------------------------------------------------------------:|
|                                                  |

下列螢幕擷取畫面顯示上面的程式碼，並在其上顯示箭號圖示。

![使用線條圖層中呈現的箭號來對應資料磁片磁碟機樣式的線條](media/android-map-add-line-layer/android-symbols-along-line-path.png)

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](how-to-add-shapes-to-android-map.md)
