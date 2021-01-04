---
ms.openlocfilehash: 0e800668bea2f744f8862292b21be814858b0c16
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486759"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="信號流動":::

上圖顯示本快速入門中的信號流動方式。 [邊緣模組](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)會模擬裝載了即時串流通訊協定 (RTSP) 伺服器的 IP 攝影機。 [RTSP 來源](../../../media-graph-concept.md#rtsp-source) 節點會從這部伺服器提取影片摘要，然後將影片畫面傳送至 [HTTP 擴充處理器](../../../media-graph-concept.md#http-extension-processor) 節點。 

HTTP 延伸模組節點扮演 Proxy 的角色。 其會對使用 `samplingOptions` 欄位所設定的傳入影片畫面進行取樣，並將影片畫面轉換為指定的影像類型。 然後，會透過 REST 將影像轉送至另一個邊緣模組，以在 HTTP 端點後方執行 AI 模型。 在此範例中，邊緣模組是使用 [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) 模型所建置的，因此能夠偵測許多類型的物件。 HTTP 延伸模組處理器節點會收集偵測結果，並將事件發佈至 [IoT 中樞接收](../../../media-graph-concept.md#iot-hub-message-sink)節點。 節點接著會將這些事件傳送至 [IoT Edge 中樞](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub)。

在本快速入門中，您將：

1. 建立和部署媒體圖表。
1. 解譯解譯。
1. 清除資源。
