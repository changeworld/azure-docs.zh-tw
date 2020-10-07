---
title: Azure 媒體播放器變更記錄
description: Azure 媒體播放器變更記錄。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 09/23/2020
ms.openlocfilehash: c63a8efc4bbcf6e5a124d439e6f9f91397e2fa53
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91315077"
---
# <a name="changelog"></a>變更記錄

## <a name="236-official-update-september-21-2020"></a>2.3.6 (官方更新 2020 年 9 月 21 日)

### <a name="features-236"></a>功能 2.3.6

新增了對 azureHtml5JS 技術 (DASH) 的僅限音訊支援、支援即時轉譯中的延遲啟動、支援即時轉譯中的語言變更

### <a name="bug-fixes-236"></a>錯誤修正 2.3.6

在 Apple 裝置上的 HLS 播放中使用 "playsinline" 時，按一下 [即時] 按鈕會導致影片重新啟動。AMP 海報影像有時會導致例外狀況。播放 HLS FairPlay 時，音量按鈕會消失。[協助工具] 使用鍵盤時不會定義按鈕的工具提示。[協助工具] 進度列的亮度比小於 1.3:1。[協助工具] 鍵盤焦點有時不會回到影片品質按鈕上。[協助工具] [影片] 畫面不會顯示控制項，導致朗讀程式找不到控制項

### <a name="changes-236"></a>變更 2.3.6

將有意義的 KeyDelivery 錯誤傳回給呼叫端應用程式

## <a name="235-official-update-june-1-2020"></a>2.3.5 (官方更新 2020 年 6 月 1 日)

### <a name="bug-fixes-235"></a>錯誤修正 2.3.5

- [協助工具] 選項窗格中的 Esc 鍵接聽程式附加到文件
- [協助工具] 防止播放器使用者介面在控制列或選項功能表包含焦點時消失
- 當 [時鐘時間顯示設定] 啟用時，控制列顯示不正確的時鐘時間

### <a name="changes-235"></a>變更 2.3.5

- 已新增錯誤碼 0x00400005 的錯誤訊息並加以敘述

## <a name="234-official-update-march-4-2020"></a>2.3.4 (官方更新 2020 年 3 月 4 日)

### <a name="bug-fixes-234"></a>錯誤修正 2.3.4

- 無法設定 PlayReady overrideLicenseAcquistionUrl
- 無法播放某些具有不連續性的內容
- [協助工具] 螢幕助讀程式警示的識別碼屬性值必須是唯一的
- [協助工具] 瀏覽 [隱藏式輔助字幕設定] 對話方塊時，焦點會移出對話方塊

### <a name="changes-234"></a>變更 2.3.4

- 成功下載後記錄 Content-Length，以協助分析解密錯誤 2.3.3 (官方更新 2019 年 11 月 12 日)

### <a name="features-234"></a>功能 2.3.4

- 已支援將影片的時鐘時間顯示為重疊，並顯示在控制列中

### <a name="bug-fixes-234"></a>錯誤修正 2.3.4

- 音軌切換可運作，但 IE11 和 Windows7 物件上的輸出錯誤不支援屬性或方法 'enabled'
- 當緩衝區完全載入時，音軌切換失敗
- 當使用者暫停影片並在曲目之間快速切換時，音軌切換失敗
- [協助工具] 未對影片播放器底下的影片控制項定義工具提示
- Html5 上是否遺漏音量按鈕，視收到 ' loadstart ' 的時間而定
- [協助工具] 無法設定海報影像的替代文字
- [協助工具] 在字幕設定對話方塊中選取 [完成] 之後，應用程式失去焦點
- [協助工具] 已對 [區段預覽] 底下的 [影片] 定義不正確的 ARIA 屬性

### <a name="changes-234"></a>變更 2.3.4

- 在 iOS 和 macOS Safari 上播放 HLS 時，已移除空白字幕標籤/曲目
- 已減少 IMSC1 字幕 的 412 數目
- 主控台中的輸出警告會有 10 個連續的空白 IMSC1 字幕回應，以協助即時偵錯

## <a name="232-official-update-october-9-2019"></a>2.3.2 (官方更新 2019 年 10 月 9 日)

### <a name="features-232"></a>2\.3.2 的功能

已對 Chromium Edge 瀏覽器的 DASH 播放新增 PlayReady 支援

### <a name="bug-fixes-232"></a>錯誤修正 2.3.2

- 目前的播放速度不會以視覺化方式顯示在播放速度功能表中，除非使用者進行手動設定
- [協助工具] 無法利用 'Esc' 鍵摺疊 [設定] 窗格
- [協助工具] 當朗讀程式開啟時，AMP 快速鍵無法運作

### <a name="changes-232"></a>變更 2.3.2

- 對於不支援 E-AC3 音訊轉碼器的瀏覽器，E-AC3 音軌會隱藏在音軌功能表中
- 對於支援 E-AC3 音訊轉碼器的瀏覽器，預設會選取 E-AC3 音軌
- 對於不支援音訊轉碼器切換的瀏覽器，轉碼器不同於所選取曲目的音軌會隱藏在音軌功能表中

## <a name="231-official-update-august-12-2019"></a>2.3.1 (官方更新 2019 年 8 月 12 日)

### <a name="features-231"></a>功能 2.3.1

- 在 DASH 播放中收到 emsg 方塊時，發出事件信號 - 已支援在瀏覽器的音訊功能表中顯示 ec-3 音軌，而此瀏覽器必須支援 ec3，並允許將音軌從 aac 切換到 ec3，而且僅在 Chromimum 型 Edge 瀏覽器上反之亦然

### <a name="bug-fixes-231"></a>錯誤修正 2.3.1

