---
title: Azure 媒體播放器示範
description: 此頁面包含 Azure 媒體播放機示範的連結清單。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: article
ms.date: 04/24/2020
ms.openlocfilehash: 584748b23f526e6f03b543b8298927e3f202f743
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82139299"
---
# <a name="azure-media-player-demos"></a>Azure 媒體播放器示範

以下是 Azure 媒體播放機示範的連結清單。 您可以從 GitHub 下載所有的[Azure 媒體播放機範例](https://github.com/Azure-Samples/azure-media-player-samples)。

## <a name="demo-listing"></a>示範清單

| 範例名稱 | 以程式設計方式透過 JavaScript | 透過 HTML5 video 元素的靜態 | 描述 |
| ------------|----------------------------|-------------------------------------|--------------|
| 基本 |
| 設定來源 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_setsource.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_setsource.html) |播放未受保護的內容。|
| 功能 |
| VOD 廣告插入-龐大 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_vast_ads_vod.html) | 不適用 | 將大量廣告的前置和後置內容插入 VOD 資產中。 |
| 播放速度 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_playback_speed.html)| 不適用 | 可讓檢視器控制他們在觀看影片的速度。 |
| AMP 清除面板 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_flush_skin.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_flush_skin.html) | 啟用新的 AMP 外觀。 **注意：** 只有 AMP 版本 2.1.0 + 支援 AMP 清除 |
| 字幕和子字幕 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_webvtt.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_webvtt.html) | 使用 WebVTT 字幕播放。
| 即時 CEA 708 的標題 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_live_captions.html) | 不適用 | 使用即時 CEA 708 輸入標題，並以靠左對齊的方式播放標題。 |
| 使用漸進式回溯的串流 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveFallback.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveFallback.html) | 如果平臺上不支援串流，則為漸進式播放的基本設定，其會回溯。 |
| 漸進式視頻 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveVideo.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveVideo.html) | 播放漸進式音訊。 |
| 漸進式音訊 MP3 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveAudio.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveAudio.html) | 漸進式音訊 MP3 的播放。 |
| DD + | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_dolbyDigitalPlus.html) | 不適用 | 使用 DD + 音訊播放內容。 |
| 選項 |
| 啟發學習法設定檔 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_heuristicsProfile.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_heuristicsProfile.html) | 變更啟發學習法設定檔 |
| 當地語系化 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_localization.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_localization.html) |
設定當地語系化 |
| 音訊追蹤功能表 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiAudio.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_multiAudio.html) |
示範如何在預設面板上顯示 [音訊追蹤] 功能表的選項。 |
| 熱鍵 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_hotKeys.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_hotKeys.html) | 這個範例示範如何設定播放機中啟用的快速鍵 |
| 事件、記錄和診斷 |
| 註冊事件 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_registerEvents.html) | 不適用 | 使用事件接聽程式播放。 |
| 記錄 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_logging.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_logging.html) | 開啟 [詳細資訊記錄至主控台]。 |
| 診斷 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_diagnostics.html) | 不適用 | 取得診斷資料。 此範例僅適用于某些 techs。 |
| AES |
| AES 無權杖 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_notoken.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_notoken.html) | 不使用權杖來播放 AES 內容。 |
| AES 權杖 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token.html) | 使用權杖播放 AES 內容。 |
| AES HLS proxy 模擬 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_withHLSProxy.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_withHLSProxy.html) | 使用權杖播放 AES 內容，顯示 HLS 的 proxy，讓權杖可與 iOS 裝置搭配使用。 |
| AES 權杖強制 flash | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_forceFlash.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_forceFlash.html) | 使用權杖播放 AES 內容，強制執行 flashSS 技術。 |
| DRM |
| 具有 PlayReady、Widevine 和 FairPlay 的多重 DRM | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | 不使用權杖來播放 DRM 內容，使用 PlayReady、Widevine 和 FairPlay 標頭。 |
| PlayReady 沒有權杖 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken.html) | 不使用權杖來播放 PlayReady 內容。 |
| PlayReady 無權杖強制 Silverlight | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken_forceSilverlight.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken_forceSilverlight.html) | 不使用權杖來播放 PlayReady 內容，強制執行 silverlightSS 技術。 |
| PlayReady 權杖 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token.html) | 使用權杖播放 PlayReady 內容。 |
| PlayReady 權杖強制 Silverlight | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token_forceSilverlight.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token_forceSilverlight.html) | 使用權杖播放 PlayReady 內容，強制執行 silverlightSS 技術。 |
| 通訊協定和技術 |
| 變更 techOrder | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_techOrder.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_techOrder.html) | 變更技術順序 |
| 只在 UrlRewriter 中強制執行 MPEG-虛線 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceDash.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_forceDash.html) | 僅使用破折號通訊協定播放未受保護的內容。 |
| 在 UrlRewriter 中排除 MPEG 虛線 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceNoDash.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_forceNoDash.html) | 僅使用平滑和 HLS 通訊協定播放未受保護的內容。 |
| 多個傳遞原則 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_multipleDeliveryPolicy.html) | [靜態](https://amp.azure.net/libs/amp/latest/samples/videotag_multipleDeliveryPolicy.html) | 以 Azure 媒體服務的多個傳遞原則來設定來源內容 |
| 以程式設計方式選取 |
| 選取文字追蹤 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectTextTrack.html) | 不適用 | 從曲目清單中選取 WebVTT 追蹤。 |
| 選取位元速率 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectBitrate.html) | 不適用 | 從位元速率清單中選取位元速率。 此範例僅適用于某些 techs。 |
| 選取音訊串流 | [效果](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectAudioStream.html) | 不適用 | 從可用的音訊資料流程清單中選取音訊串流。 此範例僅適用于某些 techs。 |

## <a name="next-steps"></a>後續步驟

<!---Some context for the following links goes here--->
- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)