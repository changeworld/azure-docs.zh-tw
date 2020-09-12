---
title: 媒體服務建議的即時串流編碼器 -  Azure | Microsoft Docs
description: 深入了解 Azure 媒體服務所建議的即時串流處理內部部署編碼器
services: media-services
keywords: encoding;encoders;media;編碼;編碼器;媒體
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/31/2020
ms.topic: conceptual
ms.service: media-services
ms.openlocfilehash: 3532032f8fd3ac6e673d3913fd13f7f83ae7759e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295354"
---
# <a name="verified-on-premises-live-streaming-encoders"></a>已驗證內部部署即時串流編碼器

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在 Azure 媒體服務中，[即時事件](/rest/api/media/liveevents) (通道) 代表處理即時串流內容的管線。 即時事件會以兩種方式之一收到即時輸入資料流。

* 內部部署即時編碼器會將多位元速率 RTMP 或 Smooth Streaming (分散式 MP4) 串流傳送到未啟用執行媒體服務即時編碼的即時事件。 內嵌的資料流會通過即時事件，而不需任何進一步處理。 此方法稱為 **傳遞**。 我們建議讓即時編碼器將多位元率串流傳送至傳遞實況活動，而不是將單一位元速率串流傳送至傳遞即時事件，以允許將彈性位元速率串流至用戶端。 

    如果您使用多位元率串流進行傳遞實況活動，則必須同步處理不同位元速率上的影片 GOP 大小和影片片段，以避免播放端出現非預期的行為。

  > [!TIP]
  > 使用傳遞方法是進行即時串流的最經濟實惠方式。
 
* 內部部署即時編碼器會將單一位元速率串流傳送至啟用的實況活動，以使用下列其中一種格式，以媒體服務執行即時編碼： RTMP 或 Smooth Streaming (分散的數量) 。 即時事件接著會執行即時編碼，將內送單一位元速率資料流編碼成多位元速率 (自適性) 視訊資料流。

本文討論已驗證的內部部署即時串流編碼器。 驗證是透過廠商自我驗證或客戶驗證來完成。 Microsoft Azure 媒體服務不會對每個編碼器進行完整或嚴格的測試，也不會持續重新驗證更新。 如需如何驗證內部部署即時編碼器的指示，請參閱 [驗證您的內部部署編碼器](become-on-premises-encoder-partner.md)

如需媒體服務即時編碼的詳細資訊，請參閱[使用媒體服務 v3 進行即時串流](live-streaming-overview.md)。

## <a name="encoder-requirements"></a>編碼器需求

使用 HTTPS 或 RTMPS 通訊協定時，編碼器必須支援 TLS 1.2。

## <a name="live-encoders-that-output-rtmp"></a>輸出 RTMP 的即時編碼器

媒體服務建議使用下列其中一種具有 RTMP 作為輸出的即時編碼器。 支援的 URL 配置是 `rtmp://` 或 `rtmps://`。

透過 RTMP 串流處理時，請檢查防火牆和/或 Proxy 設定，確認輸出 TCP 連接埠 1935 和 1936 已開啟。<br/><br/>
透過 RTMPS 串流處理時，請檢查防火牆和/或 Proxy 設定，確認輸出 TCP 連接埠 2935 和 2936 已開啟。

> [!NOTE]
> 使用 RTMPS 通訊協定時，編碼器必須支援 TLS 1.2。