- 移除 ec-3 曲目之後，音軌功能表損毀
- 目前時間可以比影片持續時間大
- 透過 initialSpeed 設定播放速度無法運作
- 有時在搜尋之後，播放器似乎停滯
- 在觸控式螢幕上的 Edge 和 IE 上，於縮放至頁面後，按住滑鼠或將其停留在搜尋列上並不會精確地取得影片的正確區段
- [協助工具] 播放/暫停的 Aria 標籤對於影片播放程式不是描述性標籤。將 flashSS 的找不到即時區段錯誤對應到正確 amp 錯誤
- [協助工具] 用於播放/暫停的 Aria 角色必須符合有效值 (.vjs-text-track-display)
- [協助工具] 某些 ARIA 角色必須由特定父代包含
- [協助工具] 對於影片播放程式的播放/暫停按鈕，沒有已定義的工具提示。搜尋後，IMSC1 字幕可能會在目前影片/音訊緩衝區內消失

### <a name="changes-231"></a>變更 2.3.1

- 在取得 segmentDecryptError 且播放程式已經在即時邊緣時，播放程式現在會重新整理資訊清單，而不會嘗試下一個區段
- 已新增更多記錄以進行診斷
- 已更新文件以包括 iOS Safari 的 FairPlay 支援
- 已新增 IMSC1 選項的 "srclang" 範例
- 已為文字曲目新增 padding、textPadding、boxShadow 覆寫。
- 已新增錯誤碼 (0x0020025B) 來區分區段下載由於沒有網際網路而失敗，而不只是擲回 0x00200259

## <a name="230-official-release-april-30-2019"></a>2.3.0 (2019 年 4 月 30 正式發行)

### <a name="features-230"></a>功能 2.3.0

- 已新增 DASH 對 IMSC1 字幕的支援
- 已新增 DASH 對僅限影片資產的支援
- 已新增 API presentationTimeOffsetInSec

### <a name="bug-fixes-230"></a>錯誤修正 2.3.0

- 由於某些語言翻譯錯誤，AMP LowLatency 啟發學習法設定檔會干擾 iOS 影片播放的「靜音」和「取消靜音」
- 進度列滑桿的 aria-valuenow 值有時不正確
- 文字曲目顯示的 aria 角色值不正確

### <a name="changes-230"></a>變更 2.3.0

- 記錄現在包含已下載的媒體片段大小
- 已移除 IE 9 和 IE 10 支援
- 已更新 CEA708 範例以顯示靠左對齊的字幕
- 在播放失敗的記錄中包含 MediaError.message

## <a name="224-official-update-february-22-2019"></a>2.2.4 (官方更新 2019 年 2 月 22 日) ##

### <a name="bug-fixes-224"></a>錯誤修正 2.2.4 ###

- [錯誤修正][AMP][協助工具] 已移除錯誤畫面出現時可連線的虛設索引標籤
- [錯誤修正][AMP] 已修正 IE11 和 Edge 的快速鍵 'M'
- [錯誤修正][AMP] 已修正 CEA708 字幕的例外狀況
- [錯誤修正][AMP] 已修正 Edge 瀏覽器的影片凍結問題

### <a name="changes-224"></a>變更 2.2.4 ###

- [變更][AMP] 發生片段解密錯誤時，播放器會重試目前的片段和各種片段來復原播放作業
- [變更][AMP] 讓 AMP 變得更能容忍重疊的影片或音訊片段

## <a name="223-official-update-january-9-2019"></a>2.2.3 (官方更新 2019 年 1 月 9 日) ##

### <a name="features-223"></a>功能 2.2.3 ###

- [功能][HLS] 已針對 Safari HLS 播放新增曲目功能表

### <a name="bug-fixes-223"></a>錯誤修正 2.2.3 ###

- [錯誤修正][AMP][協助工具] 在現場廣播播放期間，無法使用鍵盤來選取 [即時] 按鈕
- [錯誤修正][AMP] 已修正因 MSE 測試失敗而造成的誤判 0x0400003 錯誤
- [錯誤修正][AMP] 已修正啟動即時串流時，影片可能會凍結的問題

### <a name="changes-223"></a>變更 2.2.3 ###

- [變更][AMP] 已在記錄中新增更多資訊以提升診斷效果
- [變更][AMP] 當相同螢幕解析度下有多個可用的位元速率時，所有位元速率都可供選取

## <a name="222-official-update"></a>2.2.2 (官方更新) ##

### <a name="bug-fixes-222"></a>錯誤修正 2.2.2 ###

- [錯誤修正][AMP] 播放器遇到暫時性網路中斷時會立即停止播放
- [錯誤修正][AMP][協助工具] 無法透過鍵盤來存取錯誤對話方塊
- [錯誤修正][AMP] 播放只有音訊的資產時，進度環會轉個不停，而不會顯示不受支援的錯誤

### <a name="changes-222"></a>變更 2.2.2 ###

- [變更][AMP] 已針對公告 UI 新增當地語系化的字串

## <a name="221-official-update"></a>2.2.1 (官方更新) ##

### <a name="features-221"></a>功能 2.2.1 ###

- [功能][CMAF] 已新增對於 HLS CMAF 的支援

### <a name="bug-fixes"></a>錯誤修正 ###

- [錯誤修正][AMP] 重試邏輯中的計時器若未清除會產生播放錯誤
- [錯誤修正][AMP][Firefox] 即時節目停止時，Firefox 和 Chrome 上不會引發已結束的事件
- [錯誤修正][AMP] setsource 後會顯示控制項，即使已在播放器選項中將控制項設定為 false 也一樣

### <a name="changes"></a>變更 ###

- [變更][即時字幕] 已將 CEA 字幕的 API 名稱從 608 變更為 708。 如需詳細資訊，請參閱 [CEA708 字幕設定](/javascript/api/azuremediaplayer/amp.player.cea708captionssettings)-->

## <a name="220-official-release"></a>2.2.0 (正式發行) ##

### <a name="features-220"></a>功能 2.2.0 ###

- [功能][Azurehtml5JS][Flash][LiveCaptions] Azurehtml5JS 和 FlashSS 技術支援 CEA 708 字幕，以獲得清楚和 AES 的內容。

### <a name="bug-fixes-220"></a>錯誤修正 2.2.0 ###

- [錯誤修正] Flash 版本偵測在 Chrome/Edge 中沒有作用

### <a name="changes-220"></a>變更 2.2.0 ###

