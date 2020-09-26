---
title: 將反升圖圖層新增至地圖 |Microsoft Azure 對應
description: 瞭解如何將地圖上的點轉譯為具有固定大小的圓形。 請參閱如何使用 Azure 地圖服務 Web SDK 來新增和自訂此用途的反升圖層。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 446a70a4399b7ec0e16fed2d33c9ec711acfe200
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310827"
---
# <a name="add-a-bubble-layer-to-a-map"></a>將泡泡圖層新增至地圖

本文說明如何將資料來源中的點資料轉譯為地圖上的氣泡圖層。 反升圖圖層會將點轉譯為地圖上具有固定圖元半徑的圓形。 

> [!TIP]
> 根據預設，泡泡圖層會轉譯資料來源中所有幾何圖形的座標。 若要限制圖層，使其只轉譯點幾何特徵， `filter` 請將圖層的屬性設定為， `['==', ['geometry-type'], 'Point']` 或者 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` ，如果您也要包含 MultiPoint 功能。

## <a name="add-a-bubble-layer"></a>新增泡泡圖層

下列程式碼會將點陣列載入資料來源。 然後，它會將資料點連接到反升 [圖圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer)。 反升圖層會以五個圖元和白色的填滿色彩，轉譯每個氣泡的半徑。 以及藍色的筆觸色彩，以及6圖元的筆觸寬度。 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>顯示標籤與泡泡圖層

這段程式碼會示範如何使用反升圖圖層，在地圖上呈現點。 以及如何使用符號圖層來呈現標籤。 若要隱藏符號圖層的圖示，請將 `image` 圖示選項的屬性設定為 `'none'` 。

<br/>

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="customize-a-bubble-layer"></a>自訂泡泡圖層

泡泡圖層只有少數樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='泡泡圖層選項' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/eQxbGm/'>泡泡圖層選項</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [新增符號圖層](map-add-pin.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
