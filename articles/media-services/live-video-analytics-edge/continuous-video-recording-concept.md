---
title: 連續影片錄製-Azure
description: 連續影片錄製（CVR）是指從影片來源持續錄製影片的程式。 本主題討論 CVR 的內容。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 9a785125d4cfb2324224f4676e1d429342ec325c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260621"
---
# <a name="continuous-video-recording"></a>連續影片錄製  

## <a name="suggested-pre-reading"></a>建議的閱讀準備事項  

* [媒體圖表概念](media-graph-concept.md)
* [影片錄影概念](video-recording-concept.md)

## <a name="overview"></a>總覽

連續影片錄製（CVR）是指從影片來源持續錄製影片的程式。 IoT Edge 上的即時影片分析可透過由 RTSP 來源節點和資產接收器節點組成的[媒體圖形](media-graph-concept.md)，以全天候的方式持續 CCTV 攝影機錄製影片。 下圖顯示這類 media graph 的圖形表示。 這類媒體圖形的[圖形拓撲](media-graph-concept.md?branch=release-preview-media-services-lva#media-graph-topologies-and-instances)的 JSON 表示可以在[這裡](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset)找到。

![連續影片錄製](./media/continuous-video-recording/continuous-video-recording-overview.png)

上面所述的媒體圖形可以在邊緣裝置上執行，且資產接收器會將影片錄製到 Azure 媒體服務[資產](terminology.md#asset)。 只要 media graph 維持在 [已啟動] 狀態，影片就會記錄下來。 由於影片會記錄為資產，因此可以使用媒體服務的現有串流功能來播放。 如需詳細資訊，請參閱[播放錄製的內容](video-playback-concept.md)。

## <a name="resilient-recording"></a>恢復錄製

IoT Edge 上的即時影片分析支援在不理想的網路狀況下運作，Edge 裝置可能偶爾會失去與雲端的連線，或在可用的頻寬中出現下降。 為因應此情況，來自來源的影片會在本機記錄到快取中，並且會定期自動與資產同步。 如果您檢查[圖形拓撲 JSON](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json)，您會看到它定義了下列屬性：

```
    "segmentLength": "PT30S",
    "localMediaCacheMaximumSizeMiB": "2048",
    "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```
後面兩個屬性與復原記錄相關（這兩者也是資產接收節點的必要屬性）。 LocalMediaCachePath 屬性會告知資產接收器使用該資料夾路徑來快取媒體資料，然後再上傳至資產。 您可以查看[這](https://docs.microsoft.com/azure/iot-edge/how-to-access-host-storage-from-module)篇文章，以瞭解 edge 模組如何利用您裝置的本機儲存體。 LocalMediaCacheMaximumSizeMiB 屬性會定義資產接收器可使用多少磁碟空間做為快取（1 MiB = 1024 * 1024 個位元組）。 

如果您的 edge 模組長時間失去連線，而且儲存在快取資料夾中的內容達到 localMediaCacheMaximumSizeMiB 值，則資產接收會開始從最舊的資料開始，捨棄快取中的資料。 例如，如果裝置在10AM 中斷連線，而快取達到下午6點的最大限制，則資產接收會開始刪除記錄在10AM 的資料。 

還原網路連線時，資產接收會從快取開始上傳，並從最舊的資料開始。 在上述範例中，假設在恢復連線能力時，必須從快取中捨棄5分鐘的影片（例如6：02PM），則資產接收會從10：05AM 標記開始上傳。

如果您稍後使用[這些](playback-recordings-how-to.md)api 來檢查資產，您會看到資產中的間距大約從10AM 到10：05AM。

## <a name="segmented-recording"></a>分段記錄  

如上所述，[資產接收器] 節點會將影片記錄到本機快取，並定期將影片上傳至雲端。 SegmentLength 屬性（如上一節所示）將協助您控制將資料寫入至儲存體帳戶的相關寫入交易成本，其中會記錄資產。 例如，如果您將上傳期間從30秒增加至5分鐘，則儲存體交易的數量將會下降10（5 * 60/30）。

SegmentLength 屬性可確保邊緣模組每 segmentLength 秒最多可上傳一次影片。 這個屬性的最小值為30秒（也是預設值），而且可以增加30秒的增量，最多5分鐘。

>[!NOTE]
>請參閱[這](playback-recordings-how-to.md)篇文章，以瞭解 segmentLength 有播放的效果。


## <a name="see-also"></a>另請參閱

* [發生事件時錄製影片](event-based-video-recording-concept.md)
* [播放錄製的內容](video-playback-concept.md)


## <a name="next-steps"></a>後續步驟

[教學課程：連續影片錄製](continuous-video-recording-tutorial.md)
