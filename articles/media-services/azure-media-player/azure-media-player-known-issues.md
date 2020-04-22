---
title: Azure 媒體播放器已知問題
description: 當前版本具有以下已知問題。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ff8dc58b9122e5173a9a6065e2efdbc5697be0d7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727212"
---
# <a name="known-issues"></a>已知問題 #

目前版本具有以下已知問題:

## <a name="azure-media-player"></a>Azure 媒體播放器 ##

- 設定不正確的編碼器可能會導致播放問題
- 時間戳大於 2^53 的流可能有播放問題。
  - 緩解:使用 90 kHz 影片和 44.1 kHz 音效時間刻度
- 沒有使用者交互,在行動裝置上無法自動播放。 它被平台遮罩了。
- 查找接近不連續性可能會導致播放失敗。
- 下載大型演示文稿可能會導致UI鎖定。
- 演示文稿結束后,無法再次自動播放同一源。
  - 要在源結束后重播該源,需要再次設置源。
- 空清單可能會導致玩家出現問題。
  - 啟動即時流時可能會出現此問題,並且在清單中找不到足夠的塊。
- 播放位置可能位於 UI 搜索欄之外。
- 活動訂購並非在所有技術中都一致。
- 緩衝屬性在技術人員之間不一致。
- 本機控制ForTouch必須為 false(預設值),以避免"物件不支援屬性或方法'集控制'"
- 海報現在必須是絕對的網址
- 使用裝置高對比度模式時,UI 中可能會出現輕微的美觀問題
- 在完全解碼的字串中包含 '%"或'+'的網址, 在設定來源時可能有問題

## <a name="ad-insertion"></a>廣告插入 ##

- 在瀏覽器中安裝廣告攔截器時,廣告可能插入問題(按需或即時)
- 移動設備在播放廣告時可能會出現問題。

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- 在「即時內容」的 DVR 視窗中,當內容完成時,時間線將繼續增長,直到查找區域或到達演示文稿的末尾。
- 啟用 MSE 的 Firefox 中的即時展示有問題
- 僅音訊或視頻的資產不會通過 AzureHtml5JS 技術播放。
  - 如果要在沒有音訊或視訊的情況下播放資產,可以使用[Azure 媒體服務資源管理員工具](https://aka.ms/amse)插入空白音訊或視訊來執行此操作
    - 有關如何插入靜音音訊的說明[可在此處](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio)找到

## <a name="flash"></a>閃爍 ##

- 由於 Adobe 緩存邏輯中的 Bug,AES 內容在 Flash 版本 30.0.0.134 中不會播放。 Adobe 已修復該問題,並在 30.0.0.154 中發佈了此問題
- 技術和 HTTP 故障(如 404 網路超時),播放機需要比其他技術更長的時間來恢復。
- 點擊視頻區域與flashS技術不會播放/暫停播放機。
- 如果使用者安裝了 Flash,但未授予在網站上載入 Flash 的許可權,則可能發生無限旋轉。 這是因為播放機認為外掛程式已安裝並可用,並且認為外掛程式正在運行內容。 JavaScript 代碼已發送,但瀏覽器設置已阻止外掛程式執行,直到使用者接受允許外掛程式的提示。 這可能發生在所有瀏覽器中。  

## <a name="silverlight"></a>Silverlight ##

- 遺漏的功能
- 技術和 HTTP 故障(如 404 網路超時),播放機需要比其他技術更長的時間來恢復。
- 使用 Silverlight 在 Mac 上播放`"http://``https://`的 Safari 和 Firefox 需要顯式定義或為源進行定義。
- 如果缺少此技術 API,它通常會傳回 null。
- 如果使用者安裝了 Flash,但未授予在網站上載入 Flash 的許可權,則可能發生無限旋轉。 這是因為播放機認為外掛程式已安裝並可用,並且認為外掛程式正在運行內容。 JavaScript 代碼已發送,但瀏覽器設置已阻止外掛程式執行,直到使用者接受允許外掛程式的提示。 這可能發生在所有瀏覽器中。  

## <a name="native-html5"></a>本機 HTML5 ##

- Html5 技術只發送第一組源的可播放事件。
- 這項技術僅支援瀏覽器已經實現的內容。  在這項技術中可能缺少一些功能。  
- 如果缺少此技術 API,它通常會傳回 null。
- 關於這項技術的標題和字幕,有問題。 他們可能或可能不會在這個技術可用或查看。
- HLS/Html5 技術場景中的頻寬有限會導致音訊播放,無需視頻。

### <a name="microsoft"></a>Microsoft ###

- 由於與 ECMAScript 5 不相容,IE8 播放當前不起作用
- 在 IE 和某些版本的 Edge 中,無法透過選項卡輸入按鈕並選擇該按鈕或使用 F/f 熱鍵輸入全螢幕。

## <a name="google"></a>Google ##

- 同一清單中的多個編碼配置檔在 Chrome 中具有一些播放問題,不建議使用。
- Chrome 無法使用 AzureHtml5JS 播放 HE-AAC。 按照[錯誤跟蹤器](https://bugs.chromium.org/p/chromium/issues/detail?id=534301)的詳細資訊。
- 自 Chrome v58 起,必須通過HTTPs://協定載入/播放寬文內容,否則播放將失敗。

## <a name="mozilla"></a>Mozilla ##

- 音訊流交換機要求音訊流在使用 AzureHtml5JS 時具有相同的編解碼器專用數據。 火狐平臺需要這個。

## <a name="apple"></a>Apple ##

- Mac 上的 Safari 通常預設啟用節能模式,設置「停止外掛程式以節省電量」,當使用者認為 Flash 和 Silverlight 等外掛程式對使用者不利時,該模式會阻止外掛程式。 此塊不會阻止外掛程式的存在,只有功能。 給預設的 TechOrder,這可能會在嘗試播放時出現問題
  - 緩解 1:如果視頻播放機是「正面和中心」(在從文檔左上角開始的 3000 x 3000 像素邊界內),它仍應播放。
  - 緩解 2:將 Safari 的技術訂單更改為 ["azureHtml5JS","html5"]。 這種緩解意味著無法獲取 FlashSS 技術中提供的所有功能。
- 通過 Silverlight 播放就緒內容在 Safari 中播放時可能會出現問題。
- AES 和受限權杖內容不會使用iOS和較舊的Android設備播放。
  - 為了實現此方案,必須向服務添加代理。
- iOS Phone 的默認外觀顯示通過。
- iPhone 播放始終出現在本機播放器全屏中。
  - 功能(包括字幕)可能不會保留在此非內聯播放中。
- 即時演示結束時,iOS 設備將無法正確結束演示。
- iOS 不允許使用 DVR 功能。
- iOS 音效流切換只能透過 iOS 本機播放機 UI 完成,並且要求音訊串流具有相同的編解碼器專用資料
- 舊版本的 Safari 可能會在播放即時流時出現問題。

## <a name="older-android"></a>較舊的安卓 ##

- AES 和受限權杖內容不會使用iOS和較舊的Android設備播放。
  - 為了實現此方案,必須向服務添加代理。

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)