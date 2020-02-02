---
title: 將影像圖層新增至地圖 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK 來覆迭地圖上的影像。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cfdf7dfd4c16f70065e338f8983d2124d3f6f0ef
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933205"
---
# <a name="add-an-image-layer-to-a-map"></a>在地圖中新增影像圖層

本文說明如何在地圖上將影像重迭成一組固定的座標。 以下是一些影像類型的範例，通常會在地圖上重迭：

* 從無人機所捕獲的映射
* 建立 floorplans
* 歷程記錄或其他特製化地圖影像
* 作業網站的藍圖
* 氣象雷達圖影像

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)是一種簡單的方式，可在地圖上疊加影像。 請注意，瀏覽器可能難以載入大型影像。 在此情況下，請考慮將您的影像分解成磚，並將其載入至地圖中做為[TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)。

影像層支援下列影像格式：

- JPEG
- PNG
- BMP
- GIF （沒有動畫）

## <a name="add-an-image-layer"></a>新增映像圖層

下列程式碼會從地圖上的[1922 中，覆迭紐華克、New Jersey 的地圖](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)影像。 [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)的建立方式是將 URL 傳遞給影像，並以 `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`的格式來協調四個角落。

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

以下是上述程式碼的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='簡單的影像圖層' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/eQodRo/'>簡單的影像圖層</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>匯入 KML 檔案作為基礎重迭

下一個範例示範如何將 KML 地面重迭資訊覆迭為地圖上的影像圖層。 KML 地面重迭提供北、南、東和西座標和逆時針旋轉，而影像圖層則需要影像每個角落的座標。 此範例中的 KML 地面重迭是 Chartres cathedral，來自[Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)。

下列程式碼會使用[ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)類別的靜態 `getCoordinatesFromEdges` 函式。 它會從北部、南、東和 west 計算影像的四個角落，以及從 KML 地面重迭的旋轉資訊。

<br/>

<iframe height='500' scrolling='no' title='KML Ground Overlay 作為影像圖層' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML Ground Overlay 作為影像圖層</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="customize-an-image-layer"></a>自訂影像圖層

影像圖層有許多樣式選項。 以下是用來試用的工具。

<br/>

<iframe height='700' scrolling='no' title='影像圖層選項' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>影像圖層選項</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [新增圖格圖層](./map-add-tile-layer.md)