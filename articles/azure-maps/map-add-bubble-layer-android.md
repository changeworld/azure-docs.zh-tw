---
title: 將氣泡圖層新增至 Android 地圖 |Microsoft Azure 對應
description: 瞭解如何將地圖上的點轉譯為具有固定大小的圓形。 請參閱如何使用 Azure 地圖服務 Android SDK 來新增和自訂此用途的反升圖層。
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 7506a2083a34832ee3f6f6222f86d35d10228728
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681519"
---
# <a name="add-a-bubble-layer-to-a-map-android-sdk"></a>將反升圖圖層新增至地圖 (Android SDK) 

本文說明如何將資料來源中的點資料轉譯為地圖上的氣泡圖層。 反升圖圖層會將點轉譯為地圖上具有固定圖元半徑的圓形。

> [!TIP]
> 根據預設，泡泡圖層會轉譯資料來源中所有幾何圖形的座標。 若要限制圖層，使其只轉譯點幾何特徵，請將 `filter` 圖層的選項設定為 `eq(geometryType(), "Point")` 。 如果您也想要包含 MultiPoint 功能，請將 `filter` 圖層的選項設定為 `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` 。

## <a name="prerequisites"></a>必要條件

請務必完成 [快速入門：建立 Android 應用程式](quick-android-map.md) 檔中的步驟。 本文中的程式碼區塊可以插入 maps `onReady` 事件處理常式中。

## <a name="add-a-bubble-layer"></a>新增泡泡圖層

下列程式碼會將點陣列載入資料來源。 然後，它會將資料點連接到反升圖圖層。 反升圖層會以五個圖元和白色的填滿色彩，轉譯每個氣泡的半徑。 以及藍色的筆觸色彩，以及6圖元的筆觸寬度。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create point locations.
Point[] points = new Point[] {
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
};

//Add multiple points to the data source.
source.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
BubbleLayer layer = new BubbleLayer(source, 
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
);

map.layers.add(layer);
```

下列螢幕擷取畫面顯示上述程式碼在反升圖圖層中呈現點。

![使用氣泡圖層轉譯的點對應](media/map-add-bubble-layer-android/android-bubble-layer.png)

## <a name="show-labels-with-a-bubble-layer"></a>顯示標籤與泡泡圖層

這段程式碼會示範如何使用反升圖圖層，在地圖上呈現點。 以及如何使用符號圖層來呈現標籤。 若要隱藏符號圖層的圖示，請將 `iconImage` 選項設定為 `"none"` 。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641,47.627631));

//Add a bubble layer.
map.layers.add(new BubbleLayer(source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
));

//Add a symbol layer to display text, hide the icon image.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),
    textField("Museum of History & Industry (MOHAI)"),
    textColor("#005995"),
    textOffset(new Float[]{0f, -2.2f})
));
```

下列螢幕擷取畫面顯示上述程式碼轉譯反升圖圖層中的點，以及具有符號圖層的點文字標籤。

![使用反升圖圖層和文字標籤（符號圖層）所轉譯的點地圖](media/map-add-bubble-layer-android/android-bubble-symbol-layer.png)

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [新增符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [顯示功能資訊](display-feature-information-android.md)
