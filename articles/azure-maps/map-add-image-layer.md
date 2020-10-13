---
title: 將影像圖層新增至地圖 |Microsoft Azure 對應
description: 瞭解如何將影像新增至地圖。 瞭解如何使用 Azure 地圖服務 Web SDK，在固定的座標集合上自訂影像圖層和重迭影像。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 4116028cc8c4668c897d7de7bbffb607b5e5daba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310538"
---
# <a name="add-an-image-layer-to-a-map"></a>在地圖中新增影像圖層

本文說明如何將影像重迭為固定的座標集。 以下是一些可在地圖上重迭的不同影像類型範例：

* 從無人機所捕獲的影像
* 建立 floorplans
* 歷史或其他特製化地圖影像
* 作業網站的藍圖
* 氣象雷達圖影像

> [!TIP]
> [>imagelayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer)是一種簡單的方式，可在地圖上覆蓋影像。 請注意，瀏覽器載入大型影像時可能會發生困難。 在此情況下，請考慮將您的影像分解為圖格，並將其載入地圖中作為 [>tilelayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer)。

映射層支援下列影像格式：

- JPEG
- PNG
- BMP
- GIF (沒有任何動畫) 

## <a name="add-an-image-layer"></a>新增映像圖層

下列程式碼會在地圖上將 [紐華克（New Jersey）地圖](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) 的影像覆迭為1922。 [>imagelayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer)的建立方式是將 URL 傳遞至影像，並使用格式的四個角落座標 `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` 。

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

以下是上述程式碼的完整程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='簡單的影像圖層' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/eQodRo/'>簡單的影像圖層</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>將 KML 檔案匯入為地面重迭

此範例示範如何將 KML 地面重迭資訊新增為地圖上的影像圖層。 KML 地面重迭提供北、南、東和西座標，以及逆時針旋轉。 但是，影像圖層預期會有影像的每個角落的座標。 此範例中的 KML 基礎覆迭適用于 Chartres cathedral，且其來源為 [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)。

程式碼會使用 `getCoordinatesFromEdges` [>imagelayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer) 類別中的靜態函式。 它會使用 KML 地面重迭的北、南、東、西部和旋轉資訊來計算影像的四個角落。

<br/>

<iframe height='500' scrolling='no' title='KML Ground Overlay 作為影像圖層' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML Ground Overlay 作為影像圖層</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="customize-an-image-layer"></a>自訂影像圖層

影像圖層有許多樣式選項。 以下是試用的工具。

<br/>

<iframe height='700' scrolling='no' title='影像圖層選項' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>影像圖層選項</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [新增圖格圖層](./map-add-tile-layer.md)