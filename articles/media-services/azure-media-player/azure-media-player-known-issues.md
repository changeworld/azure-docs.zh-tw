---
title: Azure 媒體播放機的已知問題
description: 目前的版本具有下列已知問題。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 05/11/2020
ms.openlocfilehash: a31c089971b7e70e70c5906480deb2b17c197b9f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043612"
---
# <a name="known-issues"></a>已知問題 #

目前的版本具有下列已知問題：

## <a name="azure-media-player"></a>Azure 媒體播放器 ##

- 不正確設定的編碼器可能會導致播放問題
- 時間戳記大於 2 ^ 53 的資料流程可能會發生播放問題。
  - 緩和：使用 90-kHz 影片和 44.1-kHz 音訊刻度
- 不需要使用者互動，即可在行動裝置上自動播放。 由平臺封鎖。
- 搜尋附近的不連續可能會導致播放失敗。
- 下載大型的簡報可能會導致 UI 鎖定。
- 簡報結束後，無法再次自動播放相同的來源。
  - 若要在來源結束後重新執行，必須再次設定來源。
- 空的資訊清單可能會造成 player 的問題。
  - 當啟動即時資料流，但在資訊清單中找不到足夠的區塊時，就會發生此問題。
- 播放位置可能是在 UI seekbar 之外。
- 事件順序在所有 techs 中都不一致。
- 跨 techs 的緩衝屬性不一致。
- nativeControlsForTouch 必須是 false （預設值），以避免「物件不支援屬性或方法 ' setControls '」
- 海報現在必須是絕對 url
- 使用裝置的高對比模式時，UI 中可能會發生次要美觀問題
- 在完整解碼的字串中包含 "%" 或 "+" 的 Url，可能會在設定來源時發生問題

## <a name="ad-insertion"></a>廣告插入 ##

- 當瀏覽器中安裝了 ad 封鎖程式時，Ads 可能會有插入的問題（隨選或即時）
- 行動裝置在播放 ads 時可能會發生問題。
- Azure 媒體播放機目前不支援 Midroll ads。

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- 在即時內容的 DVR 視窗中，當內容完成時，時間軸會繼續成長，直到搜尋到該區域或到達簡報結尾為止。
- Firefox 中已啟用 MSE 的即時簡報有一些問題

- 僅限音訊的資產不會透過 AzureHtml5JS 技術播放。
  - 如果您想要播放沒有音訊的資產，您可以使用[Azure 媒體服務 Explorer 工具](https://aka.ms/amse)插入空白音訊來完成這項操作。
  - 您可以在[這裡](../previous/media-services-advanced-encoding-with-mes.md#silent_audio)找到如何插入無訊息音訊的指示。

## <a name="flash"></a>閃爍 ##

- 由於 Adobe 快取邏輯中的錯誤，AES 內容不會在 Flash 版本30.0.0.134 中播放。 Adobe 已修正此問題，並在30.0.0.154 中發行
- 技術和 HTTP 失敗（例如404網路超時），播放機會比其他 techs 需要更長的時間來進行復原。
- 按一下 [影片] 區域，flashSS tech 不會播放/暫停播放玩家。
- 如果使用者已安裝 Flash，但未授與將其載入網站的許可權，則可能會發生無限旋轉。 這是因為播放機會認為外掛程式已安裝且可供使用，而且它會認為外掛程式正在執行內容。 已傳送 JavaScript 程式碼，但瀏覽器設定已封鎖外掛程式執行，直到使用者接受允許外掛程式的提示。 這可能會發生在所有瀏覽器中。  

## <a name="silverlight"></a>Silverlight ##

- 遺漏的功能
- 技術和 HTTP 失敗（例如404網路超時），播放機會比其他 techs 需要更長的時間來進行復原。
- 使用 Silverlight 的 Mac 播放上的 Safari 和 Firefox 需要明確定義 `"http://` 來源的或 `https://` 。
- 如果此技術缺少 API，通常會傳回 null。
- 如果使用者已安裝 Flash，但未授與將其載入網站的許可權，則可能會發生無限旋轉。 這是因為播放機會認為外掛程式已安裝且可供使用，而且它會認為外掛程式正在執行內容。 已傳送 JavaScript 程式碼，但瀏覽器設定已封鎖外掛程式執行，直到使用者接受允許外掛程式的提示。 這可能會發生在所有瀏覽器中。  

## <a name="native-html5"></a>原生 HTML5 ##

- Html5 tech 只會傳送第一個設定來源的 canplaythrough 事件。
- 此技術僅支援瀏覽器已實作為目標。  某些功能可能會在此技術中遺失。  
- 如果此技術缺少 API，通常會傳回 null。
- 此技術上有字幕和子字幕的問題。 這些人員不一定可以在本技術上或無法使用或查看。
- HLS/Html5 技術案例中的頻寬有限會導致播放音訊，而不需要影片。

### <a name="microsoft"></a>Microsoft ###

- IE8 播放目前無法運作，因為 ECMAScript 5 不相容
- 在 IE 和某些 Edge 版本中，無法輸入全螢幕，方法是將滑鼠定位至按鈕，然後選取或使用 F/F 熱鍵。

## <a name="google"></a>Google ##

- 同一個資訊清單中的多個編碼設定檔在 Chrome 中有一些播放問題，因此不建議您這麼做。
- Chrome 無法播放具有 AzureHtml5JS 的 AAC。 請遵循[bug 追蹤](https://bugs.chromium.org/p/chromium/issues/detail?id=534301)程式的詳細資料。
- 從 Chrome v58，widevine 內容必須透過 HTTPs://通訊協定來載入/播放，否則播放將會失敗。

## <a name="mozilla"></a>Mozilla ##

- 使用 AzureHtml5JS 時，音訊串流交換器需要音訊串流具有相同的編解碼器私用資料。 Firefox 平臺需要此。

## <a name="apple"></a>Apple ##

- Mac 上的 Safari 通常會啟用省電模式，其設定為「停止外掛程式以節省電源」，這會在您認為外掛程式不適合用戶使用時封鎖 Flash 和 Silverlight 之類的外掛程式。 此區塊不會封鎖外掛程式存在的唯一功能。 假設有預設 techOrder，這可能會在嘗試播放時造成問題
  - 緩和措施1：如果影片播放機是「front 和 center」（從檔左上角開始，在 3000 x 3000 圖元界限內），它應該還是會播放。
  - 緩和措施2：將 Safari 的 techOrder 變更為 ["azureHtml5JS"，"html5"]。 這項緩和措施意味著不取得 FlashSS 技術中提供的所有功能。
- 透過 Silverlight 的 PlayReady 內容可能會在 Safari 中播放問題。
- AES 和受限制的權杖內容不會使用 iOS 和舊版 Android 裝置播放。
  - 為了達成此案例，您必須將 proxy 新增至您的服務。
- IOS 手機的預設面板會透過顯示。
- iPhone 播放一律會在原生播放機的全螢幕中進行。
  - 功能（包括字幕）可能不會保存在這個非內嵌播放中。
- 當即時簡報結束時，iOS 裝置將不會正確地結束簡報。
- iOS 不允許 DVR 功能。
- iOS 音訊串流交換器只能透過 iOS 原生播放機 UI 來完成，而且需要音訊資料流程具有相同的編解碼器私用資料
- 較舊版本的 Safari 可能會在播放即時資料流時發生問題。

## <a name="older-android"></a>較舊的 Android ##

- AES 和受限制的權杖內容不會使用 iOS 和舊版 Android 裝置播放。
  - 為了達成此案例，您必須將 proxy 新增至您的服務。

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)
