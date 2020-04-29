---
title: 將圖格圖層新增至地圖 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK 來覆迭地圖上的圖格圖層。 圖格圖層可讓您在地圖上呈現影像。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76988595"
---
# <a name="add-a-tile-layer-to-a-map"></a>將圖格圖層新增至地圖

本文說明如何在地圖上重迭磚圖層。 圖格圖層可讓您在 Azure 地圖服務的地圖底圖上覆蓋影像。 如需 Azure 地圖服務並排顯示系統的詳細資訊，請參閱[縮放層級和磚方格](zoom-levels-and-tile-grid.md)。

磚圖層會從伺服器載入磚。 這些映射可以是預先轉譯或動態呈現。 預先呈現的影像會以磚圖層所瞭解的命名慣例，與伺服器上的任何其他影像一樣儲存。 動態呈現的影像會使用服務來載入接近即時的影像。 Azure 地圖服務[TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)類別支援三種不同的磚服務命名慣例： 

* X、Y、縮放標記法-X 是資料行，Y 是磚方格中磚的資料列位置，而 Zoom 標記法是以縮放層級為基礎的值。
* Quadkey 標記法-將 x、y 和 zoom 資訊結合成單一字串值。 這個字串值會變成單一磚的唯一識別碼。
* 周框方塊-以周框方塊座標格式指定影像： `{west},{south},{east},{north}`。 [Web 對應服務（WMS）](https://www.opengeospatial.org/standards/wms)通常會使用此格式。

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) 是視覺化地圖上大型資料集的好方法。 圖格圖層不僅可以從影像產生，向量資料也可以呈現為磚圖層。 藉由將向量資料轉譯為圖格圖層，地圖控制項只需要載入檔案大小較小的磚，而不是它們所代表的向量資料。 這項技術通常用來在地圖上轉譯數百萬個數據列。

傳入磚圖層的磚 URL 必須是 HTTP 或 TileJSON 資源的 HTTPs URL，或是使用下列參數的磚 URL 範本： 

* `{x}` - 圖格的 X 位置。 也需要 `{y}` 和 `{z}`。
* `{y}` - 圖格的 Y 位置。 也需要 `{x}` 和 `{z}`。
* `{z}` 圖格的縮放層級。 也需要 `{x}` 和 `{y}`。
* `{quadkey}` -圖格 quadkey 識別碼，以 Bing Maps 圖格系統的命名慣例為基礎。
* `{bbox-epsg-3857}` - 使用 `{west},{south},{east},{north}` 格式的週框方塊字串，位在 EPSG 3857 空間參考系統中。
* `{subdomain}`-子域值的預留位置（如果有指定）， `subdomain`將會加入。

## <a name="add-a-tile-layer"></a>新增圖格圖層

 這個範例會示範如何建立指向一組磚的磚圖層。 這個範例會使用 x、y、縮放並排顯示系統。 此圖格圖層的來源是天氣雷達覆疊圖，資料來源：[愛荷華州立大學的愛荷華州環境氣象網 (Iowa Environmental Mesonet of Iowa State University)](https://mesonet.agron.iastate.edu/ogc/)。 在觀看雷達圖時，理想的情況下，使用者會在流覽地圖時清楚看到城市的標籤。 將圖格圖層插入`labels`圖層下方，即可實現此行為。

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='底圖圖層使用 X、Y 和 Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/BGEQjG/'>使用 X、Y 和 Z 的圖格圖層</a>。
</iframe>

## <a name="customize-a-tile-layer"></a>自訂圖格圖層

圖格圖層類別有許多樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='圖格圖層選項' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/xQeRWX/'>圖格圖層選項</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [新增映像圖層](./map-add-image-layer.md)
