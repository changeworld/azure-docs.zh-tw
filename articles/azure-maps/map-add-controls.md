---
title: 將控制項添加到地圖 |微軟 Azure 地圖
description: 如何向 Microsoft Azure 地圖中的地圖添加縮放控制項、間距控制、旋轉控制項和樣式選擇器。
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 094dc9fd01ec71f378a173a2b4fa64cc672d7c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334571"
---
# <a name="add-controls-to-a-map"></a>向地圖添加控制項

本文介紹如何向地圖添加控制項。 您還將學習如何創建包含所有控制項和[樣式選擇器](https://docs.microsoft.com/azure/azure-maps/choose-map-style)的地圖。

## <a name="add-zoom-control"></a>新增縮放控制項

縮放控制項添加用於放大和縮小地圖的按鈕。以下代碼示例創建[ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol)類的實例，並將其添加到地圖的右下角。

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

下面是上述功能的完整運行代碼示例。

<br/>

<iframe height='500' scrolling='no' title='新增縮放控制項' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/WKOQyN/'>新增縮放控制項</a>。
</iframe>

## <a name="add-pitch-control"></a>新增傾斜角度控制項

俯仰控制項添加用於傾斜音高以相對於地平線進行貼圖的按鈕。 以下代碼示例創建[PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol)類的實例。 它將間距控制添加到地圖的右上角。

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

下面是上述功能的完整運行代碼示例。

<br/>

<iframe height='500' scrolling='no' title='新增傾斜角度控制項' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/xJrwaP/'>新增傾斜角度控制項</a>。
</iframe>

## <a name="add-compass-control"></a>新增指南針控制項

指南針控制項添加用於旋轉地圖的按鈕。 以下代碼示例創建[指南針控制](/javascript/api/azure-maps-control/atlas.control.compasscontrol)類的實例，並將其添加到地圖的左下角。

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

下面是上述功能的完整運行代碼示例。

<br/>

<iframe height='500' scrolling='no' title='新增旋轉控制項' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/GBEoRb/'>新增旋轉控制項</a>。
</iframe>

## <a name="a-map-with-all-controls"></a>具有所有控制項的地圖

可以將多個控制項放入陣列中，並一次性添加到地圖中，並放置在地圖的同一區域中，以簡化開發。 下面使用此方法將標準導航控制項添加到地圖中。

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

以下代碼示例將縮放、指南針、俯仰和樣式選擇器控制項添加到地圖的右上角。 請注意它們如何自動堆疊。 控制項物件在指令碼中的順序會決定它們出現在地圖上的順序。 要更改地圖上控制項的順序，可以在陣列中更改它們的順序。

<br/>

<iframe height='500' scrolling='no' title='具有所有控制項的地圖' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/qyjbOM/'>具有所有控制項的地圖</a>。
</iframe>

樣式選擇器控制項由[樣式控制](/javascript/api/azure-maps-control/atlas.control.stylecontrol)類定義。 有關使用樣式選擇器控制項的詳細資訊，請參閱[選擇地圖樣式](choose-map-style.md)。

## <a name="customize-controls"></a>自訂控制項

下面是一個用於測試自訂控制項的各種選項的工具。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="導航控制選項" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 查看筆<a href='https://codepen.io/azuremaps/pen/LwBZMx/'>導航控制項選項</a>。
</iframe>

如果要創建自訂導航控制項，請創建從類擴展的`atlas.Control`類或創建 HTML 元素並將其放置在地圖 div 上方。 讓此 UI 控制項調用`setCamera`地圖函數來移動地圖。 

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [指南針控制](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

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
> [添加氣泡圖層](map-add-bubble-layer.md)

