---
title: 媒體服務建議的即時串流編碼器 -  Azure | Microsoft Docs
description: 深入了解 Azure 媒體服務所建議的即時串流處理內部部署編碼器
services: media-services
keywords: encoding;encoders;media;編碼;編碼器;媒體
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 5e16f1fb948ddb435c5002c16125b36fa61d50a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336252"
---
# <a name="tested-on-premises-live-streaming-encoders"></a>測試的本地即時流式處理器

在 Azure 媒體服務中，[即時事件](https://docs.microsoft.com/rest/api/media/liveevents) (通道) 代表處理即時串流內容的管線。 即時事件會以兩種方式之一收到即時輸入資料流。

* 內部部署即時編碼器會將多位元速率 RTMP 或 Smooth Streaming (分散式 MP4) 串流傳送到未啟用執行媒體服務即時編碼的即時事件。 內嵌的資料流會通過即時事件，而不需任何進一步處理。 此方法稱為 **傳遞**。 我們建議即時編碼器將多位元率流而不是單位元速率流發送到傳遞即時事件，以允許自我調整位元速率流到用戶端。 

    如果要對傳遞即時事件使用多位元率流，則必須同步不同位元速率上的視頻 GOP 大小和視頻片段，以避免播放端出現意外行為。

  > [!TIP]
  > 使用傳遞方法是進行即時串流的最經濟實惠方式。
 
* 本地即時編碼器向即時事件發送單位元速率流，該流啟用以以下格式之一對媒體服務執行即時編碼：RTMP 或平滑流（碎片 MP4）。 即時事件接著會執行即時編碼，將內送單一位元速率資料流編碼成多位元速率 (自適性) 視訊資料流。

本文討論了經過測試的本地即時流式處理編碼器。 有關如何驗證本地即時編碼器的說明，請參閱[驗證本地編碼器](become-on-premises-encoder-partner.md)

如需媒體服務即時編碼的詳細資訊，請參閱[使用媒體服務 v3 進行即時串流](live-streaming-overview.md)。

## <a name="encoder-requirements"></a>編碼器要求

使用 HTTPS 或 RTMPS 協定時，編碼器必須支援 TLS 1.2。

## <a name="live-encoders-that-output-rtmp"></a>輸出 RTMP 的即時編碼器

媒體服務建議使用下列其中一種具有 RTMP 作為輸出的即時編碼器。 支援的 URL 配置是 `rtmp://` 或 `rtmps://`。

透過 RTMP 串流處理時，請檢查防火牆和/或 Proxy 設定，確認輸出 TCP 連接埠 1935 和 1936 已開啟。<br/><br/>
透過 RTMPS 串流處理時，請檢查防火牆和/或 Proxy 設定，確認輸出 TCP 連接埠 2935 和 2936 已開啟。

> [!NOTE]
> 使用 RTMPS 協定時，編碼器必須支援 TLS 1.2。

- Adobe Flash Media Live Encoder 3.2
- [坎布里亞現場 4.3](https://www.capellasystems.net/products/cambria-live/)
- 元素即時（版本 2.14.15 及更高版本）
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- 由於 TLS 1.2 要求，遠端直播有線廣播（版本 13.0.2 或更高版本）
- 遠端有線廣播 S（僅支援 RTMP）
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming)英雄7和英雄8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>輸出分散式 MP4 的即時編碼器

媒體服務建議使用下列其中一種具有多位元速率 Smooth Streaming (分散式 MP4) 做為輸出的即時編碼器。 支援的 URL 配置是 `http://` 或 `https://`。

> [!NOTE]
> 使用 HTTPS 協定時，編碼器必須支援 TLS 1.2。

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- 元素即時（版本 2.14.15 及以上，由於 TLS 1.2 要求）
- Envivio 4Caster C4 Gen III 
- Imagine Communications Selenio MCP3
- Media Excel Hero Live 和 Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  如果要以多種語言（例如，一個英語音軌和一個西班牙文音軌）資料流即時事件，可以使用配置為將即時源發送到傳遞即時事件的 Media Excel 即時編碼器來實現此目的。

## <a name="configuring-on-premises-live-encoder-settings"></a>設定內部部署即時編碼器設定

如需您的即時事件類型可取得哪些設定的資訊，請參閱[即時事件類型比較](live-event-types-comparison.md)。

### <a name="playback-requirements"></a>播放需求

若要播放內容，音訊和視訊資料流必須都存在。 不支援僅播放視訊資料流。

### <a name="configuration-tips"></a>設定提示

- 請盡可能使用實體的有線網際網路連線。
- 當您判斷頻寬需求時，請將串流位元速率加倍。 雖然並非必要，但這個簡單的規則有助於減輕網路阻塞的影響。
- 使用軟體型編碼器時，請關閉任何不必要的程式。
- 在開始推送後變更編碼器設定會對事件產生負面影響。 組態變更可能會導致事件變得不穩定。 
- 請確保您有充足的時間來設定事件。 針對大型事件，我們建議您在一小時之前開始設定事件。
- 使用 H.264 視頻和 AAC 音訊編解碼器輸出。
- 確保視頻品質有關鍵幀或 GOP 時間對齊。
- 確保每個視頻品質都有唯一的流名稱。
- 使用建議的嚴格 CBR 編碼，以實現最佳的自我調整位元速率性能。

> [!IMPORTANT]
> 觀察機器的物理狀況（CPU/記憶體/等），因為將片段上載到雲涉及 CPU 和 IO 操作。 如果更改編碼器中的任何設置，請確定重置通道/即時事件，以便更改生效。

## <a name="see-also"></a>另請參閱

[使用媒體服務 v3 進行即時串流](live-streaming-overview.md)

## <a name="next-steps"></a>後續步驟

[如何驗證編碼器](become-on-premises-encoder-partner.md)
