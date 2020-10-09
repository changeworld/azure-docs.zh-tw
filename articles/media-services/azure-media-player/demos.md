---
title: Azure 媒體播放器示範
description: 此頁面包含 Azure 媒體播放機示範的連結清單。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: article
ms.date: 04/24/2020
ms.openlocfilehash: 584748b23f526e6f03b543b8298927e3f202f743
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82139299"
---
# <a name="azure-media-player-demos"></a>Azure 媒體播放器示範

以下是 Azure 媒體播放機示範的連結清單。 您可以從 GitHub 下載所有的 [Azure 媒體播放機範例](https://github.com/Azure-Samples/azure-media-player-samples) 。

## <a name="demo-listing"></a>示範清單

| 範例名稱 | 透過 JavaScript 以程式設計方式 | 靜態 via HTML5 video 元素 | 描述 |
| ------------|----------------------------|-------------------------------------|--------------|
| 基本 |
| 設定來源 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_setsource.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_setsource.html) |播放未受保護的內容。|
| 特性 |
| VOD 廣告插入-大型 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_vast_ads_vod.html) | N/A | 將內建的大量廣告插入 VOD 資產中。 |
| 播放速度 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_playback_speed.html)| N/A | 可讓檢視器控制觀看影片的速度。 |
| AMP Flush 外觀 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_flush_skin.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_flush_skin.html) | 啟用新的 AMP 外觀。 **注意：** Amp 2.1.0 中只支援 AMP 排清 |
| 標題和字幕 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_webvtt.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_webvtt.html) | 使用 WebVTT 字幕播放。
| Live CEA 708 標題 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_live_captions.html) | N/A | 使用 live CEA 708 輸入字幕以靠左對齊的方式播放。 |
| 具有漸進式回復的串流 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveFallback.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveFallback.html) | 當平臺上不支援串流時，具有漸進式的自動調整播放基本設定。 |
| 漸進式視頻 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveVideo.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveVideo.html) | 播放漸進式音訊數量。 |
| 漸進式音訊 MP3 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveAudio.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveAudio.html) | 播放漸進式音訊 MP3。 |
| DD + | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_dolbyDigitalPlus.html) | N/A | 使用 DD + 音訊播放內容。 |
| 選項。 |
| 啟發學習法設定檔 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_heuristicsProfile.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_heuristicsProfile.html) | 變更啟發學習法設定檔 |
| 當地語系化 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_localization.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_localization.html) |
設定當地語系化 |
| 音訊曲目功能表 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiAudio.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_multiAudio.html) |
顯示如何在預設面板上顯示 [音訊播放軌] 功能表的選項。 |
| 熱鍵 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_hotKeys.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_hotKeys.html) | 此範例示範如何設定在播放玩家中啟用的熱鍵 |
| 事件、記錄和診斷 |
| 註冊事件 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_registerEvents.html) | N/A | 使用事件接聽程式播放。 |
| 記錄 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_logging.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_logging.html) | 開啟詳細資訊記錄至主控台。 |
| 診斷 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_diagnostics.html) | N/A | 取得診斷資料。 此範例僅適用于一些 techs。 |
| AES |
| AES 無權杖 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_notoken.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_notoken.html) | 不使用權杖來播放 AES 內容。 |
| AES 權杖 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token.html) | 使用權杖播放 AES 內容。 |
| AES HLS proxy 模擬 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_withHLSProxy.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_withHLSProxy.html) | 使用權杖播放 AES 內容，顯示 HLS 的 proxy，讓權杖可以與 iOS 裝置搭配使用。 |
| AES 權杖強制 flash | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_forceFlash.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_forceFlash.html) | 使用權杖播放 AES 內容，強制執行 flashSS 技術。 |
| DRM |
| 具有 PlayReady、Widevine 和 FairPlay 的多重 DRM | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | 使用 PlayReady、Widevine 和 FairPlay 標頭來播放沒有權杖的 DRM 內容。 |
| PlayReady 無權杖 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken.html) | 不使用權杖來播放 PlayReady 內容。 |
| PlayReady 無權杖強制 Silverlight | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken_forceSilverlight.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken_forceSilverlight.html) | 不使用權杖來播放 PlayReady 內容，強制執行 silverlightSS 技術。 |
| PlayReady 權杖 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token.html) | 使用權杖播放 PlayReady 內容。 |
| PlayReady 權杖強制 Silverlight | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token_forceSilverlight.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token_forceSilverlight.html) | 使用權杖播放 PlayReady 內容，並強制執行 silverlightSS 技術。 |
| 通訊協定與技術 |
| 變更 techOrder | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_techOrder.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_techOrder.html) | 變更技術訂單 |
| 只在 UrlRewriter 中強制執行 MPEG 虛線 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceDash.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_forceDash.html) | 只使用破折號通訊協定播放未受保護的內容。 |
| 在 UrlRewriter 中排除 MPEG 虛線 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceNoDash.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_forceNoDash.html) | 只使用流暢且 HLS 的通訊協定播放未受保護的內容。 |
| 多個傳遞原則 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_multipleDeliveryPolicy.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_multipleDeliveryPolicy.html) | 使用具有多個傳遞原則的內容設定來源 Azure 媒體服務 |
| 以程式設計方式選取 |
| 選取文字播放軌 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectTextTrack.html) | N/A | 從追蹤清單中選取 WebVTT 軌。 |
| 選取位元速率 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectBitrate.html) | N/A | 從位元速率清單中選取位元速率。 此範例僅適用于一些 techs。 |
| 選取音訊串流 | [動態](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectAudioStream.html) | N/A | 從可用的音訊串流清單中選取音訊串流。 此範例僅適用于一些 techs。 |

## <a name="next-steps"></a>後續步驟

<!---Some context for the following links goes here--->
- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)