- [變更][AMP][啟發] 已將啟發式設定檔名稱從 QuickStartive 變更為 LowLatency
- [變更][Flash] 變更了 Flash播放器中的版本偵測，以便能使用新的 Adobe Flash 更新來播放 AES 內容。

## <a name="219-official-hotfix"></a>2.1.9 (官方 Hotfix) ##

### <a name="bug-fixes-219"></a>錯誤修正 2.1.9 ###

- [錯誤修正][即時] 當即時串流轉換至點播視訊/即時封存時會發生例外狀況

### <a name="changes-219"></a>變更 2.1.9 ###

- [變更][Flash][AES] 已將 Flash 技術邏輯修改為不使用 sharedbytearrays 來進行 AES 解密，因為從 Flash 30 開始，Adobe 便已封鎖此使用方式。 請注意，因為 v30 中有錯誤，因此只有在 Adobe 部署新版 Flash 後，播放才會有作用。 如需詳細資訊，請參閱[已知問題](azure-media-player-known-issues.md)

## <a name="218-official-update"></a>2.1.8 (官方更新) ##

### <a name="bug-fixes-218"></a>錯誤修正 2.1.8 ###

- [錯誤修正] 進度環有時不會顯示後續搜尋和預先播放
- [錯誤修正] 已啟用靜音選項時，播放器卻未開始靜音
- [錯誤修正] 當控制項設定為 false 時，會顯示磁碟區滑杆
- [錯誤修正] 使用者跳到即時邊緣時，有時會重複播放
- [錯誤修正][Firefox] 播放器有時會在載入時擲回 JavaScript 例外狀況
- [錯誤修正][協助工具] 使用鍵盤控制項選取 [播放]/[暫停]/[音量] 按鈕時，按鈕會遺失焦點外框
- [錯誤修正] 已修正播放器上已處理的記憶體流失
- [錯誤修正] 在播放器發生錯誤後呼叫 src() 並不會重設來源
- [錯誤修正][即時] 當使用者在廣播結束後按一下 [即時] 按鈕時，AMP 會處於持續載入的狀態
- [錯誤修正][Chrome] 當瀏覽器最小化至背景時，播放器會停止回應且播放會失敗。

### <a name="changes-218"></a>變更 2.1.8 ###

- [變更] 已更新 0x0600001 錯誤碼，使其在使用 Flash 30 播放 AES 內容時顯示，因為目前還不支援此功能。 如需詳細資訊，請參閱[已知問題](azure-media-player-known-issues.md)
- [變更] 當資訊清單要求 404 或傳回空白資訊清單時，已針對即時案例新增額外的重試機會。

## <a name="217-official-update"></a>2.1.7 (官方更新) ##

### <a name="features-217"></a>功能 2.1.7 ###

- [功能][AzureHtml5JS] 已新增設定選項以排清媒體來源緩衝區中的過時資料

### <a name="bug-fixes-217"></a>錯誤修正 2.1.7 ###

- [錯誤修正][協助工具][螢幕助讀程式] 已移除未設定標題時播放器所包含的空白標頭
- [錯誤修正][UWA] 在通用 Windows 應用程式中播放時，AMP 會擲回例外狀況
- [錯誤修正][OSX] setActiveTextTrack() 在 OSx 上的 Safari 中沒有作用
- [錯誤修正][即時] 在處理並重新初始化播放器後，若按一下即時邊緣，將會產生例外狀況
- [錯誤修正][面板] 特定資產的目前時間遭到截斷
- [錯誤修正][DRM] 已納入修正程式，從而在支援多個 CENC DRM 的瀏覽器中支援播放作業

### <a name="changes-217"></a>變更 2.1.7 ###

- [變更][範例][協助工具] 已對所有範例新增語言標籤

## <a name="216-official-update"></a>2.1.6 (官方更新) ##

### <a name="bug-fixes-216"></a>錯誤修正 2.1.6 ###

- [錯誤修正] 針對特定資產，AMP 顯示的持續時間不正確
- [錯誤修正][FairPlay-HLS] Fairplay 錯誤未傳播至 UI
- [錯誤修正] AMP 2.1.5 將會忽略自訂啟發式屬性。

### <a name="changes-216"></a>變更 2.1.6 ###

- [變更][FairPlayDRM] 已移除 FairPlay 的憑證要求和授權要求逾時，以便與 PlayReady 和 Widevine 實作保持同位
- [變更] 已改良其他啟發式功能以對抗模糊內容

### <a name="features-216"></a>功能 2.1.6 ###

- [功能] 已新增支援mpd-time-cmaf 格式

## <a name="215-official-hotfix"></a>2.1.5 (官方 Hotfix) ##

### <a name="bug-fixes-215"></a>錯誤修正 2.1.5 ###

- [錯誤修正][字幕] 播放器未正確轉譯 VTT 樣式
- [錯誤修正][協助工具] [即時] 按鈕沒有 aria 標籤

## <a name="214-official-update"></a>2.1.4 (官方更新) ##

### <a name="bug-fixes-214"></a>錯誤修正 2.1.4 ###

- [錯誤修正][協助工具][焦點] 使用者無法透過 Tab 鍵將焦點放在已新增至控制列 [全螢幕] 按鈕右邊的自訂按鈕
- [錯誤修正][IE11][音量條] 在全螢幕模式下的 IE11 中，若透過 Tab 鍵移至音量快顯將會使整個影片畫面閃爍不停
- [錯誤修正][面板|排清] 控制列和音量條快顯之間出現間隔
- [錯誤修正][AMP][字幕] 現有播放器上的來源變更時，不會清除已內嵌的舊有曲目
- [錯誤修正][協助工具][朗讀程式] 螢幕助讀程式所念出的音量控制並不正確
- [錯誤修正][FlashSS] 有時不會從 Flash 技術引發播放事件
- [錯誤修正][AMP][焦點] 播放器擁有焦點且處於全螢幕模式時，播放/暫停需要按兩下滑鼠
- [錯誤修正][AMP][面板] 特定資產的進度列所顯示的持續時間不正確
- [錯誤修正][廣告][Ad Butler] VAST 剖析器不會處理沒有進度事件的 VAST 檔案
- [錯誤修正][SDN][AMP 2.1.1] 已修正 Hive SDN 外掛程式支援的問題
- [錯誤修正][協助工具] 當使用者的焦點在音量按鈕時，朗讀程式卻念出「午夜靜音按鈕」

