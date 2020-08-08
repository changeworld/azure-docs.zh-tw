---
title: 使用 Azure 地圖服務建立無障礙地圖應用程式 | Microsoft Azure 地圖服務
description: 深入瞭解 Azure 地圖服務中的協助工具考慮。 查看哪些功能可用來讓地圖應用程式可供存取，以及查看協助工具提示。
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: ec88437a0fad3a6bd94a67a5ef5c75b3e506f9e8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88006208"
---
# <a name="building-an-accessible-application"></a>建置無障礙應用程式

超過 20% 的網際網路使用者需要無障礙的 Web 應用程式。 因此，請務必確定已將您的應用程式設計成可讓任何使用者輕鬆地加以使用。 您不需要將協助工具視為一組要完成的工作，而是將其視為整體使用者體驗的一部分。 您的應用程式越容易存取，使用的人數就越多。 

若應用程式會提供地圖之類的豐富互動式內容，則以下是一些常見的協助工具考慮事項：
- 為對於看到 Web 應用程式有困難的使用者提供螢幕助讀程式的支援。
- 有多種方法可以與 Web 應用程式進行互動和瀏覽，例如滑鼠、觸控和鍵盤。
- 確保色彩對比，讓色彩不會混合在一起，並且變得難以區分。 

Azure 地圖服務 Web SDK 預先建置了許多協助工具功能，例如：
- 螢幕助讀程式會在地圖移動時，以及當使用者的焦點位於控制項或快顯時提供描述。
- 滑鼠、觸控和鍵盤支援。
- 道路地圖樣式中支援可存取的色彩對比。
- 支援高對比。

您可以在[這裡](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) \(英文\) 找到所有 Microsoft 產品的完整協助工具符合性詳細資料。 搜尋「Azure 地圖服務 Web」，以尋找專為 Azure 地圖服務 Web SDK 提供的文件。 

## <a name="navigating-the-map"></a>瀏覽地圖

有數種不同方式可用來對地圖進行縮放、移動瀏覽、旋轉及調整傾斜度。 以下將詳細說明瀏覽地圖的各種不同方式。

**縮放地圖**

- 使用滑鼠，按兩下地圖即可放大一個等級。
- 使用滑鼠，捲動滾輪來縮放地圖。
- 使用觸控式螢幕，以兩支手指接觸地圖，捏合即可縮小，張開手指即可放大。
- 使用觸控式螢幕，點兩下地圖即可放大一個等級。
- 將焦點放在地圖上，使用加號 (`+`) 或等號 (`=`) 來放大一個等級。
- 將焦點放在地圖上，使用減號 (`-`) 或底線 (`_`) 來縮小一個等級。
- 使用縮放控制項並搭配滑鼠、觸控或鍵盤 Tab/Enter 鍵。
- 按住 `Shift` 按鈕並在地圖上按下滑鼠左鍵，然後拖曳來繪製一個區域，以將地圖縮放至其中。
- 使用一些多點觸控板，將兩個手指上下拖曳以縮小，或向下縮放。

**移動瀏覽地圖**

- 使用滑鼠、在地圖上按下滑鼠左鍵，然後以任何方向拖曳。
- 使用觸控式螢幕，觸控地圖，然後以任何方向拖曳。
- 將焦點放在地圖上，使用方向鍵來移動地圖。

**旋轉地圖**

- 使用滑鼠、在地圖上按下滑鼠右鍵，然後向左或向右拖曳。 
- 使用觸控式螢幕，以兩支手指觸控地圖並旋轉。
- 將焦點放在地圖上，使用 Shift 鍵和向左鍵或向右鍵。
- 使用旋轉控制項並搭配滑鼠、觸控或鍵盤 Tab/Enter 鍵。

**調整地圖傾斜度**

- 使用滑鼠、在地圖上按下滑鼠右鍵，然後向上或向下拖曳。 
- 使用觸控式螢幕，以兩支手指觸控地圖，然後將其一起向上或向下拖曳。
- 將焦點放在地圖上，使用 Shift 鍵加上向上鍵或向下鍵。 
- 使用調整傾斜度控制項並搭配滑鼠、觸控或鍵盤 Tab/Enter 鍵。

## <a name="change-the-map-style"></a>變更地圖樣式

