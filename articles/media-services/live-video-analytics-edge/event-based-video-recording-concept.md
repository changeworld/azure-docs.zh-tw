---
title: 以事件為基礎的影片錄影-Azure
description: 以事件為基礎的影片錄製 (EVR) 是指事件觸發的影片的錄製流程。 有問題的事件可能源自于視訊訊號本身的處理（例如，動作偵測），或可能來自獨立的來源（例如開啟門）。  這篇文章說明一些與事件型影片錄影相關的使用案例。
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 0a6f7ca4233c195c7494fc6f63e7dfb5bf654c17
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260741"
---
# <a name="event-based-video-recording"></a>以事件為基礎的影片錄製  
 
## <a name="suggested-pre-reading"></a>建議的閱讀準備事項  

* [連續影片錄製](continuous-video-recording-concept.md)
* [播放錄製的內容](video-playback-concept.md)
* [媒體圖表概念](media-graph-concept.md)

## <a name="overview"></a>總覽 

以事件為基礎的影片錄製 (EVR) 是指事件觸發的影片的錄製流程。 有問題的事件可能源自于視訊訊號本身的處理（例如，動作偵測），或可能來自獨立的來源（例如開啟門）。 

您可以使用由[RTSP 來源](media-graph-concept.md#rtsp-source)節點、[資產接收器](media-graph-concept.md#asset-sink)節點和其他節點（如下列使用案例中所述）所組成的媒體圖形，將 CCTV 攝影機中的影片（由事件觸發）記錄到媒體服務資產。 您可以設定「[資產接收](media-graph-concept.md#asset-sink)」節點，在每次發生事件時產生新的資產，讓對應到每個事件的影片都在自己的資產中。 您也可以選擇使用一個資產來存放所有事件的影片。 

除了 [資產接收] 節點以外，您還可以使用 [檔案[接收](media-graph-concept.md#file-sink)] 節點，將簡短的影片程式碼片段（與感關注的事件相關）捕捉到邊緣裝置上本機檔案系統上的指定位置。 

這篇文章說明一些與事件型影片錄影相關的使用案例。

### <a name="video-recording-based-on-motion-detection"></a>以動作偵測為基礎的影片錄影  

在此使用案例中，您只可以在影片中偵測到動作時錄製影片剪輯，並在產生這類影片剪輯時收到警示。 這可能與涉及重要基礎結構保護的商業安全性案例有關。 當偵測到非預期的動作時，藉由產生警示和錄製影片，您可以改善人力操作員的效率，因為只有在產生警示時才需要採取動作。

下圖顯示可解決此使用案例之媒體圖形的圖形表示。 這類媒體圖形的圖形拓撲的 JSON 表示可以在[這裡](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json)找到。

![以動作偵測為基礎的影片錄影](./media/event-based-video-recording/motion-detection.png)

在圖表中，RTSP 來源節點會從相機中捕捉即時影片摘要，並將它傳遞至 [[動作偵測處理器](media-graph-concept.md#motion-detection-processor)] 節點。 在即時影片中偵測到動作時，[動作偵測處理器] 節點會產生事件，並移至 [信號] 網[關 [處理器](media-graph-concept.md#signal-gate-processor)] 節點，以及 [IoT 中樞的訊息接收] 節點。 第二個節點會將事件傳送到 IoT Edge 中樞，從此處可以將它們路由至其他目的地以觸發警示。 

[動作偵測器] 節點中的事件將會觸發 [信號閘道處理器] 節點，並讓來自 RTSP 來源節點的即時影片摘要流向 [資產接收] 節點。 如果沒有後續的動作偵測事件，閘道會在設定的一段時間之後關閉。 這會決定錄製影片的持續時間。

### <a name="video-recording-based-on-events-from-other-sources"></a>以其他來源的事件為基礎的影片錄影  

在此使用案例中，來自另一個 IoT 感應器的信號可用於觸發影片錄製。 下圖顯示可解決此使用案例之媒體圖形的圖形表示。 這類媒體圖形的圖形拓撲的 JSON 表示可以在[這裡](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json)找到。

![以其他來源的事件為基礎的影片錄影](./media/event-based-video-recording/other-sources.png)

在圖表中，外部感應器會將事件傳送至 IoT Edge 中樞。 然後，事件會透過[IoT 中樞訊息來源](media-graph-concept.md#iot-hub-message-source)節點，路由傳送至 [信號閘道處理器] 節點。 [信號閘道處理器] 節點的行為與先前的使用案例相同-它會開啟，並讓即時影片摘要在外來事件觸發時，從 RTSP 來源節點流向 [file sink] 節點（或 [資產接收器] 節點）。 

如果您使用 file sink 節點，影片將會記錄到 edge 裝置上的本機檔案系統中。 否則，如果您使用資產接收節點，影片將會記錄到資產中。

### <a name="video-recording-based-on-an-external-inferencing-module"></a>以外部推斷模組為基礎的影片錄製 

在此使用案例中，您可以根據來自外部邏輯系統的信號來錄製影片剪輯。 這種使用案例的範例，只有在高速公路上的流量影像中偵測到卡車時，才會錄製影片剪輯。 下圖顯示可解決此使用案例之媒體圖形的圖形表示。 這類媒體圖形的圖形拓撲的 JSON 表示可以在[這裡](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)找到。

![以外部推斷模組為基礎的影片錄製](./media/event-based-video-recording/external-inferencing-module.png)

在圖表中，RTSP 來源節點會從相機捕捉即時影片摘要，並將其傳遞給兩個分支：一個具有[信號閘道處理器](media-graph-concept.md#signal-gate-processor)節點，另一個則使用[HTTP 擴充](media-graph-concept.md)節點將資料傳送至外部邏輯模組。 [HTTP 延伸模組] 節點可讓 media graph 將影像框架（JPEG、BMP 或 PNG 格式）傳送至 REST 上的外部推斷服務。 此信號路徑通常只能支援低畫面播放速率（<5fps）。 您可以使用 [[畫面播放速率篩選器處理器](media-graph-concept.md#frame-rate-filter-processor)] 節點，來降低進入 HTTP 擴充功能節點之影片的畫面播放速率。

來自外部推斷服務的結果會由 HTTP 延伸模組節點抓取，並透過 IoT 中樞訊息接收節點來轉送到 IoT Edge 中樞，供外部邏輯模組進一步處理。 例如，如果推斷服務能夠偵測車輛，則邏輯模組可能會尋找特定車輛，例如匯流排或卡車。 當邏輯模組偵測到感興趣的物件時，它可以透過 IoT Edge 中樞將事件傳送至圖形中的 IoT 中樞訊息來源節點，以觸發信號閘道處理器節點。 [信號] 閘道的輸出會顯示為移至 [file sink] 節點或 [資產接收器] 節點。 在先前的案例中，影片會記錄到 edge 裝置上的本機檔案系統中。 在第二種情況下，影片會記錄到資產中。

此範例的增強功能是在 [畫面播放速率篩選器處理器] 節點之前使用動作偵測處理器。 這會降低推斷服務的負載，例如夜間，當高速公路上沒有車輛時，可能會有很長的一段時間。 

## <a name="next-steps"></a>後續步驟

[教學課程：以事件為基礎的影片錄影](event-based-video-recording-tutorial.md)