### <a name="changes-214"></a>變更 2.1.4 ###

- [變更][協助工具][輔助技術] 按鈕現在具有 aria-live 屬性，可改善輔助技術的體驗
- [變更][協助工具][音量按鈕|朗讀程式] 已藉由修改按 Tab 鍵的功能和滑桿行為，而改善 [音量] 按鈕的協助工具。 這些變更可讓鍵盤使用者更容易修改播放器的音量
- [變更] 已將無活動捷徑功能表的逾時從 3 秒增加為 5 秒
- [變更][協助工具][亮度] 已改善字幕設定中下拉式功能表上的亮度對比比例

## <a name="213-official-update"></a>2.1.3 (官方更新) ##

### <a name="bug-fixes-213"></a>錯誤修正 2.1.3 ###

- [錯誤修正][外掛程式|標題重疊] 標題重疊外掛程式會在使用 AMP v2.X+ 時擲回 JS 例外狀況
- [錯誤修正] 即使關閉記錄功能，仍會將「來源設定」事件傳送至 JavaScript 主控台
- [錯誤修正][面板] 當滑鼠游標停留在任一端的持續期間列時，播放器的時間提示會呈現在播放器內容之外
- [錯誤修正][協助工具][螢幕助讀程式] 當觀看者的焦點放在播放器上時，朗讀程式會念出「區域地標」或「影片播放器區域地標」
- [錯誤修正][AMP] 無法透過 CSS 停用播放器外框
- [錯誤修正][協助工具] 當使用者處於全螢幕模式時，無法透過 Tab 鍵將焦點放在整個播放器上
- [錯誤修正][面板][即時] 面板無法回應日文的當地語系化即時文字
- [錯誤修正][面板] 當串流 > 60 分鐘時，持續時間和目前時間會遭到截斷 - [錯誤修正][iPhone|即時] 播放器會在控制列中顯示目前時間/持續時間的文字
- [錯誤修正][AMP] 呼叫播放器啟發式 API 會產生 JavaScript 例外狀況
- [錯誤修正][原生 Html5|iOS] Videotag 屬性 "playsinline" 未傳播至播放器
- [錯誤修正][iOS|iframe] 如果在 iframe 中載入播放器，播放器無法在 iPhone 上進入全螢幕
- [錯誤修正][AMP][啟發] AMP 一律會使用混合式設定檔來運作，而不理會任何播放器選項
- [錯誤修正][AMP|Win 8.1] 使用 Web 檢視裝載於 Win 8.1 應用程式時會擲回

### <a name="changes-213"></a>變更 2.1.3 ###

- [變更][AMP] 已在 FragmentDownloadComplete 事件中新增 CDN 端點資訊
- [變更][AMP][即時] 即時串流延遲已獲得改良並予以最佳化

## <a name="212-official-hotfix"></a>2.1.2 (官方 Hotfix) ##

### <a name="bug-fixes-212"></a>錯誤修正 2.1.2 ####

- [錯誤修正][協助工具][Windows 朗讀程式] 當使用者具有進度列的內容，且目前時間為 0:00 時，朗讀程式會念出「進度午夜」
- [錯誤修正][面板] 標誌大小會硬式編碼到 JavaScript 程式碼中
- [協助工具][快速鍵] 按一下播放器時，不會啟用快速鍵。

### <a name="changes-212"></a>變更 2.1.2 ####

- [變更][記錄] 當播放器無法載入資訊清單時，會記錄資訊清單 URL

### <a name="features-212"></a>功能 2.1.2 ###

- [變更][效能][最佳化] 已改良播放器的載入和啟動時間

## <a name="211-official-update"></a>2.1.1 (官方更新) ##

### <a name="bug-fixes-211"></a>錯誤修正 2.1.1 ####

- [錯誤修正][iOS] 在 iOS 版的 Safari 中將 [自動播放] 設定為 false 會產生轉個不停的進度環
- [錯誤修正] 搜尋大於內容持續時間的時間會產生轉個不停的進度環
- [錯誤修正] 快速鍵需要多次按鍵盤上的 Tab 鍵，才能讓播放器的內容運作
- [錯誤修正] 在特定資產中調整播放器的大小後，影片會凍結幾秒鐘
- [錯誤修正] 當使用者快速進行多次搜尋時，進度環會轉個不停 (在搜尋完成後)
- [錯誤修正] 非活動期間不會隱藏控制列
- [錯誤修正] 開啟裝載 AMP 的 webapp 會導致網頁載入兩次
- [錯誤修正] 透過 Flash 技術播放特定資產的內容時，進度環會轉個不停
- [錯誤修正] 第三方外掛程式不會顯示 [更多選項] 功能表
- [錯誤修正][外觀|Tube][即時] 當播放器位於程式的即時邊緣時，會顯示兩個即時圖示
- [錯誤修正][面板] 無法停用標誌
- [錯誤修正][DD+ 內容] 包含 Dolby Digital 曲目的資產，進度環會轉個不停
- [錯誤修正] 在即時串流期間切換音訊語言音軌時，最新的 AMP 會凍結
- [錯誤修正] 已針對進度環修正背景消失問題
- [錯誤修正] AES flash 權杖靜態範例的進度環轉個不停錯誤修正

### <a name="changes-211"></a>變更 2.1.1 ####

- [變更] 已針對 Widevine Https 需求新增錯誤碼：從 Chrome v58 起，必須透過 `https://` 通訊協定來載入/播放 Widevine 內容，否則播放將會失敗。
- [變更] 已新增用來載入進度環的 aria 標籤，讓輔助技術可以在載入內容時念出「影片載入中」  

## <a name="210-official-release"></a>2.1.0 (官方發行) ##

### <a name="features-210"></a>功能 2.1.0 ###

