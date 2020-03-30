---
title: 向地圖添加切片圖層 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Microsoft Azure 地圖 Web SDK 在地圖上疊加切片圖層。 切片圖層允許您在地圖上渲染圖像。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988595"
---
# <a name="add-a-tile-layer-to-a-map"></a>將圖格圖層新增至地圖

本文介紹如何在地圖上疊加磁貼圖層。 圖格圖層可讓您在 Azure 地圖服務的地圖底圖上覆蓋影像。 有關 Azure 地圖平鋪系統的詳細資訊，請參閱[縮放級別和切片網格](zoom-levels-and-tile-grid.md)。

磁貼圖層從伺服器載入切片。 這些圖像可以預渲染或動態渲染。 使用切片層可以理解的命名約定，預渲染的圖像與伺服器上的任何其他圖像一樣存儲。 動態渲染的圖像使用服務載入接近即時的圖像。 Azure 地圖[切片層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)類支援三種不同的切片服務命名約定： 

* X、Y、縮放標記法 - X 是列，Y 是磁貼網格中磁貼的行位置，縮放標記法基於縮放級別的值。
* Quadkey 標記法 - 將 x、y 和縮放資訊合併到單個字串值中。 此字串值成為單個磁貼的唯一識別碼。
* 邊界框 - 在邊界框座標格式中指定圖像： `{west},{south},{east},{north}`. Web[映射服務 （WMS）](https://www.opengeospatial.org/standards/wms)通常使用此格式。

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) 是視覺化地圖上大型資料集的好方法。 不僅可以從圖像生成切片圖層，還可以將向量資料渲染為切片圖層。 通過將向量資料呈現為切片圖層，地圖控制項只需載入檔案大小小於它們表示的向量資料的切片。 此技術通常用於在地圖上呈現數百萬行資料。

傳遞到磁貼圖層中的磁貼 URL 必須是指向 TileJSON 資源的 HTTP 或 HTTPs URL 或使用以下參數的磁貼 URL 範本： 

* `{x}` - 圖格的 X 位置。 也需要 `{y}` 和 `{z}`。
* `{y}` - 圖格的 Y 位置。 也需要 `{x}` 和 `{z}`。
* `{z}` 圖格的縮放層級。 也需要 `{x}` 和 `{y}`。
* `{quadkey}` -圖格 quadkey 識別碼，以 Bing Maps 圖格系統的命名慣例為基礎。
* `{bbox-epsg-3857}` - 使用 `{west},{south},{east},{north}` 格式的週框方塊字串，位在 EPSG 3857 空間參考系統中。
* `{subdomain}`- 子域值的預留位置（如果指定）`subdomain`將添加。

## <a name="add-a-tile-layer"></a>新增圖格圖層

 此示例演示如何創建指向一組切片的切片圖層。 此示例使用 x、y 縮放平鋪系統。 此圖格圖層的來源是天氣雷達覆疊圖，資料來源：[愛荷華州立大學的愛荷華州環境氣象網 (Iowa Environmental Mesonet of Iowa State University)](https://mesonet.agron.iastate.edu/ogc/)。 在查看雷達資料時，理想情況下，使用者在流覽地圖時會清楚地看到城市的標籤。 可以通過在`labels`圖層下方插入切片圖層來實現此行為。

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

下面是上述功能的完整運行代碼示例。

<br/>

<iframe height='500' scrolling='no' title='底圖圖層使用 X、Y 和 Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/BGEQjG/'>使用 X、Y 和 Z 的圖格圖層</a>。
</iframe>

## <a name="customize-a-tile-layer"></a>自訂圖格圖層

切片圖層類具有許多樣式選項。 以下是可讓您試用這些選項的工具。

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
