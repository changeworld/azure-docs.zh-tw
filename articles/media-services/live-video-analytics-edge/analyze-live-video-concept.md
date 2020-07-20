---
title: 分析即時影片而不進行任何錄製-Azure
description: Media graph 可以用來只從即時影片串流中解壓縮分析，而不需要將它記錄在邊緣或雲端中。 本文討論此概念。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 29e00b9c04a652771ca150e2a45e980d20f8bc1f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260181"
---
# <a name="analyzing-live-video-without-any-recording"></a>分析即時影片而不進行任何錄製

## <a name="suggested-pre-reading"></a>建議的閱讀準備事項 

* [媒體圖表概念](media-graph-concept.md)
* [發生事件時錄製影片](event-based-video-recording-concept.md)

## <a name="overview"></a>總覽  

您可以使用 media graph 分析直播影片，而不需要將影片的任何部分錄製到檔案或資產。 下面所示的媒體圖形類似于以[事件為基礎的影片錄製](event-based-video-recording-concept.md)文章，但沒有資產接收節點或檔案接收節點。

### <a name="motion-detection"></a>動作偵測

如下所示的媒體圖形包含一個[RTSP 來源](media-graph-concept.md#rtsp-source)節點、一個 [[動作偵測處理器](media-graph-concept.md#motion-detection-processor)] 節點，以及一個 [ [IoT 中樞的訊息接收](media-graph-concept.md#iot-hub-message-sink)] 節點。 這類媒體圖形的圖形拓撲的 JSON 表示可以在[這裡](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json)找到。 此圖形可讓您偵測傳入即時影片串流中的動作，並透過 IoT 中樞的訊息接收節點，將動作事件轉送至其他應用程式和服務。 外部應用程式或服務可以觸發警示，或傳送通知給適當的人員。

![以動作偵測為基礎的 Live Video Analytics](./media/analyze-live-video/motion-detection.png)

### <a name="analyzing-video-using-a-custom-vision-model"></a>使用自訂視覺模型分析影片 

下面所示的媒體圖形可讓您使用封裝在不同模組中的自訂視覺模型來分析即時影片串流。 這類媒體圖形的圖形拓撲的 JSON 表示可以在[這裡](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)找到。 您可以在[這裡](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)看到一些將模型包裝到以推斷服務形式執行的 IoT Edge 模組中的範例。

![以外部推斷模組為基礎的即時影片分析](./media/analyze-live-video/external-inferencing-module.png)

在此媒體圖形中，[畫面播放速率篩選器處理器] 節點會在將傳入的即時影片串流傳送到[HTTP 擴充處理器](media-graph-concept.md#http-extension-processor)節點之前，降低其畫面播放速率，以透過 REST 將影像框架（JPEG、BMP 或 PNG 格式）傳送到外部推斷服務。 來自外部推斷服務的結果會由 HTTP 延伸模組節點抓取，並透過 IoT 中樞的訊息接收節點轉送到 IoT Edge 中樞。 這種類型的媒體圖形可以用來建立各種案例的解決方案，例如瞭解車輛在交集的時間序列分佈、瞭解零售商店中的取用者流量模式等等。

此範例的增強功能是在 [畫面播放速率篩選器處理器] 節點之前使用動作偵測處理器。 這會減少推斷服務的負載，因為它只會在影片中有動作活動時使用。

![透過外部推斷模組，以動態方式偵測畫面格的即時影片分析](./media/analyze-live-video/motion-detected-frames.png)

## <a name="next-steps"></a>後續步驟

[連續影片錄製](continuous-video-recording-concept.md)
