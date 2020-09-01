---
title: Azure 媒體服務 LiveEvent 類型 | Microsoft Docs
description: 在 Azure 媒體服務中，實況活動可以設定為 *通過* 或 *即時編碼*。 本文顯示比較即時事件種類的詳細資料表。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 41df31cde95ae7ed1d05dac572718622067194c9
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265247"
---
# <a name="live-event-types-comparison"></a>實況活動類型比較

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在 Azure 媒體服務中，[實況活動](/rest/api/media/liveevents)可以設定為傳遞 (內部部署即時編碼器會傳送多位元率串流) 或*即時編碼* (內部部署即時編碼器會*傳送*單一位元速率串流) 。 

本文將比較實況活動類型的功能。

## <a name="types-comparison"></a>類型比較 

下表比較即時事件種類的功能。 使用 [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype)在建立期間設定類型：

* **LiveEventEncodingType** ：內部部署即時編碼器會傳送多位元率串流。 內嵌資料流程會通過實況活動，而不需要進一步處理。 也稱為傳遞實況活動。
* **LiveEventEncodingType** ：內部部署即時編碼器會將單一位元速率串流傳送至實況活動，而媒體服務會建立多位元率串流。 如果投稿摘要是720p 或更高的解析度， **Default720p** 預設會將一組6個解析/位元速率組編碼 (詳細資料，請參閱本文稍後的) 。
* **LiveEventEncodingType. Premium1080p** -內部部署即時編碼器會將單一位元速率串流傳送至實況活動，而媒體服務會建立多位元率串流。 Default1080p 預設值會指定一組解析/位元速率組的輸出集 (詳細資料，請參閱) 的後續文章。 

| 功能 | 傳遞實況活動 | 標準或 Premium1080p 實況活動 |
| --- | --- | --- |
| 單一位元速率輸入會在雲端編碼為多重位元速率 |否 |是 |
| 發佈摘要的影片最大解析度 |4K (4096x2160，每秒 60 格畫面) |1080p (1920x1088，每秒 30 格畫面)|
| 發佈摘要中層級數的建議上限|最多 12 個|一個音訊|
| 輸出中的層級數上限| 與輸入相同|最多6個 (請參閱下方的系統預設) |
| 發佈摘要的彙總頻寬上限|60 Mbps|N/A|
| 發佈中單一層級的最大位元速率 |20 Mbps|20 Mbps|
| 支援多種語言音訊播放軌|是|否|
| 支援的輸入視訊轉碼器 |H.264/AVC 和 H.265/HEVC|H.264/AVC|
| 支援的輸出視訊轉碼器|與輸入相同|H.264/AVC|
| 支援的視訊位元深度、輸入及輸出|最多 10 位元，包括 HDR 10/HLG|8 位元|
| 支援的輸入音訊轉碼器|AAC-LC、HE-AAC v1、HE-AAC v2|AAC-LC、HE-AAC v1、HE-AAC v2|
| 支援的輸出音訊轉碼器|與輸入相同|AAC-LC|
| 輸出影片的影片解析度上限|與輸入相同|標準-720p、Premium1080p-1080p|
| 輸入影片的最大畫面播放速率|每秒60個畫面格|標準或 Premium1080p-每秒30個畫面格|
| 輸入通訊協定|RTMP，分散式 MP4 (Smooth Streaming)|RTMP，分散式 MP4 (Smooth Streaming)|
| 價格|請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) 並按一下 [即時影片] 索引標籤|請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) 並按一下 [即時影片] 索引標籤|
| 最長執行時間| 24 小時 x 365 天，即時線性 | 24小時 x 365 天，即時線性 (預覽) |
| 能夠透過內嵌的 CEA 608/708 字幕資料傳遞|是|是|
| 開啟即時轉譯的能力|是|是|
| 插入靜態圖像支援|否|否|
| 支援透過 API 發出廣告訊號| 否|否|
| 支援透過 SCTE-35 頻內訊息發出廣告訊號|是|是|
| 在比重摘要內能從短暫延遲中復原的能力|是|部分|
| 支援未統一輸入的 GOP|是|否 – 輸入必須有固定的 GOP 持續期間|
| 支援變動畫面播放速率輸入|是|否 – 輸入必須為固定畫面播放速率。 輕微的差異可以接受，例如：處於高速動態場景的情況。 但投稿摘要無法捨棄畫面播放速率 (例如15個框架/秒) 。|
| 在遺失輸入摘要時自動關閉實況活動|否|經過 12 個小時，如果沒有 LiveOutput 仍在執行|

## <a name="system-presets"></a>系統預設

