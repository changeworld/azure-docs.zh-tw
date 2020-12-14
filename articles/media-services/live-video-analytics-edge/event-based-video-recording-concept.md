---
title: 以事件為基礎的影片錄製-Azure
description: 以事件為基礎的影片錄製 (EVR) 是指事件觸發的影片的錄製流程。 有問題的事件可能是因為處理影片信號本身 (例如，動作的偵測) 或可能來自獨立的來源 (例如，開啟門) 。  本文將說明一些與以事件為基礎的影片記錄相關的使用案例。
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 6a5f4873b2cfef8d9a6594916d82cd30a3bc1cc2
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401591"
---
# <a name="event-based-video-recording"></a>以事件為基礎的影片錄製  
 
## <a name="suggested-pre-reading"></a>建議的閱讀準備事項  

* [連續影片錄製](continuous-video-recording-concept.md)
* [播放錄製的內容](video-playback-concept.md)
* [媒體圖表概念](media-graph-concept.md)

## <a name="overview"></a>概觀 

以事件為基礎的影片錄製 (EVR) 是指事件觸發的影片的錄製流程。 有問題的事件可能是因為處理影片信號本身 (例如，動作的偵測) 或可能來自獨立的來源 (例如，開啟門) 。 

您可以使用) 由 [RTSP 來源](media-graph-concept.md#rtsp-source) 節點、 [資產接收器](media-graph-concept.md#asset-sink) 節點和其他節點組成的媒體圖形（如下列使用案例所述），將 CCTV 攝影機所觸發的影片 (記錄到媒體服務資產。 您可以設定「 [資產接收器](media-graph-concept.md#asset-sink) 」節點，在每次發生事件時產生新的資產，讓對應至每個事件的影片都在自己的資產中。 您也可以選擇使用一個資產來保存所有事件的影片。 

除了「資產接收器」節點以外，您可以使用「檔案 [接收](media-graph-concept.md#file-sink) 」節點，以在 Edge 裝置上的本機檔案系統上的指定位置中，捕獲與感) 興趣的事件相關的簡短影片 (片段。 

本文將說明一些與以事件為基礎的影片記錄相關的使用案例。

### <a name="video-recording-based-on-motion-detection"></a>根據動作偵測錄製影片  

在此使用案例中，您只能在影片中偵測到動作時錄製影片剪輯，並在產生這類影片剪輯時發出警示。 這可能與涉及重要基礎結構保護的商業安全性案例有關。 當偵測到未預期的動作時，產生警示並錄製影片時，您可以改善人力操作員的效率，因為只有在產生警示時才需要採取動作。

下圖顯示可處理此使用案例之媒體圖形的圖形標記法。 這類媒體圖形的圖形拓撲的 JSON 標記法可在 [這裡](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json)找到。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="根據動作偵測錄製影片":::

在圖表中，RTSP 來源節點會從相機中捕捉實況影片摘要，並將它傳遞至「 [動作偵測處理器](media-graph-concept.md#motion-detection-processor) 」節點。 偵測到即時影片中的動作時，[動作偵測處理器] 節點會產生事件，以移至 [ [信號閘道處理器](media-graph-concept.md#signal-gate-processor) ] 節點，以及 IoT 中樞的 [訊息接收] 節點。 第二個節點會將事件傳送至 IoT Edge Hub，讓它們可以路由傳送到其他目的地來觸發警示。 

來自「動作偵測器」節點的事件將會觸發「信號閘道處理器」節點，並可讓來自 RTSP 來源節點的即時影片摘要流向「資產接收器」節點。 如果沒有後續的這類動作偵測事件，閘道會在設定的一段時間之後關閉。 這會決定錄製影片的持續時間。

### <a name="video-recording-based-on-events-from-other-sources"></a>根據其他來源的事件錄製影片  

在此使用案例中，來自另一個 IoT 感應器的信號可以用來觸發影片的錄製。 下圖顯示可處理此使用案例之媒體圖形的圖形標記法。 這類媒體圖形的圖形拓撲的 JSON 標記法可在 [這裡](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json)找到。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="根據其他來源的事件錄製影片":::

在圖表中，外部感應器會將事件傳送至 IoT Edge 中樞。 然後會透過 [IoT 中樞訊息來源](media-graph-concept.md#iot-hub-message-source) 節點，將事件路由傳送至信號閘道處理器節點。 信號閘道處理器節點的行為與先前的使用案例相同-它將會開啟，並讓即時影片摘要從 RTSP 來源節點流向 file sink 節點 (或資產接收器節點，) 由外來事件觸發。 

如果您使用 file sink 節點，影片將會記錄到 edge 裝置上的本機檔案系統。 否則，如果您使用「資產接收器」節點，將會將影片記錄至資產。

### <a name="video-recording-based-on-an-external-inferencing-module"></a>以外部推斷模組為依據的影片錄製 

在此使用案例中，您可以根據外部邏輯系統的信號錄製影片剪輯。 這種使用案例的範例，只有在高速公路上的流量的影片摘要中偵測到貨車時，才能錄製影片剪輯。 下圖顯示可處理此使用案例之媒體圖形的圖形標記法。 這類媒體圖形的圖形拓撲的 JSON 標記法可在 [這裡](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)找到。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="以外部推斷模組為依據的影片錄製":::

在圖表中，RTSP 來源節點會從相機中捕捉實況影片摘要，並將其傳遞至兩個分支：一個具有「 [信號閘道處理器](media-graph-concept.md#signal-gate-processor) 」節點，另一個則使用 [HTTP 擴充](media-graph-concept.md) 節點將資料傳送至外部邏輯模組。 [HTTP 擴充功能] 節點可讓 media graph 以 JPEG、BMP 或 PNG 格式將影像框架 (傳送) 至外部推斷服務的 REST。 此信號路徑通常只能支援低畫面播放速率 ( # B0 5fps) 。 您可以使用 [HTTP 擴充功能處理器] 節點來降低影片進入外部推斷模組的畫面播放速率。

外部推斷服務的結果是由 HTTP 延伸模組節點抓取，並透過 IoT 中樞的 [訊息接收] 節點轉送至 IoT Edge 中樞，可供外部邏輯模組進一步處理。 例如，如果推斷服務能夠偵測車輛，則邏輯模組可能會尋找特定車輛，例如匯流排或貨車。 當邏輯模組偵測到感興趣的物件時，它可以透過 IoT Edge 中樞將事件傳送到圖形中的 IoT 中樞訊息來源節點，藉此觸發信號閘道處理器節點。 信號閘道的輸出會顯示為移至 [file sink] 節點或 [資產接收] 節點。 在先前的案例中，影片將會記錄到 edge 裝置上的本機檔案系統。 在後者的情況下，會將影片記錄至資產。

此範例的增強功能是在 HTTP 擴充處理器節點之前使用動作偵測器處理器。 這會減少推斷服務的負載，例如，當高速公路上沒有車輛時，夜間可能會很長的時間。 

## <a name="next-steps"></a>後續步驟

[教學課程：以事件為基礎的影片錄製](event-based-video-recording-tutorial.md)
