---
title: Azure 媒體服務 LiveEvent 類型 | Microsoft Docs
description: 在 Azure 媒體服務中，即時事件可以設定為*傳遞*或*即時編碼*。 本文顯示比較即時事件種類的詳細資料表。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: a28d4d96f643c12eeb6aa542db2c6af06f4fd954
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78244650"
---
# <a name="live-event-types-comparison"></a>實況活動類型比較

在 Azure 媒體服務中，[即時事件](https://docs.microsoft.com/rest/api/media/liveevents)可以設定為傳遞（內部部署即時編碼器會傳送多位元率串流）或*即時編碼*（內部部署即時編碼器會*傳送*單一位元速率串流）。 

這篇文章會比較即時事件種類的功能。

## <a name="types-comparison"></a>類型比較 

下表比較即時事件種類的功能。 在建立期間，會使用[LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)來設定類型：

* **LiveEventEncodingType。 None** -內部部署即時編碼器會傳送多位元率串流。 內嵌串流會通過即時事件，而不需要進行任何進一步的處理。 也稱為傳遞實況活動。
* **LiveEventEncodingType** -內部部署即時編碼器會將單一位元速率串流傳送至即時事件，媒體服務會建立多位元率串流。 如果「貢獻摘要」是720p 或更高解析度，則**Default720p**預設會將一組6個解析/位元速率配對（詳細資料會在本文稍後所述）進行編碼。
* **LiveEventEncodingType. Premium1080p** -內部部署即時編碼器會將單一位元速率串流傳送至即時事件，媒體服務會建立多位元率串流。 Default1080p 預設值會指定解析/位元速率配對的輸出集（詳細資料會在本文稍後所述）。 

| 功能 | 傳遞實況活動 | 標準或 Premium1080p 即時事件 |
| --- | --- | --- |
| 單一位元速率輸入會在雲端編碼為多重位元速率 |否 |是 |
| 發佈摘要的影片最大解析度 |4K (4096x2160，每秒 60 格畫面) |1080p (1920x1088，每秒 30 格畫面)|
| 發佈摘要中層級數的建議上限|最多 12 個|一個音訊|
| 輸出中的層級數上限| 與輸入相同|最多6個（請參閱下方的系統預設值）|
| 發佈摘要的彙總頻寬上限|60 Mbps|N/A|
| 發佈中單一層級的最大位元速率 |20 Mbps|20 Mbps|
| 支援多種語言音訊播放軌|Yes|否|
| 支援的輸入視訊轉碼器 |H.264/AVC 和 H.265/HEVC|H.264/AVC|
| 支援的輸出視訊轉碼器|與輸入相同|H.264/AVC|
| 支援的視訊位元深度、輸入及輸出|最多 10 位元，包括 HDR 10/HLG|8 位元|
| 支援的輸入音訊轉碼器|AAC-LC、HE-AAC v1、HE-AAC v2|AAC-LC、HE-AAC v1、HE-AAC v2|
| 支援的輸出音訊轉碼器|與輸入相同|AAC-LC|
| 輸出影片的影片解析度上限|與輸入相同|標準-720p、Premium1080p-1080p|
| 輸入影片的最大畫面播放速率|60框架/秒|Standard 或 Premium1080p-每秒30個畫面|
| 輸入通訊協定|RTMP，分散式 MP4 (Smooth Streaming)|RTMP，分散式 MP4 (Smooth Streaming)|
| Price|請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) 並按一下 [即時影片] 索引標籤|請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) 並按一下 [即時影片] 索引標籤|
| 最長執行時間| 24 小時 x 365 天，即時線性 | 24小時 x 365 天，即時線性（預覽）|
| 能夠透過內嵌的 CEA 608/708 字幕資料傳遞|Yes|Yes|
| 開啟即時轉譯的能力|Yes|Yes|
| 插入靜態圖像支援|否|否|
| 支援透過 API 發出廣告訊號| 否|否|
| 支援透過 SCTE-35 頻內訊息發出廣告訊號|Yes|Yes|
| 在比重摘要內能從短暫延遲中復原的能力|是|部分|
| 支援未統一輸入的 GOP|Yes|否 – 輸入必須有固定的 GOP 持續期間|
| 支援變動畫面播放速率輸入|Yes|否 – 輸入必須為固定畫面播放速率。 輕微的差異可以接受，例如：處於高速動態場景的情況。 但是，貢獻摘要無法捨棄畫面播放速率（例如，到15個畫面）。|
| 在遺失輸入摘要時自動關閉實況活動|No|經過 12 個小時，如果沒有 LiveOutput 仍在執行|

## <a name="system-presets"></a>系統預設

即時編碼器的輸出中所包含的解決方式和位元速率是由[presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding)所決定。 如果使用**標準**即時編碼器（LiveEventEncodingType），則*Default720p*預設值會指定一組6個解析度/位元速率配對，如下所述。 否則，如果使用**Premium1080p**即時編碼器（LiveEventEncodingType. Premium1080p），則*Default1080p*預設值會指定解析/位元速率配對的輸出集。

> [!NOTE]
> 如果已針對標準即時編碼進行設定，您無法將 Default1080p 預設值套用至即時事件-您會收到錯誤。 如果您嘗試將 Default720p 預設值套用至 Premium1080p 即時編碼器，您也會收到錯誤。

