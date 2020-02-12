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
ms.openlocfilehash: c8cf8883c80dad7988793a898dcaf01dd8f860c3
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152630"
---
# <a name="recommended-live-streaming-encoders"></a>建議使用即時串流編碼器

在 Azure 媒體服務中，[即時事件](https://docs.microsoft.com/rest/api/media/liveevents) (通道) 代表處理即時串流內容的管線。 即時事件會以兩種方式之一收到即時輸入資料流。

* 內部部署即時編碼器會將多位元速率 RTMP 或 Smooth Streaming (分散式 MP4) 串流傳送到未啟用執行媒體服務即時編碼的即時事件。 內嵌的資料流會通過即時事件，而不需任何進一步處理。 此方法稱為 **傳遞**。 建議將即時編碼器傳送多位元率串流，而不是將單一位元速率串流傳送至傳遞即時事件，以允許彈性位元速率串流至用戶端。 

    如果您使用多位元率串流來進行傳遞實況活動，則必須同步處理不同位元速率的影片 GOP 大小和影片片段，以避免播放端發生非預期的行為。

  > [!NOTE]
  > 使用傳遞方法是進行即時串流的最經濟實惠方式。
 
* 內部部署即時編碼器會將單一位元速率串流傳送至即時事件，而此活動已啟用為使用下列其中一種格式的媒體服務執行即時編碼： RTMP 或 Smooth Streaming （分散的專案類型）。 即時事件接著會執行即時編碼，將內送單一位元速率資料流編碼成多位元速率 (自適性) 視訊資料流。

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
- [Cambria Live 4。3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live （版本2.14.15 和更新版本）
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast （因為 TLS 1.2 需求，版本為13.0.2 或更高）
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming)主圖7和主圖8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>輸出分散式 MP4 的即時編碼器

媒體服務建議使用下列其中一種具有多位元速率 Smooth Streaming (分散式 MP4) 做為輸出的即時編碼器。 支援的 URL 配置是 `http://` 或 `https://`。

> [!NOTE]
> 使用 HTTPS 通訊協定時，編碼器必須支援 TLS 1.2。

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live （因為 TLS 1.2 需求，版本2.14.15 和更高版本）
- Envivio 4Caster C4 Gen III 
- Imagine Communications Selenio MCP3
- Media Excel Hero Live 和 Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  如果您要以多種語言（例如，一個英文音訊軌和一個西班牙文音訊播放軌）串流處理實況活動，您可以使用已設定的 Media Excel live 編碼器來完成這項工作，以將即時摘要傳送至傳遞實況活動。

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

## <a name="becoming-an-on-premises-encoder-partner"></a>成為內部部署編碼器合作夥伴

身為 Azure 媒體服務內部部署編碼器合作夥伴，媒體服務將會向企業客戶建議您的編碼器，以協助推廣您的產品。 若要成為內部部署編碼器合作夥伴，您必須向媒體服務驗證您內部部署編碼器的相容性。 若要這樣做，請完成下列驗證。

### <a name="pass-through-live-event-verification"></a>傳遞即時事件驗證

1. 在您的媒體服務帳戶中，確認**串流端點**正在執行。 
2. 建立並啟動**傳遞**即時事件。 <br/> 如需詳細資訊，請參閱[實況活動狀態和計費](live-event-states-billing.md)。
3. 取得內嵌 URL 並設定您的內部部署編碼器使用該 URL，以將多位元速率即時資料流傳送到媒體服務。
4. 取得預覽 URL 並使用它來確認實際上已收到來自編碼器的輸入。
5. 建立新的 [資產] 物件。
6. 建立 [即時輸出] 並使用您建立的資產名稱。
7. 使用內建的**串流原則**類型來建立**串流定位器**。
8. 列出**串流定位器**上的路徑，以取回要使用的 URL。
9. 取得您想要串流的來源**串流端點**主機名稱。
10. 結合步驟 8 的 URL 和步驟 9 的主機名稱，即可取得完整的 URL。
11. 將您的即時編碼器執行約 10 分鐘的時間。
12. 停止即時事件。 
13. 使用 [Azure 媒體播放器](https://aka.ms/azuremediaplayer)等播放程式觀看已封存的資產)，以確保所有品質層級的播放沒有可見問題。 或者，在即時工作階段期間透過預覽 URL 觀看和驗證。
14. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本。
15. 於建立每個範例之後，重設即時事件狀態。
16. 針對您編碼器所支援的所有設定 (包含及不包含廣告訊號、字幕，或不同的編碼速度) 重複步驟 5 到步驟 15。

### <a name="live-encoding-live-event-verification"></a>即時編碼即時事件驗證

1. 在您的媒體服務帳戶中，確認**串流端點**正在執行。 
2. 建立並啟動**即時編碼**即時事件。 <br/> 如需詳細資訊，請參閱[實況活動狀態和計費](live-event-states-billing.md)。
3. 取得內嵌 URL 並設定您的編碼器將單一位元速率即時資料流推送到媒體服務。
4. 取得預覽 URL 並使用它來確認實際上已收到來自編碼器的輸入。
5. 建立新的 [資產] 物件。
6. 建立 [即時輸出] 並使用您建立的資產名稱。
7. 使用內建的**串流原則**類型來建立**串流定位器**。
8. 列出**串流定位器**上的路徑，以取回要使用的 URL。
9. 取得您想要串流的來源**串流端點**主機名稱。
10. 結合步驟 8 的 URL 和步驟 9 的主機名稱，即可取得完整的 URL。
11. 將您的即時編碼器執行約 10 分鐘的時間。
12. 停止即時事件。
13. 使用 [Azure 媒體播放器](https://aka.ms/azuremediaplayer)等播放程式觀看已封存的資產)，以確保所有品質層級的播放沒有可見問題。 或者，在即時工作階段期間透過預覽 URL 觀看和驗證。
14. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本。
15. 於建立每個範例之後，重設即時事件狀態。
16. 針對您編碼器所支援的所有設定 (包含及不包含廣告訊號、字幕，或不同的編碼速度) 重複步驟 5 到步驟 15。

### <a name="longevity-verification"></a>使用壽命驗證

除了步驟 11 外，遵循與[傳遞即時事件驗證](#pass-through-live-event-verification)中相同的其他步驟。 <br/>讓您的即時編碼器執行一週或更久，而不是 10 分鐘。 使用 [Azure 媒體播放器](https://aka.ms/azuremediaplayer)等播放程式不時觀看即時串流 (或已封存的資產)，以確保播放沒有可見問題。

### <a name="email-your-recorded-settings"></a>用電子郵件傳送記錄的設定

最後，以電子郵件將記錄的設定和即時封存參數傳送到 amshelp@microsoft.com 的 Azure 媒體服務，做為所有自我驗證檢查已通過的通知。 此外，包含任何後續動作的連絡資訊。 若有任何關於此程序的問題，請連絡 Azure 媒體服務團隊。

## <a name="next-steps"></a>後續步驟

[使用媒體服務 v3 進行即時串流](live-streaming-overview.md)
