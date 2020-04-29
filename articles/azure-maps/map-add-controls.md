---
title: 將控制項新增至地圖 |Microsoft Azure 對應
description: 如何將縮放控制項、音調控制項、旋轉控制項和樣式選擇器新增至 Microsoft Azure 對應中的地圖。
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 094dc9fd01ec71f378a173a2b4fa64cc672d7c97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334571"
---
# <a name="add-controls-to-a-map"></a>將控制項新增至對應

本文說明如何將控制項新增至地圖。 您也將瞭解如何使用所有控制項和[樣式選擇器](https://docs.microsoft.com/azure/azure-maps/choose-map-style)來建立地圖。

## <a name="add-zoom-control"></a>新增縮放控制項

縮放控制項會加入按鈕，以放大和縮小地圖。下列程式碼範例會建立[ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol)類別的實例，並將其新增至地圖的右下角。

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='新增縮放控制項' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/WKOQyN/'>新增縮放控制項</a>。
</iframe>

## <a name="add-pitch-control"></a>新增傾斜角度控制項

[音調] 控制項會新增按鈕，以傾斜相對於水準地圖的間距。 下列程式碼範例會建立[PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol)類別的實例。 它會將 PitchControl 新增至地圖的右上角。

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='新增傾斜角度控制項' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/xJrwaP/'>新增傾斜角度控制項</a>。
</iframe>

## <a name="add-compass-control"></a>新增指南針控制項

羅盤控制項會加入按鈕來旋轉地圖。 下列程式碼範例會建立[羅盤控制項](/javascript/api/azure-maps-control/atlas.control.compasscontrol)類別的實例，並將其新增到地圖的左下角。

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='新增旋轉控制項' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/GBEoRb/'>新增旋轉控制項</a>。
</iframe>

## <a name="a-map-with-all-controls"></a>具有所有控制項的地圖

多個控制項可以放入陣列中，並一次新增至地圖，並放在對應的相同區域，以簡化開發工作。 以下會使用這種方法，將標準導覽控制項新增至地圖。

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

下列程式碼範例會將 [zoom]、[羅盤]、[音調] 和 [樣式選擇器] 控制項新增至地圖的右上角。 請注意它們如何自動堆疊。 控制項物件在指令碼中的順序會決定它們出現在地圖上的順序。 若要變更地圖上控制項的順序，您可以在陣列中變更其順序。

<br/>

<iframe height='500' scrolling='no' title='具有所有控制項的地圖' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/qyjbOM/'>具有所有控制項的地圖</a>。
</iframe>

樣式選擇器控制項是由[StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)類別所定義。 如需使用樣式選擇器控制項的詳細資訊，請參閱[選擇地圖樣式](choose-map-style.md)。

## <a name="customize-controls"></a>自訂控制項

以下是用來測試各種選項以自訂控制項的工具。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="導覽控制選項" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的畫筆<a href='https://codepen.io/azuremaps/pen/LwBZMx/'>導覽控制選項</a>。
</iframe>

如果您想要建立自訂的導覽控制項，請建立從`atlas.Control`類別擴充的類別，或建立 HTML 專案，並將其放在地圖 div 上方。 讓這個 UI 控制項呼叫 maps `setCamera`函式來移動地圖。 

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
> [新增氣泡圖層](map-add-bubble-layer.md)