- [功能][AzureHtml5JS] VOD 廣告支援 pre- mid- post-rolls
- [功能][Beta][AzureHtml5JS] 即時廣告支援 pre- mid- post-rolls
- [功能] 已新增新的面板選項 - AMP-flush
- [功能] 已新增改良後的 aria 標籤，以便與螢幕助讀程式/輔助技術更緊密整合
- [功能][面板] 面板現在會以高對比模式清楚地顯示所有圖示和按鈕

### <a name="bug-fixes-210"></a>錯誤修正 2.1.0 ###

- [錯誤修正] 協助工具和 UI 修正程式的數目
- [錯誤修正] AMP 無法在 IE9 中正確載入

### <a name="changes-210"></a>變更 2.1.0 ###

- [變更] 已重構播放器中的 DOM 元素以配合廣告工作
- [變更] 面板開發已從 CSS 切換到 SCSS
- [變更] 已新增 VOD 廣告範例
- [變更] 已新增播放速度範例
- [變更] 已新增排清面板範例

## <a name="200-beta-release"></a>2.0.0 (Beta 版) ##

- [變更] 已更新為 VJS5
- [功能] 已新增新的流暢 API，讓播放器回應流暢
- [功能] 播放速度
- [變更] 面板已從 CSS 切換到 SCSS

## <a name="183-official-hotfix-update"></a>1.8.3 (官方 Hotfix更新) ##

### <a name="bug-fixes-183"></a>錯誤修正 1.8.3 ###

- [錯誤修正][AzureHtml5JS] 具有負面 DTS 的特定資產不會在 Chrome 中播放

## <a name="182-official-hotfix-update"></a>1.8.2 (官方 Hotfix更新) ##

### <a name="bug-fixes-182"></a>錯誤修正 1.8.2 ###

- [錯誤修正][AzureHtml5JS] 較高的音訊位元速率不會透過 AzureHtml5JS 播放

## <a name="181-official-update"></a>1.8.1 (官方更新) ##

### <a name="bug-fixes-181"></a>錯誤修正 1.8.1 ###

- [錯誤修正][iOS] 原生播放器中未顯示原文字幕/翻譯字幕
- [錯誤修正][AMP] 附加了驗證權杖的 CDN 支援串流 URL 不會播放
- [錯誤修正][FairPlay] FairPlay 錯誤碼遺漏技術識別碼 (錯誤碼的 Bits [31-28]) 如需詳細資訊，請參閱錯誤碼
- [錯誤修正][Safari][PlayReady] Safari 中的 PlayReady 內容會產生轉個不停的進度環

### <a name="changes-181"></a>變更 1.8.1 ###

- [變更][Html5] 變更原生 Html5 技術詳細資訊記錄以包含來自 VideoTag 的事件

## <a name="180-official-update"></a>1.8.0 (官方更新) ##

### <a name="features-180"></a>功能 1.8.0 ###

- [功能][DRM] 已新增 FairPlay 支援 (如需詳細資訊，請參閱[受保護內容](azure-media-player-protected-content.md))

### <a name="bug-fixes-180"></a>錯誤修正 1.8.0 ###

- [錯誤修正][AMP] 當網路受到節流處理時，使用者搜尋不會觸發等待事件
- [錯誤修正][FlashSS] 在 Flash 技術中選取品質會擲回例外狀況
- [錯誤修正][AMP] 以動態方式選取品質會顯示在捷徑功能表中
- [錯誤修正][面板] 難以選取捷徑功能表的最後一個功能表項目

### <a name="changes-180"></a>變更 1.8.0 ###

- [變更] 已將播放器更新為目前的 Chrome EME 需求
- [變更] 預設 techOrder 已變更為配合新的技術 - html5FairPlayHLS (如需詳細資訊，請參閱[受保護內容](azure-media-player-protected-content.md))
- [變更][AzureHtml5JS] 已在 Safari 中啟用 MPEG-Dash 播放
- [變更][範例] 已變更多重 DRM 範例以配合 FairPlay

## <a name="174-official-hotfix-update"></a>1.7.4 (官方 Hotfix更新) ##

### <a name="bug-fixes-174"></a>錯誤修正 1.7.4 ###

- [錯誤修正][Chrome] 當使用者具有播放器的內容時，搜尋控點周圍會出現藍色外框
- [錯誤修正][IE9] 在 IE9 中載入播放器時會擲回 JavaScript 例外狀況

## <a name="173-official-hotfix-update"></a>1.7.3 (官方 Hotfix更新) ##

### <a name="bug-fixes-173"></a>錯誤修正 1.7.3 ###

- [錯誤修正][AzureHtml5JS] 播放器在受限網路中會逾時

### <a name="changes-173"></a>變更 1.7.3 ###

- [變更] 在 Edge 上啟用 Webcrypto 以解密 AES 內容
- [變更] 最佳化 AMP 啟發學習法以考量已快取的區塊
- [變更][AzureHtml5JS] 藉由減少頻寬估計延遲將啟發學習法最佳化

## <a name="172-official-hotfix-update"></a>1.7.2 (官方 Hotfix更新) ##

### <a name="features-172"></a>功能 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- [功能][AzureHtml5JS|Firefox] 針對 Firefox 47+ 啟用以 EME 進行 Widevine 播放的功能
- [功能] 新增播放器處理的事件
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>錯誤修正 1.7.2 ###

- [錯誤修正] 已編碼的 Akamai CDN URL 查詢參數未能正確解碼
- [錯誤修正] manifestPlayableWindowLength() 上將會擲回例外狀況
- [錯誤修正] 當影片結束於重新觀看後，觀看者不一定能按一下影片上的播放
- [錯誤修正] 回應式調整大小不符合快速視窗大小變更
- [錯誤修正][Edge|IE] 回應式調整大小不會在 width=x, height=auto 的頁面載入上生效
- [錯誤修正][Android|Chrome] Chrome 會在內容未加密時，要求取得播放 DRM 內容的權限
- [錯誤修正][協助工具][Edge] 鍵盤控制項未正確地選取捷徑功能表項目
- [錯誤修正][協助工具] 在高對比模式中會失去已顯示的框線
- [錯誤修正][FlashSS] 在播放器處理之後未移除滑鼠上移事件接聽程式將會造成例外狀況
- [錯誤修正][FlashSS] 剖析具有已編碼空格的資訊清單 URL 時發生問題
- [錯誤修正][iOS] 評估 tech.featuresVolumeControl 時發生類型錯誤

