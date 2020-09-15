---
title: 將控制項新增至地圖 |Microsoft Azure 對應
description: 如何在 Microsoft Azure 對應中，將縮放控制項、音調控制、旋轉控制項和樣式選擇器新增至地圖。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-javascript
ms.openlocfilehash: a1194b65f7f2cbd79ec5744d92ab65154305f7c1
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086210"
---
# <a name="add-controls-to-a-map"></a>將控制項新增至地圖

本文說明如何將控制項新增至地圖。 您也將瞭解如何建立具有所有控制項和 [樣式選擇器](https://docs.microsoft.com/azure/azure-maps/choose-map-style)的地圖。

## <a name="add-zoom-control"></a>新增縮放控制項

縮放控制項會加入縮放地圖的放大和縮小按鈕。下列程式碼範例會建立 [>zoomcontrol](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 類別的實例，並將它新增至地圖的右下角。

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='新增縮放控制項' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/WKOQyN/'>新增縮放控制項</a>。
</iframe>

## <a name="add-pitch-control"></a>新增傾斜角度控制項

「音調」控制項會新增按鈕，以傾斜相對於水準的地圖間距。 下列程式碼範例會建立 [>pitchcontrol](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 類別的實例。 它會將 >pitchcontrol 新增至地圖的右上角。

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='新增傾斜角度控制項' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/xJrwaP/'>新增傾斜角度控制項</a>。
</iframe>

## <a name="add-compass-control"></a>新增指南針控制項

羅盤控制項會加入用來旋轉地圖的按鈕。 下列程式碼範例會建立 [羅盤控制項](/javascript/api/azure-maps-control/atlas.control.compasscontrol) 類別的實例，並將它新增至地圖的左下角。

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='新增旋轉控制項' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/GBEoRb/'>新增旋轉控制項</a>。
</iframe>

## <a name="a-map-with-all-controls"></a>具有所有控制項的地圖

您可以將多個控制項放入陣列中，並一次加入至地圖，並放在對應的相同區域中，以簡化開發工作。 以下會使用這種方法，將標準導覽控制項新增至地圖。

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

下列程式碼範例會將縮放、羅盤、音調和樣式選擇器控制項新增至地圖的右上角。 請注意它們如何自動堆疊。 控制項物件在指令碼中的順序會決定它們出現在地圖上的順序。 若要變更地圖上控制項的順序，您可以在陣列中變更其順序。

<br/>

<iframe height='500' scrolling='no' title='具有所有控制項的地圖' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/qyjbOM/'>具有所有控制項的地圖</a>。
</iframe>

樣式選擇器控制項是由 [>stylecontrol](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 類別定義。 如需使用樣式選擇器控制項的詳細資訊，請參閱 [選擇地圖樣式](choose-map-style.md)。

## <a name="customize-controls"></a>自訂控制項

以下是用來測試自訂控制項之各種選項的工具。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="導覽控制選項" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
在 >codepen 上 Azure 地圖服務 () ，查看畫筆 <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>導覽控制項選項</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>

如果您想要建立自訂的導覽控制項，請建立從類別延伸的類別， `atlas.Control` 或建立 HTML 專案並將其放在地圖 div 上方。 讓這個 UI 控制項呼叫 map 函式 `setCamera` 來移動地圖。 

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [羅盤控制項](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

如需完整的程式碼，請參閱下列文章：

> [!div class="nextstepaction"]
> [新增釘選](./map-add-pin.md)

> [!div class="nextstepaction"]
> [新增快顯](./map-add-popup.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer.md)

