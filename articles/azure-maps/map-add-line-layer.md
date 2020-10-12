---
title: 將線條圖層新增至地圖 | Microsoft Azure 地圖服務
description: 瞭解如何將線條新增至地圖。 請參閱使用 Azure 地圖服務 Web SDK 將線條圖層新增至地圖，以及自訂具有符號和色彩漸層之線條的範例。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: c1968df19ebc62d47da77b0338d93ceb5f49f2c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310521"
---
# <a name="add-a-line-layer-to-the-map"></a>將線條圖層新增至地圖

線條圖層可用來將 `LineString` 和 `MultiLineString` 功能轉譯為地圖上的路徑或路線。 線條圖層也可用來呈現 `Polygon` 和 `MultiPolygon` 功能的外框。 資料來源會連線到線條圖層，以為其提供要呈現的資料。 

> [!TIP]
> 根據預設，線條圖層會轉譯資料來源中的多邊形座標及線條。 若要限制圖層，使其只呈現 LineString 功能，請將圖層的 `filter` 屬性設定為 `['==', ['geometry-type'], 'LineString']`，如果也想包含 MultiLineString 功能，則請設為 `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]`。

下列程式碼示範如何建立線條。 將這行程式碼新增至資料來源，然後使用 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer) 類別，以線條圖層呈現。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='在地圖中新增線條' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/qomaKv/'>在地圖中新增線條</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

您可使用 [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) 以及[使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)來將線條圖層樣式化。

## <a name="add-symbols-along-a-line"></a>沿著線條新增符號

這個範例會示範如何沿著地圖上的線條新增箭號圖示。 使用符號圖層時，請將 [放置] 選項設定為 [線條]。 此選項會沿著線條呈現符號，並旋轉圖示 (0 度 = 右方)。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="沿著線條顯示箭號" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
查看畫筆的 <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Show arrow along line</a> (沿路線顯示箭號)，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

> [!TIP]
> Azure 地圖服務 Web SDK 提供數個可自訂的影像範本，以供作為符號圖層使用。 如需詳細資訊，請參閱[如何使用影像範本](how-to-use-image-templates-web-sdk.md)文件。

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>將筆觸漸層新增至線條

您可為線條套用單一筆觸色彩。 您也可使用色彩漸層填滿線條來顯示從一個線段轉換到下一個線段。 例如，您可使用線條漸層表示經過一段時間和距離的變更，或連接線上物件的不同溫度。 若要將這項功能套用至線條，則資料來源必須將 `lineMetrics` 選項設為 true，色彩漸層運算式才能傳遞至該線條的 `strokeColor` 選項。 筆觸漸層運算式必須參考向運算式公開計算結果行計量的 `['line-progress']` 日期運算式。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="使用筆觸漸層的線條" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
查看畫筆的 <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>Line with Stroke Gradient</a> (筆觸漸層線條)，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="customize-a-line-layer"></a>自訂線條圖層

線條圖層有數個樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='線條圖層選項' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>線條圖層選項</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [新增快顯](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用影像範本](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)