- Adobe Flash Media Live Encoder 3.2
- [Blackmagic ATEM 迷你和 ATEM 迷你 PRO](https://www.blackmagicdesign.com/products/atemmini)
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (2.14.15 和更高版本的) 
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) 主圖7和主圖8
- Haivision KB
- Haivision Makito X HEVC
- [Restream.io](https://restream.io/)
- OBS Studio
- [Streamlabs OBS](https://streamlabs.com/)
- [Switcher Studio (iOS)](https://www.switcherstudio.com/)
- Telestream Wirecast (版本13.0.2 或更高版本，因為 TLS 1.2 需求) 
- 僅支援 RTMP 的 Telestream Wirecast S (。 因為缺少 TLS 1.2 +) ，所以不支援 RTMPS
- Teradek Slice 756
- VMIX
- xStream

> [!WARNING]
> 上述的編碼器清單只是建議清單。 編碼器不會不斷地經過 Microsoft 測試或驗證，而且更新或重大變更可能會由編碼器廠商或可能中斷相容性的開放原始碼專案所引進。 

## <a name="live-encoders-that-output-fragmented-mp4-smooth-streaming-ingest"></a>輸出分散的 (Smooth Streaming 內嵌) 的即時編碼器

媒體服務建議使用下列其中一種具有多位元速率 Smooth Streaming (分散式 MP4) 做為輸出的即時編碼器。 支援的 URL 配置是 `http://` 或 `https://`。

> [!NOTE]
> 使用 HTTPS 通訊協定時，編碼器必須支援 TLS 1.2。

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (version 2.14.15 和更高版本，因為 TLS 1.2 需求) 
- Envivio 4Caster C4 Gen III 
- [Ffmpeg](https://www.ffmpeg.org)
- Imagine Communications Selenio MCP3
- Media Excel Hero Live 和 Hero 4K (UHD/HEVC)

> [!TIP]
>  如果您要以多種語言串流處理實況活動 (例如，一個英文音訊播放軌和一個西班牙文音訊播放軌) ，您可以使用設定為將即時摘要傳送至傳遞實況活動的 Media Excel live 編碼器來完成此操作。

> [!WARNING]
> 上述的編碼器清單只是建議清單。 編碼器不會不斷地經過 Microsoft 測試或驗證，並且支援或 bug 可能會由編碼器廠商或開放原始碼專案引進，以在任何時間中斷相容性。 

## <a name="configuring-on-premises-live-encoder-settings"></a>設定內部部署即時編碼器設定

如需您的即時事件類型可取得哪些設定的資訊，請參閱[即時事件類型比較](live-event-types-comparison.md)。

### <a name="playback-requirements"></a>播放需求

若要播放內容，音訊和視訊資料流必須都存在。 不支援僅播放視訊資料流。

### <a name="configuration-tips"></a>設定提示

- 請盡可能使用實體的有線網際網路連線。
- 當您判斷頻寬需求時，請將串流位元速率加倍。 雖然並非必要，但這個簡單的規則有助於減輕網路阻塞的影響。
- 使用軟體型編碼器時，請關閉任何不必要的程式。
- 在開始推送後變更編碼器設定會對事件產生負面影響。 組態變更可能會導致事件變得不穩定。 
- 請一律測試並驗證較新版本的編碼器軟體，以持續與 Azure 媒體服務相容。 Microsoft 不會重新驗證這份清單上的編碼器，而大部分的驗證都是由軟體廠商直接做為「自我認證」來完成。
- 請確保您有充足的時間來設定事件。 針對大型事件，我們建議您在一小時之前開始設定事件。
- 使用 h.264 video 和 AAC-LC 音訊編解碼器輸出。
- 針對您要廣播的實況活動類型，請遵循支援的解析度和畫面播放速率 (例如，60fps 目前已遭到拒絕。 ) 
- 確定在影片品質之間有主要畫面格或 GOP 時態性對齊。
- 請確定每個影片品質都有唯一的資料流程名稱。
- 針對最佳彈性位元速率效能，請使用嚴格的 CBR 編碼建議。

> [!IMPORTANT]
> 查看電腦 (CPU/記憶體/等) 的實體狀況，因為將片段上傳至雲端牽涉到 CPU 和 IO 作業。 如果您變更編碼器中的任何設定，請確定要讓變更生效的通道/實況活動。

## <a name="see-also"></a>另請參閱

[使用媒體服務 v3 進行即時串流](live-streaming-overview.md)

## <a name="next-steps"></a>接下來的步驟

[如何驗證您的編碼器](become-on-premises-encoder-partner.md)
