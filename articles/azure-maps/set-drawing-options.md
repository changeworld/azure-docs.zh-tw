---
title: 繪圖工具模組 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK 來設定繪圖選項資料
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6c64820cdfa03e833bfd2fbad3ba7489170b14e5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988918"
---
# <a name="use-the-drawing-tools-module"></a>使用繪圖工具模組

Azure 地圖服務 Web SDK 提供*繪圖工具模組*。 此模組可讓您輕鬆地使用輸入裝置（例如滑鼠或觸控螢幕），在地圖上繪製和編輯圖形。 此模組的核心類別是「[繪圖管理員](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)」。 繪圖管理員會提供在地圖上繪製和編輯圖形所需的所有功能。 它可以直接使用，並與自訂工具列 UI 整合。 您也可以使用內建的[繪圖工具欄](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)類別。 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>在網頁中載入繪圖工具模組

1. 建立新的 HTML 檔案，並[照常執行對應](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。
2. 載入 [Azure 地圖服務繪圖工具] 模組。 您可以透過下列兩種方式的其中一種來載入它：
    - 使用 Azure 地圖服務服務模組的全球託管 Azure 內容傳遞網路版本。 在檔案的 `<head>` 元素中新增 JavaScript 和 CSS 樣式表單的參考：

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.js"></script>
        ```

    - 或者，您可以使用 npm，在本機載入適用于 Azure 地圖服務 Web SDK 原始程式碼的繪圖工具模組，然後將它裝載在[您的應用](https://www.npmjs.com/package/azure-maps-drawing-tools)程式中。 此套件也包含 TypeScript 定義。 使用此命令：
    
        > **npm 安裝 azure-maps-繪圖-工具**
    
        然後，在檔案的 `<head>` 元素中，新增 JavaScript 和 CSS 樣式表單的參考：

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>直接使用繪圖管理員

在應用程式中載入「繪圖工具」模組之後，您就可以使用「[繪圖管理員](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)」來啟用繪製和編輯功能。 您可以在具現化時指定 [繪圖管理員] 的選項，或使用 `drawingManager.setOptions()` 函數。

### <a name="set-the-drawing-mode"></a>設定繪製模式

下列程式碼會建立繪圖管理員的實例，並設定 [繪圖**模式]** 選項。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

以下程式碼是完整的執行範例，說明如何設定繪圖管理員的繪圖模式。 按一下對應以開始繪製多邊形。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="繪製多邊形" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的畫筆 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>繪製多邊形</a>。
</iframe>


### <a name="set-the-interaction-type"></a>設定互動類型

「繪圖管理員」支援三種不同的方式來與地圖互動，以繪製圖形。

* 按一下滑鼠或觸控時，會加入 `click` 座標。
* 當滑鼠或觸控拖曳至地圖上時，會加入 `freehand ` 座標。 
* 當按一下或拖曳滑鼠或觸控時，會加入 `hybrid` 座標。

下列程式碼會啟用多邊形繪製模式，並設定繪圖管理員應遵守 `freehand`的繪圖互動類型。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 這個程式碼範例會執行在地圖上繪製多邊形的功能。 只要按住滑鼠左鍵並隨意拖曳就能了。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自由繪圖" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的畫筆<a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>自由繪圖</a>。
</iframe>


### <a name="customizing-drawing-options"></a>自訂繪圖選項

先前的範例示範如何在具現化繪圖管理員時自訂繪製選項。 您也可以使用 `drawingManager.setOptions()` 函數來設定 [繪圖管理員] 選項。 以下工具可讓您使用 setOptions 函式來測試繪圖管理員所有選項的自訂。

<br/>

<iframe height="685" title="自訂繪圖管理員" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的使用畫筆<a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>取得圖形資料</a>Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>


## <a name="next-steps"></a>後續步驟

瞭解如何使用 [繪圖工具] 模組的其他功能：

> [!div class="nextstepaction"]
> [加入繪圖工具欄](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [取得圖形資料](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [對繪製事件做出反應](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [互動類型和鍵盤快速鍵](drawing-tools-interactions-keyboard-shortcuts.md)

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [繪圖管理員](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [繪製工具列](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
