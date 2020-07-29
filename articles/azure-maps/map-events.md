---
title: 處理地圖事件 | Microsoft Azure 地圖服務
description: 在本文中，您將了解如何使用 Microsoft Azure 地圖服務 Web SDK 來建立與地圖事件互動的 Web SDK。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 667ab684ef4a453d210af36c0221d350b25c9829
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282886"
---
# <a name="interact-with-the-map"></a>與地圖互動

本文說明如何使用[地圖事件類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events)。 屬性會醒目提示地圖上事件及地圖的不同圖層。 您也可以在與 HTML 標記互動時醒目提示事件。

## <a name="interact-with-the-map"></a>與地圖互動

試用下面的地圖，並查看右邊醒目提示的對應滑鼠事件。 您可以按一下 [JS] 索引標籤來檢視和編輯 JavaScript 程式碼。 您也可以按一下 [在 CodePen 上編輯] 按鈕，以在 CodePen 上修改程式碼。

<br/>

<iframe height='600' scrolling='no' title='與地圖互動 – 滑鼠事件' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>與地圖互動 – 滑鼠事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="interact-with-map-layers"></a>與地圖圖層互動

下列程式碼會在您與符號圖層互動時，醒目提示引發的事件。 符號、泡泡圖、線條與多邊形圖層全都支援一組相同的事件。 熱度圖和圖格層不支援這些事件。

<br/>

<iframe height='600' scrolling='no' title='與地圖互動 – 地圖事件' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>與地圖互動 – 圖層事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="interact-with-html-marker"></a>與 HTML 標記互動

下列程式碼會將 Javascript 地圖事件新增至 HTML 標記。 它也會醒目提示當您與 HTML 標記互動時所引發的事件名稱。

<br/>

<iframe height='500' scrolling='no' title='與地圖互動 – HTML 標記事件' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>與地圖互動 – HTML 標記事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

下表列出所有支援的地圖類別事件。

| 事件               | 描述 |
|---------------------|-------------|
| `boxzoomend`        | 在「方塊縮放」互動結束時引發。|
| `boxzoomstart`      | 在「方塊縮放」互動開始時引發。|
| `click`             | 在地圖上相同點按下並放開指標裝置時引發。|
| `close`             | 以手動或程式設計方式關閉快顯視窗時引發。|
| `contextmenu`       | 按一下滑鼠右鍵時引發。|
| `data`              | 載入或變更任何地圖資料時引發。 |
| `dataadded`         | 圖形新增至 `DataSource` 時引發。|
| `dataremoved`       | 圖形從 `DataSource` 中移除時引發。|
| `datasourceupdated` | 更新 `DataSource` 物件時引發。|
| `dblclick`          | 在地圖上相同點按兩下指標裝置時引發。|
| `drag`              | 在地圖、快顯或 HTML 標記的「拖曳至平移」互動期間重複引發。|
| `dragend`           | 在地圖、快顯或 HTML 標記的「拖曳至平移」互動結束時引發。|
| `dragstart`         | 在地圖、快顯或 HTML 標記的「拖曳至平移」互動開始時引發。|
| `error`             | 發生錯誤時引發。|
| `idle`              | <p>在地圖進入「閒置」狀態之前最後一個轉譯的畫面之後引發：<ul><li>沒有任何觀景窗正在轉換。</li><li>所有目前要求的圖格都已載入。</li><li>所有淡出或轉換動畫都已完成。</li></ul></p>|
| `keydown`           | 按下按鍵時引發。|
| `keypress`          | 按下會產生可鍵入字元 (ANSI 鍵) 的按鍵時引發。|
| `keyup`             | 釋放按鍵時引發。|
| `layeradded`        | 圖層新增至地圖時引發。|
| `layerremoved`      | 從地圖移除圖層時引發。|
| `load`              | 在已下載所有必要資源並已產生地圖的第一個視覺化完整轉譯之後，立即引發。|
| `mousedown`         | 在地圖內或在項目上方按下指標裝置時引發。|
| `mouseenter`        | 指標裝置開始在地圖或項目上移動時引發。 |
| `mouseleave`        | 指標裝置移出地圖或項目時引發。 |
| `mousemove`         | 指標裝置在地圖或項目內移動時引發。|
| `mouseout`          | 指標裝置離開地圖的畫布或離開項目時引發。|
| `mouseover`         | 指標裝置在地圖或項目上移動時引發。|
| `mouseup`           | 在地圖內或在項目上方放開指標裝置時引發。|
| `move`              | 在因使用者互動或方法的結果，而從某個檢視動態轉換到另一個檢視期間重複引發。|
| `moveend`           | 在地圖因使用者互動或方法的結果，而完成從某個檢視轉換至另一個檢視之後引發。|
| `movestart`         | 在地圖因使用者互動或方法的結果，而開始從某個檢視轉換到另一個檢視之前引發。|
| `open`              | 以手動或程式設計方式開啟快顯視窗時引發。|
| `pitch`             | 每當地圖因使用者互動或方法的結果，而變更傾斜度時引發。|
| `pitchend`          | 在地圖因使用者互動或方法的結果，而結束變更傾斜度之後立即引發。|
| `pitchstart`        | 每當地圖因使用者互動或方法的結果，而開始變更傾斜度時引發。|
| `ready`             | 當地圖已準備好以程式設計方式進行互動，以在載入最低的必要地圖資源時引發。|
| `render`            | <p>每當地圖因下列原因而繪製到畫面時引發：<ul><li>變更地圖的位置、縮放、傾斜度或方位。</li><li>變更地圖的樣式。</li><li>變更 `DataSource` 來源。</li><li>載入向量圖格、GeoJSON 檔案、字符或原件。</li></ul></p>|
| `resize`            | 調整地圖大小之後立即引發。|
| `rotate`            | 在「拖曳至旋轉」互動期間重複引發。|
| `rotateend`         | 在「拖曳至旋轉」互動結束時引發。|
| `rotatestart`       | 在「拖曳至旋轉」互動開始時引發。|
| `shapechanged`      | 在變更圖形物件屬性時引發。|
| `sourcedata`        | 載入或變更其中一個地圖的來源 (包括屬於來源的圖格) 時引發。 |
| `sourceadded`       | `DataSource` 或 `VectorTileSource` 新增至地圖時引發。|
| `sourceremoved`     | 從地圖移除 `DataSource` 或 `VectorTileSource` 時引發。|
| `styledata`         | 載入或變更地圖的樣式時引發。|
| `styleimagemissing` | 圖層嘗試從不存在的影像原件載入影像時引發 |
| `tokenacquired`     | 取得 AAD 存取權杖時引發。|
| `touchcancel`       | 在地圖內發生觸控取消事件時引發。|
| `touchend`          | 在地圖內發生觸控結束事件時引發。|
| `touchmove`         | 在地圖內發生觸控移動事件時引發。|
| `touchstart`        | 在地圖內發生觸控開始事件時引發。|
| `wheel`             | 在地圖內發生滑鼠滾輪事件時引發。|
| `zoom`              | 在因使用者互動或方法的結果，而從某個縮放層級動態轉換到另一個縮放層級期間重複引發。|
| `zoomend`           | 在地圖因使用者互動或方法的結果，而完成從某個縮放層級轉換至另一個縮放層級之後引發。|
| `zoomstart`         | 在地圖因使用者互動或方法的結果，而開始從某個縮放層級轉換至另一個縮放層級之後引發。|


## <a name="next-steps"></a>後續步驟

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務的服務模組](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