### <a name="changes-172"></a>變更 1.7.2 ###

- [變更][DRM] 已將 DRM 檢查移動到在將來源設定為僅在內容已加密時進行檢查
- [變更][AES] 已從金鑰傳遞要求中移除未定義的類型/一般主體
- [變更][協助工具] 當內容在播放器上 (而非屬性) 時，Windows 朗讀程式現在會念出「媒體播放器」

## <a name="171-official-hotfix-update"></a>1.7.1 (官方 Hotfix更新) ##

### <a name="features-171"></a>功能 1.7.1 ###

- [功能] 已新增混合式啟發式設定檔的選項 (依預設會設定此設定檔)

### <a name="bug-fixes-171"></a>錯誤修正 1.7.1 ###

- [錯誤修正] 回應式設計不會按照 HTML5 標準 (width=100%, height=auto) 來運作
- [錯誤修正] 寬度和高度的百分比值在 v1.7.0 中不會如預期般運作

## <a name="170-official-update"></a>1.7.0 (官方更新) ##

### <a name="features-170"></a>功能 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- [功能][AzureHtml5JS][FlashSS] 已新增 currentMediaTime() 以取得目前時間的編碼器媒體時間 (以秒為單位)
- [功能][FlashSS] 已使用 videoBufferData()和 audioBufferData()來實作下載遙測 API<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- [功能][FlashSS] 已新增 'downloadbitratechanged' 事件
- [功能] 相較於舊版播放器，載入時間已改善
- [功能] 錯誤會記錄到 JavaScript 主控台

### <a name="bug-fixes-170"></a>錯誤修正 1.7.0 ###

- [錯誤修正] 具有查詢字串參數的已編碼海報 URL 不會顯示在播放器中
- [錯誤修正] 未載入任何技術並呼叫 API amp.Player.poster() 時，會擲回例外狀況
- [錯誤修正] 當函式嘗試在處理後存取播放器時，會擲回例外狀況
- [錯誤修正][協助工具] 進度列搜尋前端的焦點上會遺失外框
- [錯誤修正][協助工具] 捷徑功能表在高對比模式中會有陰影
- [錯誤修正][iOS] 原生播放器 WebVTT 字幕播放無法運作
- [錯誤修正][AzureHtml5JS] 在 HTTPS 網站上播放 HTTP 串流時，應該要顯示錯誤 0x0100002，卻因為混合式內容的結果而產生轉個不停的進度環
- [錯誤修正][AzureHtml5JS] 缺少結束區段導致迴圈健康情況檢查錯誤顯示看得到的不斷緩衝狀態
- [錯誤修正][AzureHtml5JS] useManifestForLabel=false 並使用三個字母的語言代碼時，功能表中的曲目名稱不正確
- [錯誤修正][AzureHtml5JS|Chrome] 在 Chrome 中使用 JavaScript 期間，浮點不精確會造成內容結尾出現不斷緩衝狀態
- [錯誤修正][FlashSS] 建立 Flash播放器時，會暫時顯示非嚴重的間歇性錯誤
- [錯誤修正][FlashSS] 當影片和音訊串流使用不同的時間刻度時，播放會失敗，原因是發生舍入不精確失敗，錯誤訊息為「片段 url (...) 無法產生 FLVTags」
- [錯誤修正][FlashSS] 剖析具有已編碼空格的資訊清單 URL 時發生問題
- [錯誤修正][FlashSS] 遺漏會判斷 Flash播放器版本 >= 11.4 的檢查，這會導致播放錯誤，而不是回復到 techOrder 中的下一個技術
- [錯誤修正][FlashSS][AES] 在接受內有底線的 AES 權杖時發生問題
- [錯誤修正][SilverlightSS|OSX] 在資訊清單而非通訊協定 (HTTP 或 HTTPS) 前面加上 "//" 會讓系統認為是本機檔案，而產生轉個不停的進度環

### <a name="changes-170"></a>變更 1.7.0 ###

- [變更][FlashSS] 已將 SWF 指令碼 ("MSAdaptiveStreamingPlugin-osmf2.0.swf" 和 "StrobeMediaPlayback.2.0.swf") 合併至名為 "StrobeMediaPlayback.2.0.swf" 的單一 SWF
- [變更][FlashSS] 已更新錯誤碼傳播，以取得更精確的錯誤碼 (例如 404 現在會產生 0x30200194，而不是一般錯誤 0x30200000)

## <a name="163-official-hotfix-update"></a>1.6.3 (官方 Hotfix更新) ##

### <a name="bug-fixes-163"></a>錯誤修正 1.6.3 ###

- [錯誤修正] 在處理播放器之後執行快速鍵事件處理常式時，發生 JavaScript 執行階段例外狀況
- [錯誤修正][Android][AzureHtml5JS] 使用行動電話通訊網路的行動裝置上無法播放
- [錯誤修正] 已將 Forge 更新為以 Web 背景工作的形式來執行，以釋放 UI

## <a name="162-official-hotfix-update"></a>1.6.2 (官方 Hotfix更新) ##

### <a name="features-162"></a>功能 1.6.2 ###

- [功能] 已新增可當地語系化的其他語言 (如需詳細資訊，請參閱文件)

### <a name="bug-fixes-162"></a>錯誤修正 1.6.2 ###

- [錯誤修正][IE9-10] 按一下播放器周圍的區域會讓瀏覽器視窗最小化，因為 IE9/IE10 有錯誤而會在 window.blur() 上最小化
- [錯誤修正][FlashSS] 不接受內有底線的 AES 權杖

## <a name="161-official-hotfix-update"></a>1.6.1 (官方 Hotfix更新) ##

### <a name="bug-fixes-161"></a>錯誤修正 1.6.1 ###

