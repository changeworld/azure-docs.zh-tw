---
title: 將熱度圖層新增至 Android 地圖 |Microsoft Azure 對應
description: 瞭解如何建立熱度圖。 瞭解如何使用 Azure MapsAndroid SDK 將熱度圖層新增至地圖。 瞭解如何自訂熱度圖圖層。
author: rbrundritt
ms.author: richbrun
ms.date: 12/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 4de59bd0b2a9dc9b11acf55a59b82724d2c7b862
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681506"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>將熱度圖層 (Android SDK) 

熱度圖 (也稱為點密度圖) 是一種資料視覺效果。 它們是用來表示使用一系列色彩的資料密度，並在地圖上顯示資料「作用點」。 熱度圖適合用來呈現具有大量點的資料集。 

將數十萬個點轉譯為符號，可以涵蓋大部分的地圖區域。 這種情況可能會導致許多符號重迭。 讓您難以更清楚地瞭解資料。 不過，將這個相同的資料集視覺化為熱度圖，可讓您輕鬆查看每個資料點的密度和相對密度。

您可以在許多不同的案例中使用熱度圖，包括：

- **溫度資料**：為兩個資料點之間的溫度提供近似值。
- **雜訊感應器的資料**：不僅顯示感應器所在的雜訊濃度，還可讓您深入瞭解在距離之間的散熱。 任何一個網站的雜訊等級可能不會太高。 如果多個感應器的噪音涵蓋區域重迭，可能是這個重迭的區域可能會遇到較高的雜訊等級。 因此，重迭的區域會顯示在熱度圖中。
- **GPS 追蹤**：以加權高度地圖的形式包含速度，其中每個資料點的濃度以速度為基礎。 例如，此功能提供一種方式來查看車輛的進行加速。

> [!TIP]
> 熱度圖圖層預設會轉譯資料來源中所有幾何的座標。 若要限制圖層，使其只轉譯點幾何特徵，請將 `filter` 圖層的選項設定為 `eq(geometryType(), "Point")` 。 如果您也想要包含 MultiPoint 功能，請將 `filter` 圖層的選項設定為 `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` 。

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>必要條件

請務必完成 [快速入門：建立 Android 應用程式](quick-android-map.md) 檔中的步驟。 本文中的程式碼區塊可以插入 maps `onReady` 事件處理常式中。

## <a name="add-a-heat-map-layer"></a>新增熱度圖層

若要將點的資料來源轉譯為熱度圖，請將您的資料來源傳遞至類別的實例 `HeatMapLayer` ，並將其新增至地圖。