並非所有開發人員都想要在其應用程式中提供所有可能的地圖樣式。 如果開發人員顯示地圖的樣式選擇器控制項，則使用者可以使用滑鼠、觸控或鍵盤 (搭配 Tab 或 Enter 鍵) 來變更地圖樣式。 開發人員可以指定其想要在地圖樣式選擇器控制項中使用的地圖樣式。 此外，開發人員也可以透過程式設計方式來設定和變更地圖樣式。

**使用高對比**

- 載入地圖控制項時，其會檢查以查看是否已啟用高對比，且瀏覽器是否支援。
- 地圖控制項不會監視裝置的高對比模式。 如果裝置模式變更，地圖將不會變更。 因此，使用者將需要重新整理頁面，以便重新載入地圖。
- 偵測到高對比時，地圖樣式將自動切換為高對比，而所有內建控制項都將使用高對比樣式。 例如，ZoomControl、PitchControl、CompassControl、StyleControl 和其他內建控制項都將使用高對比樣式。
- 高對比有兩種類型：淺色和深色。 如果地圖控制項可以偵測到高對比的類型，則地圖的行為將據以調整。 若為淺色，即會載入 grayscale_light 地圖樣式。 如果無法偵測到類型或其為深色，則會載入 high_contrast_dark 樣式。
- 如果要建立自訂控制項，那麼，知道內建控制項是否正在使用高對比樣式就很實用。 開發人員可以在要檢查的地圖容器 div 上新增 CSS 類別。 要新增的 CSS 類別是 `high-contrast-dark` 和 `high-contrast-light`。 若要使用 JavaScript 進行檢查，請使用：

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

或者，使用：

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>鍵盤快速鍵

地圖中有多個內建的鍵盤快速鍵，讓您能夠更輕鬆地使用地圖。 當地圖具有焦點時，這些鍵盤快速鍵就會運作。

| Key      | 動作                            |
|----------|-----------------------------------|
| `Tab` | 瀏覽地圖中的各個控制項和快顯。 |
| `ESC` | 將焦點從地圖中的任意元素移至最上層地圖元素。 |
| `Ctrl` + `Shift` + `D` | 切換螢幕助讀程式詳細等級。  |
| 向左鍵 | 將地圖向左移動瀏覽 100 個像素 |
| 向右鍵 | 將地圖向右移動瀏覽 100 個像素 |
| 向下鍵 | 將地圖向下移動瀏覽 100 個像素 |
| 向上鍵 | 將地圖向上移動瀏覽 100 個像素 |
| `Shift` + 向上鍵 | 將地圖傾斜度調高 10 度 |
| `Shift` + 向下鍵 | 將地圖傾斜度降低 10 度 |
| `Shift` + 向右鍵 | 將地圖順時針旋轉 15 度 |
| `Shift` + 向左鍵 | 將地圖逆時針旋轉 15 度 |
| 加號 (`+`) 或 <sup>*</sup>等號 (`=`) | 放大 |
| 減號、連字號 (`-`) 或 <sup>*</sup>底線 (`_`) | 縮小 | 
| `Shift` + 在地圖上拖曳滑鼠以繪製區域 | 放大區域 |

<sup>*</sup> 這些按鍵快速鍵通常會共用鍵盤上的相同按鍵。 這些快速鍵已新增來改善使用者體驗。 如果使用者使用 shift 鍵，或不是針對這些快捷方式，也不會有任何影響。

## <a name="screen-reader-support"></a>螢幕助讀程式支援

使用者可以使用鍵盤瀏覽地圖。 如果螢幕助讀程式正在執行，地圖將通知使用者其狀態的變更。 例如，使用者在移動瀏覽或縮放地圖時，會收到地圖變更的通知。 根據預設，地圖會提供簡化的描述，以排除地圖中心的縮放等級和座標。 使用者可以使用鍵盤快速鍵 `Ctrl` + `Shift` + `D`，來切換這些描述的詳細等級。

