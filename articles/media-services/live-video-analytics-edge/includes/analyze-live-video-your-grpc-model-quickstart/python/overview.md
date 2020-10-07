---
ms.openlocfilehash: fa0c2f5bb00122b40fb4f4ea06b7cf55c0248904
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91024948"
---

![概觀](../../../media/quickstarts/gRPC-extension.svg)

上圖顯示本快速入門中的信號流動方式。 [邊緣模組](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)會模擬裝載了即時串流通訊協定 (RTSP) 伺服器的 IP 攝影機。 [RTSP 來源](../../../media-graph-concept.md#rtsp-source)節點會從這部伺服器提取影片摘要，然後將影片畫面傳送至[動作偵測處理器](../../../media-graph-concept.md#motion-detection-processor)節點。 此處理器會偵測動作，並在偵測時將影片畫面推送至 [gRPC 擴充處理器](../../../media-graph-concept.md#grpc-extension-processor)節點。

HTTP 擴充節點扮演著 Proxy 的角色。 其會將影片畫面轉換成指定的影像類型。 然後會透過 gRPC 將影像轉送至另一個邊緣模組，以透過[共用記憶體](https://en.wikipedia.org/wiki/Shared_memory)在 gRPC 端點後方執行 AI 模型。 在此範例中，邊緣模組是使用 [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) 模型所建置的，因此能夠偵測許多類型的物件。 gRPC 擴充處理器節點會收集偵測結果，並將事件發佈至 [IoT 中樞接收](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/media-graph-concept#iot-hub-message-sink)節點。 節點接著會將這些事件傳送至 [IoT Edge 中樞](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub)。

在本快速入門中，您將：

1. 建立和部署媒體圖表。
1. 解譯解譯。
1. 清除資源。
