---
title: 將多邊形延伸圖層新增至地圖 |Microsoft Azure 對應
description: 如何將多邊形的延伸圖層新增至 Microsoft Azure Maps Web SDK。
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334548"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>將多邊形延伸圖層新增至地圖

本文說明如何使用多邊形的「延伸圖層」，將和`Polygon` `MultiPolygon`特徵幾何呈現為拉伸的圖形。 Azure 地圖服務 Web SDK 支援以[擴充 GeoJSON 架構](extend-geojson.md#circle)中所定義的方式呈現圓形幾何。 在地圖上轉譯時，可以將這些圓形轉換成多邊形。 以阿特拉斯包裝時，可以輕鬆地更新所有功能幾何[。Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)類別。

## <a name="use-a-polygon-extrusion-layer"></a>使用多邊形延伸圖層

將[多邊形的延伸圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)連接至資料來源。 然後，將它載入對應上。 多邊形拉伸圖層會將`Polygon`和`MultiPolygon`特徵的區域轉譯為拉伸圖形。 多邊形`height`拉伸`base`圖層的和屬性，會定義從拉伸圖形的地面和高度到量（以度量為**單位**）的基底距離。 下列程式碼示範如何建立多邊形、將它加入至資料來源，並使用多邊形的 [延伸層] 類別來呈現它。

> [!Note]
> 多邊形`base`延伸層中定義的值應該小於或等於的值`height`。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="已拉伸多邊形" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的畫筆已<a href='https://codepen.io/azuremaps/pen/wvvBpvE'>拉伸多邊形</a>。</iframe>


## <a name="add-data-driven-polygons"></a>新增資料驅動的多邊形

分級著色圖地圖可以使用多邊形的 [延伸圖層] 來呈現。 將 [ `height`延伸`fillColor` ] 圖層的和屬性設定為`Polygon`和`MultiPolygon`特徵幾何中統計變數的測量。 下列程式碼範例會根據州的人口密度測量，顯示分級著色圖的拉伸對應。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="已拉伸的分級著色圖對應" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的手寫筆<a href='https://codepen.io/azuremaps/pen/eYYYNox'>分級著色圖對應</a>。
</iframe>

## <a name="add-a-circle-to-the-map"></a>將圓形新增至地圖

Azure 地圖服務會使用 GeoJSON 架構的擴充版本，如[這裡](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)所述，提供圓形的定義。 藉由建立`point`具有`subType`屬性的功能，並以量`Circle`表表示半徑的編號`Radius`屬性，即可在地圖上轉譯實心圓 **。** 例如：

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure 地圖服務的 Web SDK 會將`Point`這些功能`Polygon`轉換成幕後的功能。 這些`Point`功能可以使用多邊形延伸層在地圖上轉譯，如下列程式碼範例所示。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="無人機空域多邊形" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的 Pen<a href='https://codepen.io/azuremaps/pen/zYYYrxo'>無人機空域多邊形</a>。
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>自訂多邊形的延伸圖層

多邊形的 [延伸圖層] 有數個樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的 Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> 。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [多邊形延伸圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

其他資源：

> [!div class="nextstepaction"]
> [Azure 地圖服務 GeoJSON 規格延伸模組](extend-geojson.md#circle)