下列程式碼範例會載入過去一周的地震 GeoJSON 摘要，並將其呈現為熱度圖。 每個資料點都會以每個縮放層級的10個圖元半徑來呈現。 為了確保更好的使用者體驗，熱度圖會位於標籤圖層下方，讓標籤保持清楚可見。 此範例中的資料來自 [USGS 地震風險計畫](https://earthquake.usgs.gov/)。 此範例會使用「 [建立資料來源](create-data-source-android-sdk.md) 檔」中所提供的資料匯入公用程式程式碼區塊，從 Web 載入 GeoJSON 的資料。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

下列螢幕擷取畫面顯示使用上述程式碼載入熱度圖的地圖。

![地圖與最近地震的熱度圖圖層](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>自訂熱度圖圖層

前一個範例已藉由設定半徑和透明度選項來自訂熱度圖。 熱度圖層提供數個自訂選項，包括：

- `heatmapRadius`：定義要在其中呈現每個資料點的圖元半徑。 您可以將 radius 設定為固定數位或運算式。 藉由使用運算式，您可以根據縮放層級調整半徑，並在地圖上表示一致的空間區域 (例如5英里的半徑) 。
- `heatmapColor`：指定熱度圖的以色彩標示方式。 色彩漸層是熱度圖的常見功能。 您可以使用運算式來達成效果 `interpolate` 。 您也可以使用 `step` 運算式來標示熱度圖，將密度以視覺化方式細分為類似于輪廓或雷達圖樣式地圖的範圍。 這些色彩調色盤會定義最小密度值到最大密度值的顏色。

  您可以將熱度圖的色彩值指定為值的運算式 `heatmapDensity` 。 在「插補」運算式的索引0定義沒有資料的區域色彩，或是「逐步」運算式的預設色彩。 您可以使用這個值來定義背景色彩。 通常，這個值會設定為透明或半透明的黑色。 

  以下是色彩運算式的範例：

  | 插補色彩運算式 | 分階色彩運算式 |
  |--------------------------------|--------------------------|
  | 插補 (<br/>&nbsp;&nbsp;&nbsp;&nbsp;線性 ( # A1、 <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity ( # A1、<br/>&nbsp;&nbsp;&nbsp;&nbsp;停止 (0，色彩 (色彩。透明) # A3，<br/>&nbsp;&nbsp;&nbsp;&nbsp;停止 (0.01，color (Color. 洋紅) # A3，<br/>&nbsp;&nbsp;&nbsp;&nbsp;停止 (0.5、color (parseColor ( "#fb00fb" ) # A4 # A5、<br/>&nbsp;&nbsp;&nbsp;&nbsp;停止 (1、color (parseColor ( "#00c3ff" ) # A4 # A5<br/>)` | 步驟 (<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity ( # A1、<br/>&nbsp;&nbsp;&nbsp;&nbsp;色彩 (色彩。透明) 、<br/>&nbsp;&nbsp;&nbsp;&nbsp;停止 (0.01、color (parseColor ( "#000080" ) # A4 # A5、<br/>&nbsp;&nbsp;&nbsp;&nbsp;停止 (0.25、color (parseColor ( "#000080" ) # A4 # A5、<br/>&nbsp;&nbsp;&nbsp;&nbsp;停止 (0.5、色彩 (色彩。綠色) # A3，<br/>&nbsp;&nbsp;&nbsp;&nbsp;停止 (0.5、色彩 (色彩。黃色) # A3，<br/>&nbsp;&nbsp;&nbsp;&nbsp;停止 (1，色彩 (色彩。紅色) # A3<br/>) |

- `heatmapOpacity`：指定熱度圖圖層的不透明或透明程度。
- `heatmapIntensity`：將乘數套用至每個資料點的權數，以增加熱度圖的整體濃度。 它會導致資料點的權數差異，讓您更輕鬆地將其視覺化。
- `heatmapWeight`：根據預設，所有資料點的權數為1，而且會平均加權。 權數選項可作為乘數，您可以將它設定為數字或運算式。 如果數位設定為權數，則將每個資料點放在地圖上兩次就是相等的。 例如，如果權數為，則 `2` 密度會加倍。 將加權選項設定為數字所呈現的熱度圖，與使用強度選項呈現的熱度圖相似。

  但是，如果您使用運算式，每個資料點的加權都可以根據每個資料點的屬性。 例如，假設每個資料點都代表地震。 量值對於每個地震資料點而言都是重要的度量。 地震會一直發生，但大部分的情況下都不會察覺到。 使用運算式中的量值，將權數指派給每個資料點。 藉由使用 [量值] 來指派權數，您可以更清楚地表示熱度圖內地震的重要性。
- `minZoom` 和 `maxZoom` ：顯示圖層的縮放層級範圍。
- `filter`：用來限制從來源抓取並在圖層中呈現的篩選運算式。
- `sourceLayer`：如果連接至圖層的資料來源是向量圖格來源，則必須指定向量磚內的來源層。
- `visible`：隱藏或顯示圖層。

以下是用來建立平滑色彩漸層的熱度圖範例。 `mag`在資料中定義的屬性會搭配指數插補使用，以設定每個資料點的權數或相關性。

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

下列螢幕擷取畫面顯示上述的自訂熱度圖層，使用先前熱度圖範例中的相同資料。

![具有最近地震的自訂熱度圖層的地圖](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>一致的可熱度圖

根據預設，熱度圖圖層中轉譯的資料點半徑在所有縮放層級中都有固定的圖元半徑。 當您縮放地圖時，資料匯總和熱度圖圖層看起來會不同。 下列影片顯示熱度圖的預設行為，其會在縮放地圖時維持圖元半徑。

![顯示地圖縮放的動畫顯示具有一致圖元大小的熱度圖圖層](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

使用 `zoom` 運算式來調整每個縮放層級的半徑，讓每個資料點都涵蓋地圖的相同實體區域。 此運算式會讓熱度圖圖層看起來更為靜態且一致。 地圖的每個縮放層級都有兩倍垂直和水準的圖元作為先前的縮放層級。

調整半徑，使其每個縮放層級加倍，以建立在所有縮放層級上都一致的熱度圖。 若要套用這項調整，請使用 `zoom` 和基底 2 `exponential interpolation` 運算式，並針對最小縮放層級設定圖元半徑，並針對所計算的最大縮放層級調整半徑，如 `2 * Math.pow(2, minZoom - maxZoom)` 下列範例所示。 縮放地圖以查看熱度圖如何以縮放層級進行縮放。

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

下列影片顯示執行上述程式碼的地圖，此程式碼會在地圖縮放時調整半徑，以在縮放層級建立一致的熱度圖轉譯。

![顯示地圖縮放的動畫顯示具有一致地理空間大小的熱度圖圖層](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

## <a name="next-steps"></a>後續步驟

如需更多可新增至地圖的程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-android-sdk.md)
