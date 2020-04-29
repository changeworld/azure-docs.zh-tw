---
title: 使用 Azure 地圖服務建立可存取的對應應用程式 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure 對應來建立具有協助工具功能的應用程式。
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 5fa9e159fa0ac76ce8c585936059fb7f3151c7c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79473314"
---
# <a name="building-an-accessible-application"></a>建置無障礙應用程式

有20% 的網際網路使用者需要可存取的 web 應用程式。 因此，請務必確定您的應用程式已設計成可讓任何使用者輕鬆地使用它。 您不需要將協助工具視為一組要完成的工作，而是將其視為整體使用者體驗的一部分。 您的應用程式更容易存取，使用它的人數就愈多。 

至於類似地圖的豐富互動式內容，以下是一些常見的協助工具考慮：
- 針對看不到 web 應用程式的使用者，支援螢幕讀取器。
- 有多種方法可以與 web 應用程式互動和流覽，例如滑鼠、觸控和鍵盤。
- 確保色彩對比，讓色彩不會混合在一起，並且變得難以區分。 

Azure 地圖服務的 Web SDK 已預先建立，其中包含許多協助工具功能，例如：
- 當地圖移動時以及使用者著重于控制項或快顯時的螢幕閱讀程式說明。
- 滑鼠、觸控和鍵盤支援。
- 道路地圖樣式中可存取的色彩對比支援。

您可以在[這裡](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/)找到所有 Microsoft 產品的完整協助工具一致性詳細資料。 搜尋「Azure 地圖服務 web」以尋找專為 Azure 地圖服務 Web SDK 所提供的檔。 

## <a name="navigating-the-map"></a>導覽地圖

有數種不同的方式可以縮放、移動流覽、旋轉和音調。 以下詳細說明導覽地圖的各種不同方式。

**縮放地圖**

- 使用滑鼠按兩下地圖以放大一個層級。
- 使用滑鼠滾動滾輪來縮放地圖。
- 使用觸控式螢幕，以兩個手指接觸地圖並縮小，以放大或散佈手指來放大。
- 使用觸控式螢幕，按兩下地圖以放大一個層級。
- 以地圖為焦點，使用加號（`+`）或等號（`=`）來放大一個層級。
- 以地圖為焦點，使用減號、連字號（`-`）或底線（`_`）縮小一層。
- 使用 [縮放] 控制項搭配滑鼠、觸控或鍵盤索引標籤/enter 鍵。
- 按住`Shift`按鈕，並在地圖上按下滑鼠左鍵，然後拖曳來繪製一個區域，以將地圖縮放至其中。

**平移地圖**

- 使用滑鼠，按下地圖上的滑鼠左鍵，並以任何方向拖曳。
- 使用觸控式螢幕，觸控地圖並以任何方向拖曳。
- 以地圖為焦點時，使用方向鍵來移動地圖。

**旋轉地圖**

- 使用滑鼠，在地圖上按下滑鼠右鍵，然後向左或向右拖曳。 
- 使用觸控式螢幕，以兩個手指和旋轉來觸控地圖。
- 在地圖專注的情況下，使用 shift 鍵和向左鍵或向右鍵鍵。
- 使用 [旋轉] 控制項搭配滑鼠、觸控或鍵盤索引標籤/enter 鍵。

**音調地圖**

- 使用滑鼠，在地圖上按下滑鼠右鍵，然後向上或向下拖曳。 
- 使用觸控式螢幕，以兩個手指接觸地圖，並將其上下拖曳。
- 在地圖專注的情況下，使用 shift 鍵加上向上或向下鍵。 
- 使用 [音調] 控制項搭配滑鼠、觸控或鍵盤索引標籤/enter 鍵。

## <a name="change-the-map-style"></a>變更地圖樣式

並非所有開發人員都想要在其應用程式中提供所有可能的地圖樣式。 如果開發人員顯示地圖的 [樣式選擇器] 控制項，則使用者可以使用滑鼠、觸控或鍵盤（使用 tab 或 enter 鍵）來變更地圖樣式。 開發人員可以指定他們想要在地圖樣式選擇器控制項中使用的地圖樣式。 此外，開發人員也可以透過程式設計方式設定和變更地圖樣式。

