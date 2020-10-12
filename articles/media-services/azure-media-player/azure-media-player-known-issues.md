---
title: Azure 媒體播放機的已知問題
description: 目前的版本具有下列已知問題。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 05/11/2020
ms.openlocfilehash: a31c089971b7e70e70c5906480deb2b17c197b9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87043612"
---
# <a name="known-issues"></a>已知問題 #

目前的版本具有下列已知問題：

## <a name="azure-media-player"></a>Azure 媒體播放器 ##

- 未正確設定的編碼器可能會在播放時發生問題
- 時間戳記大於 2 ^ 53 的資料流程可能有播放問題。
  - 風險降低：使用 90-kHz 影片和 44.1-kHz 音訊的刻度
- 沒有使用者互動的行動裝置上沒有自動播放。 它被平臺封鎖。
- 搜尋近不連續可能會導致播放失敗。
- 下載大型簡報可能會導致 UI 鎖定。
- 在簡報結束之後，就無法再自動播放相同的來源。
  - 若要在來源結束後重新執行，則必須再次設定來源。
- 空白的資訊清單可能會導致播放玩家發生問題。
  - 當啟動即時資料流，但在資訊清單中找不到足夠的區塊時，就會發生此問題。
- 播放位置可能超出 UI seekbar。
- 事件順序在所有 techs 中都不一致。
- Techs 之間的緩衝屬性不一致。
- nativeControlsForTouch 必須是 false (預設) ，以避免「物件不支援屬性或方法 ' setControls '」
- 海報現在必須是絕對 url
- 使用裝置的高對比模式時，UI 中可能會發生次要美觀問題
- 在完整解碼的字串中包含 "%" 或 "+" 的 Url 可能會在設定來源時發生問題

## <a name="ad-insertion"></a>廣告插入 ##

- 當瀏覽器中安裝了 ad 封鎖程式時，廣告可能會有 (隨選或即時) 插入的問題
- 行動裝置可能會在播放 ads 時發生問題。
- Azure 媒體播放機目前不支援未 Midroll 的廣告。

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- 在 [即時內容] 的 [DVR] 視窗中，當內容完成時，時間軸將會持續成長，直到搜尋至該區域或到達簡報的結尾為止。
- 啟用 MSE 的 Firefox 中的即時簡報有一些問題

- 只有音訊的資產不會透過 AzureHtml5JS 技術播放。
  - 如果您想要播放沒有音訊的資產，您可以使用[Azure 媒體服務 Explorer 工具](https://aka.ms/amse)插入空白音訊來達到此目的
  - 您可以在[這裡](../previous/media-services-advanced-encoding-with-mes.md#silent_audio)找到如何插入無訊息音訊的指示。

## <a name="flash"></a>閃光燈 ##

- 因為 Adobe 的快取邏輯中有錯誤，所以 AES 內容不會在 Flash 版本30.0.0.134 中播放。 Adobe 已修正此問題，並在30.0.0.154 中發行
- 技術和 HTTP 失敗 (例如404網路超時) ，播放玩家比其他 techs 需要更長的時間來復原。
- 按一下具有 flashSS tech 的影片區域不會播放/暫停播放玩家。
- 如果使用者已安裝 Flash 但未提供在網站上載入它的許可權，可能會發生無限的旋轉情形。 這是因為播放程式會認為外掛程式已安裝且可供使用，且會認為外掛程式正在執行內容。 已傳送 JavaScript 程式碼，但瀏覽器設定封鎖了外掛程式執行，直到使用者接受允許該外掛程式的提示。 這可能會發生在所有瀏覽器中。  

## <a name="silverlight"></a>Silverlight ##

- 遺漏的功能
- 技術和 HTTP 失敗 (例如404網路超時) ，播放玩家比其他 techs 需要更長的時間來復原。
- 在 Mac 上使用 Silverlight 播放的 Safari 和 Firefox 需要明確定義 `"http://` 或 `https://` 作為來源。
- 如果此技術缺少 API，通常會傳回 null。
- 如果使用者已安裝 Flash 但未提供在網站上載入它的許可權，可能會發生無限的旋轉情形。 這是因為播放程式會認為外掛程式已安裝且可供使用，且會認為外掛程式正在執行內容。 已傳送 JavaScript 程式碼，但瀏覽器設定封鎖了外掛程式執行，直到使用者接受允許該外掛程式的提示。 這可能會發生在所有瀏覽器中。  

## <a name="native-html5"></a>原生 HTML5 ##

- Html5 tech 只會傳送第一個 set 來源的 canplaythrough 事件。
- 此技術僅支援瀏覽器所執行的工作。  本技術可能遺漏部分功能。  
- 如果此技術缺少 API，通常會傳回 null。
- 此技術上有標題和字幕的問題。 這些技術上的或可能無法使用或查看。
- HLS/Html5 技術案例中的頻寬有限，會導致音訊播放而不需要影片。

### <a name="microsoft"></a>Microsoft ###

- IE8 播放目前無法運作，因為 ECMAScript 5 不相容
- 在 IE 和某些版本的 Edge 中，您無法透過 tab 鍵並選取該按鈕，然後選取或使用 F/f 快速鍵來輸入全螢幕。

## <a name="google"></a>Google ##

- 相同資訊清單中的多個編碼設定檔在 Chrome 中有一些播放問題，因此不建議使用。
- Chrome 無法使用 AzureHtml5JS 播放 AAC。 遵循 [bug 追蹤](https://bugs.chromium.org/p/chromium/issues/detail?id=534301)程式的詳細資料。
- 從 Chrome v58，widevine 內容必須透過 HTTPs://通訊協定載入/播放，否則播放將會失敗。

## <a name="mozilla"></a>Mozilla ##

- 使用 AzureHtml5JS 時，音訊串流切換需要音訊串流具有相同的編解碼器私用資料。 Firefox 平臺需要此。

## <a name="apple"></a>Apple ##

- 根據預設，Mac 上的 Safari 通常會啟用「停止外掛程式以節省電源」設定的「省電」模式，這會封鎖像是 Flash 和 Silverlight 的外掛程式，而不是使用者的偏好。 此區塊不會封鎖外掛程式的存在，只會封鎖功能。 由於預設 techOrder，這可能會在嘗試播放時發生問題
  - 緩和措施1：如果影片播放程式是「前端和中心」 (在檔) 左上角開始的 3000 x 3000 圖元界限內，它應該還是可以播放。
  - 風險降低2：將 Safari 的 techOrder 變更為 ["azureHtml5JS"，"html5"]。 這項緩和措施的含意是，無法取得 FlashSS 技術所提供的所有功能。
- 透過 Silverlight 的 PlayReady 內容在 Safari 中播放時可能會發生問題。
- AES 和受限的權杖內容不會使用 iOS 和較舊的 Android 裝置播放。
  - 為了達成此案例，必須將 proxy 加入至您的服務。
- IOS 電話顯示的預設面板。
- iPhone 播放一律會出現在原生播放機的全螢幕中。
  - 這些功能（包括字幕）可能不會保存在此非內嵌播放中。
- 當實況簡報結束時，iOS 裝置將不會正確地結束簡報。
- iOS 不允許 DVR 功能。
- iOS 音訊串流切換只能透過 iOS 原生播放機 UI 來完成，且需要音訊串流具有相同的編解碼器私用資料
- 較舊版本的 Safari 可能會在播放即時資料流時發生問題。

## <a name="older-android"></a>舊版 Android ##

- AES 和受限的權杖內容不會使用 iOS 和較舊的 Android 裝置播放。
  - 為了達成此案例，必須將 proxy 加入至您的服務。

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)
