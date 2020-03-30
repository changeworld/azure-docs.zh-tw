---
title: Azure 媒體服務 LiveEvent 類型 | Microsoft Docs
description: 在 Azure 媒體服務中，可以將即時事件設置為*傳遞*或*即時編碼*。 本文顯示了比較即時事件種類的詳細表。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244650"
---
# <a name="live-event-types-comparison"></a>實況活動類型比較

在 Azure 媒體服務中，[可以將即時事件](https://docs.microsoft.com/rest/api/media/liveevents)設置為*傳遞*（本地即時編碼器發送多個位元速率流）或*即時編碼*（本地即時編碼器發送單個位元速率流）。 

本文比較了即時事件種類的功能。

## <a name="types-comparison"></a>類型比較 

下表比較了即時事件種類的功能。 類型在創建期間使用[LiveEvent 編碼類型](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)設置 ：

* **LiveEvent 編碼類型.無**- 本地即時編碼器發送多個位元速率流。 引入的流通過即時事件，無需任何進一步處理。 也稱為傳遞即時事件。
* **LiveEventEncodeType.標準**- 本地即時編碼器向即時事件發送單個位元速率流，媒體服務創建多個位元速率流。 如果貢獻源的解析度為 720p 或更高，**則 Default720p**預設將對一組 6 解析度/位元速率對進行編碼（詳細資訊如下文章後面）。
* **LiveEventEncodeType.Premium1080p** - 本地即時編碼器向即時事件發送單個位元速率流，媒體服務創建多個位元速率流。 Default1080p 預設指定解析度/位元速率對的輸出集（詳細資訊如下文章後面）。 

| 功能 | 傳遞實況活動 | 標準或高級 1080p 現場活動 |
| --- | --- | --- |
| 單一位元速率輸入會在雲端編碼為多重位元速率 |否 |是 |
| 發佈摘要的影片最大解析度 |4K (4096x2160，每秒 60 格畫面) |1080p (1920x1088，每秒 30 格畫面)|
| 發佈摘要中層級數的建議上限|最多 12 個|一個音訊|
| 輸出中的層級數上限| 與輸入相同|最多 6 個（請參閱下面的系統預設）|
| 發佈摘要的彙總頻寬上限|60 Mbps|N/A|
| 發佈中單一層級的最大位元速率 |20 Mbps|20 Mbps|
| 支援多種語言音訊播放軌|是|否|
| 支援的輸入視訊轉碼器 |H.264/AVC 和 H.265/HEVC|H.264/AVC|
| 支援的輸出視訊轉碼器|與輸入相同|H.264/AVC|
| 支援的視訊位元深度、輸入及輸出|最多 10 位元，包括 HDR 10/HLG|8 位元|
| 支援的輸入音訊轉碼器|AAC-LC、HE-AAC v1、HE-AAC v2|AAC-LC、HE-AAC v1、HE-AAC v2|
| 支援的輸出音訊轉碼器|與輸入相同|AAC-LC|
| 輸出影片的影片解析度上限|與輸入相同|標準 - 720p， 高級 1080p - 1080p|
| 輸入視頻的最大畫面播放速率|60 幀/秒|標準或高級 1080p - 30 幀/秒|
| 輸入通訊協定|RTMP，分散式 MP4 (Smooth Streaming)|RTMP，分散式 MP4 (Smooth Streaming)|
| Price|請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) 並按一下 [即時影片] 索引標籤|請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) 並按一下 [即時影片] 索引標籤|
| 最長執行時間| 24 小時 x 365 天，即時線性 | 24 小時 x 365 天，即時線性（預覽）|
| 能夠透過內嵌的 CEA 608/708 字幕資料傳遞|是|是|
| 能夠打開即時轉錄|是|是|
| 插入靜態圖像支援|否|否|
| 支援透過 API 發出廣告訊號| 否|否|
| 支援透過 SCTE-35 頻內訊息發出廣告訊號|是|是|
| 在比重摘要內能從短暫延遲中復原的能力|是|部分|
| 支援未統一輸入的 GOP|是|否 – 輸入必須有固定的 GOP 持續期間|
| 支援變動畫面播放速率輸入|是|否 – 輸入必須為固定畫面播放速率。 輕微的差異可以接受，例如：處於高速動態場景的情況。 但是，貢獻源不能降低畫面播放速率（例如，將畫面播放速率降至 15 幀/秒）。|
| 在遺失輸入摘要時自動關閉實況活動|否|經過 12 個小時，如果沒有 LiveOutput 仍在執行|

## <a name="system-presets"></a>系統預設

即時編碼器輸出中包含的解析度和位元速率由[預設名稱](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding)確定。 如果使用**標準**即時編碼器（LiveEventEncodeType.Standard），則*預設720p*預設指定一組 6 解析度/位元速率對，如下所述。 否則，如果使用高級**1080p**即時編碼器（LiveEventEncodeType.Premium1080p），則*預設 1080p*預設指定解析度/位元速率對的輸出集。

> [!NOTE]
> 如果"預設 1080p"預設已設置為標準即時編碼，則無法將預設 1080p 預設應用於即時事件 - 您將收到錯誤。 如果您嘗試將 Default720p 預設應用於高級 1080p 即時編碼器，您也會收到錯誤。