- [錯誤修正][FlashSS|Edge,IE][SilverlightSS|IE] 無法將焦點放在其他 UI 元素，以在 IE/Edge 中進行輸入或其他工作
- [錯誤修正] 當 forge 未定義時，AES 播放會失敗
- [錯誤修正][Android][AzureHtml5JS|Chrome] 在處於健康情況檢查迴圈中時，進度環會轉個不停而不會播放內容
- [錯誤修正][IE9] IE 9 不支援 console.log() 而造成例外狀況

## <a name="160-official-update"></a>1.6.0 (官方更新) ##

### <a name="features-160"></a>功能 1.6.0 ###

- [功能] azuremediaplayer.min.js 的大小縮減 33%
- [功能][AzureHtml5JS|Edge][未測試] 在 Edge 中支援 DD+ 音訊串流 (在初始選擇之後，不會切換轉碼器)。 應用程式此時必須選取正確的音訊串流。
- [功能] 快速鍵控制項 (如需詳細資訊，請參閱文件)
- [功能] 進度時間提示會停留，以進行時間精確搜尋
- [功能] 如果外掛程式中有 setupDone 方法，允許對外掛程式進行非同步偵測

### <a name="bug-fixes-160"></a>錯誤修正 1.6.0 ###

- [錯誤修正] getMemoryLog(true) 上的記憶體記錄未排清
- [錯誤修正] 滑鼠移動時位元速率選取方塊會重設，導致透過滑鼠控制項選取較低的位元速率時會發生問題
- [錯誤修正] 執行 DRM 檢查時，應用程式中的 Mac Office 會當機
- [錯誤修正] 很容易就會不小心覆寫 CSS 類別
- [錯誤修正][Chrome] user-agent 字串瀏覽器的更新識別為 Edge
- [錯誤修正][AzureHtml5JS] 標題按鈕未顯示在 Edge (Win10) 或 Chrome (Mac) 中的工具列
- [錯誤修正][Android][AzureHtml5JS|Chrome] 在短片上呼叫 endOfStream() 時會發生 InvalidStateError 例外狀況
- [錯誤修正][Firefox] 移除了 Firefox 在檢查瀏覽器功能時所造成的 DRM 警告
- [錯誤修正][Html5] 未顯示漸進式 mp4 內容的翻譯字幕/原文字幕
- [錯誤修正][FlashSS] 具有相符時間戳記的訊息會以反向順序來記錄
- [錯誤修正][協助工具][Chrome|Firefox] 透過 Tab 鍵選取控制項會自動選取第一個功能表項目
- [錯誤修正][協助工具] 透過 Tab 鍵來控制音量按鈕

### <a name="changes-160"></a>變更 1.6.0 ###

- [變更] 在品質等級選擇上使用 AES 解密時間
- [變更] 針對多重音訊串流，將 URL 重寫工具更新為先使用 HLS v4 再使用 HLS v3
- [變更] 將 nativeControlsForTouch 的預設值設定為 false (必須為 false 才能正常運作)

## <a name="150-official-update"></a>1.5.0 (官方更新) ##

### <a name="features-150"></a>功能 1.5.0 ###

- [功能] 增強一般 Web 安全性 (預防插入、XSS 等)
- [功能] 針對 sourceset 事件和 options.sdn 的 SDN 外掛程式整合掛勾
- [功能] 在播放期間的 5XX 和 4XX 錯誤強固性處理

### <a name="bug-fixes-150"></a>錯誤修正 1.5.0 ###

- [錯誤修正] 將 CSS 縮製更新為針對按鈕使用 HTML 實體字型代碼，而不是 Unicode
- [錯誤修正][AzureHtml5JS] 多重 DRM 內容一律會從 protectionInfo 選取第一個元素的權杖，而導致第二個 DRM 失敗
- [錯誤修正][AzureHtml5JS] 搜尋缺少區段的區域時，搜尋永遠不會完成。
- [錯誤修正][AzureHtml5JS|Edge] 在 PlayReady 播放的 Edge 更新中啟用前置詞 EME
- [錯誤修正][AzureHtml5JS|Firefox] 更新 EME 檢查，以允許 Firefox v42+ (含 MSE) 遞補到 Silverlight，以取得受保護內容
- [錯誤修正][FlashSS] 將 error.message 從數字更新為詳細字串

### <a name="changes-150"></a>變更 1.5.0 ###

- [變更] 海報目前只能作為絕對 URL。

## <a name="140-official-update"></a>1.4.0 (官方更新) ##

### <a name="features-140"></a>功能 1.4.0 ###

- [功能][AzureHtml5JS|Chrome] 簡單的 Widevine DRM 支援
- [功能][AzureHtml5JS] 在播放期間的 404/412 錯誤強固性處理

### <a name="bug-fixes-140"></a>錯誤修正 1.4.0 ###

- [錯誤修正][FlashSS] 增強參數驗證

## <a name="130-official-update"></a>1.3.0 (官方更新) ##

### <a name="features-130"></a>功能 1.3.0 ###

- [功能][AzureHtml5JS][FlashSS] 相同轉碼器多重音訊內容的音訊切換

### <a name="bug-fixes-130"></a>錯誤修正 1.3.0 ###

- [錯誤修正][AzureHtml5JS|Chrome] 進度環會間歇性轉個不停
- [錯誤修正][AzureHtml5JS|IE][Windows Phone] 有例外狀況會導致 Windows Phone 出現播放問題
- [錯誤修正][FlashSS] 針對其他執行個體將自動播放設定為 false 會失敗
- [錯誤修正] UI 功能表調整大小問題

## <a name="120-official-update"></a>1.2.0 (官方更新) ##

### <a name="features-120"></a>功能 1.2.0 ###

- [功能][AzureHtml5JS|Firefox] 啟用 MSE 時的支援
- [功能] 不再需要應用程式來提供遞補技術二進位檔 (swf、xap) 的路徑。 路徑會與 Azure 媒體播放器的指令碼相對。

### <a name="bug-fixes-120"></a>錯誤修正 1.2.0 ###

