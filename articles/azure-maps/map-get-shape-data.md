---
title: 從地圖上的圖形取得資料 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK 來取得地圖上繪製的圖形資料。
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 63d947b85e75e3809445c5bc65577aeaed38caa1
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209659"
---
# <a name="get-shape-data"></a>取得圖形資料

本文說明如何取得地圖上繪製的圖形資料。 我們在[繪圖管理員](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--)內使用**drawingManager. getSource （）** 函數。 在許多情況下，您會想要將繪製圖形的 geojson 資料解壓縮，並在其他地方使用。  


## <a name="get-data-from-drawn-shape"></a>從繪製的圖形取得資料

下列函式會取得所繪製圖形的來源資料，並將它輸出到螢幕上。 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

以下是完整的執行中程式碼範例，您可以在其中繪製圖形來測試功能：

<br/>

<iframe height="686" title="取得圖形資料" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的使用畫筆<a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>取得圖形資料</a>Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>


## <a name="next-steps"></a>後續步驟

瞭解如何使用 [繪圖工具] 模組的其他功能：

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
