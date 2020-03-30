---
title: 向地圖添加繪圖工具欄 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Microsoft Azure 地圖 Web SDK 將繪圖工具欄添加到地圖中
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: cf9c79f608aa3ffd1137be41ff3348f62b890867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198304"
---
# <a name="drawing-tool-events"></a>繪圖工具事件

在地圖上使用繪圖工具時，當使用者在地圖上繪製時，對某些事件做出反應非常有用。 此表列出了`DrawingManager`類支援的所有事件。

| 事件 | 描述 |
|-------|-------------|
| `drawingchanged` | 當添加或更改形狀中的任何座標時觸發。 | 
| `drawingchanging` | 顯示形狀的任何預覽座標時觸發。 例如，此事件將在拖動座標時觸發多次。 | 
| `drawingcomplete` | 當形狀繪製完成或從編輯模式中取出時觸發。 |
| `drawingmodechanged` | 當繪圖模式已更改時觸發。 新的繪圖模式將傳遞到事件處理常式中。 |
| `drawingstarted` | 當使用者開始繪製形狀或將形狀置於編輯模式時觸發。  |

以下代碼顯示了"繪圖工具"模組中的事件的工作原理。 在地圖上繪製形狀並觀看事件觸發。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="繪圖工具事件" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 查看筆<a href='https://codepen.io/azuremaps/pen/dyPMRWo'>繪圖工具事件</a>。
</iframe>

<br/>

## <a name="examples"></a>範例

讓我們看看一些使用繪圖工具事件的常見方案。

### <a name="select-points-in-polygon-area"></a>選擇多邊形區域中的點

此代碼演示如何監視使用者繪圖形狀的事件。 在此示例中，代碼監視多邊形、矩形和圓的形狀。 然後，它確定地圖上的資料點在繪製區域內。 該`drawingcomplete`事件用於觸發選擇邏輯。 在選擇邏輯中，代碼迴圈遍歷地圖上的所有資料點。 它檢查點和繪製形狀的區域是否有交集。 此示例使用開源[Turf.js](https://turfjs.org/)庫執行空間交集計算。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="在繪製的多邊形區域中選擇資料" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，通過 Azure 地圖 （）<a href='https://codepen.io/azuremaps'>@azuremaps</a>查看<a href='https://codepen.io/azuremaps/pen/XWJdeja'>繪製的多邊形區域中的筆選擇資料</a>。
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>在多邊形區域中繪製和搜索

此代碼在使用者繪製完形狀後搜索形狀區域內的興趣點。 您可以通過按一下框架右上角的"編輯代碼筆"來修改和執行代碼。 該`drawingcomplete`事件用於觸發搜索邏輯。 如果使用者繪製矩形或多邊形，則執行幾何體內的搜索。 如果繪製圓，則半徑和中心位置用於執行興趣點搜索。 該`drawingmodechanged`事件用於確定使用者何時切換到繪圖模式，並且此事件清除繪圖畫布。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="在多邊形區域中繪製和搜索" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，通過 Azure 地圖 （）<a href='https://codepen.io/azuremaps'>@azuremaps</a>在<a href='https://codepen.io/azuremaps/pen/eYmZGNv'>多邊形區域查看筆繪製和搜索</a>。
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>創建測量工具

下面的代碼顯示了如何使用繪圖事件來創建測量工具。 `drawingchanging`用於監視正在繪製的形狀。 當使用者移動滑鼠時，將計算形狀的尺寸。 該`drawingcomplete`事件用於在繪製形狀後對形狀進行最終計算。 該`drawingmodechanged`事件用於確定使用者何時切換到繪圖模式。 此外，事件`drawingmodechanged`清除繪圖畫布並清除舊的測量資訊。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="測量工具" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地圖 （） 查看筆<a href='https://codepen.io/azuremaps/pen/RwNaZXe'>測量工具</a>。
</iframe>

<br/>

## <a name="next-steps"></a>後續步驟

瞭解如何使用繪圖工具模組的其他功能：

> [!div class="nextstepaction"]
> [取得圖形資料](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [互動類型和鍵盤快速鍵](drawing-tools-interactions-keyboard-shortcuts.md)

瞭解有關服務模組的更多資訊：

> [!div class="nextstepaction"]
> [服務模組](how-to-use-services-module.md)

查看更多程式碼範例：

> [!div class="nextstepaction"]
> [程式碼範例頁面](https://aka.ms/AzureMapsSamples) \(英文\)