### <a name="output-video-streams-for-default720p"></a>輸出預設720p的視頻流

如果貢獻源的解析度為 720p 或更高，**則 Default720p**預設將編碼到以下 6 層中。 在下表中，Bitrate 以 kbps 表示，MaxFPS 表示允許的最大畫面播放速率（以幀/秒為單位），設定檔表示使用的 H.264 設定檔。

| Bitrate | 寬度 | 高度 | MaxFPS | 設定檔 |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |高 |
| 2200 |960 |540 |30 |高 |
| 1350 |704 |396 |30 |高 |
| 850 |512 |288 |30 |高 |
| 550 |384 |216 |30 |高 |
| 200 |340 |192 |30 |高 |

> [!NOTE]
> 如果需要自訂即時編碼預設，請通過 Azure 門戶打開支援票證。 您應指定解析度和位元速率的所需資料表。 請確認只有一個圖層為 720p，且最多只有 6 個圖層。 還要指定您正在請求標準即時編碼器的預設。
> 位元速率和解析度的特定值可能會隨時間而調整

### <a name="output-video-streams-for-default1080p"></a>輸出預設1080p的視頻流

如果貢獻源的解析度為 1080p，**則預設 1080p**預設將編碼到以下 6 層中。

| Bitrate | 寬度 | 高度 | MaxFPS | 設定檔 |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |高 |
| 3000 |1280 |720 |30 |高 |
| 1600 |960 |540 |30 |高 |
| 800 |640 |360 |30 |高 |
| 400 |480 |270 |30 |高 |
| 200 |320 |180 |30 |高 |

> [!NOTE]
> 如果需要自訂即時編碼預設，請通過 Azure 門戶打開支援票證。 您應指定解析度和位元速率的所需資料表。 請驗證 1080p 處只有一個圖層，最多 6 層。 還要指定您正在請求高級 1080p 即時編碼器的預設。
> 位元速率和解析度的特定值可能會隨時間而調整。

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>輸出預設720p和預設1080p的音訊流

對於*Default720p*和*Default1080p*預設，音訊以 128 kbps 編碼為身歷聲 AAC-LC。 取樣速率遵循貢獻源中音軌的取樣速率。

## <a name="implicit-properties-of-the-live-encoder"></a>即時編碼器的隱式屬性

上一節介紹可通過預設顯式控制的即時編碼器的屬性，例如圖層數、解析度和位元速率。 本節闡明隱式屬性。

### <a name="group-of-pictures-gop-duration"></a>圖片組 （GOP） 持續時間

即時編碼器遵循貢獻源的[GOP](https://en.wikipedia.org/wiki/Group_of_pictures)結構 - 這意味著輸出圖層將具有相同的 GOP 持續時間。 因此，建議您配置本地編碼器以生成具有固定 GOP 持續時間（通常為 2 秒）的貢獻源。 這將確保來自服務的傳出 HLS 和 MPEG DASH 流也有固定的 GOP 持續時間。 大多數設備都可能容忍 GOP 持續時間的微小變化。

### <a name="frame-rate"></a>畫面播放速率

即時編碼器還遵循貢獻源中各個視頻幀的持續時間 - 這意味著輸出圖層將具有具有相同持續時間的幀。 因此，建議您配置本地編碼器以生成具有固定畫面播放速率（最多 30 幀/秒）的貢獻源。 這將確保來自服務的傳出 HLS 和 MPEG DASH 流也有固定畫面播放速率持續時間。 大多數設備可以容忍畫面播放速率的微小變化，但不能保證即時編碼器會產生正確播放的輸出。 本地即時編碼器不應丟棄幀（例如。 在低電池條件下），或以任何方式改變畫面播放速率。

### <a name="resolution-of-contribution-feed-and-output-layers"></a>貢獻饋送和輸出層的解析度

即時編碼器配置為避免向上轉換投送。 因此，輸出圖層的最大解析度不會超過貢獻源的解析度。

例如，如果將 720p 的饋送發送到配置為 Default1080p 即時編碼的即時事件，則輸出將只有 5 個圖層，從 3Mbps 的 720p 開始，在 200 kbp 時下降到 1080p。 或者，如果將 360p 的饋送發送到配置為標準即時編碼的即時事件，則輸出將包含 3 層（解析度為 288p、216p 和 192p）。 在退化情況下，如果將 160x90 圖元的貢獻源發送到標準即時編碼器，則輸出將包含一個解析度為 160x90 的圖層，其位元速率與饋送的位元速率相同。

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>貢獻饋送和輸出層的位元速率

即時編碼器配置為遵循預設中的位元速率設置，而不考慮饋送的位元速率。 因此，輸出圖層的位元速率可能超過貢獻源的位元速率。 例如，如果以 1 Mbps 的解析度 720p 發送投送，則輸出層將保持不變，與上[表中](live-event-types-comparison.md#output-video-streams-for-default720p)相同。

## <a name="next-steps"></a>後續步驟

[即時串流概觀](live-streaming-overview.md)
