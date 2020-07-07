---
title: 在 Azure 地圖服務中變更地圖的樣式 |Microsoft Azure 對應
description: 在本文中，您將瞭解 Microsoft Azure Maps web SDK 中可用的樣式相關功能。
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335677"
---
# <a name="change-the-style-of-the-map"></a>變更地圖樣式

對應支援數種不同的[樣式選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)，可在對應初始化或稍後使用 maps 函式時設定 `setStyle` 。 本文說明如何使用這些樣式選項來自訂地圖的外觀。 瞭解如何在載入對應時設定樣式，並瞭解如何使用樣式選擇器控制項來設定新的地圖樣式。

## <a name="set-the-style-options"></a>設定樣式選項 

樣式選項在使用 map 函式進行初始化或更新時，可以傳遞至對應 `setStyle` 。

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

下列工具顯示不同樣式選項如何改變地圖的呈現方式。 若要查看3D 建築物，請以靠近主要城市的方式放大。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="地圖樣式選項" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱 CodePen 上 Azure 地圖服務（）的畫筆<a href='https://codepen.io/azuremaps/pen/eYNMjPb'>地圖樣式選項</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>

## <a name="choose-a-base-map-style"></a>選擇基底地圖樣式

其中一個最常見的地圖樣式選項是用來變更樣式化之基底地圖的樣式。 在 Azure 地圖服務中，有許多[支援的地圖樣式](supported-map-styles.md)可在 Web SDK 中取得。 

### <a name="set-base-map-style-on-map-load"></a>設定地圖負載上的基底地圖樣式


藉由設定選項來初始化對應時，可以指定地圖樣式 `style` 。 在下列程式碼中， `style` 對應的選項會在初始化時設定為 `grayscale_dark` 。

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='設定地圖負載上的樣式' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/WKOQRq/'>在地圖負載上設定樣式</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="update-the-base-map-style"></a>更新基底地圖樣式

 您可以使用函式來更新地圖樣式 `setStyle` ，並將 `style` 選項設定為所需的地圖樣式。

```javascript
map.setStyle({ style: 'satellite' });
```

在下列程式碼中，載入對應實例之後，地圖樣式會從更新 `road` 為 `satellite` 使用[setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-)函數。

<br/>

<iframe height='500' scrolling='no' title='更新樣式' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/yqXYzY/'>更新樣式</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="add-the-style-picker"></a>新增樣式選擇器

樣式選擇器控制項提供一個便於使用的按鈕，具有 [飛出視窗] 面板，可供使用者用來變更地圖樣式。 樣式選擇器有兩個不同的版面配置選項。 根據預設，樣式選擇器會使用 `icons` 版面配置，並將所有地圖樣式顯示為圖示的水準列。 

<center>

![樣式選擇器圖示版面配置](media/choose-map-style/style-picker-icon-layout.png)</center>

第二個版面配置選項稱為 `list` ，並顯示地圖樣式的可滾動清單。  

<center>

![樣式選擇器清單版面配置](media/choose-map-style/style-picker-list-layout.png)</center>


下列程式碼顯示如何建立樣式選擇器控制項的實例，並將它加入至地圖的右上角。 樣式選擇器會設定為具有深色樣式，並使用清單圖層顯示所選取的幾個地圖樣式。

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

下列程式碼會將其預設設定中的樣式選擇器控制項新增至地圖，讓使用者可以輕鬆地在不同的地圖樣式之間切換。 使用靠近右上角的地圖樣式控制項來切換地圖樣式。

<br/>

<iframe height='500' scrolling='no' title='新增樣式選擇器' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/OwgyvG/'>新增樣式選擇器</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

> [!TIP]
> 根據預設，使用 Azure 地圖服務的 S0 定價層時，樣式選擇器控制項會列出所有可用的樣式。 如果您想要減少此清單中的樣式數目，請將您想要顯示在清單中的樣式陣列，傳遞至 `mapStyle` 樣式選擇器的選項。 如果您使用 S1，而且想要顯示所有可用的樣式，請將 `mapStyles` 樣式選擇器的選項設定為 `"all"` 。

## <a name="next-steps"></a>後續步驟

深入了解此文章中使用的類別與方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

將控制項新增至您的對應：

> [!div class="nextstepaction"]
> [新增地圖控制項](map-add-controls.md)

> [!div class="nextstepaction"]
> [新增釘選](map-add-pin.md)
