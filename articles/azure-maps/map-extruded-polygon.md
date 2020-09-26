---
title: 將多邊形延伸圖層新增至地圖 |Microsoft Azure 對應
description: 如何將多邊形的延伸圖層新增至 Microsoft Azure Maps Web SDK。
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: eedbbc0126adacc2a9bdc151aa6dbc27c7ba0750
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310249"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>將多邊形的延伸圖層新增至地圖

本文說明如何使用多邊形的拉伸圖層，將 `Polygon` 和特徵幾何的範圍轉譯 `MultiPolygon` 為拉伸圖形。 Azure 地圖服務 Web SDK 支援以 [擴充 GeoJSON 架構](extend-geojson.md#circle)中定義的方式來呈現圓形幾何。 在地圖上轉譯時，可以將這些圓形轉換成多邊形。 所有特徵幾何都可在使用塔包裝時輕鬆更新 [。Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) 類別。

## <a name="use-a-polygon-extrusion-layer"></a>使用多邊形的延伸圖層

將 [多邊形延伸圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer) 連接至資料來源。 然後，將它載入地圖上。 多邊形的延伸圖層會將和特徵的區域轉譯 `Polygon` `MultiPolygon` 為拉伸圖形。 `height` `base` 多邊形延伸圖層的和屬性會定義從拉伸形狀的地面和高度算起的基底距離（以量為**單位**）。 下列程式碼會示範如何建立多邊形、將其加入至資料來源，以及使用多邊形延伸圖層類別來呈現。

> [!Note]
> `base`多邊形延伸圖層中定義的值應該小於或等於 `height` 。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="拉伸多邊形" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
在 >codepen 上 Azure 地圖服務 () ，查看畫筆<a href='https://codepen.io/azuremaps/pen/wvvBpvE'>拉伸多邊形</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a></iframe>


## <a name="add-data-driven-polygons"></a>新增資料驅動的多邊形

分區著色圖地圖可以使用多邊形的拉伸圖層來呈現。 將 `height` `fillColor` [延伸] 圖層的和屬性設定為 `Polygon` 和特徵幾何中統計變數的度量 `MultiPolygon` 。 下列程式碼範例會根據依州的人口密度測量，顯示分區著色圖的已分佈分布圖。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="已拉伸的分區著色圖對應" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
在 >codepen 上 Azure 地圖服務 () ，查看畫筆已 <a href='https://codepen.io/azuremaps/pen/eYYYNox'>拉伸分區著色圖地圖</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>

## <a name="add-a-circle-to-the-map"></a>將圓形新增至地圖

Azure 地圖服務使用 GeoJSON 架構的延伸版本，其 [提供如下所](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)述之圓形的定義。 您可以藉由建立 `point` 具有 `subType` 屬性的功能 `Circle` ，並使用代表半徑（以量為單位）的編號 `Radius` 屬性**meters**，在地圖上呈現拉伸圓形。 例如：

```javascript
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

Azure 地圖服務 Web SDK 會將這些 `Point` 功能轉換成 `Polygon` 本質上的功能。 您 `Point` 可以使用多邊形延伸圖層在地圖上呈現這些功能，如下列程式碼範例所示。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="無人機空域多邊形" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
請參閱 >codepen 上的 Azure 地圖服務 () ，查看 Pen <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>無人機空域多邊形</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>自訂多邊形的延伸圖層

多邊形的延伸圖層有數個樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 >codepen 上的 Azure 地圖服務 () 的畫筆<a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [多邊形延伸圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer)

其他資源：

> [!div class="nextstepaction"]
> [Azure 地圖服務 GeoJSON 規格延伸模組](extend-geojson.md#circle)
