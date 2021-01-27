---
title: IoT Edge 常見問題的即時影片分析-Azure
description: 本文針對 IoT Edge 上的即時影片分析，提供常見問題的解答。
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 0cb378bf614582070dd1bdd0a11706b26437af53
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880045"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>IoT Edge 常見問題的即時影片分析

本文針對 Azure IoT Edge 上的即時影片分析，提供常見問題的解答。

## <a name="general"></a>一般

**我可以在圖形拓撲定義中使用哪些系統變數？**

| 變數   |  描述  | 
| --- | --- | 
| [System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | 代表 UTC 時間的瞬間，通常會以下列格式以一天的日期和時程表示：<br>*yyyyMMddTHHmmssZ* | 
| System. PreciseDateTime | 以符合 ISO8601 檔案標準格式的日期時間實例（以毫秒為單位）來國際標準時間表示 (UTC) 日期時間實例，格式如下：<br>*yyyyMMddTHHmmss. Ss.fffz* | 
| System. GraphTopologyName   | 代表 media graph 拓撲，並保留圖形的藍圖。 | 
| System. GraphInstanceName |    代表 media graph 實例、保存參數值，並參考拓撲。 | 

## <a name="configuration-and-deployment"></a>設定和部署

**我可以將 media edge 模組部署到 Windows 10 裝置嗎？**

是。 如需詳細資訊，請參閱 [Windows 10 上的 Linux 容器](/virtualization/windowscontainers/deploy-containers/linux-containers)。

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>從 IP 相機和 RTSP 設定進行捕捉

**我需要在裝置上使用特殊的 SDK 才能傳送影片串流嗎？**

否，IoT Edge 上的即時影片分析支援使用 RTSP (即時串流通訊協定) 進行影片串流，在大部分的 IP 攝影機上都受到支援。

**我是否可以使用 Real-Time 訊息通訊協定 (RTMP) 或 Smooth Streaming 通訊協定 (（例如 Media Services 即時事件) ）將媒體推送至 IoT Edge 上的即時影片分析？**

否，即時影片分析僅支援從 IP 攝影機捕獲影片的 RTSP。 任何透過 TCP/HTTP 支援 RTSP 串流的攝影機都應可運作。 

**我可以重設或更新圖形實例中的 RTSP 來源 URL 嗎？**

是，當圖形實例處於 *非* 使用中狀態時。  

**RTSP 模擬器是否可在測試和開發期間使用？**

是的，您可以在快速入門和教學課程中使用 [RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) 模擬器 edge 模組，以支援學習流程。 此課程模組是以最佳方式提供，而且可能無法隨時使用。 強烈建議您 *不要* 使用模擬器超過數小時。 在規劃生產部署之前，您應該先投資實際的 RTSP 來源測試。

**是否支援於邊緣 ONVIF 探索 IP 攝影機？**

否，我們不支援開放網路視頻界面論壇 (ONVIF) 在 edge 上探索裝置。

## <a name="streaming-and-playback"></a>串流和播放

**我可以使用 HLS 或虛線之類的串流技術，從邊緣播放記錄到 Azure 媒體服務的資產嗎？**

是。 您可以串流記錄的資產，例如 Azure 媒體服務中的任何其他資產。 若要串流處理內容，您必須建立串流端點並處於執行中狀態。 使用標準串流定位器建立程式，可讓您存取 Apple HTTP 即時串流 (HLS) 或透過 HTTP 的動態自動調整資料流程 (破折號，也稱為 MPEG 虛線) 資訊清單，以串流至任何可用的播放程式架構。 如需有關建立和發佈 HLS 或虛線資訊清單的詳細資訊，請參閱 [動態封裝](../latest/dynamic-packaging-overview.md)。

**我可以在封存的資產上，使用媒體服務的標準內容保護和 DRM 功能嗎？**

是。 所有標準動態加密內容保護和數位版權管理 (DRM) 功能都可用於從 media graph 錄製的資產。

**我可以使用哪些玩家來查看所記錄資產的內容？**

支援符合規範的 HLS 第3版或第4版的所有標準播放程式都受到支援。 此外，也支援任何能夠符合標準之 MPEG 播放的播放機。

建議的測試播放機包括：

* [Azure 媒體播放器](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka Player](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple 原生 HTTP 即時串流](https://developer.apple.com/streaming/)
* Edge、Chrome 或 Safari 內建的 HTML5 影片播放
* 支援 HLS 或虛線播放的商業播放機

**串流 media graph 資產有哪些限制？**

從 media graph 串流處理實況或已錄製的資產時，會使用媒體服務支援的相同高規模基礎結構和串流端點，以供媒體 & 娛樂的隨選和即時串流處理，並放在最上層 (OTT) 及廣播客戶。 這表示您可以快速且輕鬆地啟用 Azure 內容傳遞網路、Verizon 或 Akamai，以根據您的案例，將內容傳遞給使用者，只需幾個檢視器或多達數百萬個。

您可以使用 Apple HLS 或 MPEG 虛線來傳遞內容。

## <a name="design-your-ai-model"></a>設計您的 AI 模型 

**我有多個 AI 模型包裝在 Docker 容器中。如何搭配即時影片分析使用它們？** 

解決方案會根據推斷伺服器用來與即時影片分析進行通訊的通訊協定而有所不同。 下列各節說明每個通訊協定的運作方式。

*使用 HTTP 通訊協定*：

* 單一容器 (單一 lvaExtension) ：  

   在您的推斷伺服器中，您可以使用單一端口，但不同的 AI 模型各有不同的端點。 例如，針對 Python 範例，每個模型可以使用不同的 `route` ，如下所示： 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   然後，在您的即時影片分析部署中，當您具現化圖形時，請設定每個實例的推斷伺服器 URL，如下所示： 

   第1個實例：推斷伺服器 URL =`http://lvaExtension:44000/score/face_detection`<br/>
   第二個實例：推斷伺服器 URL =`http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > 或者，您可以在不同的埠上公開 AI 模型，並在具現化圖形時呼叫它們。  

* 多個容器： 

   每個容器都會以不同的名稱部署。 先前，在「即時影片分析」檔集中，我們示範了如何部署名為 *lvaExtension* 的延伸模組。 現在您可以開發兩個不同的容器，每個容器都有相同的 HTTP 介面，這表示它們具有相同的 `/score` 端點。 使用不同的名稱部署這兩個容器，並確定兩者都在 *不同的埠* 上接聽。 

   例如，一個名為的容器 `lvaExtension1` 會接聽埠 `44000` ，而另一個名為的容器 `lvaExtension2` 會接聽埠 `44001` 。 

   在您的即時影片分析拓撲中，您會使用不同的推斷 Url 來將兩個圖形具現化，如下所示： 

   第一個實例：推斷伺服器 URL = `http://lvaExtension1:44001/score`    
   第二個實例：推斷伺服器 URL = `http://lvaExtension2:44001/score`
   
*使用 gRPC 通訊協定*： 

* 使用即時影片分析模組1.0 時，當您使用一般用途的遠端程序呼叫 (gRPC) 通訊協定時，唯一的方法就是 gRPC 伺服器透過不同的埠公開不同的 AI 模型。 在 [此程式碼範例](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)中，單一端口44000會公開所有的 yolo 模型。 理論上，可以重寫 yolo gRPC 伺服器，以在埠44000和其他埠45000公開一些模型。 

* 使用即時影片分析模組2.0 時，會將新的屬性新增至 gRPC 延伸模組節點。 這個屬性 **extensionConfiguration** 是選擇性字串，可當做 gRPC 合約的一部分使用。 當您將多個 AI 模型封裝在單一推斷伺服器中時，您不需要為每個 AI 模型公開一個節點。 相反地，針對圖形實例，您可以使用 **extensionConfiguration** 屬性來定義如何選取不同的 AI 模型，作為擴充提供者。 在執行期間，即時影片分析會將此字串傳遞至推斷伺服器，以用來叫用所需的 AI 模型。 

**我正在建立 AI 模型的 gRPC 伺服器，而且我希望能夠支援多個相機或圖形實例的使用。如何建立我的伺服器？** 

 首先，請確定您的伺服器可以一次處理一個以上的要求，或在平行線程中工作。 

例如，下列 [即時影片分析 gRPC 範例](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)中已設定了預設的平行通道數目： 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

在先前的 gRPC 伺服器具現化中，伺服器一次只能開啟三個通道，或針對每個圖表拓撲實例開啟。 請勿嘗試將三個以上的實例連接到伺服器。 如果您嘗試開啟三個以上的通道，要求將會暫止，直到現有的通道下降為止。  

上述的 gRPC 伺服器會在 Python 範例中使用。 如果您是開發人員，您可以執行自己的伺服器，或使用先前的預設值來增加背景工作角色編號，而您可以將其設定為用於影片摘要的相機數目。 

若要設定和使用多個相機，您可以具現化多個圖形拓撲實例，每個實例都會指向相同或不同的推斷伺服器 (例如，上一段所述的伺服器) 。 

**我希望能夠在進行推斷決策之前，從上游接收多個畫面格。如何啟用此功能？** 

目前的 [預設範例](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) 以 *無狀態* 模式運作。 它們不會保留先前呼叫的狀態，甚至是呼叫者。 這表示多個拓撲實例可能會呼叫相同的推斷伺服器，但是伺服器無法區分呼叫者或每個呼叫端的狀態。 

*使用 HTTP 通訊協定*：

若要保留狀態、每個呼叫端或圖形拓樸實例，請使用呼叫端唯一的 HTTP 查詢參數來呼叫推斷伺服器。 例如，以下顯示每個實例的推斷伺服器 URL 位址：  

第1個拓撲實例 = `http://lvaExtension:44000/score?id=1`<br/>
第2個拓撲實例 = `http://lvaExtension:44000/score?id=2`

… 

在伺服器端，分數路由知道正在呼叫的物件。 如果識別碼 = 1，則可以針對該呼叫者或圖形拓樸實例，分別保留狀態。 然後，您可以將接收的影片框架保留在緩衝區中。 例如，使用陣列或具有 DateTime 索引鍵的字典，而值為框架。 然後，您可以定義要處理的伺服器， (在收到 *x* 個畫面格數目之後) 推斷。 

*使用 gRPC 通訊協定*： 

使用 gRPC 延伸模組時，每個會話都適用于單一相機摘要，因此不需要提供識別碼。 現在，使用 extensionConfiguration 屬性，您可以將影片框架儲存在緩衝區中，並定義要處理的伺服器 (推斷在收到 *x* 個畫面格數目之後) 。 

**特定容器上的所有 ProcessMediaStreams 都執行相同的 AI 模型嗎？** 

否。 在圖形實例中啟動或停止來自終端使用者的呼叫會構成會話，或可能是相機中斷連線或重新連線。 目標是要在相機正在串流處理影片時保存一個會話。 

* 傳送影片進行處理的兩個攝影機會建立兩個會話。 
* 一個相機進入具有兩個 gRPC 延伸模組節點的圖形會建立兩個會話。 

每個會話都是即時影片分析和 gRPC 伺服器之間的全雙工連線，而且每個會話可以有不同的模型或管線。 

> [!NOTE]
> 當攝影機中斷連線或重新連線時，如果攝影機離線一段時間超過容錯限制，則即時影片分析將會開啟 gRPC 伺服器的新會話。 伺服器不需要追蹤這些會話之間的狀態。 

Live Video Analytics 也為圖形實例中的單一相機新增了多個 gRPC 擴充功能的支援。 您可以使用這些 gRPC 延伸模組，以平行方式或兩者的組合來執行 AI 處理。 

> [!NOTE]
> 以平行方式執行多個擴充功能將會影響您的硬體資源。 當您選擇的硬體符合您的計算需求時，請記住這一點。 

**同時 ProcessMediaStreams 的最大數目為何？** 

即時影片分析不會對此數目套用限制。  

**如何決定我的推斷伺服器是否應使用 CPU 或 GPU 或任何其他硬體加速器？** 

您的決定取決於開發 AI 模型的複雜度，以及您想要如何使用 CPU 和硬體加速器。 當您開發 AI 模型時，您可以指定模型應該使用的資源，以及應執行的動作。 

**如何? 在處理後使用周框方塊儲存影像？** 

目前，我們只會將周框方塊座標提供為推斷訊息。 您可以建立自訂 MJPEG 流處理器，以使用這些訊息，並覆迭影片畫面上的周框方塊。  

## <a name="grpc-compatibility"></a>gRPC 相容性 

**我要如何知道媒體串流描述項的強制欄位為何？** 

任何您未提供值的欄位都會提供 [預設值，如 gRPC 所指定](https://developers.google.com/protocol-buffers/docs/proto3#default)。  

即時影片分析會使用 *proto3* 版本的通訊協定緩衝區語言。 即時影片分析合約所使用的所有通訊協定緩衝區資料都可在 [通訊協定緩衝區](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)檔中使用。 

**如何確保我使用的是最新的通訊協定緩衝區檔案？** 

您可以取得 [合約檔案網站](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)上的最新通訊協定緩衝區檔。 只要更新合約檔案，這些檔案就會在此位置。 沒有立即的方案可以更新通訊協定檔案，因此請在檔案頂端尋找套件名稱以瞭解版本。 它必須是： 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

這些檔案的任何更新都會遞增名稱結尾的 "v-value"。 

> [!NOTE]
> 因為即時影片分析會使用 proto3 版本的語言，所以欄位是選擇性的，而版本是回溯和向前相容。 

**我可以使用哪些 gRPC 功能來搭配即時影片分析？哪些是必要的功能，哪些是選擇性？** 

您可以使用任何伺服器端 gRPC 功能，前提是已滿足通訊協定緩衝區 (Protobuf) 合約。 

## <a name="monitoring-and-metrics"></a>監視和計量

**我可以使用 Azure 事件方格來監視邊緣上的媒體圖形嗎？**

是。 您可以取用 Prometheus 計量，並將其發佈至您的事件方格。 

**我可以使用 Azure 監視器來查看我在雲端或邊緣上媒體圖形的健康情況、計量和效能嗎？**

是的，我們支援這種方法。 若要深入瞭解，請參閱 [Azure 監視器計量總覽](../../azure-monitor/platform/data-platform-metrics.md)。

**是否有任何工具可讓您更輕鬆地監視 Media Services IoT Edge 模組？**

Visual Studio Code 支援 Azure IoT Tools 擴充功能，可讓您輕鬆地監視 LVAEdge 模組端點。 您可以使用此工具來快速開始監視 IoT 中樞內建端點的「事件」，並查看從 edge 裝置路由傳送至雲端的推斷訊息。 

此外，您可以使用此擴充功能來編輯 LVAEdge 模組的模組對應項，以修改 media graph 設定。

如需詳細資訊，請參閱 [監視和記錄](monitoring-logging.md) 文章。

## <a name="billing-and-availability"></a>計費與可用性

**Live Video Analytics 如何計費 IoT Edge？**

如需計費的詳細資訊，請參閱 [媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="next-steps"></a>後續步驟

[快速入門：開始使用 IoT Edge 上的即時影片分析](get-started-detect-motion-emit-events-quickstart.md)