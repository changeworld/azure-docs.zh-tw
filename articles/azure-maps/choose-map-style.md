---
title: 更改 Azure 地圖中的地圖樣式 |微軟 Azure 地圖
description: 在本文中，您將瞭解 Microsoft Azure 地圖 Web SDK 中提供的與樣式相關的功能。
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335677"
---
# <a name="change-the-style-of-the-map"></a>變更地圖樣式

地圖支援幾個不同的[樣式選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)，可在地圖初始化或以後使用地圖`setStyle`函數時設置。 本文演示如何使用這些樣式選項自訂地圖外觀。 在載入地圖時學習設置樣式，並瞭解如何使用樣式選擇器控制項設置新的地圖樣式。

## <a name="set-the-style-options"></a>設置樣式選項 

樣式選項可在以後使用地圖函數初始化或更新時傳遞到地圖`setStyle`中。

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

以下工具顯示不同樣式選項如何更改地圖的呈現方式。 要查看 3D 建築，可以放大大城市附近。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="地圖樣式選項" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地圖 （） 查看筆<a href='https://codepen.io/azuremaps/pen/eYNMjPb'>貼圖樣式選項</a>。
</iframe>

## <a name="choose-a-base-map-style"></a>選擇底圖樣式

最常見的地圖樣式選項之一用於更改樣式設置的基本地圖的樣式。 [Azure 地圖中支援的許多地圖樣式](supported-map-styles.md)在 Web SDK 中可用。 

### <a name="set-base-map-style-on-map-load"></a>在地圖負載上設置基地圖樣式


通過設置選項在初始化地圖時，`style`可以指定地圖樣式。 在以下代碼中，`style`映射的選項設置為`grayscale_dark`初始化。

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='設定地圖負載上的樣式' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/WKOQRq/'>在地圖負載上設定樣式</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="update-the-base-map-style"></a>更新基本地圖樣式

 可以使用`setStyle`函數更新地圖樣式，`style`並將選項設置為所需的地圖樣式。

```javascript
map.setStyle({ style: 'satellite' });
```

在以下代碼中，載入地圖實例後，地圖樣式將從`road`更新到`satellite`使用[setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-)函數。

<br/>

<iframe height='500' scrolling='no' title='更新樣式' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/yqXYzY/'>更新樣式</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="add-the-style-picker"></a>新增樣式選擇器

樣式選擇器控制項提供了一個便於使用的按鈕，帶有快顯視窗面板，最終使用者可以使用該按鈕更改地圖樣式。 樣式選擇器有兩個不同的佈局選項。 預設情況下，樣式選擇器使用`icons`佈局，並將所有地圖樣式顯示為圖示水準行。 

<center>

![樣式選擇器圖示佈局](media/choose-map-style/style-picker-icon-layout.png)</center>

調用`list`第二個佈局選項並顯示可滾動的地圖樣式清單。  

<center>

![樣式選擇器清單佈局](media/choose-map-style/style-picker-list-layout.png)</center>


以下代碼演示如何創建樣式選擇器控制項的實例並將其添加到地圖的右上角。 樣式選擇器設置為具有深色樣式，並使用清單圖層顯示選定的幾個地圖樣式。

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

以下代碼將樣式選擇器控制項及其預設設置添加到地圖中，以便使用者可以輕鬆地在不同的地圖樣式之間切換。 使用右上角附近的地圖樣式控制項切換地圖樣式。

<br/>

<iframe height='500' scrolling='no' title='新增樣式選擇器' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/OwgyvG/'>新增樣式選擇器</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

> [!TIP]
> 預設情況下，當使用 Azure 地圖的 S0 定價層時，樣式選擇器控制項列出所有可用的樣式。 如果要減少此清單中的樣式數，請將要在清單中顯示的樣式的陣列傳遞到樣式選擇器`mapStyle`的選項中。 如果使用 S1 並希望顯示所有可用樣式，則將`mapStyles`樣式選擇器的選項設置為`"all"`。

## <a name="next-steps"></a>後續步驟

深入了解此文章中使用的類別與方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [樣式選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [樣式控制選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

向地圖添加控制項：

> [!div class="nextstepaction"]
> [新增地圖控制項](map-add-controls.md)

> [!div class="nextstepaction"]
> [新增釘選](map-add-pin.md)
