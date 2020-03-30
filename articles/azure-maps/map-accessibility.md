---
title: 使用 Azure 地圖創建可訪問的地圖應用程式 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Microsoft Azure 映射構建具有協助工具的應用程式。
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 5fa9e159fa0ac76ce8c585936059fb7f3151c7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473314"
---
# <a name="building-an-accessible-application"></a>建置無障礙應用程式

超過 20% 的互聯網使用者需要可訪問的 Web 應用程式。 因此，請務必確保應用程式的設計使任何使用者都可以輕鬆使用它。 與其將協助工具視為要完成的任務集，不如將其視為整體使用者體驗的一部分。 應用程式越可訪問，可以使用該應用程式的人就越多。 

當涉及到豐富的互動式內容（如地圖）時，一些常見的協助工具注意事項包括：
- 為難以查看 Web 應用程式的使用者支援螢幕閱讀器。
- 具有多種方法與 Web 應用程式（如滑鼠、觸摸和鍵盤）進行交互和導航。
- 確保顏色對比度使顏色不會混合在一起，並且變得難以區分。 

Azure 地圖 Web SDK 預構建了許多協助工具，例如：
- 當地圖移動以及使用者將焦點放在控制項或快顯視窗上時，螢幕閱讀器說明。
- 滑鼠、觸摸和鍵盤支援。
- 路線圖樣式中可訪問的顏色對比度支援。

所有 Microsoft 產品的完整協助工具符合性詳細資訊可[在此處](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/)找到。 搜索"Azure 地圖 Web"以查找專門針對 Azure 地圖 Web SDK 的文檔。 

## <a name="navigating-the-map"></a>導航地圖

地圖可以縮放、平移、旋轉和傾斜幾種不同的方式。 下面詳細介紹了導航地圖的所有不同方法。

**縮放地圖**

- 使用滑鼠，按兩下地圖以放大一個級別。
- 使用滑鼠滾動滾輪以縮放地圖。
- 使用觸控式螢幕，用兩根手指觸摸地圖，然後捏在一起縮小或將手指分開以放大。
- 使用觸控式螢幕，按兩下地圖可放大一個級別。
- 將地圖聚焦後，使用 Plus 符號`+`（ ） 或`=`等於符號 （ ） 放大一個級別。
- 將地圖聚焦後，使用減號、連字號 （）`-`或下`_`劃線 （ ） 縮小一個級別。
- 使用帶有滑鼠、觸摸或鍵盤選項卡/輸入鍵的縮放控制項。
- 按住按鈕`Shift`，按下地圖上的滑鼠左鍵，然後拖動以繪製區域以放大地圖。

**平移地圖**

- 使用滑鼠，按下地圖上的滑鼠左鍵，然後朝任意方向拖動。
- 使用觸控式螢幕，觸摸地圖並朝任意方向拖動。
- 將地圖聚焦後，使用方向鍵移動地圖。

**旋轉地圖**

- 使用滑鼠，按下地圖上的滑鼠右鍵，向左或向右拖動。 
- 使用觸控式螢幕，用兩根手指觸摸地圖並旋轉。
- 將地圖聚焦時，請使用移位鍵和左箭頭或右方向鍵。
- 使用帶有滑鼠、觸摸或鍵盤選項卡/輸入鍵的旋轉控制項。

**間距地圖**

- 使用滑鼠，按下地圖上的滑鼠右鍵，然後向上或向下拖動。 
- 使用觸控式螢幕，用兩根手指觸摸地圖，然後一起向上或向下拖動。
- 將地圖聚焦時，請使用移位鍵加上向上或向下方向鍵。 
- 使用帶有滑鼠、觸摸或鍵盤選項卡/輸入鍵的間距控制項。

## <a name="change-the-map-style"></a>更改地圖樣式

並非所有開發人員都希望所有可能的地圖樣式在其應用程式中可用。 如果開發人員顯示地圖的樣式選擇器控制項，則使用者可以使用滑鼠、觸摸或帶有選項卡的鍵盤更改地圖樣式或輸入鍵。 開發人員可以指定他們希望在地圖樣式選擇器控制項中提供的地圖樣式。 此外，開發人員可以以程式設計方式設置和更改地圖樣式。