**使用高對比**

- 載入地圖控制項時，它會檢查是否已啟用高對比，而且瀏覽器是否支援。
- 地圖控制項不會監視裝置的高對比模式。 如果裝置模式變更，則對應將不會。 因此，使用者將需要重新整理頁面來重載對應。
- 當偵測到高對比時，地圖樣式會自動切換為高對比，而所有內建控制項都會使用高對比樣式。 例如，ZoomControl、PitchControl、CompassControl、StyleControl 和其他內建控制項都會使用高對比樣式。
- 高對比、淺色和深色有兩種類型。 如果地圖控制項可以偵測到高對比的類型，則對應的行為會相應調整。 若為 light，則會載入 grayscale_light 地圖樣式。 如果無法偵測到類型或其為深色，則會載入 high_contrast_dark 樣式。
- 如果要建立自訂控制項，瞭解內建控制項是否使用高對比樣式會很有説明。 開發人員可以在要檢查的地圖容器 div 上加入 css 類別。 要加入的 css 類別為`high-contrast-dark`和。 `high-contrast-light` 若要使用 JavaScript 進行檢查，請使用：

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

或者，使用：

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>鍵盤快速鍵

地圖中有多個內建的鍵盤快速鍵，可讓您更輕鬆地使用對應。 當地圖具有焦點時，這些鍵盤快速鍵會正常執行。

| Key      | 動作                            |
|----------|-----------------------------------|
| `Tab` | 流覽地圖中的各個控制項和快顯功能表。 |
| `ESC` | 將焦點從地圖中的任何元素移至最上層的地圖元素。 |
| `Ctrl` + `Shift` + `D` | 切換畫面閱讀程式詳細層級。  |
| 向左鍵 | 將地圖向左平移100圖元 |
| 向右鍵 | 將地圖向右平移100圖元 |
| 向下鍵 | 將地圖向下平移100圖元 |
| 向上鍵 | 將地圖向上平移100圖元 |
| `Shift`+ 向上鍵 | 將地圖音調增加10度 |
| `Shift`+ 向下鍵 | 將地圖音調減少10度 |
| `Shift`+ 向右鍵 | 順時針旋轉15度的地圖 |
| `Shift`+ 向左鍵 | 逆時針旋轉15度 |
| 加正負號`+`（） <sup>*</sup>或等號`=`（） | 放大 |
| 負號、連字號`-`（）或<sup>*</sup>底線（`_`） | 縮小 | 
| `Shift`+ 滑鼠拖曳至地圖上繪製區域 | 放大區域 |

<sup>*</sup>這些主要快捷方式通常會在鍵盤上共用相同的按鍵。 這些快速鍵已加入以改善使用者體驗。 如果使用者使用 shift 鍵，或不是針對這些快捷方式，也不會有任何影響。

## <a name="screen-reader-support"></a>螢幕閱讀程式支援

使用者可以使用鍵盤瀏覽地圖。 如果螢幕助讀程式正在執行，地圖將通知使用者其狀態的變更。 例如，使用者在移動瀏覽或縮放地圖時，會收到地圖變更的通知。 根據預設，地圖會提供簡化的描述，以排除地圖中心的縮放層級和座標。 `Ctrl`  +  `Shift`  +  `D`使用者可以使用鍵盤簡短剪下來切換這些描述的詳細層級。

