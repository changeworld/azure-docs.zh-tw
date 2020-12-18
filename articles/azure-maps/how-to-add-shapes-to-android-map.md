---
title: 將多邊形圖層新增至 Android 地圖 |Microsoft Azure 對應
description: 瞭解如何將多邊形或圓形新增至地圖。 瞭解如何使用 Azure 地圖服務 Android SDK 來自訂幾何圖案，並使其更容易更新和維護。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1712cedab9cef23108fcc48b8e09bdc3e33065c4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679486"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>將多邊形圖層新增至地圖 (Android SDK) 

本文說明如何使用多邊形圖層，在地圖上呈現 `Polygon` 和 `MultiPolygon` 特徵幾何的區域。

## <a name="prerequisites"></a>必要條件

請務必完成 [快速入門：建立 Android 應用程式](quick-android-map.md) 檔中的步驟。 本文中的程式碼區塊可以插入 maps `onReady` 事件處理常式中。

## <a name="use-a-polygon-layer"></a>使用多邊形圖層

當多邊形圖層連線至資料來源並載入至地圖時，將會呈現具有 `Polygon` 和 `MultiPolygon` 特徵的區域。 若要建立多邊形，請將其新增至資料來源，並使用類別以多邊形圖層呈現 `PolygonLayer` 。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

下列螢幕擷取畫面顯示上述程式碼如何使用多邊形圖層來呈現多邊形的區域。

![呈現其填滿區域的多邊形](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>同時使用多邊形和線條圖層

線條圖層可用來呈現多邊形的外框。 下列程式碼範例會呈現如同上述範例的多邊形，但現在會新增線條圖層。 此條線圖層是連線至資料來源的第二個圖層。  

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

下列螢幕擷取畫面顯示上述程式碼如何轉譯多邊形，並使用線條圖層來呈現其輪廓。

![呈現其填滿區域和外框的多邊形](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> 使用線條圖層來大綱多邊形時，請務必關閉多邊形中的所有環形，讓每個點陣列都有相同的起點和終點。 如果未這樣做，線條圖層可能不會將多邊形的最後一點連接到第一個點。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [新增線條圖層](android-map-add-line-layer.md)