**使用高對比**

- 載入地圖控制項時，它會檢查是否啟用了高對比，並且瀏覽器支援它。
- 地圖控制項不監視設備的高對比模式。 如果設備模式發生更改，則映射不會更改。 因此，使用者需要通過刷新頁面來重新載入地圖。
- 檢測到高對比時，地圖樣式將自動切換到高對比，並且所有內置控制項都將使用高對比樣式。 例如，縮放控制、間距控制、指南針控制、樣式控制和其他內置控制項將使用高對比樣式。
- 有兩種高對比，即光和暗。 如果地圖控制項可以檢測到高對比類型，則地圖的行為將相應地調整。 如果光線，則將載入grayscale_light地圖樣式。 如果無法檢測到類型或為深色，則將載入high_contrast_dark樣式。
- 如果創建自訂控制項，則瞭解內置控制項是否使用高對比樣式非常有用。 開發人員可以在地圖容器 div 上添加 css 類來檢查。 要添加的 css 類是`high-contrast-dark``high-contrast-light`和 。 要檢查使用 JavaScript，請使用：

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

或者，使用：

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>鍵盤快速鍵

地圖內置了許多鍵盤快速鍵，便於使用地圖。 當地圖具有焦點時，這些鍵盤快速鍵工作。

| Key      | 動作                            |
|----------|-----------------------------------|
| `Tab` | 在地圖中的控制項和快顯視窗中導航。 |
| `ESC` | 將焦點從地圖中的任何元素移動到頂級地圖元素。 |
| `Ctrl` + `Shift` + `D` | 切換畫面閱讀器詳細資訊級別。  |
| 向左鍵 | 平移地圖左側 100 圖元 |
| 向右鍵 | 向右平移地圖 100 圖元 |
| 向下鍵 | 將地圖向下平移 100 圖元 |
| 向上鍵 | 將地圖向上平移 100 圖元 |
| `Shift`• 向上箭號 | 將地圖間距提高 10 度 |
| `Shift`• 向下箭頭 | 將地圖間距降低 10 度 |
| `Shift`• 右箭頭 | 順時針旋轉地圖 15 度 |
| `Shift`• 左箭頭 | 逆時針旋轉地圖 15 度 |
| 加號`+`（ <sup>*</sup>） 或等於`=`符號 （ ） | 放大 |
| 減號、連字號`-`（ <sup>*</sup>）`_`或底線 （ ） | 縮小 | 
| `Shift`• 滑鼠在地圖上拖動以繪製區域 | 放大縮小字體功能 放大縮小字體功能 |

<sup>*</sup>這些鍵快速鍵通常在鍵盤上共用同一個鍵。 添加了這些快捷方式是為了改善使用者體驗。 使用者是否對這些快捷方式使用移位鍵也無關緊要。

## <a name="screen-reader-support"></a>螢幕閱讀器支援

使用者可以使用鍵盤瀏覽地圖。 如果螢幕助讀程式正在執行，地圖將通知使用者其狀態的變更。 例如，使用者在移動瀏覽或縮放地圖時，會收到地圖變更的通知。 預設情況下，地圖提供排除地圖中心的縮放級別和座標的簡化說明。 `Ctrl`  +  `Shift`  +  `D`使用者可以使用鍵盤短截頭切換這些描述的細節級別。

