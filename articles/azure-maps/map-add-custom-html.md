---
title: 在地圖中新增 HTML 標記 | Microsoft Azure 地圖服務
description: 瞭解如何將 HTML 標籤新增至地圖。 瞭解如何使用 Azure 地圖服務 Web SDK 自訂標記，以及將快顯視窗和滑鼠事件新增至標記。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 4004977851911a521649e5644e0bef6cfb35769e
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086193"
---
# <a name="add-html-markers-to-the-map"></a>在地圖中新增 HTML 標記

本文說明如何在地圖中新增自訂 HTML (例如影像檔) 來作為 HTML 標記。

> [!NOTE]
> HTML 標記不會連線至資料來源。 相反地，位置資訊會直接新增至標記中，標記則會新增至地圖的 `markers` 屬性，即 [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager)。

> [!IMPORTANT]
> Azure 地圖服務 Web 控制項中的大多數圖層會使用 WebGL 來進行轉譯，HTML 標記則與之不同，會使用傳統的 DOM 元素來進行轉譯。 因此，新增至頁面的 HTML 標記越多，其中的 DOM 項目就會越多。 在新增幾百個 HTML 標記之後，效能就會下降。 因此，對於規模較大的資料集，請考慮將資料叢集化，或使用「符號」或「泡泡」圖層。

## <a name="add-an-html-marker"></a>新增 HTML 標記

[HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker) 類別具有預設樣式。 若要自訂標記，請設定標記的色彩和文字選項。 HTML 標記類別的預設樣式是 SVG 範本，其具有 `{color}` 和 `{text}` 預留位置。 若要快速自訂，請在 HTML 標記選項中設定 color 和 text 屬性。 

下列程式碼會建立 HTML 標籤，並將 color 屬性設為 "DodgerBlue"，並將 text 屬性設定為 "10"。 快顯視窗會附加至標記，並使用 `click` 事件來切換快顯視窗的可見度。

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='在地圖中新增 HTML 標記' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>在地圖中新增 HTML 標記</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="create-svg-templated-html-marker"></a>建立 SVG 樣板化 HTML 標記

HTML 標記的預設 `htmlContent` 是內含預留位置 `{color}` 和 `{text}` 的 SVG 範本。 您可以建立自訂 SVG 字串，並在 SVG 中新增同樣的預留位置，讓標記的 `color` 和 `text` 選項的設定更新 SVG 中的這些預留位置。

<br/>

<iframe height='500' scrolling='no' title='HTML 標記與自訂 SVG 範本' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML 標記與自訂 SVG 範本</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

> [!TIP]
> Azure 地圖服務 Web SDK 提供數個 SVG 影像範本，其可與 HTML 標籤搭配使用。 如需詳細資訊，請參閱[如何使用影像範本](how-to-use-image-templates-web-sdk.md)文件。

## <a name="add-a-css-styled-html-marker"></a>新增 CSS 樣式的 HTML 標記

HTML 標記的好處之一是您可以使用 CSS 來實現許多完美的自訂功能。 在此範例中，HtmlMarker 的內容是由 HTML 和 CSS 所組成，其會建立可就定位和有脈衝波的動畫圖釘。

<br/>

<iframe height='500' scrolling='no' title='HTML DataSource' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="draggable-html-markers"></a>可拖曳的 HTML 標記

此範例說明如何建立可拖曳的 HTML 標記。 HTML 標記支援 `drag`、`dragstart` 和 `dragend` 事件。

<br/>

<iframe height='500' scrolling='no' title='可拖曳的 HTML 標記' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>可拖曳的 HTML 標記</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>在 HTML 標記中新增滑鼠事件

這些範例會示範如何將滑鼠和拖曳事件新增至 HTML 標記。

<br/>

<iframe height='500' scrolling='no' title='在 HTML 標記中新增滑鼠事件' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>在 HTML 標記中新增滑鼠事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager)

如需更多可新增至地圖的程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"]
> [如何使用影像範本](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [新增符號圖層](./map-add-pin.md)

> [!div class="nextstepaction"]
> [新增泡泡圖層](./map-add-bubble-layer.md)
