---
title: 將繪圖工具工具列新增至地圖 |Microsoft Azure 對應
description: 如何使用 Azure 地圖服務 Web SDK 將繪圖工具欄新增至地圖
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 05a409faedb2093637b3d93045b35eb4e4929626
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310742"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>將繪圖工具工具列新增至地圖

本文說明如何使用繪圖工具模組，並在地圖上顯示繪圖工具欄。 [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)控制項會在地圖上新增繪圖工具欄。 您將學習如何建立只包含一個和所有繪圖工具的地圖，以及如何在繪圖管理員中自訂繪圖圖形的呈現。

## <a name="add-drawing-toolbar"></a>新增繪製工具列

下列程式碼會建立繪圖管理員的實例，並在地圖上顯示工具列。

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

以下是上述功能的完整程式碼範例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="新增繪製工具列" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
在 >codepen 上，查看 [畫筆 <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>新增繪圖] 工具列</a> 的 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io'> </a>。
</iframe>


## <a name="limit-displayed-toolbar-options"></a>限制顯示的工具列選項

下列程式碼會建立繪圖管理員的實例，並在地圖上只顯示多邊形繪圖工具的工具列。 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

以下是上述功能的完整程式碼範例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="新增多邊形繪圖工具" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
請參閱畫筆 Azure 地圖服務中的 () ，以在 >codepen 上<a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>新增多邊形繪圖工具</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>


## <a name="change-drawing-rendering-style"></a>變更繪圖呈現樣式

您可以使用 `drawingManager.getLayers()` 函數，然後設定個別圖層上的選項，藉以自訂繪製的圖形樣式。 編輯圖形時，針對座標顯示的拖曳控點是 HTML 標籤。 您可以藉由將 HTML 標籤選項傳遞至 `dragHandleStyle` 繪圖管理員的和選項，來自訂拖曳控點的樣式 `secondaryDragHandleStyle` 。  

下列程式碼會從繪圖管理員取得轉譯圖層，並修改其選項來變更繪圖的呈現樣式。 在此情況下，點將會以藍色標記圖示呈現。 線條會是紅色和四個圖元寬。 多邊形會有綠色填滿色彩和橙色輪廓。 然後，它會將拖曳控點的樣式變更為正方形圖示。 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

以下是上述功能的完整程式碼範例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="變更繪圖呈現樣式" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
查看畫筆 <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>變更繪圖轉譯樣式</a> 的方式，方法是 <a href='https://codepen.io/azuremaps'>@azuremaps</a> 在 <a href='https://codepen.io'>>codepen</a>上 Azure 地圖服務 () 。
</iframe>


## <a name="next-steps"></a>後續步驟

了解如何使用繪圖工具模組的其他功能：

> [!div class="nextstepaction"]
> [取得圖形資料](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [回應繪圖事件](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [互動類型和鍵盤快速鍵](drawing-tools-interactions-keyboard-shortcuts.md)

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [繪圖工具欄](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [繪圖管理員](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)