即時編碼器的輸出中所包含的解析度和位元速率是由 [presetName](/rest/api/media/liveevents/create#liveeventencoding)所決定。 如果使用 **標準** 即時編碼器 (LiveEventEncodingType) ，則 *Default720p* 預設值會指定一組6解析/位元速率配對，如下所述。 否則，如果使用 **Premium1080p** 即時編碼器 (LiveEventEncodingType. Premium1080p) ，則 *Default1080p* 預設值會指定解析/位元速率配對的輸出集。

> [!NOTE]
> 如果已設定標準即時編碼，則無法將 Default1080p 預設值套用至實況活動-您會收到錯誤訊息。 如果您嘗試將 Default720p 預設值套用至 Premium1080p 即時編碼器，您也會收到錯誤。

### <a name="output-video-streams-for-default720p"></a>輸出 Default720p 的影片串流

如果投稿摘要是720p 或更高的解析度， **Default720p** 預設會將摘要編碼為下列6層。 在下表中，位元速率是以 kbps 為單位，MaxFPS 代表每秒畫面格數 (允許的畫面播放速率上限) ，設定檔代表使用的 h.264 設定檔。

| Bitrate | 寬度 | 高度 | MaxFPS | 設定檔 |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |高 |
| 2200 |960 |540 |30 |高 |
| 1350 |704 |396 |30 |高 |
| 850 |512 |288 |30 |高 |
| 550 |384 |216 |30 |高 |
| 200 |340 |192 |30 |高 |

> [!NOTE]
> 如果您需要自訂即時編碼預設值，請透過 Azure 入口網站開啟支援票證。 您應指定解析度和位元速率的所需資料表。 請確認只有一個圖層為 720p，且最多只有 6 個圖層。 也請指定您要求標準即時編碼器的預設值。
> 位元速率和解決方式的特定值可能會隨著時間而調整

### <a name="output-video-streams-for-default1080p"></a>輸出 Default1080p 的影片串流

如果投稿摘要是1080p 解析度， **Default1080p** 預設會將摘要編碼為下列6層。

| Bitrate | 寬度 | 高度 | MaxFPS | 設定檔 |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |高 |
| 3000 |1280 |720 |30 |高 |
| 1600 |960 |540 |30 |高 |
| 800 |640 |360 |30 |高 |
| 400 |480 |270 |30 |高 |
| 200 |320 |180 |30 |高 |

> [!NOTE]
> 如果您需要自訂即時編碼預設值，請透過 Azure 入口網站開啟支援票證。 您應指定解析度和位元速率的所需資料表。 請確認只有一個層級為1080p，最多6層。 也請指定您要求 Premium1080p 即時編碼器的預設值。
> 位元速率和解決方式的特定值可能會隨著時間而調整。

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Default720p 和 Default1080p 的輸出音訊串流

針對 *Default720p* 和 *Default1080p* 預設，音訊會以 128 KBPS 編碼為身歷聲 AAC-LC。 取樣率會在「投稿」摘要中的音訊播放軌之後。

## <a name="implicit-properties-of-the-live-encoder"></a>即時編碼器的隱含屬性

上一節描述可透過預設值（例如圖層、解析度和位元速率的數目）明確控制的即時編碼器屬性。 本節說明隱含屬性。

### <a name="group-of-pictures-gop-duration"></a> (GOP) 持續時間的圖片群組

即時編碼器會遵循投稿摘要的 [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) 結構，這表示輸出圖層會有相同的 GOP 持續時間。 因此，建議您設定內部部署編碼器，以產生具有固定 GOP 持續期間的投稿摘要， (通常是2秒) 。 這可確保來自服務的傳出 HLS 和 MPEG 破折號資料流程也有固定的 GOP 持續時間。 大部分的裝置都可能容許 GOP 持續時間中的小型變化。

### <a name="frame-rate"></a>畫面播放速率

即時編碼器也會遵循在投稿摘要中個別影片畫面的持續時間，這表示輸出圖層會有相同持續時間的框架。 因此，建議您設定內部部署編碼器，以產生具有固定畫面播放速率的投稿摘要， (最多30個畫面格/秒) 。 這可確保來自服務的傳出 HLS 和 MPEG 破折號資料流程也有固定的畫面播放速率持續時間。 大部分的裝置都可以容許畫面播放速率中的小型變化，但是無法保證即時編碼器會產生可正常播放的輸出。 您的內部部署即時編碼器不應卸載框架 (例如 在電力偏低的情況下) 或以任何方式改變畫面播放速率。

### <a name="resolution-of-contribution-feed-and-output-layers"></a>解決貢獻摘要和輸出層

即時編碼器設定為避免 upconverting 投稿摘要。 因此，輸出層的最大解析度將不會超過投稿摘要的最大解析度。

例如，如果您將720p 的投稿摘要傳送到設定為 Default1080p 即時編碼的即時事件，則輸出只會有5層，從3Mbps 開始，到1080p （以 200 kbps）。 或者，如果您將360p 的投稿摘要傳送到設定為標準即時編碼的即時事件，輸出將會包含3層 (288p、216p 和 192p) 的解析度。 在退化案例中，如果您將160x90 圖元的投稿摘要傳送給標準即時編碼器，則輸出將會在160x90 解析度上包含一個圖層，其比投稿摘要的位元速率相同。

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>貢獻摘要和輸出層的位元速率

即時編碼器設定為接受預設值中的位元速率設定，而不考慮貢獻摘要的位元速率。 因此，輸出層的位元速率可能會超過投稿摘要的位元速率。 例如，如果您以720p （1 Mbps）的解析度傳送投稿摘要，輸出層將維持與上 [表](live-event-types-comparison.md#output-video-streams-for-default720p) 相同。

## <a name="next-steps"></a>後續步驟

[即時串流概觀](live-streaming-overview.md)
