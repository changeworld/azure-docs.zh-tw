---
title: 什麼是媒體圖表擴充功能 - Azure
description: IoT Edge 上的即時影片分析可讓您透過圖表擴充節點來擴充媒體圖表處理功能。
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 2e1ca730a6736776425cd70b323147b58e8eacbf
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716073"
---
# <a name="media-graph-extension"></a>媒體圖表擴充功能

IoT Edge 上的即時影片分析可讓您透過圖表擴充節點來擴充媒體圖表處理功能。 您的分析擴充外掛程式可利用傳統的影像處理技術或電腦視覺 AI 模型。 啟用圖表擴充功能的方式是在媒體圖表中包含擴充處理器節點。 擴充處理器節點會將影片畫面轉送到設定的端點，以及作為擴充功能的介面。 您可以與本機或遠端端點連線，並在必要時透過驗證和 TLS 加密來保護連線。 此外，在將影片畫面提交至自訂擴充之前，圖表擴充處理器節點可允許您選擇性地調整和編碼影片。

即時影片分析支援兩種媒體圖表擴充處理器：

* [HTTP 擴充處理器](media-graph-concept.md#http-extension-processor)
* [gRPC 擴充處理器](media-graph-concept.md#grpc-extension-processor)

## <a name="http-extension-processor"></a>HTTP 擴充處理器

HTTP 擴充處理器可讓您使用 HTTP 通訊協定來啟用擴充性案例，而在這之中，效能和 (或) 最佳資源使用率不是主要考量。 您可以透過 HTTP REST 端點，將您自己的 AI 公開給媒體圖表。 

使用 HTTP 擴充處理器節點的時機：

* 您想要與現有的 HTTP 推斷系統有更好的互通性。
* 可以接受低效能的資料傳輸。
* 您想要使用簡單的「要求-回應」介面來進行即時影片分析。

## <a name="grpc-extension-processor"></a>gRPC 擴充處理器

gRPC 擴充處理器可讓您使用以 gRPC 為基礎且高效能的結構化通訊協定來啟用擴充性案例。 這適用於以效能和 (或) 最佳資源使用率為優先考量的案例。 gRPC 擴充處理器可讓您享有結構化資料定義的完整優勢。 gRPC 使用下列項目提供高度的內容傳輸效能：

* [內建共用記憶體](https://en.wikipedia.org/wiki/Shared_memory)或 
* 直接將內容內嵌至 gRPC 訊息的主體。 

gRPC 擴充處理器可用於傳送媒體屬性及交換推斷訊息。
因此，當您想要進行下列動作時，請使用 gRPC 擴充處理器節點：

* 使用結構化合約 (例如，要求和回應的結構化訊息)
* 使用通訊協定緩衝區 ([protobuf](https://developers.google.com/protocol-buffers)) 作為其基礎訊息交換格式以進行通訊。
* 在單一串流工作階段中與 gRPC 伺服器通訊，而不是使用傳統的「要求-回應」模型，其不僅需要自訂要求處理常式來剖析傳入要求，還要呼叫正確的實作函式。 
* 即時影片分析與您模組之間需要低延遲和高輸送量的通訊。

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>搭配即時影片分析使用您的推斷模型

媒體圖表擴充功能可讓您在任何可用的推斷執行階段 (例如 ONNX、TensorFlow、PyTorch 或您自有 Docker 容器中的其他執行階段) 上，執行您選擇的推斷模型。 推斷自訂擴充應與即時影片分析邊緣模組一起部署，以獲得最佳效能，然後透過您圖表拓撲中包含的 HTTP 擴充處理器或 gRPC 擴充處理器來叫用。 此外，您可以選擇性地將[動作偵測器處理器](media-graph-concept.md#motion-detection-processor)和[畫面播放速率篩選處理器](media-graph-concept.md#frame-rate-filter-processor)新增至媒體擴充處理器的上游，藉此對您的自訂擴充進行節流。

下圖描述資料流程概觀：
 
![設計師中](./media/media-graph-extension/data-flow.png)

## <a name="samples"></a>範例

查看我們用於即時影片分析的部分 [Jupyter 筆記本](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/readme.md)範例。 這些筆記本將提供逐步指示，說明：

* 如何建立擴充服務的 Docker 容器映像
* 如何部署容器形式的擴充服務和即時影片分析容器
* 如何搭配擴充用戶端使用即時影片分析媒體圖表，並將其指向擴充 gRPC 端點
