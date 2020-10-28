---
title: 將多邊形圖層新增至地圖 | Microsoft Azure 地圖服務
description: 瞭解如何將多邊形或圓形新增至地圖。 瞭解如何使用 Azure 地圖服務 Web SDK 自訂幾何圖案，並使其更容易更新和維護。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 96553e75d4b982cfe67d03961d4356a3844f253c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890992"
---
# <a name="add-a-polygon-layer-to-the-map"></a>將多邊形圖層新增至地圖

本文說明如何使用多邊形圖層，在地圖上呈現 `Polygon` 和 `MultiPolygon` 特徵幾何的區域。 Azure 地圖服務 Web SDK 也支援建立圓形幾何，如[擴充的 GeoJSON 結構描述](extend-geojson.md#circle)所定義。 在地圖上呈現時，這些圓形會轉換為多邊形。 以 [atlas.Shape](/javascript/api/azure-maps-control/atlas.shape) 類別包裝時，所有特徵幾何都可輕易更新。

## <a name="use-a-polygon-layer"></a>使用多邊形圖層 

當多邊形圖層連線至資料來源並載入至地圖時，將會呈現具有 `Polygon` 和 `MultiPolygon` 特徵的區域。 若要建立多邊形，請將其新增至資料來源，並使用 [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer) 類別和多邊形圖層加以呈現。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

以下是上述程式碼的完整和執行範例。

<br/>

<iframe height='500' scrolling='no' title='在地圖中新增多邊形 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>在地圖中新增多邊形</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>同時使用多邊形和線條圖層

線條圖層可用來呈現多邊形的外框。 下列程式碼範例會呈現如同上述範例的多邊形，但現在會新增線條圖層。 此條線圖層是連線至資料來源的第二個圖層。  

<br/>

<iframe height='500' scrolling='no' title='用來新增多邊形的多邊形和線條圖層' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>用來新增多邊形的多邊形和線條圖層</a>。
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>使用圖樣填滿多邊形

除了以色彩填滿多邊形以外，您還可以使用影像圖樣來填滿多邊形。 請將影像圖樣載入地圖影像原件資源，然後使用多邊形圖層的 `fillPattern` 屬性來參考此影像。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多邊形填滿圖樣" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
查看畫筆 <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>多邊形填滿圖樣</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>


> [!TIP]
> Azure 地圖服務 Web SDK 提供數個可自訂的影像範本，供您作為填滿圖樣。 如需詳細資訊，請參閱[如何使用影像範本](how-to-use-image-templates-web-sdk.md)文件。

## <a name="customize-a-polygon-layer"></a>自訂多邊形圖層

多邊形圖層只有少數樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>將圓形新增至地圖

Azure 地圖服務會使用可提供圓形定義的擴充版 GeoJSON 結構描述，如[這裡](extend-geojson.md#circle)所說明。 藉由建立 `Point` 特徵，即可在地圖上呈現圓形。 此 `Point` 具有值為 `"Circle"` 的 `subType` 屬性，且具有 `radius` 屬性，會以數字表示半徑 (以公尺為單位)。 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Azure 地圖服務 Web SDK 會將這些 `Point` 特徵轉換為 `Polygon` 特徵。 然後，這些特徵會使用多邊形和線條圖層呈現在地圖上，如下列程式碼範例所示。

<br/>

<iframe height='500' scrolling='no' title='在地圖中新增圓形' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>在地圖中新增圓形</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>讓幾何易於更新

`Shape` 類別會包裝[幾何](/javascript/api/azure-maps-control/atlas.data.geometry)或[特徵](/javascript/api/azure-maps-control/atlas.data.feature)，使這些特徵更容易更新和維護。 若要具現化圖形變數，請將幾何或一組屬性傳至圖形建構函式。

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

下列程式碼範例說明如何使用圖形類別來包裝圓形 GeoJSON 物件。 當圖形中的半徑值變更時，圓形會自動呈現在地圖上。

<br/>

<iframe height='500' scrolling='no' title='更新圖形屬性' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen<a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>更新圖形屬性</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [Polygon](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

如需更多可新增至地圖的程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [新增快顯](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用影像範本](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

其他資源：

> [!div class="nextstepaction"]
> [Azure 地圖服務 GeoJSON 規格延伸模組](extend-geojson.md#circle)