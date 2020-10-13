---
title: 分析即時影片而不錄製任何記錄-Azure
description: Media graph 可以用來從即時影片串流中取出分析，而不需要將它記錄在邊緣或雲端中。 本文討論此概念。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 5dda18b68cb19d29623f2120fe07d7cc617f0c2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893032"
---
# <a name="analyzing-live-video-without-any-recording"></a>分析即時影片而不錄製任何節目

## <a name="suggested-pre-reading"></a>建議的預先閱讀 

* [媒體圖表概念](media-graph-concept.md)
* [發生事件時錄製影片](event-based-video-recording-concept.md)

## <a name="overview"></a>概觀  

您可以使用 media graph 分析即時影片，而不將影片的任何部分記錄到檔案或資產。 以下顯示的媒體圖形類似于以 [事件為基礎的影片記錄](event-based-video-recording-concept.md)，但沒有資產接收器節點或 file sink 節點。

### <a name="motion-detection"></a>動作偵測

如下所示的媒體圖形包含 [RTSP 來源](media-graph-concept.md#rtsp-source) 節點、 [動作偵測處理器](media-graph-concept.md#motion-detection-processor) 節點和 [IoT 中樞訊息接收](media-graph-concept.md#iot-hub-message-sink) 節點。 這類媒體圖形的圖形拓撲的 JSON 標記法可在 [這裡](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json)找到。 此圖表可讓您偵測傳入即時影片串流中的動作，並透過 IoT 中樞的 [訊息接收] 節點將動作事件轉送至其他應用程式和服務。 外部應用程式或服務可以觸發警示，或傳送通知給適當的人員。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="以動作偵測為基礎的 Live Video Analytics":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>使用自訂視覺模型來分析影片 

下方顯示的媒體圖形可讓您使用封裝在個別模組中的自訂視覺模型來分析即時影片串流。 這類媒體圖形的圖形拓撲的 JSON 標記法可在 [這裡](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)找到。 您可以在 [這裡](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) 看到一些範例，說明如何將模型包裝到以推斷服務形式執行的 IoT Edge 模組。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detected-frames.svg" alt-text="以動作偵測為基礎的 Live Video Analytics":::

在此媒體圖形中，[畫面播放速率篩選處理器] 節點會在將內送的即時影片串流傳送至 [HTTP 擴充處理器](media-graph-concept.md#http-extension-processor) 節點之前，先降低其畫面播放速率，將 JPEG、BMP 或 PNG 格式的圖像框架 (傳送) 至外部推斷服務的 REST。 來自外部推斷服務的結果是由 HTTP 擴充功能節點抓取，並透過 IoT 中樞的 [訊息接收] 節點轉送至 IoT Edge 的中樞。 這種類型的 media graph 可以用來建立各種案例的解決方案，例如瞭解車輛在交集的時間序列分佈、瞭解零售商店的取用者交通模式等等。

此範例的增強功能是在畫面播放速率篩選處理器節點之前使用動作偵測器處理器。 這會減少推斷服務的負載，因為只有在影片中有動作活動時，才會使用它。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/custom-model.svg" alt-text="以動作偵測為基礎的 Live Video Analytics":::

## <a name="next-steps"></a>後續步驟

[連續影片錄製](continuous-video-recording-concept.md)
