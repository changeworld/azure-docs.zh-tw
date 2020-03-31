---
title: 繪圖工具模組 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Microsoft Azure 地圖 Web SDK 設置繪圖選項資料
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 711609f9382e2153cbc738d544933796dbbe2e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334300"
---
# <a name="use-the-drawing-tools-module"></a>使用繪圖工具模組

Azure 地圖 Web SDK 提供了一個*繪圖工具模組*。 此模組便於使用輸入裝置（如滑鼠或觸控式螢幕）在地圖上繪製和編輯形狀。 此模組的核心類是[繪圖管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)。 繪圖管理器提供在地圖上繪製和編輯形狀所需的所有功能。 它可以直接使用，並且與自訂工具列 UI 集成。 您還可以使用內置[繪圖工具欄](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)類。 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>在網頁中載入繪圖工具模組

1. 創建新的 HTML 檔案，並[像往常一樣實現映射](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。
2. 載入 Azure 地圖繪圖工具模組。 您可以通過兩種方式之一載入它：
    - 使用 Azure 地圖服務模組的全域託管 Azure 內容傳遞網路版本。 在檔`<head>`元素中添加對 JavaScript 和 CSS 樣式表的引用：

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - 或者，可以使用[Azure 映射繪圖工具](https://www.npmjs.com/package/azure-maps-drawing-tools)npm 包在本地載入 Azure 地圖 Web SDK 原始程式碼的繪圖工具模組，然後將其與應用一起託管。 此套件也包含 TypeScript 定義。 使用此命令：
    
        > **npm 安裝 azure 映射繪圖工具**
    
        然後，在檔的元素中添加對 JavaScript 和 CSS`<head>`樣式表的引用：

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>直接使用繪圖管理器

在應用程式中載入繪圖工具模組後，可以使用[繪圖管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)啟用繪圖和編輯功能。 您可以在具現化圖形管理器時為圖形管理器指定選項，也可以使用函數`drawingManager.setOptions()`。

### <a name="set-the-drawing-mode"></a>設置繪圖模式

以下代碼創建繪圖管理器的實例並設置繪圖**模式**選項。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

下面的代碼是如何設置繪圖管理器的繪圖模式的完整運行示例。 按一下地圖開始繪製多邊形。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="繪製多邊形" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在 CodePen 上，請參閱按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （）<a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>繪製多邊形</a><a href='https://codepen.io'>的筆</a>。
</iframe>


### <a name="set-the-interaction-type"></a>設置交互類型

繪圖管理器支援與地圖交互以繪製形狀的三種不同方式。

* `click`- 按一下滑鼠或觸摸時添加座標。
* `freehand `- 在地圖上拖動滑鼠或觸摸時添加座標。 
* `hybrid`- 按一下或拖動滑鼠或觸摸時添加座標。

以下代碼啟用多邊形繪圖模式，並設置繪圖管理器應遵循的繪圖交互的類型`freehand`。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 此代碼示例實現在地圖上繪製多邊形的功能。 只需按住滑鼠左鍵，然後自由地拖動它。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自由手繪圖" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，請參閱 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地圖 （）<a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>的筆自由手繪圖</a>。
</iframe>


### <a name="customizing-drawing-options"></a>自訂繪圖選項

前面的示例演示了如何在具現化繪圖管理器時自訂繪圖選項。 您還可以使用 函數設置繪圖管理器選項`drawingManager.setOptions()`。 下面是一個工具，用於使用 setOptions 函數測試繪圖管理器的所有選項的自訂。

<br/>

<iframe height="685" title="自訂繪圖管理器" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，請參閱按<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure 映射 （）<a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>獲取圖形資料的</a>筆。
</iframe>


## <a name="next-steps"></a>後續步驟

瞭解如何使用繪圖工具模組的其他功能：

> [!div class="nextstepaction"]
> [新增繪圖工具列](map-add-drawing-toolbar.md)

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
> [繪圖管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [繪圖工具欄](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