### <a name="output-video-streams-for-default720p"></a>輸出 Default720p 的影片串流

如果「貢獻摘要」是720p 或更高解析度，則**Default720p**預設會將摘要編碼成下列6層。 在下表中，位元速率是以 kbps 為單位，MaxFPS 代表允許的畫面播放速率上限（以框架/秒為單位），設定檔代表所使用的 h.264 設定檔。

| Bitrate | 寬度 | 高度 | MaxFPS | 設定檔 |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |高 |
| 2200 |960 |540 |30 |高 |
| 1350 |704 |396 |30 |高 |
| 850 |512 |288 |30 |高 |
| 550 |384 |216 |30 |高 |
| 200 |340 |192 |30 |高 |

> [!NOTE]
> 如果您需要自訂即時編碼預設值，請透過 Azure 入口網站開啟支援票證。 您應指定解析度和位元速率的所需資料表。 請確認只有一個圖層為 720p，且最多只有 6 個圖層。 此外，請指定您要求標準即時編碼器的預設值。
> 位元速率和解決方式的特定值可能會隨著時間調整

### <a name="output-video-streams-for-default1080p"></a>輸出 Default1080p 的影片串流

如果「貢獻摘要」是1080p 解析，則**Default1080p**預設會將摘要編碼成下列6個層級。

| Bitrate | 寬度 | 高度 | MaxFPS | 設定檔 |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |高 |
| 3000 |1280 |720 |30 |高 |
| 1600 |960 |540 |30 |高 |
| 800 |640 |360 |30 |高 |
| 400 |480 |270 |30 |高 |
| 200 |320 |180 |30 |高 |

> [!NOTE]
> 如果您需要自訂即時編碼預設值，請透過 Azure 入口網站開啟支援票證。 您應指定解析度和位元速率的所需資料表。 請確認只有一個圖層為1080p，最多有6個層級。 此外，請指定您要要求 Premium1080p 即時編碼器的預設值。
> 位元速率和解決方式的特定值可能會隨著時間調整。

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>輸出 Default720p 和 Default1080p 的音訊串流

針對*Default720p*和*Default1080p*預設值，音訊會編碼為身歷聲 AAC-LC 128 kbps。 取樣率會遵循「貢獻摘要」中的音訊播放軌。

## <a name="implicit-properties-of-the-live-encoder"></a>即時編碼器的隱含屬性

上一節描述可以透過預設值（例如圖層數目、解析度和位元速率）明確控制的即時編碼器屬性。 本節說明隱含屬性。

### <a name="group-of-pictures-gop-duration"></a>圖片群組（GOP）持續時間

即時編碼器會遵循貢獻摘要的[GOP](https://en.wikipedia.org/wiki/Group_of_pictures)結構-這表示輸出層會有相同的 GOP 持續時間。 因此，建議您設定內部部署編碼器，以產生具有固定 GOP 持續時間（通常是2秒）的貢獻摘要。 這可確保來自服務的傳出 HLS 和 MPEG 破折號資料流程也有固定的 GOP 持續時間。 大部分的裝置都可能容許 GOP 持續時間的小型變化。

### <a name="frame-rate"></a>畫面播放速率

即時編碼器也會遵循「貢獻摘要」中個別影片畫面的持續時間，這表示輸出層會有具有相同持續時間的框架。 因此，建議您設定內部部署編碼器，以產生具有固定畫面播放速率的貢獻摘要（每秒最多30個畫面）。 這可確保來自服務的傳出 HLS 和 MPEG 破折號資料流程也具有固定的畫面播放速率持續時間。 大部分的裝置都可以接受畫面播放速率中的小型變化，但不保證即時編碼器會產生可正確播放的輸出。 您的內部部署即時編碼器不應放置框架（例如 在電力偏低的情況下），或以任何方式改變畫面播放速率。

### <a name="resolution-of-contribution-feed-and-output-layers"></a>解決貢獻摘要和輸出層

即時編碼器已設定為避免 upconverting 發佈摘要。 因此，輸出層的最大解析度將不會超過發佈摘要的上限。

例如，如果您將720p 的貢獻摘要傳送到設定為 Default1080p 即時編碼的即時事件，則輸出只會有5個層級，從3Mbps 開始，在 200 kbps 到1080p 為止。 或者，如果您將360p 的貢獻摘要傳送至針對標準即時編碼所設定的即時事件，則輸出會包含3個層級（解析度為288p、216p 和192p）。 在退化案例中，如果您將160x90 圖元的貢獻摘要傳送至標準即時編碼器，輸出將會在160x90 解析中包含一個圖層，其比對發佈摘要的位元速率相同。

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>貢獻摘要和輸出層的位元速率

即時編碼器已設定為接受預設值中的位元速率設定，而不考慮發佈摘要的位元速率。 因此，輸出層的位元速率可能會超過發佈摘要的位元速率。 例如，如果您以 1 Mbps 的720p 解析度傳送貢獻摘要，輸出層將維持與上[表](live-event-types-comparison.md#output-video-streams-for-default720p)中的相同。

## <a name="next-steps"></a>後續步驟

[即時串流概觀](live-streaming-overview.md)
