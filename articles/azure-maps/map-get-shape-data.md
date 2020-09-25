---
title: 從地圖上的圖形取得資料 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK 取得地圖上繪製的圖形資料。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: ecefac68a4348eeae23860d542f949b1c7ff23a1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310199"
---
# <a name="get-shape-data"></a>取得圖形資料

本文說明如何取得地圖上繪製的圖形資料。 我們在[繪圖管理員](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--)內使用**drawingManager. GetSource ( # B1**函數。 當您想要將繪製圖形的 geojson 資料解壓縮並在其他位置使用它時，有許多案例。  


## <a name="get-data-from-drawn-shape"></a>從繪製的圖形取得資料

下列函式會取得繪製的圖形來源資料，並將其輸出到畫面。 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

以下是完整的程式碼範例，您可以在其中繪製圖形來測試功能：

<br/>

<iframe height="686" title="取得圖形資料" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>在 >codepen 上 Azure 地圖服務 () ，查看畫筆 <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>取得圖形資料</a> 的方式 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>


## <a name="next-steps"></a>後續步驟

了解如何使用繪圖工具模組的其他功能：

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
