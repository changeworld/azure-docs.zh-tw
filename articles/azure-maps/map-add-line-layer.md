---
title: 向地圖添加線圖層 |微軟 Azure 地圖
description: 在本文中，您將學習如何使用 Microsoft Azure 地圖 Web SDK 將線圖層添加到地圖中。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8503b12be628fe7d5651221c9d0379bee3e292bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76933471"
---
# <a name="add-a-line-layer-to-the-map"></a>向地圖添加線圖層

線圖層可用於在地圖上渲染`LineString`和`MultiLineString`要素作為路徑或路徑。 線圖層還可用於呈現 和`Polygon``MultiPolygon`要素的輪廓。 資料來源連接到線圖層，以提供要呈現的資料。 

> [!TIP]
> 預設情況下，線圖層將呈現資料來源中的多邊形和行的座標。 要限制圖層，使其僅渲染 LineString 要素，`filter`請將圖層的屬性設置為`['==', ['geometry-type'], 'LineString']``['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]`或是否還希望也包含多LineString要素。

以下代碼演示如何創建行。 將線添加到資料來源，然後使用[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)類使用線圖層呈現它。

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

下面是上述功能的完整運行代碼示例。

<br/>

<iframe height='500' scrolling='no' title='在地圖中新增線條' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/qomaKv/'>在地圖中新增線條</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

可以使用[LineLayer 選項](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)[並使用資料驅動的樣式表達式](data-driven-style-expressions-web-sdk.md)對線圖層進行樣式設置。

## <a name="add-symbols-along-a-line"></a>沿線添加符號

此示例演示如何沿地圖上的線條添加箭頭圖示。 使用符號圖層時，將"放置"選項設置為"行"。 此選項將沿直線渲染符號並旋轉圖示（0 度 = 右）。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="沿線顯示箭頭" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地圖 （） 沿行查看"筆<a href='https://codepen.io/azuremaps/pen/drBJwX/'>顯示"箭頭</a>。
</iframe>

> [!TIP]
> Azure 地圖 Web SDK 提供了多個可自訂的圖像範本，可用於符號圖層。 有關詳細資訊，請參閱[如何使用圖像範本](how-to-use-image-templates-web-sdk.md)文檔。

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>向線條添加描邊漸變

您可以將單個描邊顏色應用於線條。 您還可以用色彩坡形填充線條，以顯示從一個線段到下一個線段的過渡。 例如，線漸變可用於表示隨時間和距離的變化，或跨連接的物件線的不同溫度。 為了將此功能應用於行，資料來源必須將`lineMetrics`選項設置為 true，然後可以將色彩坡形運算式傳遞給行`strokeColor`的選項。 描邊漸變運算式必須引用向`['line-progress']`運算式公開計算的行指標的日期運算式。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="帶描邊漸變的線條" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地圖 （） 顯示<a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>具有描邊漸變的筆線</a>。
</iframe>

## <a name="customize-a-line-layer"></a>自訂線條圖層

Line 圖層具有多個樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='線條圖層選項' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>線條圖層選項</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [線層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [線圖層選項](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [新增快顯](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用映像範本](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)
