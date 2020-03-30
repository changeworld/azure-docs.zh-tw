---
title: 處理地圖事件 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Microsoft Azure 地圖 Web SDK 使用地圖事件製作互動式 Web SDK 地圖。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b97371d1b63ad4abfe1635e426df1449ab5f3f14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534893"
---
# <a name="interact-with-the-map"></a>與地圖互動

本文介紹如何使用[地圖事件類](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events)。 屬性突出顯示地圖上和地圖不同圖層上的事件。 您還可以在與 HTML 標籤交互時突出顯示事件。

## <a name="interact-with-the-map"></a>與地圖互動

使用下面的地圖播放，並看到右側突出顯示的相應滑鼠事件。 您可以按一下 [JS]**** 索引標籤來檢視和編輯 JavaScript 程式碼。 您還可以按一下**CodePen 上的"編輯**"來修改 CodePen 上的代碼。

<br/>

<iframe height='600' scrolling='no' title='與地圖互動 – 滑鼠事件' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>與地圖互動 – 滑鼠事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="interact-with-map-layers"></a>與地圖圖層互動

以下代碼突出顯示與符號圖層交互時觸發的事件。 符號、氣泡、線和麵圖層都支援同一組事件。 熱圖和切片圖層不支援任何這些事件。

<br/>

<iframe height='600' scrolling='no' title='與地圖互動 – 地圖事件' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>與地圖互動 – 圖層事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="interact-with-html-marker"></a>與 HTML 標記互動

以下代碼將 JAVAscript 映射事件添加到 HTML 標籤中。 它也會醒目提示當您與 HTML 標記互動時所引發的事件名稱。

<br/>

<iframe height='500' scrolling='no' title='與地圖互動 – HTML 標記事件' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>與地圖互動 – HTML 標記事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

下表列出了所有支援的地圖類事件。

| 事件               | 描述 |
|---------------------|-------------|
| `boxzoomend`        | 當"框縮放"交互結束時激發。|
| `boxzoomstart`      | 啟動"框縮放"交互時激發。|
| `click`             | 當按下指標裝置並在地圖上的同一點釋放時觸發。|
| `close`             | 當快顯視窗手動或以程式方式關閉時觸發。|
| `contextmenu`       | 按一下滑鼠右鍵時觸發。|
| `data`              | 當任何地圖資料載入或更改時觸發。 |
| `dataadded`         | 當將形狀添加到 時`DataSource`觸發。|
| `dataremoved`       | 從 中刪除形狀時觸發`DataSource`。|
| `datasourceupdated` | 更新`DataSource`物件時觸發。|
| `dblclick`          | 當在地圖上的同一點按一下指標裝置兩次時觸發。|
| `drag`              | 在地圖、快顯視窗或 HTML 標籤上的"拖動到平移"交互期間重複觸發。|
| `dragend`           | 當"拖動到平移"交互在地圖、快顯視窗或 HTML 標籤上結束時觸發。|
| `dragstart`         | 當"拖動到平移"交互開始在地圖、快顯視窗或 HTML 標籤上時觸發。|
| `error`             | 發生錯誤時觸發。|
| `idle`              | <p>在地圖進入"空閒"狀態之前渲染的最後一幀後觸發：<ul><li>沒有攝像機轉換正在進行。</li><li>已載入所有當前請求的磁貼。</li><li>所有淡入淡出/過渡動畫都已完成。</li></ul></p>|
| `keydown`           | 按下鍵時觸發。|
| `keypress`          | 按下生成可鍵入字元（ANSI 金鑰）的鍵時觸發。|
| `keyup`             | 釋放金鑰時觸發。|
| `layeradded`        | 將圖層添加到地圖時觸發。|
| `layerremoved`      | 從地圖中刪除圖層時觸發。|
| `load`              | 下載所有必要的資源並首次直觀地呈現地圖後立即觸發。|
| `mousedown`         | 在地圖中按下指標裝置或元素頂部時觸發。|
| `mouseenter`        | 當指標裝置最初在地圖或元素上移動時觸發。 |
| `mouseleave`        | 當指標裝置移出地圖或元素時觸發。 |
| `mousemove`         | 在地圖或元素中移動指標裝置時觸發。|
| `mouseout`          | 當點設備離開地圖的畫布時，我們留下一個元素。|
| `mouseover`         | 當指標裝置在地圖或元素上移動時觸發。|
| `mouseup`           | 當在地圖中釋放指標裝置或在元素頂部釋放時觸發。|
| `move`              | 在從一個視圖到另一個視圖的動畫轉換期間，由於使用者交互或方法的結果，重複觸發。|
| `moveend`           | 在地圖完成從一個視圖到另一個視圖的過渡後觸發，這是使用者交互或方法的結果。|
| `movestart`         | 在地圖開始從一個視圖轉換到另一個視圖之前觸發，這是使用者交互或方法的結果。|
| `open`              | 手動或以程式設計方式打開快顯視窗時觸發。|
| `pitch`             | 每當地圖的間距（傾斜）因使用者交互或方法而改變時觸發。|
| `pitchend`          | 在地圖的間距（傾斜）完成更改後，立即觸發，因為使用者交互或方法。|
| `pitchstart`        | 每當地圖的間距（傾斜）由於使用者交互或方法而開始更改時，都會觸發。|
| `ready`             | 在地圖準備好以程式設計方式與之交互之前載入所需的最小地圖資源時觸發。|
| `render`            | <p>每當地圖繪製到螢幕時，由於以下情況，即：<ul><li>更改地圖的位置、縮放、俯仰或軸承。</li><li>更改地圖樣式。</li><li>對源的`DataSource`更改。</li><li>向量磁貼、GeoJSON 檔、字形或精靈的載入。</li></ul></p>|
| `resize`            | 在地圖調整大小後立即觸發。|
| `rotate`            | 在"拖動旋轉"交互期間反復觸發。|
| `rotateend`         | 當"拖動旋轉"交互結束時觸發。|
| `rotatestart`       | 當"拖動旋轉"交互啟動時觸發。|
| `shapechanged`      | 更改形狀物件屬性時觸發。|
| `sourcedata`        | 當地圖的一個源載入或更改時觸發，包括屬於源的磁貼載入或更改。 |
| `sourceadded`       | 當 或`DataSource``VectorTileSource`添加到地圖時觸發。|
| `sourceremoved`     | 從`DataSource`地圖中刪除`VectorTileSource`或 時觸發。|
| `styledata`         | 當地圖的樣式載入或更改時觸發。|
| `styleimagemissing` | 當圖層嘗試從不存在的圖像子畫面載入圖像時觸發 |
| `tokenacquired`     | 獲取 AAD 訪問權杖時觸發。|
| `touchcancel`       | 當地圖中發生觸摸取消事件時觸發。|
| `touchend`          | 當地圖中發生觸摸端事件時觸發。|
| `touchmove`         | 當在地圖中發生觸摸移動事件時觸發。|
| `touchstart`        | 當在地圖中發生觸摸啟動事件時觸發。|
| `wheel`             | 當在地圖中發生滑鼠滾輪事件時觸發。|
| `zoom`              | 在從一個縮放級別到另一個縮放級別的動畫過渡期間反復激發，這是使用者交互或方法的結果。|
| `zoomend`           | 在地圖完成從一個縮放級別到另一個縮放級別的轉換後，由於使用者交互或方法的結果，即激發。|
| `zoomstart`         | 在地圖開始從一個縮放級別過渡到另一個縮放級別之前觸發，這是使用者交互或方法的結果。|


## <a name="next-steps"></a>後續步驟

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務模組](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
