---
title: 將多邊形圖層添加到地圖 |微軟 Azure 地圖
description: 在本文中，您將學習如何在 Microsoft Azure 地圖 Web SDK 中的地圖上呈現多邊形和多面。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9191f054ca3c7374bcbc7bec46573289a512612c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535047"
---
# <a name="add-a-polygon-layer-to-the-map"></a>向地圖添加多邊形圖層

本文演示如何使用多邊形圖層渲染地圖上的`Polygon`面積`MultiPolygon`和要素幾何體。 Azure 地圖 Web SDK 還支援創建擴展的[GeoJSON 架構](extend-geojson.md#circle)中定義的圓幾何體。 在地圖上渲染時，這些圓將轉換為多邊形。 使用地圖集包裝時，可以輕鬆地更新所有要素幾何體[。形狀](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)類。

## <a name="use-a-polygon-layer"></a>使用多邊形圖層 

當多邊形圖層連接到資料來源並在地圖上載入時，它將使用`Polygon`和`MultiPolygon`要素呈現區域。 要創建多邊形，請將其添加到資料來源，並使用[多邊形圖層使用多邊形圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)呈現它。

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

下面是上述代碼的完整和運行示例。

<br/>

<iframe height='500' scrolling='no' title='在地圖中新增多邊形 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>在地圖中新增多邊形</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>一起使用多邊形和線圖層

線圖層用於呈現多邊形的輪廓。 以下代碼示例呈現與上一個示例一樣的多邊形，但現在添加了一個線圖層。 此線圖層是連接到資料來源的第二層。  

<iframe height='500' scrolling='no' title='用來新增多邊形的多邊形和線條圖層' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>用來新增多邊形的多邊形和線條圖層</a>。
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>用圖案填充多邊形

除了用顏色填充多邊形外，您還可以使用圖像圖案填充多邊形。 將圖像圖案載入到地圖圖像精靈資源中，然後使用此圖像與多邊形圖層`fillPattern`的屬性引用此圖像。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多邊形填充圖案" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 顯示筆<a href='https://codepen.io/azuremaps/pen/JzQpYX/'>多邊形填充圖案</a>。
</iframe>


> [!TIP]
> Azure 地圖 Web SDK 提供了多個可自訂的圖像範本，這些範本可以用作填充模式。 有關詳細資訊，請參閱[如何使用圖像範本](how-to-use-image-templates-web-sdk.md)文檔。

## <a name="customize-a-polygon-layer"></a>自訂多邊形圖層

多邊形圖層只有少數樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>將圓形新增至地圖

Azure 映射使用 GeoJSON 架構的擴展版本，該架構為圓提供定義，[如下文](extend-geojson.md#circle)所述。 通過創建要素在地圖上渲染一個`Point`圓。 這`Point`具有值`subType`的屬性`"Circle"`和一個`radius`表示半徑（以米為單位）的數位的屬性。 

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

Azure 地圖 Web SDK`Point`將這些功能`Polygon`轉換為功能。 然後，這些要素使用多邊形和線圖層在地圖上呈現，如下代碼示例所示。

<br/>

<iframe height='500' scrolling='no' title='在地圖中新增圓形' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>在地圖中新增圓形</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>使幾何體易於更新

類`Shape`[包裝幾何或](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest)[特徵](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)，便於更新和維護這些要素。 要具現化形狀變數，將幾何或一組屬性傳遞給形狀建構函式。

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

以下代碼示例演示如何用形狀類包裝圓 GeoJSON 物件。 當半徑值在形狀中發生變化時，圓圈會自動在地圖上呈現。

<br/>

<iframe height='500' scrolling='no' title='更新圖形屬性' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen<a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>更新圖形屬性</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [多邊形圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [多邊形圖層選項](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

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
> [Azure 映射 GeoJSON 規範擴展](extend-geojson.md#circle)