放在底圖上的任何其他資訊都應該具有適用於螢幕助讀程式使用者的對應文字資訊。 請務必在適當的情況下添加[可訪問的富 Internet 應用程式 （ARIA）](https://www.w3.org/WAI/standards-guidelines/aria/)、alt 和標題屬性。 

## <a name="make-popups-keyboard-accessible"></a>使快顯視窗鍵盤可訪問

標記或符號通常用於表示地圖上的位置。 當使用者與標記交互時，有關該位置的其他資訊通常顯示在快顯視窗中。 在大多數應用程式中，當使用者按一下或點擊標記時會出現快顯視窗。 但是，按一下和點擊需要使用者分別使用滑鼠和觸控式螢幕。 一個好的做法是使快顯視窗在使用鍵盤時可訪問。 此功能可以通過為每個資料點創建一個快顯視窗並將其添加到地圖來實現。 

下面的示例使用符號圖層載入地圖上的興趣點，並為每個興趣點的地圖添加一個快顯視窗。 每個快顯視窗的引用存儲在每個資料點的屬性中。 也可以檢索標記，例如按一下標記時。 聚焦在地圖上時，按下選項卡鍵將允許使用者單一步驟地圖上的每個快顯視窗。

<br/>

<iframe height='500' scrolling='no' title='製作無障礙應用程式' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務所提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>製作無障礙應用程式</a> ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> )。 </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>其他協助工具提示

下面是一些額外的提示，使 Web 映射應用程式更易於訪問。

- 如果在地圖上顯示許多互動式點資料，請考慮減少雜亂並使用聚類。 
- 確保文本/符號和背景顏色之間的顏色對比度為 4.5：1 或更多。
- 使螢幕閱讀器（ARIA、alt 和標題屬性）消息保持簡短、描述性和有意義性。 避免不必要的行話和首字母縮略詞。
- 嘗試優化發送給螢幕閱讀器的郵件，以提供使用者易於消化的短而有意義的資訊。 例如，如果要以高頻率更新螢幕閱讀器（例如，當地圖移動時），請考慮執行以下幾點：
    - 等待地圖完成移動以更新螢幕閱讀器。
    - 將更新限制到每幾秒鐘一次。 
    - 以邏輯方式將消息組合在一起。 
- 避免使用色彩作為傳達資訊的唯一方式。 使用文本、圖示或圖案來補充或替換顏色。 一些考量事項如下：
    - 如果使用氣泡圖層來顯示資料點之間的相對值，請考慮縮放每個氣泡的半徑、為氣泡著色或兩者著色。 
    - 請考慮對不同的指標類別（如三角形、星形和正方形）使用具有不同圖示的符號圖層。 符號圖層還支援縮放圖示的大小。 也可以顯示文字標籤。
    - 如果顯示線資料，則寬度可用於表示權重或大小。 短劃線陣列模式可用於表示不同類別的行。 符號圖層可與線結合使用，以覆蓋沿線的圖示。 使用箭頭圖示可用於顯示線條的流或方向。
    - 如果顯示多邊形資料，則模式（如條紋）可用作顏色的替代方法。 
- 某些視覺化效果（如熱圖、切片圖層和圖像圖層）對於視力障礙的使用者是無法訪問的。 一些考量事項如下：
    - 讓螢幕閱讀器描述圖層添加到地圖時顯示的內容。 例如，如果顯示天氣雷達切片圖層，則讓螢幕閱讀器說"天氣雷達資料疊加在地圖上"。
- 限制需要滑鼠懸停的功能量。 使用鍵盤或觸摸設備與應用程式交互的使用者將無法訪問這些功能。 請注意，對於互動式內容（如可按一下的圖示、連結和按鈕），最好採用懸停樣式。
- 請嘗試使用鍵盤導航應用程式。 確保選項卡排序是合乎邏輯的。
- 如果創建鍵盤快速鍵，請嘗試將其限制為兩個或更少的鍵。 

## <a name="next-steps"></a>後續步驟

瞭解 Web SDK 模組中的可訪問性。

> [!div class="nextstepaction"]
> [繪圖工具可訪問性](drawing-tools-interactions-keyboard-shortcuts.md)

瞭解如何使用 Microsoft 學習開發可訪問的應用程式：

> [!div class="nextstepaction"]
> [操作中的協助工具 數位徽章學習路徑](https://ready.azurewebsites.net/learning/track/2940)

查看這些有用的協助工具工具：
> [!div class="nextstepaction"]
> [開發可訪問的應用程式](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [WAI-ARIA 概述](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Web 協助工具評估工具 （WAVE）](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [WebAim 顏色對比檢查器](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [無咖啡視覺模擬器](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
