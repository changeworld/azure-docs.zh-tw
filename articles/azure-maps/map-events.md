---
title: 處理對應事件 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK，建立互動式 Web SDK 與地圖事件的對應。
author: Philmea
ms.author: philmea
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 92108873d4a91c845f4a1f2685cf50942513510e
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124001"
---
# <a name="interact-with-the-map"></a>與地圖互動

本文說明如何使用[map 事件類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events)。 屬性會反白顯示地圖上的事件和地圖的不同圖層。 您也可以在與 HTML 標籤互動時，反白顯示事件。

## <a name="interact-with-the-map"></a>與地圖互動

請使用下面的地圖來播放，並查看右邊反白顯示的對應滑鼠事件。 您可以按一下 [JS]**** 索引標籤來檢視和編輯 JavaScript 程式碼。 您也可以按一下 [在**CodePen 上編輯**]，以修改 CodePen 上的程式碼。

<br/>

<iframe height='600' scrolling='no' title='與地圖互動 – 滑鼠事件' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>與地圖互動 – 滑鼠事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="interact-with-map-layers"></a>與地圖圖層互動

下列程式碼會在您與符號層互動時，反白顯示引發的事件。 符號、反升、線條和多邊形圖層都支援相同的事件集。 熱度圖和磚圖層不支援任何這些事件。

<br/>

<iframe height='600' scrolling='no' title='與地圖互動 – 地圖事件' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>與地圖互動 – 圖層事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="interact-with-html-marker"></a>與 HTML 標記互動

下列程式碼會將 JAVAscript 地圖事件新增至 HTML 標籤。 它也會醒目提示當您與 HTML 標記互動時所引發的事件名稱。

<br/>

<iframe height='500' scrolling='no' title='與地圖互動 – HTML 標記事件' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>與地圖互動 – HTML 標記事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

下表列出所有支援的對應類別事件。

| 事件               | 描述 |
|---------------------|-------------|
| `boxzoomend`        | 當「框縮放」互動結束時引發。|
| `boxzoomstart`      | 在「方塊縮放」互動開始時引發。|
| `click`             | 在地圖上的相同點按下並放開指標裝置時引發。|
| `close`             | 在以手動方式或以程式設計方式關閉快顯視窗時引發。|
| `contextmenu`       | 按一下滑鼠右鍵時引發。|
| `data`              | 在任何對應資料載入或變更時引發。 |
| `dataadded`         | 當圖形加入至時引發 `DataSource` 。|
| `dataremoved`       | 當圖形從中移除時引發 `DataSource` 。|
| `datasourceupdated` | 在 `DataSource` 更新物件時引發。|
| `dblclick`          | 當指標裝置在地圖上的相同點按兩次時引發。|
| `drag`              | 在地圖、快顯或 HTML 標籤的「拖曳至平移」互動期間重複引發。|
| `dragend`           | 當地圖、快顯或 HTML 標籤上的「拖曳至移動流覽」互動結束時引發。|
| `dragstart`         | 當地圖、快顯或 HTML 標籤上有「拖曳至移動流覽」互動時引發。|
| `error`             | 發生錯誤時引發。|
| `idle`              | <p>在對應進入「閒置」狀態之前呈現的最後一個畫面格之後引發：<ul><li>沒有任何相機轉換正在進行中。</li><li>所有目前要求的磚都已載入。</li><li>所有淡出/轉換動畫都已完成。</li></ul></p>|
| `keydown`           | 當按下按鍵時引發。|
| `keypress`          | 在產生 typable 字元（ANSI 索引鍵）的索引鍵被按下時引發。|
| `keyup`             | 在釋放按鍵時引發。|
| `layeradded`        | 在圖層新增至地圖時引發。|
| `layerremoved`      | 從對應中移除圖層時引發。|
| `load`              | 在下載所有必要的資源後立即引發，並已完成對應的第一個視覺化呈現。|
| `mousedown`         | 在地圖內按下指標或在專案上方時引發。|
| `mouseenter`        | 當指標裝置一開始在地圖或元素上移動時引發。 |
| `mouseleave`        | 當指標裝置移出地圖或元素時引發。 |
| `mousemove`         | 當指標裝置在地圖或元素內移動時引發。|
| `mouseout`          | 當點裝置離開地圖的畫布時引發，我們會離開元素。|
| `mouseover`         | 當指標裝置移至地圖或元素上方時引發。|
| `mouseup`           | 當指標裝置在地圖內或在專案上方時引發。|
| `move`              | 在動畫轉換期間重複引發，做為使用者互動或方法的結果。|
| `moveend`           | 在對應完成從一個 view 至另一個視圖的轉換後引發，做為使用者互動或方法的結果。|
| `movestart`         | 在對應開始從一個 view 轉換到另一個視圖之前就引發，做為使用者互動或方法的結果。|
| `open`              | 在以手動方式或以程式設計方式開啟快顯視窗時引發。|
| `pitch`             | 每當地圖的音調（傾斜）變更為使用者互動或方法的結果時，就會引發。|
| `pitchend`          | 在地圖的螺距（傾斜）結束之後，立即引發，做為使用者互動或方法的結果。|
| `pitchstart`        | 每當地圖的音調（傾斜）開始變更為使用者互動或方法的結果時，就會引發。|
| `ready`             | 當對應已準備好要以程式設計方式與互動時，載入最小的必要對應資源時引發。|
| `render`            | <p>當地圖繪製到螢幕時引發，做為的結果：<ul><li>地圖的位置、縮放、音調或軸承的變更。</li><li>地圖樣式的變更。</li><li>來源的變更 `DataSource` 。</li><li>向量磚、GeoJSON 檔、圖像或 sprite 的載入。</li></ul></p>|
| `resize`            | 在地圖已調整大小之後立即引發。|
| `rotate`            | 在「拖曳至旋轉」互動期間重複引發。|
| `rotateend`         | 當「拖曳至旋轉」互動結束時引發。|
| `rotatestart`       | 在「拖曳至旋轉」互動開始時引發。|
| `shapechanged`      | 當繪圖物件屬性變更時引發。|
| `sourcedata`        | 當其中一個對應的來源載入或變更時（包括屬於來源的磚載入或變更時）引發。 |
| `sourceadded`       | 當 `DataSource` 或 `VectorTileSource` 加入至對應時引發。|
| `sourceremoved`     | `DataSource` `VectorTileSource` 從對應中移除或時引發。|
| `styledata`         | 對應的樣式載入或變更時引發。|
| `styleimagemissing` | 當圖層嘗試從不存在的影像 sprite 載入影像時引發 |
| `tokenacquired`     | 取得 AAD 存取權杖時引發。|
| `touchcancel`       | 在對應中發生 touchcancel 事件時引發。|
| `touchend`          | 在對應中發生 touchend 事件時引發。|
| `touchmove`         | 在對應中發生 system.windows.uielement.touchmove> 事件時引發。|
| `touchstart`        | 在對應中發生 touchstart 事件時引發。|
| `wheel`             | 在對應中發生滑鼠滾輪事件時引發。|
| `zoom`              | 在從一個縮放層級動畫轉換到另一個時重複引發，做為使用者互動或方法的結果。|
| `zoomend`           | 在對應完成從一個縮放層級轉換為另一個，做為使用者互動或方法的結果之後，就會引發。|
| `zoomstart`         | 在對應開始從一個縮放層級轉換為另一個，做為使用者互動或方法的結果，就會引發。|


## <a name="next-steps"></a>後續步驟

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務 Services 模組](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
