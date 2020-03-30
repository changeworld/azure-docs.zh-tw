---
title: 將多邊形拉伸圖層添加到地圖 |微軟 Azure 地圖
description: 如何向 Microsoft Azure 映射 Web SDK 添加多邊形拉伸圖層。
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334548"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>向地圖添加多邊形拉伸圖層

本文介紹如何使用多邊形拉伸圖層將幾何區域渲染為拉伸形狀`Polygon`，並將`MultiPolygon`幾何體特徵化為拉伸形狀。 Azure 地圖 Web SDK 支援呈現擴展的[GeoJSON 架構](extend-geojson.md#circle)中定義的圓幾何體。 在地圖上渲染時，這些圓可以轉換為多邊形。 使用地圖集包裝時，所有要素幾何體都可以輕鬆更新[。形狀](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)類。

## <a name="use-a-polygon-extrusion-layer"></a>使用多邊形拉伸圖層

將[多邊形拉伸層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)連接到資料來源。 然後，載入到地圖上。 多邊形拉伸圖層將 和`Polygon``MultiPolygon`要素的面積渲染為拉伸形狀。 多邊`height`形`base`拉伸圖層的 和 屬性定義與拉伸形狀的基本距離和高度 **（以米**為單位）。 以下代碼演示如何創建多邊形、將其添加到資料來源，以及使用多邊形拉伸圖層類呈現它。

> [!Note]
> 在`base`多邊形拉伸圖層中定義的值應小於或等於 。 `height`

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="拉伸多邊形" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure 貼<a href='https://codepen.io/azuremaps'>@azuremaps</a>圖 （）<a href='https://codepen.io/azuremaps/pen/wvvBpvE'>拉伸的</a>筆拉伸面。</iframe>


## <a name="add-data-driven-polygons"></a>添加資料驅動的多邊形

可以使用多邊形拉伸圖層渲染色色譜貼圖。 將`height`拉伸圖層`fillColor`和屬性設置為`Polygon`和`MultiPolygon`要素幾何中統計變數的度量。 以下代碼示例顯示基於按州測量的人口密度的美國擠壓性喬普勒圖。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="拉伸的喬龍圖" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure 地圖（）<a href='https://codepen.io/azuremaps'>@azuremaps</a>繪製的筆拉伸<a href='https://codepen.io/azuremaps/pen/eYYYNox'>圖</a>。
</iframe>

## <a name="add-a-circle-to-the-map"></a>將圓形新增至地圖

Azure 映射使用 GeoJSON 架構的擴展版本，該架構為[此處](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)所述的圓圈提供定義。 通過`point`創建屬性`subType``Circle`和表示半徑（`Radius`**以米**為單位的編號屬性）的要素，可以在地圖上渲染拉伸圓。 例如：

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

Azure 地圖 Web SDK`Point`將這些功能`Polygon`轉換為引擎蓋下的要素。 這些`Point`要素可以使用多邊形拉伸圖層在地圖上呈現，如下代碼示例所示。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="無人機空域多邊形" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 的筆<a href='https://codepen.io/azuremaps/pen/zYYYrxo'>無人機空域面</a>。
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>自訂多邊形拉伸圖層

多邊形拉伸層具有多個樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='普格佈雷傑' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上通過 Azure 地圖<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看 Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ。</a>
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [多邊形拉伸層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

其他資源：

> [!div class="nextstepaction"]
> [Azure 映射 GeoJSON 規範擴展](extend-geojson.md#circle)
