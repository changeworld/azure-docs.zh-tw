---
title: 持續錄製影片-Azure
description: 持續錄製的影片 (CVR) 是指從影片來源持續錄製影片的流程。 本主題討論 CVR 是什麼。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 04f09f1968e647c57ba0913a9e7f9e601d045771
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566684"
---
# <a name="continuous-video-recording"></a>連續影片錄製  

## <a name="suggested-pre-reading"></a>建議的閱讀準備事項  

* [媒體圖表概念](media-graph-concept.md)
* [影片錄製概念](video-recording-concept.md)

## <a name="overview"></a>概觀

持續錄製的影片 (CVR) 是指從影片來源持續錄製影片的流程。 IoT Edge 上的即時影片分析可透過由 RTSP 來源節點和資產接收器節點組成的 [媒體圖形](media-graph-concept.md) ，以全天候的方式持續錄製影片。 下圖顯示這類 media graph 的圖形標記法。 這類媒體圖形的 [圖形拓撲](media-graph-concept.md?branch=release-preview-media-services-lva#media-graph-topologies-and-instances) 的 JSON 標記法可在 [這裡](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset)找到。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="連續影片錄製":::

上述媒體圖形可以在邊緣裝置上執行，資產接收器會將影片記錄到 Azure 媒體服務 [資產](terminology.md#asset)。 只要 media graph 處於啟動狀態，影片就會記錄下來。 由於影片會記錄為資產，因此可以使用媒體服務的現有串流功能來播放。 如需詳細資訊，請參閱 [播放記錄的內容](video-playback-concept.md) 。

## <a name="resilient-recording"></a>復原錄製

IoT Edge 上的即時影片分析支援在不太理想的網路狀況下運作，其中 Edge 裝置可能會偶爾失去與雲端的連線，或遇到可用頻寬的下降。 為了進行這項處理，來源的影片會在本機記錄到快取中，並定期自動與資產同步處理。 如果您檢查 [圖形拓撲 JSON](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json)，您會看到它已定義下列屬性：

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```

後兩個屬性與復原記錄相關 (兩者也是資產接收節點) 的必要屬性。 LocalMediaCachePath 屬性會告知資產接收器使用該資料夾路徑來快取媒體資料，然後再上傳至資產。 您可以查看 [這](../../iot-edge/how-to-access-host-storage-from-module.md) 篇文章，以瞭解 edge 模組如何利用您裝置的本機儲存體。 LocalMediaCacheMaximumSizeMiB 屬性定義資產接收器可以用來做為快取的磁碟空間量 (1 MiB = 1024 * 1024 位元組) 。 

如果您的 edge 模組中斷連線相當長的時間，而且儲存在快取資料夾中的內容達到 localMediaCacheMaximumSizeMiB 值，則資產接收器將會從最舊的資料開始，從快取中捨棄資料。 例如，如果裝置在10AM 中斷連線，而快取達到了下午6點的上限，則資產接收器會開始刪除記錄在10AM 的資料。 

當網路連線恢復時，資產接收會從快取開始上傳，從最舊的資料開始。 在上述範例中，假設有5分鐘的影片必須從快取中捨棄，因為連線已還原 (例如6： 02PM) ，則資產接收器將會從10：05AM 標示開始上傳。

如果您稍後使用 [這些](playback-recordings-how-to.md) api 檢查資產，您會看到資產中有大約10AM 到10：05AM 的間距。

## <a name="segmented-recording"></a>分段錄製  

如上所述，資產接收器節點會將影片記錄到本機快取，並定期將影片上傳至雲端。 上一節所示的 segmentLength 屬性 () 將協助您控制將資料寫入儲存體帳戶（資產正在記錄）的相關寫入交易成本。 例如，如果您將上傳期間從30秒增加到5分鐘，則儲存體交易的數目會以 10 (5 * 60/30) 的因數來下降。

SegmentLength 屬性可確保 edge 模組每 segmentLength 秒最多可上傳一次影片。 這個屬性的最小值為30秒， (也是預設) ，而且可以增加30秒，最多5分鐘的增量。

> [!NOTE]
> 請參閱 [播放記錄](playback-recordings-how-to.md) 文章，以瞭解 segmentLength 播放時的效果。

## <a name="see-also"></a>另請參閱

* [發生事件時錄製影片](event-based-video-recording-concept.md)
* [播放錄製的內容](video-playback-concept.md)

## <a name="next-steps"></a>接下來的步驟

[教學課程：持續錄製影片](continuous-video-recording-tutorial.md)
