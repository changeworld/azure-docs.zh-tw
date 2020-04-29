---
title: 將線條圖層新增至地圖 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK，將線條圖層新增至地圖。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c473be25907eb3a761fbccd598bb9b732e5be5b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80802343"
---
# <a name="add-a-line-layer-to-the-map"></a>將線條圖層新增至地圖

線條圖層可用來呈現`LineString`和`MultiLineString`特徵做為地圖上的路徑或路線。 線條圖層也可以用來呈現`Polygon`和`MultiPolygon`功能的外框。 資料來源會連接到線條圖層，以提供要呈現的資料。 

> [!TIP]
> 根據預設，線條圖層會轉譯多邊形和資料來源中線條的座標。 若要限制圖層，使其只呈現 LineString 功能， `filter`請將圖層的`['==', ['geometry-type'], 'LineString']`屬性`['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]`設為，或者，如果您也想要包含 MultiLineString 功能。

下列程式碼顯示如何建立線條。 將這一行加入至資料來源，然後使用[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)類別，以線條圖層呈現。

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

<iframe height='500' scrolling='no' title='在地圖中新增線條' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/qomaKv/'>在地圖中新增線條</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

線條圖層可以使用[LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)來樣式化，並[使用資料驅動樣式表達式](data-driven-style-expressions-web-sdk.md)。

## <a name="add-symbols-along-a-line"></a>沿著一行新增符號

這個範例會示範如何在地圖上沿著線條加入箭號圖示。 使用符號層時，請將「位置」選項設定為「行」。 此選項會沿著這一行呈現符號，並旋轉圖示（0度 = 右方）。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="沿著線條顯示箭號" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 [依 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>），<a href='https://codepen.io/azuremaps/pen/drBJwX/'>沿著行顯示</a>畫筆] 箭號。
</iframe>

> [!TIP]
> Azure 地圖服務 web SDK 提供數個可自訂的影像範本，您可以搭配符號層使用。 如需詳細資訊，請參閱[如何使用影像範本](how-to-use-image-templates-web-sdk.md)檔。

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>將筆觸漸層新增至線條

您可以將單一筆劃色彩套用至線條。 您也可以使用色彩漸層填滿一行，以顯示從一個線段到下一個線段的轉換。 例如，您可以使用線條漸層來代表經過一段時間和距離的變更，或在連接的物件線上有不同的溫度。 若要將這項功能套用至一行，資料來源必須將`lineMetrics`選項設定為 true，然後將色彩漸層運算式傳遞至行的`strokeColor`選項。 筆劃漸層運算式必須參考向運算式`['line-progress']`公開計算結果行度量的日期運算式。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="具有筆觸漸層的線條" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的<a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>使用筆觸</a>漸層的<a href='https://codepen.io/azuremaps'>@azuremaps</a>畫筆線，Azure 地圖服務（）。
</iframe>

## <a name="customize-a-line-layer"></a>自訂線條圖層

線條圖層有數個樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='線條圖層選項' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>線條圖層選項</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

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
