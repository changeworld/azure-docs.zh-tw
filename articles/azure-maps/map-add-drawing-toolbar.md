---
title: 向地圖添加繪圖工具欄 |微軟 Azure 地圖
description: 如何使用 Azure 地圖 Web SDK 向地圖添加繪圖工具欄
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bebf1ddfbca3aec5a551193609381cf3510bc3ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334491"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>向地圖添加繪圖工具工具列

本文介紹如何使用繪圖工具模組並在地圖上顯示繪圖工具欄。 ["繪圖工具欄"](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)控制項在地圖上添加繪圖工具欄。 您將學習如何僅使用一個和所有繪圖工具創建地圖，以及如何自訂繪圖管理器中圖形形狀的渲染。

## <a name="add-drawing-toolbar"></a>新增繪製工具列

以下代碼創建繪圖管理器的實例，並在地圖上顯示工具列。

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

下面是上述功能的完整運行代碼示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="新增繪製工具列" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在 CodePen 上，請參閱按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 添加繪圖<a href='https://codepen.io'>欄的筆</a><a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>添加繪圖工具欄</a>。
</iframe>


## <a name="limit-displayed-toolbar-options"></a>限制顯示的工具列選項

以下代碼創建繪圖管理器的實例，並在地圖上僅顯示一個多邊形繪圖工具的工具列。 

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

下面是上述功能的完整運行代碼示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="添加多邊形繪圖工具" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱在 CodePen 上按 Azure 映射 （）<a href='https://codepen.io/azuremaps'>@azuremaps</a><a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>添加多邊形繪圖工具</a><a href='https://codepen.io'>的筆</a>。
</iframe>


## <a name="change-drawing-rendering-style"></a>更改繪圖渲染樣式

通過使用`drawingManager.getLayers()`函數，然後在各個圖層上設置選項，可以自訂繪製的形狀樣式，從而檢索繪圖管理器的基礎圖層。 編輯形狀時為座標顯示的拖動控點是 HTML 標籤。 通過將 HTML 標籤選項傳遞到繪圖管理器 的 和`dragHandleStyle``secondaryDragHandleStyle`選項，可以自訂拖動控點的樣式。  

以下代碼從繪圖管理器獲取呈現圖層，並修改其選項以更改繪圖的呈現樣式。 在這種情況下，點將呈現與藍色標記圖示。 線條為紅色，四個圖元寬。 多邊形將具有綠色填充顏色和橙色輪廓。 然後，它將拖動控點的樣式更改為方形圖示。 

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

下面是上述功能的完整運行代碼示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="更改繪圖渲染樣式" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure 地圖<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看筆<a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>更改繪圖呈現樣式</a>。
</iframe>


## <a name="next-steps"></a>後續步驟

瞭解如何使用繪圖工具模組的其他功能：

> [!div class="nextstepaction"]
> [取得圖形資料](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [回應繪圖事件](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [互動類型和鍵盤快速鍵](drawing-tools-interactions-keyboard-shortcuts.md)

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [繪圖工具欄](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [繪圖管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)