放在底圖上的任何其他資訊都應該具有適用於螢幕助讀程式使用者的對應文字資訊。 請務必在適當的情況下新增[可存取的豐富網際網路應用程式（ARIA）](https://www.w3.org/WAI/standards-guidelines/aria/)、alt 和 title 屬性。 

## <a name="make-popups-keyboard-accessible"></a>使快顯功能表可供存取

標記或符號通常用來代表地圖上的位置。 當使用者與標記互動時，此位置的其他資訊通常會顯示在快顯視窗中。 在大部分的應用程式中，當使用者按一下或按下標記時，會出現快顯視窗。 不過，按一下和按下需要使用者分別使用滑鼠和觸控螢幕。 最佳做法是在使用鍵盤時讓快顯視窗可供存取。 這項功能可以藉由建立每個資料點的快顯，並將其新增至對應來達到。 

下列範例會使用符號圖層，在地圖上載入感興趣的點，並針對每個感興趣的點新增一個快顯至地圖。 每個快顯的參考會儲存在每個資料點的屬性中。 您也可以針對標記來抓取它，例如按一下標記時。 當焦點在地圖上時，按 tab 鍵可讓使用者逐步執行地圖上的每個快顯。

<br/>

<iframe height='500' scrolling='no' title='製作無障礙應用程式' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務所提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>製作無障礙應用程式</a> ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> )。 </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>其他協助工具秘訣

以下提供一些額外的秘訣，讓您的 web 對應應用程式更容易存取。

- 如果在地圖上顯示許多互動點資料，請考慮減少雜亂並使用群集。 
- 確保文字/符號和背景色彩之間的色彩對比比例為4.5：1或更多。
- 讓您的螢幕閱讀程式（ARIA、alt 和 title 屬性）簡短、描述性和有意義。 避免不必要的術語和縮寫。
- 嘗試將傳送給螢幕讀取器的訊息優化，以提供簡短的意義資訊，方便使用者進行摘要。 例如，如果您想要以較高的頻率更新螢幕讀取器，例如當地圖移動時，請考慮執行下列各點：
    - 等到地圖完成移動以更新螢幕讀取器。
    - 每隔幾秒就會將更新節流處理一次。 
    - 以邏輯方式將訊息結合在一起。 
- 避免使用色彩作為傳達資訊的唯一方式。 使用文字、圖示或模式來補充或取代色彩。 一些考量事項如下：
    - 如果使用反升圖層來顯示資料點之間的相對值，請考慮調整每個反升的半徑、為氣泡著色，或兩者。 
    - 請考慮針對不同的計量類別使用具有不同圖示的符號圖層，例如三角形、星星和正方形。 符號圖層也支援調整圖示的大小。 也可以顯示文字標籤。
    - 如果顯示行資料，則寬度可以用來代表權數或大小。 虛線陣列模式可以用來代表不同的行類別。 符號圖層可以與線條組合使用，以沿著線條迭迭圖示。 使用箭號圖示有助於顯示線條的流程或方向。
    - 如果顯示多邊形資料，則可以使用模式（例如條紋）做為色彩的替代方式。 
- 視覺受損的使用者無法存取某些視覺效果，例如熱度圖、磚圖層和影像圖層。 一些考量事項如下：
    - 讓螢幕閱讀程式描述圖層新增至地圖時所顯示的內容。 例如，如果顯示氣象雷達圖圖層，則會讓螢幕閱讀程式指出地圖上的 [氣象雷達圖資料已重迭]。
- 限制需要滑鼠停留的功能數量。 使用鍵盤或觸控裝置來與您的應用程式互動的使用者將無法存取這些功能。 請注意，具有動態內容的暫留樣式（例如可按的圖示、連結和按鈕）仍是很好的作法。
- 嘗試使用鍵盤流覽您的應用程式。 請確定索引標籤順序為 [邏輯]。
- 如果要建立鍵盤快速鍵，請嘗試將它限制為兩個或更少的索引鍵。 

## <a name="next-steps"></a>後續步驟

深入瞭解 Web SDK 模組中的協助工具。

> [!div class="nextstepaction"]
> [繪圖工具協助工具](drawing-tools-interactions-keyboard-shortcuts.md)

瞭解如何使用 Microsoft Learn 開發可存取的應用程式：

> [!div class="nextstepaction"]
> [作用中的協助工具數位徽章學習路徑](https://ready.azurewebsites.net/learning/track/2940)

請查看這些實用的協助工具工具：
> [!div class="nextstepaction"]
> [開發可存取的應用程式](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [WAI-ARIA 總覽](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Web 協助工具評估工具（WAVE）](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [WebAim 色彩對比檢查](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [無咖啡願景模擬器](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
