---
title: 繪圖工具模組 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK 來設定繪圖選項資料
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 9b40dbd91357038d1e98b04628bd866ebb336381
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328882"
---
# <a name="use-the-drawing-tools-module"></a>使用繪圖工具模組

Azure 地圖服務 Web SDK 提供 *繪圖工具模組*。 此課程模組可讓您輕鬆地使用輸入裝置（例如滑鼠或觸控式螢幕），在地圖上繪製和編輯圖形。 此課程模組的核心類別是「 [繪圖管理員](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-)」。 繪圖管理員提供在地圖上繪製和編輯圖形所需的所有功能。 您可以直接使用它，並與自訂工具列 UI 整合。 您也可以使用內建的 [繪圖工具欄](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) 類別。 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>在網頁中載入繪圖工具模組

1. 建立新的 HTML 檔案，並 [照常執行對應](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。
2. 載入 Azure 地圖服務繪圖工具模組。 您可以透過下列兩種方式之一載入它：
    - 使用 Azure 地圖服務服務模組的全球託管 Azure 內容傳遞網路版本。 在檔案的元素中，將參考新增至 JavaScript 和 CSS 樣式表單 `<head>` ：

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - 或者，您可以使用 [Azure 地圖](https://www.npmjs.com/package/azure-maps-drawing-tools) 服務 npm 套件，在本機載入 AZURE 地圖服務 Web SDK 原始程式碼的繪圖工具模組，然後將它裝載在您的應用程式中。 此套件也包含 TypeScript 定義。 使用此命令：
    
        > **npm 安裝 azure-地圖-繪圖-工具**
    
        然後，在檔案的元素中加入 JavaScript 和 CSS 樣式表單的參考 `<head>` ：

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>直接使用繪圖管理員

在您的應用程式中載入繪圖工具模組之後，您就可以使用 [繪圖管理員](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-)來啟用繪圖和編輯功能。 您可以指定繪圖管理員的選項，同時將其具現化，或使用 `drawingManager.setOptions()` 函數。

### <a name="set-the-drawing-mode"></a>設定繪圖模式

下列程式碼會建立繪圖管理員的實例，並設定繪圖 **模式** 選項。 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

下列程式碼是一個完整的範例，說明如何設定繪圖管理員的繪圖模式。 按一下地圖以開始繪製多邊形。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="繪製多邊形" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在 >codepen 上，查看畫筆的 Azure 地圖服務 () 來<a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>繪製多邊形</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>


### <a name="set-the-interaction-type"></a>設定互動類型

繪圖管理員支援三種不同的方式來與地圖互動以繪製圖形。

* `click` -按一下滑鼠或觸控時，就會加入座標。
* `freehand ` -當滑鼠或觸控拖曳至地圖上時，就會加入座標。 
* `hybrid` -在按一下或拖曳滑鼠或觸控時，會加入座標。

下列程式碼會啟用多邊形繪圖模式，並設定繪圖管理員應遵守的繪圖互動類型 `freehand` 。 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 這個程式碼範例會在地圖上實行繪製多邊形的功能。 按住滑鼠左鍵，隨意將它拖曳到周圍。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自由繪圖" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在 >codepen 上 Azure 地圖服務 () ，查看畫筆<a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>自由繪圖</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>


### <a name="customizing-drawing-options"></a>自訂繪圖選項

先前的範例示範如何在具現化繪圖管理員時自訂繪圖選項。 您也可以使用函數來設定 [繪圖管理員] 選項 `drawingManager.setOptions()` 。 以下是使用 >setoptions 函數測試繪圖管理員所有選項的自訂工具。

<br/>

<iframe height="685" title="自訂繪圖管理員" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>在 >codepen 上 Azure 地圖服務 () ，查看畫筆 <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>取得圖形資料</a> 的方式 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>


## <a name="next-steps"></a>後續步驟

了解如何使用繪圖工具模組的其他功能：

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
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [繪圖管理員](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [繪圖工具欄](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)