- [錯誤修正][AzureHtml5JS|Chrome] 播放器在背景中會偏離到即時邊緣後面
- [錯誤修正][AzureHtml5JS|Edge] 全螢幕無法運作
- [錯誤修正][AzureHtml5JS] 在選項中設定時，記錄未正確啟用
- [錯誤修正] 在等待事件期間，會同時顯示「緩衝」和緩衝圖示
- [錯誤修正] 允許在初始頻寬要求失敗時繼續播放
- [錯誤修正] 使用未定義的選項初始化時，播放器無法載入
- [錯誤修正] 嘗試處理已處理的播放器之後，會發生 vdata 例外狀況
- [錯誤修正] 品質列圖示未正確對應

## <a name="111-official-hotfix-update"></a>1.1.1 (官方 Hotfix更新) ##

### <a name="bug-fixes-111"></a>錯誤修正 1.1.1 ###

- [錯誤修正] 舊版 IE 全螢幕問題
- [錯誤修正] 外掛程式不會再遭到覆寫

## <a name="110-official-update"></a>1.1.0 (官方更新) ##

### <a name="features-110"></a>功能 1.1.0 ###

- [功能] 更新 UI 當地語系化字串

### <a name="bug-fixes-110"></a>錯誤修正 1.1.0 ###

- [錯誤修正] 大型播放按鈕沒有足夠的對比
- [錯誤修正] 視覺索引標籤焦點指標
- [錯誤修正] 現在會使用正確的解析度資訊來選取位元速率功能表
- [錯誤修正] [更多選項] 功能表現在會動態調整大小
- [錯誤修正] 各種 UI 問題

## <a name="100-official-release"></a>1.0.0 (正式發行) ##

### <a name="features-100"></a>功能 1.0.0 ###

- [功能] Tab 鍵控制項、焦點控制項、螢幕助讀程式、高對比 UI 的基本協助工具測試
- [功能] 更新了 UI
- [功能] 開發記錄
- [功能] 用來動態設定原文字幕/翻譯字幕軌道的 API
- [功能] 基本當地語系化功能
- [功能] 跨技術的錯誤碼彙總
- [功能] 未安裝外掛程式 (例如 Flash 或 Silverlight) 時有適用的新錯誤碼
- [功能][AzureHtml5JS] 已實作基本診斷事件

### <a name="bug-fixes-100"></a>錯誤修正 1.0.0 ###
<!---What is that actually supposed to say?--->
- [錯誤修正][AzureHtml5JS] 當時間戳記中有小型非精度時，即時播放會在 MPD 更新時凍結
- [錯誤修正][AzureHtml5JS] 已減輕數個即時播放問題
- [錯誤修正][AzureHtml5JS] 視窗大小啟發學習法開啟時排清緩衝區，並進入較高解析度的畫面
- [錯誤修正][AzureHtml5JS] Chrome 現在會正確顯示已結束的事件。 已連結到先前已知的問題：*Chrome 在使用 AzureHtml5JS 時不會正確地傳送已結束的事件。基礎瀏覽器中發生問題。*
- [錯誤修正][AzureHtml5JS] 已針對此技術停用 Safari，以解決 *OSX Yosemite 與 AzureHtml5JS 技術搭配使用時的播放問題。MSE 有實作問題。暫時的避免方法：強制以 flashSS、silverlightSS 作為這些使用者代理程式的技術順序*
- [錯誤修正][FlashSS] 發生錯誤後會引發 loadstart

## <a name="020-beta"></a>0.2.0 (Beta) ##

### <a name="features-020"></a>功能 0.2.0 ###

- [功能] 已針對隨選和即時的 PlayReady 和 AES 完成測試 - 請參閱相容性矩陣
- [功能] 處理不連續
- [功能] 支援大於 2^53 的時間戳記
- [功能] URL 查詢參數會保存到資訊清單要求
- [功能][未測試] 支援 `QuickStart` 和 `HighQuality` 啟發學習法設定檔
- [功能][未測試] 在 AzureHtml5JS 和 FlashSS 上公開位元速率、寬度和高度的影片串流資訊
- [功能][未測試] 在 AzureHtml5JS 和 FlashSS 上選取位元速率 (請參閱 API 文件)

### <a name="bug-fixes-020"></a>錯誤修正 0.2.0 ###

- [錯誤修正] 現在可於 WP8.1 上看到大型播放按鈕
- [錯誤修正] 已修正多個即時播放問題
- [錯誤修正] [取消靜音] 按鈕現在可於 UI 上運作
- [錯誤修正] 已更新自動播放模式的 UI 載入體驗
- [錯誤修正] AMD 載入器問題並定義方法衝突
- [錯誤修正] WP 8.1 Cordova 應用程式載入問題
- [錯誤修正] 受保護內容會查詢平台/技術支援的 ProtectionType，以選取適當的播放技術。  修正先前已知的問題：「_在 Chrome (桌面)/Safari 8 (在 OSX Yosemite 上) 上的 PlayReady 內容目前並未遞補到 Silverlight 播放器_」
- [錯誤修正] WinServer 2012 R2 上有未攔截到的例外狀況，因為該機器上預設不會安裝媒體基礎。  嘗試使用未實作的 HTML 影片標籤 API，因而擲回錯誤。 目前的緩和措施是攔截該錯誤並傳回 false，而不是擲回錯誤。
- [錯誤修正] 一律會在搜尋或 http 失敗後取得 init 區段，以防止播放期間發生問題
- [錯誤修正] 關閉曲目模擬進度，並在發生錯誤時 timeupdates。
- [錯誤修正] 移除右鍵功能表
- [錯誤修正][AzureHtml5JS] 針對 PlayReady 內容設定的權杖無效時，將不會顯示錯誤訊息
- [錯誤修正][AzureHtml5JS] 在即時播放期間進入全螢幕時，不會讓視窗大小啟發學習法納入考量
- [錯誤修正][FlashSS] 已移除 Strobe Media Player 顯示的訊息，而只顯示 Azure 媒體播放器的訊息
- [錯誤修正][SilverlightSS] 搜尋超過持續時間或小於 0 時，無法取得 'seeked' 事件

## <a name="010-beta-release"></a>0.1.0 (Beta 版) ##

初始發行前版本

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)
