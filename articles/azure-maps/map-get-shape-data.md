---
title: 從地圖上的形狀獲取資料 |微軟 Azure 地圖
description: 在本文中，瞭解如何使用 Microsoft Azure 地圖 Web SDK 在地圖上繪製圖形資料。
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 88db018575f92e777223f383c65cd6db51ba515a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334402"
---
# <a name="get-shape-data"></a>取得圖形資料

本文介紹如何獲取在地圖上繪製的形狀的資料。 我們使用**繪圖管理器內部的繪圖管理器.getSource（）** 函數。 [drawing manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--) 當您要提取繪製形狀的 Geojson 資料並將其用於其他位置時，有各種方案。  


## <a name="get-data-from-drawn-shape"></a>從繪製的形狀獲取資料

以下函數獲取繪製的形狀的來源資料並將其輸出到螢幕。 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

下面是完整的運行代碼示例，您可以在其中繪製形狀以測試功能：

<br/>

<iframe height="686" title="取得圖形資料" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，請參閱按<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure 映射 （）<a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>獲取圖形資料的</a>筆。
</iframe>


## <a name="next-steps"></a>後續步驟

瞭解如何使用繪圖工具模組的其他功能：

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