放在底圖上的任何其他資訊都應該具有適用於螢幕助讀程式使用者的對應文字資訊。 請務必在適當的情況下，新增[無障礙豐富網際網路應用程式 (ARIA)](https://www.w3.org/WAI/standards-guidelines/aria/) \(英文\)、alt 與 title 屬性。 

## <a name="make-popups-keyboard-accessible"></a>讓快顯鍵盤可供存取

通常會使用標記或符號來代表地圖上的位置。 當使用者與標記互動時，關於此位置的其他資訊通常會顯示在快顯中。 在大部分的應用程式中，當使用者按一下或點選標記時，即會出現快顯。 不過，按一下和點選需要使用者分別使用滑鼠和觸控式螢幕。 最佳做法是在使用鍵盤時讓快顯可供存取。 此功能可以藉由建立每個資料點的快顯，並將其新增到地圖來實現。 

下列範例會使用符號圖層，在地圖上載入感興趣的點，並針對每個興趣點，在地圖上新增一個快顯。 每個快顯的參考都會儲存於每個資料點的屬性中。 您也可以針對某個標記進行擷取，例如，按一下標記時。 當焦點在地圖上時，按 Tab 鍵可讓使用者逐步瀏覽地圖上的每個快顯。

<br/>

<iframe height='500' scrolling='no' title='製作無障礙應用程式' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務所提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>製作無障礙應用程式</a> ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> )。 </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>其他協助工具祕訣

以下提供一些額外的祕訣，讓您的 Web 地圖應用程式更容易存取。

- 如果地圖上顯示許多互動式點資料，請考慮降低雜亂情況並使用群集。 
- 確保文字/符號和背景色彩之間的色彩對比率為 4.5:1 或更多。
- 讓您的螢幕助讀程式 (ARIA、alt 和 title 屬性) 訊息保持簡短、具描述性且有意義。 避免不必要的術語和縮寫。
- 嘗試將傳送給螢幕助讀程式的訊息最佳化，以提供簡短且有意義的資訊，方便使用者進行摘要。 例如，若要以較高的頻率更新螢幕助讀程式 (例如，當地圖移動時)，請考慮執行下列各點：
    - 等待地圖完成移動以更新螢幕助讀程式。
    - 每隔幾秒就將更新節流處理一次。 
    - 以邏輯方式將訊息合併在一起。 
- 避免使用色彩作為傳達資訊的唯一方法。 使用文字、圖示或模式來補充或取代色彩。 一些考量事項如下：
    - 如果使用泡泡圖層來顯示資料點之間的相對值，請考慮調整每個泡泡的半徑、為泡泡著色，或兩者。 
    - 考慮針對不同的計量類別使用具有不同圖示的符號圖層，例如三角形、星星和正方形。 符號圖層也支援調整圖示的大小。 同時也可以顯示文字標籤。
    - 如果顯示線條資料，就可以使用寬度來代表權數或大小。 虛線陣列模式可以用來代表不同類別的線條。 符號圖層可以與線條組合使用，以便沿著線條重疊圖示。 使用箭號圖示，有助於顯示該線條的流程或方向。
    - 如果顯示多邊形資料，則可使用模式 (例如條紋) 作為色彩的替代項目。 
- 視障使用者無法存取某些視覺效果，例如，熱度圖、圖格圖層及影像圖層。 一些考量事項如下：
    - 讓螢幕助讀程式描述圖層在新增至地圖時所顯示的內容。 例如，如果顯示了氣象雷達圖層，則會讓螢幕助讀程式說出「氣象雷達資料已覆蓋在地圖上」。
- 限制需要滑鼠暫留的功能數量。 使用鍵盤或觸控式裝置來與您應用程式互動的使用者將無法存取這些功能。 請注意，具有互動式內容的暫留樣式 (例如，可按式圖示、連結和按鈕) 仍是很好的做法。
- 嘗試使用鍵盤來瀏覽您的應用程式。 請確認定位順序是合乎邏輯的。
- 如果正在建立鍵盤快速鍵，請嘗試將其限制為兩個或更少的按鍵。 

## <a name="next-steps"></a>後續步驟

深入了解 Web SDK 模組中的協助工具。

> [!div class="nextstepaction"]
> [繪圖工具協助工具](drawing-tools-interactions-keyboard-shortcuts.md)

了解如何使用 Microsoft Learn 來開發無障礙應用程式：

> [!div class="nextstepaction"]
> [協助工具實務的數位徽章學習路徑](https://ready.azurewebsites.net/learning/track/2940) \(英文\)

查看這些實用的協助工具：
> [!div class="nextstepaction"]
> [開發無障礙應用程式](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [WAI-ARIA 概觀](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Web 協助工具評估工具 (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [WebAim 色彩對比檢查程式](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [NoCoffee 視覺模擬器](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
