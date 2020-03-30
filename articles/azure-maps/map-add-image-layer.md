---
title: 向地圖添加圖像圖層 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Microsoft Azure 地圖 Web SDK 在地圖上疊加圖像。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209710"
---
# <a name="add-an-image-layer-to-a-map"></a>在地圖中新增影像圖層

本文介紹如何將圖像疊加到一組固定的座標。 下面是一些可以疊加在地圖上的不同圖像類型的示例：

* 從無人機捕獲的圖像
* 建築平面圖
* 歷史或其他專用地圖圖像
* 作業地點的藍圖
* 天氣雷達圖像

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)是一種在地圖上疊加圖像的簡單方法。 請注意，瀏覽器可能難以載入大型圖像。 在這種情況下，請考慮將圖像分解為切片，並將其載入到地圖中作為[TileLayer。](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

圖像圖層支援以下圖像格式：

- JPEG
- PNG
- BMP
- GIF（無動畫）

## <a name="add-an-image-layer"></a>新增映像圖層

以下代碼在地圖上疊加了[1922 年新澤西州紐華克地圖](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)的圖像。 [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)是通過將 URL 傳遞給圖像來創建的，並且以格式`[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`對四個角的座標。

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

下面是前面代碼的完整運行代碼示例。

<br/>

<iframe height='500' scrolling='no' title='簡單的影像圖層' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/eQodRo/'>簡單的影像圖層</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>將 KML 檔導入地面疊加

此示例演示如何在地圖上將 KML 地面疊加資訊添加為圖像圖層。 KML 地面疊加提供北、南、東和西座標，以及逆時針旋轉。 但是，圖像圖層需要圖像的每個角的座標。 此示例中的 KML 地面覆蓋是為查特雷斯大教堂，它來自[維琪媒體](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)。

代碼使用`getCoordinatesFromEdges`[ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)類中的靜態函數。 它使用 KML 地面疊加的北、南、東、西和旋轉資訊計算圖像的四個角。

<br/>

<iframe height='500' scrolling='no' title='KML Ground Overlay 作為影像圖層' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML Ground Overlay 作為影像圖層</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="customize-an-image-layer"></a>自訂影像圖層

圖像圖層具有許多樣式選項。 這裡有一個工具來嘗試它們。

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