---
title: 將多邊形圖層新增至地圖 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何在 Microsoft Azure Maps Web SDK 的地圖上轉譯多邊形和多個多邊形。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9191f054ca3c7374bcbc7bec46573289a512612c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79535047"
---
# <a name="add-a-polygon-layer-to-the-map"></a>將多邊形圖層新增至地圖

本文說明如何使用多邊形圖層，在地圖`Polygon`上`MultiPolygon`轉譯的區域和功能幾何。 Azure 地圖服務 Web SDK 也支援建立 Circle 幾何，如[擴充 GeoJSON 架構](extend-geojson.md#circle)中所定義。 在地圖上轉譯時，這些圓形會轉換成多邊形。 以阿特拉斯包裝時，可以輕鬆地更新所有功能幾何[。Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)類別。

## <a name="use-a-polygon-layer"></a>使用多邊形圖層 

當多邊形圖層連接至資料來源並載入到地圖上時，它會呈現具有`Polygon`和`MultiPolygon`功能的區域。 若要建立多邊形，請將它加入至資料來源，並使用[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)類別來呈現多邊形圖層。

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

<iframe height='500' scrolling='no' title='在地圖中新增多邊形 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>在地圖中新增多邊形</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>同時使用多邊形和線條圖層

線條圖層用來呈現多邊形的外框。 下列程式碼範例會轉譯如前一個範例的多邊形，但現在會加入線條圖層。 這條線圖層是連接到資料來源的第二層。  

<iframe height='500' scrolling='no' title='用來新增多邊形的多邊形和線條圖層' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>用來新增多邊形的多邊形和線條圖層</a>。
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>使用模式填滿多邊形

除了以色彩填滿多邊形外，您還可以使用影像模式來填滿多邊形。 將影像模式載入地圖影像 sprite 資源，然後使用多邊形圖層的`fillPattern`屬性來參考此影像。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多邊形填滿模式" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的畫筆<a href='https://codepen.io/azuremaps/pen/JzQpYX/'>多邊形填滿模式</a>。
</iframe>


> [!TIP]
> Azure 地圖服務 web SDK 提供數個可自訂的影像範本，供您用來做為填滿模式。 如需詳細資訊，請參閱[如何使用影像範本](how-to-use-image-templates-web-sdk.md)檔。

## <a name="customize-a-polygon-layer"></a>自訂多邊形圖層

多邊形圖層只有少數樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>將圓形新增至地圖

Azure 地圖服務會使用 GeoJSON 架構的延伸版本，它會提供圓形的定義，如[這裡](extend-geojson.md#circle)所述。 藉由建立`Point`功能，在地圖上呈現圓形。 這`Point`有一個`subType`屬性，其值為`"Circle"` ，而`radius`屬性具有表示半徑的數位（以量為單位）。 

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

Azure 地圖服務 Web SDK 會將這些`Point`功能轉換`Polygon`成功能。 然後，這些功能會使用多邊形和線條圖層呈現在地圖上，如下列程式碼範例所示。

<br/>

<iframe height='500' scrolling='no' title='在地圖中新增圓形' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>在地圖中新增圓形</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>讓 geometry 易於更新

類別會包裝[幾何](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest)或功能，並可讓您輕鬆地更新和維護這些功能。 [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) `Shape` 若要具現化圖形變數，請將幾何或一組屬性傳遞給圖形的函式。

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

下列程式碼範例示範如何使用 shape 類別來包裝 circle GeoJSON 物件。 當圖形中的半徑值變更時，圓形會自動呈現在地圖上。

<br/>

<iframe height='500' scrolling='no' title='更新圖形屬性' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen<a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>更新圖形屬性</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

如需更多可新增至地圖的程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [新增快顯](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用映像範本](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

其他資源：

> [!div class="nextstepaction"]
> [Azure 地圖服務 GeoJSON 規格延伸模組](extend-geojson.md#circle)