---
title: 變更 Azure 地圖服務 Web 地圖控制項的樣式
description: 瞭解如何變更地圖的樣式和選項。 請參閱如何在 Azure 地圖服務中將樣式選擇器控制項新增至地圖，讓使用者可以在不同的樣式之間切換。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-javascript
ms.openlocfilehash: 15224afac33a3e236c2d1ed9f60515bdc2f34c4f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036788"
---
# <a name="change-the-style-of-the-map"></a>變更地圖樣式

地圖控制項支援數種不同的地圖[樣式選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)和[基底地圖樣式](supported-map-styles.md)。 當對應控制項初始化時，可以設定所有樣式。 或者，您可以使用地圖控制項的函式來設定樣式 `setStyle` 。 本文說明如何使用這些樣式選項來自訂地圖的外觀。 此外，您將瞭解如何在地圖中執行樣式選擇器控制項。 樣式選擇器控制項可讓使用者在不同的基底樣式之間切換。

## <a name="set-map-style-options"></a>設定地圖樣式選項

樣式選項可以在 web 控制項初始化期間設定。 或者，您可以藉由呼叫地圖控制項的函式來更新樣式選項 `setStyle` 。 若要查看所有可用的樣式選項，請參閱[樣式選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)。

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo = true,
    showFeedbackLink = true,
    style='road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo = false,
    showFeedbackLink = false
});
```

下列工具顯示不同樣式選項如何改變地圖的呈現方式。 若要查看3D 建築物，請以靠近主要城市的方式放大。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="地圖樣式選項" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請在 CodePen 上 Azure 地圖服務 () ，查看 [畫筆<a href='https://codepen.io/azuremaps/pen/eYNMjPb'>地圖樣式] 選項</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="set-a-base-map-style"></a>設定基底地圖樣式

您也可以使用 Web SDK 中提供的其中一個[基底地圖樣式](supported-map-styles.md)來初始化地圖控制項。 接著，您可以使用函式 `setStyle` ，以不同的地圖樣式來更新基底樣式。

### <a name="set-a-base-map-style-on-initialization"></a>設定初始化時的基底地圖樣式

地圖控制項的基本樣式可以在初始化期間設定。 在下列程式碼中， `style` 地圖控制項的選項會設定為[ `grayscale_dark` 基底地圖樣式](supported-map-styles.md#grayscale_dark)。  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='設定地圖負載上的樣式' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/WKOQRq/'>在地圖負載上設定樣式</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="update-the-base-map-style"></a>更新基底地圖樣式

您可以使用函式來更新基底地圖樣式 `setStyle` ，並將 `style` 選項設定為變更為不同的基底地圖樣式，或加入其他樣式選項。

```javascript
map.setStyle({ style: 'satellite' });
```

在下列程式碼中，載入對應實例之後，地圖樣式會從更新 `grayscale_dark` 為 `satellite` 使用[setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-)函數。

<br/>

<iframe height='500' scrolling='no' title='更新樣式' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/yqXYzY/'>更新樣式</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="add-the-style-picker-control"></a>新增樣式選擇器控制項

樣式選擇器控制項提供一個容易使用的按鈕，具有 [飛出視窗] 面板，可供終端使用者在基底樣式之間切換。

樣式選擇器有兩個不同的版面配置選項： `icon` 和 `list` 。 此外，樣式選擇器也可讓您選擇兩種不同的樣式選擇器控制項 `style` 選項： `light` 和 `dark` 。 在此範例中，樣式選擇器會使用 `icon` 版面配置，並以圖示的形式顯示基底地圖樣式的選取清單。 樣式控制項選擇器包含下列基本樣式集： `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]` 。 如需樣式選擇器控制項選項的詳細資訊，請參閱[樣式控制項選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)。

下圖顯示版面配置中顯示的樣式選擇器控制項 `icon` 。

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="樣式選擇器清單版面配置":::

下圖顯示版面配置中顯示的樣式選擇器控制項 `list` 。

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="樣式選擇器清單版面配置":::

> [!IMPORTANT]
>根據預設，樣式選擇器控制項會列出 Azure 地圖服務的 S0 定價層底下可用的所有樣式。 如果您想要減少此清單中的樣式數目，請將您想要顯示在清單中的樣式陣列，傳遞至 `mapStyle` 樣式選擇器的選項。 如果您使用 S1，而且想要顯示所有可用的樣式，請將 `mapStyles` 樣式選擇器的選項設定為 `"all"` 。

下列程式碼示範如何覆寫預設的 `mapStyles` 基本樣式清單。 在此範例中，我們要設定 `mapStyles` 選項，以列出要由樣式選擇器控制項顯示的基底樣式。

<br/>

<iframe height='500' scrolling='no' title='新增樣式選擇器' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/OwgyvG/'>新增樣式選擇器</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

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
