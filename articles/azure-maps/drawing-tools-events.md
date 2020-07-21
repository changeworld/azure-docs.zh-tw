---
title: 繪製工具事件 |Microsoft Azure 對應
description: 在本文中，您會了解如何使用 Microsoft Azure 地圖服務 Web SDK，將繪圖工具列新增至地圖
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b5cbd5311c6070f59f8a5953d8b99789ab758c9c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524602"
---
# <a name="drawing-tool-events"></a>繪圖工具事件

在地圖上使用繪圖工具可方便使用者在地圖上繪製時回應某些事件。 此表列出 `DrawingManager` 類別支援的所有事件。

| 事件 | 描述 |
|-------|-------------|
| `drawingchanged` | 圖形中新增座標或有任何座標變更時引發。 | 
| `drawingchanging` | 顯示圖形的任何預覽座標時引發。 例如，當拖曳某個座標時，就會多次引發這個事件。 | 
| `drawingcomplete` | 當圖形完成繪製或離開編輯模式時引發。 |
| `drawingmodechanged` | 繪製模式發生變更後引發。 新的繪圖模式會傳遞至事件處理常式。 |
| `drawingstarted` | 當使用者開始繪製圖形或將圖形置於編輯模式時引發。  |

下列程式碼顯示繪圖工具模組中的事件運作方式。 在地圖上繪製圖形，並監看事件的引發過程。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="繪圖工具事件" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
查看畫筆的 <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>Drawing tools events</a> (繪圖工具事件)，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

<br/>

## <a name="examples"></a>範例

讓我們看看一些使用繪圖工具事件的常見案例。

### <a name="select-points-in-polygon-area"></a>選取多邊形區域中的點

這段程式碼會示範如何監視使用者繪製圖形的事件。 此範例中的程式碼會監視多邊形、矩形和圓形。 然後，會判斷地圖上哪些資料點是在繪製的區域內。 `drawingcomplete` 事件用以觸發選取邏輯。 在選取邏輯中，程式碼會迴圈地圖上的所有資料點。 檢查點和繪圖區域是否有交集。 這個範例會使用開放原始碼的 [Turf.js](https://turfjs.org/) 程式庫來執行空間交集計算。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="選取已繪製多邊形區域中的資料" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
查看畫筆的 <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Select data in drawn polygon area</a> (選取已繪製多邊形區域中的資料)，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>在多邊形區域中繪圖與搜尋

這段程式碼會在使用者完成圖形繪製之後，搜尋繪圖區域內感興趣的點。 您可按一下畫面右上角的 [Edit on Code pen] \(用 CodePen 編輯\) 來修改及執行程式碼。 `drawingcomplete` 事件用以觸發搜尋邏輯。 如果使用者繪製矩形或多邊形，即會在幾何圖形內執行搜尋。 如果繪製的是圓形，則使用半徑和圓心搜尋感興趣的點。 `drawingmodechanged` 事件用以判斷使用者何時切換至繪圖模式，且會清除繪圖的畫布。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="在多邊形區域中繪圖與搜尋" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
查看畫筆的 <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Draw and search in polygon area</a> (繪製多邊形區域並在其中搜尋)，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>建立測量工具

下列程式碼顯示如何利用繪圖事件建立測量工具。 `drawingchanging` 用以監視圖形的繪製過程。 當使用者移動滑鼠時，就會計算圖形的維度。 `drawingcomplete` 事件用以在圖形繪製完成後，對圖形執行最後的計算。 `drawingmodechanged` 事件用以判斷使用者何時切換成繪圖模式。 而且，`drawingmodechanged` 事件也會清除繪圖畫布，並清除舊的度量資訊。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="測量工具" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
查看畫筆的 <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>Measuring tool</a> (測量工具)，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

<br/>

## <a name="next-steps"></a>後續步驟

了解如何使用繪圖工具模組的其他功能：

> [!div class="nextstepaction"]
> [取得圖形資料](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [互動類型和鍵盤快速鍵](drawing-tools-interactions-keyboard-shortcuts.md)

深入了解服務模組：

> [!div class="nextstepaction"]
> [服務模組](how-to-use-services-module.md)

查看更多程式碼範例：

> [!div class="nextstepaction"]
> [程式碼範例頁面](https://aka.ms/AzureMapsSamples) \(英文\)
