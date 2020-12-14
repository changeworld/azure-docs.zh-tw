---
title: IoT Edge 常見問題的即時影片分析-Azure
description: 本主題提供 IoT Edge 常見問題的即時影片分析解答。
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 521cd0e4f5fc8232a000e10520298a979ba1c14c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401571"
---
# <a name="frequently-asked-questions-faqs"></a>常見問題集 (FAQ)

本主題提供 IoT Edge 常見問題的即時影片分析解答。

## <a name="general"></a>一般

### <a name="what-are-the-system-variables-that-can-be-used-in-graph-topology-definition"></a>可以在圖形拓撲定義中使用的系統變數有哪些？

|變數   |描述|
|---|---|
|[System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|代表 UTC 時間的瞬間，通常表示為 (基本標記法 yyyyMMddTHHmmssZ) 的日期和時間。|
|System. PreciseDateTime|表示以 ISO8601 檔案相容格式的 UTC 日期時間實例，以毫秒為單位 (基本表示 yyyyMMddTHHmmss. Ss.fffz) 。|
|System. GraphTopologyName   |代表 media graph 拓撲，其中包含圖形的藍圖。|
|System. GraphInstanceName|  代表 media graph 實例、保存參數值，並參考拓撲。|

## <a name="configuration-and-deployment"></a>設定和部署

### <a name="can-i-deploy-the-media-edge-module-to-a-windows-10-device"></a>我可以將 media edge 模組部署到 Windows 10 裝置嗎？

是。 請參閱 Windows 10 上的 [Linux 容器](/virtualization/windowscontainers/deploy-containers/linux-containers)文章。

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>從 IP 相機和 RTSP 設定進行捕捉

### <a name="do-i-need-to-use-a-special-sdk-on-my-device-to-send-in-a-video-stream"></a>我需要在裝置上使用特殊的 SDK 才能傳送影片串流嗎？

不正確。 IoT Edge 上的即時影片分析支援使用 RTSP 影片串流通訊協定來捕捉媒體， (在大部分的 IP 攝影機) 中都有支援。

### <a name="can-i-push-media-to-live-video-analytics-on-iot-edge-using-rtmp-or-smooth-like-a-media-services-live-event"></a>我可以使用 RTMP 或平滑 (（像是媒體服務實況活動) ）將媒體推送至 IoT Edge 的媒體到即時影片分析嗎？

* 不正確。 即時影片分析僅支援從 IP 攝影機捕獲影片的 RTSP。
* 任何透過 TCP/HTTP 支援 RTSP 串流的攝影機都應可運作。 

### <a name="can-i-reset-or-update-the-rtsp-source-url-on-a-graph-instance"></a>我可以在圖形執行個體上重設或更新 RTSP 來源 URL 嗎？

是，當圖形實例處於非使用中狀態時。  

### <a name="is-there-a-rtsp-simulator-available-to-use-during-testing-and-development"></a>在測試和開發期間是否可使用 RTSP 模擬器？

是。 您可以在快速入門和教學課程中使用 [RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) 模擬器 edge 模組，以支援學習流程。 該課程模組會盡可能提供，但不一定隨時可用。 強烈建議您不要使用此功能超過數小時。 在規劃生產環境部署的計畫之前，您應該先投資實際的 RTSP 來源進行測試。

### <a name="do-you-support-onvif-discovery-of-ip-cameras-at-the-edge"></a>是否支援於邊緣 ONVIF 探索 IP 攝影機？

否，不支援邊緣的 ONVIF 裝置探索。

## <a name="streaming-and-playback"></a>串流和播放

### <a name="can-assets-recorded-to-ams-from-the-edge-be-played-back-using-media-services-streaming-technologies-like-hls-or-dash"></a>是否可以使用媒體服務串流技術（例如 HLS 或虛線）來播放從 edge 記錄到 AMS 的資產？

是。 記錄的資產可以像 Azure 媒體服務中的任何其他資產一樣進行串流處理。 若要串流處理內容，您必須建立串流端點並處於執行中狀態。 使用標準串流定位器建立程式，將可讓您存取 HLS 或虛線資訊清單，以串流至任何可用的播放程式架構。 如需建立發行 HLS 或虛線資訊清單的詳細資訊，請參閱 [動態封裝](../latest/dynamic-packaging-overview.md)。

### <a name="can-i-use-the-standard-content-protection-and-drm-features-of-media-services-on-an-archived-asset"></a>我可以在封存的資產上，使用媒體服務的標準內容保護和 DRM 功能嗎？

是。 所有標準動態加密內容保護和 DRM 功能都可用於從媒體圖形記錄的資產。

### <a name="what-players-can-i-use-to-view-content-from-the-recorded-assets"></a>我可以使用哪些玩家來查看所記錄資產的內容？

支援符合規範的 Apple HTTP 即時串流 (HLS) 第3版或第4版的所有標準播放程式都受到支援。 此外，也支援任何能夠符合標準之 MPEG 播放的播放機。

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

### <a name="what-are-the-limits-on-streaming-a-media-graph-asset"></a>串流 media graph 資產有哪些限制？

從 media graph 串流處理實況或已錄製的資產，會使用媒體服務支援的相同高規模基礎結構和串流端點，以供媒體 & 娛樂、OTT 和廣播客戶的隨選和即時串流使用。 這表示您可以快速且輕鬆地啟用 Azure CDN、Verizon 或 Akamai，將您的內容傳遞給使用者，只要使用幾個檢視器，或根據您的案例，最多可達百萬。

您可以使用 Apple HTTP 即時串流 (HLS) 或 MPEG 虛線來傳遞內容。

## <a name="design-your-ai-model"></a>設計您的 AI 模型 

### <a name="i-have-multiple-ai-models-wrapped-in-a-docker-container-how-should-i-use-them-with-live-video-analytics"></a>我有多個 AI 模型包裝在 docker 容器中。 如何搭配即時影片分析使用它們？ 

解決方案會根據推斷伺服器用來與即時影片分析進行通訊的通訊協定而有所不同。 以下是執行這項操作的一些方式。

#### <a name="http-protocol"></a>HTTP 通訊協定：

* 單一容器 (單一 lvaExtension) ：  

   在您的推斷伺服器中，您可以使用單一端口，但不同的 AI 模型各有不同的端點。 例如，針對 Python 範例，每個模型可以使用不同的 `route` 作為： 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   然後，在您的即時影片分析部署中，當您具現化圖形時，請將每個實例的推斷伺服器 URL 設定為： 

   第1個實例：推斷伺服器 URL =`http://lvaExtension:44000/score/face_detection`<br/>
   第二個實例：推斷伺服器 URL =`http://lvaExtension:44000/score/vehicle_detection`  
    > [!NOTE]
    > 或者，您也可以在不同的埠上公開 AI 模型，並在具現化圖形時呼叫它們。  

* 多個容器： 

   每個容器都會以不同的名稱部署。 目前，在即時影片分析檔集中，我們示範了如何部署名稱為： **lvaExtension** 的延伸模組。 現在您可以開發兩個不同的容器。 每個容器都有相同的 HTTP 介面 (表示相同 `/score` 的端點) 。 使用不同的名稱部署這兩個容器，並確定兩者都在 **不同的埠** 上接聽。 

   例如，一個名稱為的容器 `lvaExtension1` 正在接聽埠 `44000` ，另一個名稱為的容器 `lvaExtension2` 正在接聽埠 `44001` 。 

   在您的即時影片分析拓撲中，您會使用不同的推斷 Url 來具現化兩個圖形，例如： 

   第一個實例：推斷伺服器 URL = `http://lvaExtension1:44001/score`    
   第二個實例：推斷伺服器 URL = `http://lvaExtension2:44001/score`
   
#### <a name="grpc-protocol"></a>GRPC 通訊協定： 

使用即時影片分析模組1.0，使用 gRPC 通訊協定時，唯一的方法是 gRPC 伺服器透過不同的埠公開不同的 AI 模型。 在 [此範例](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)中，有一個會公開所有 yolo 模型的單一端口44000。 理論上，可以重寫 yolo gRPC 伺服器，以公開一些位於44000的模型，其他則是在45000，.。。 

使用即時影片分析模組2.0 時，會將新的屬性新增至 gRPC 延伸模組節點。 這個屬性稱為 **extensionConfiguration** ，它是選擇性字串，可當做 gRPC 合約的一部分使用。 當您將多個 AI 模型封裝在單一推斷伺服器中時，您不需要為每個 AI 模型公開一個節點。 相反地，針對圖形實例，您) 的擴充提供者 (可以定義如何使用 **extensionConfiguration** 屬性來選取不同的 AI 模型，而在執行期間，即時影片分析會將此字串傳遞至推斷伺服器，而此伺服器可以用來叫用所需的 AI 模型。 

### <a name="i-am-building-a-grpc-server-around-an-ai-model-and-want-to-be-able-to-support-being-used-by-multiple-camerasgraph-instances-how-should-i-build-my-server"></a>我在 AI 模型周圍建立了 gRPC 伺服器，而且想要能夠支援多個相機/圖形實例使用。 如何建立我的伺服器？ 

 首先，請確定您的伺服器一次可以處理一個以上的要求。 或者，確定您的伺服器可在平行線程中運作。 

例如，在其中一個 [即時影片分析 GRPC 範例](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)中，會設定預設的平行通道數目。 請參閱： 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

在上述的 gRPC 伺服器具現化中，伺服器只能開啟每個相機的三個通道 (因此，每個圖表拓撲實例) 一次。 您不應該嘗試將三個以上的實例連接到伺服器。 如果您嘗試開啟三個以上的通道，要求將會暫止，直到現有的要求都下降為止。  

上述的 gRPC 伺服器會在 Python 範例中使用。 開發人員可以執行自己的伺服器，或在上述的預設執行中，將背景工作號碼設定為用來取得影片摘要的相機數目。 

若要設定和使用多個相機，開發人員可以將多個圖形拓撲實例具現化，其中每個實例指向相同或不同的推斷伺服器 (例如，上一段所述的伺服器) 。 

### <a name="i-want-to-be-able-to-receive-multiple-frames-from-upstream-before-i-make-an-inferencing-decision-how-can-i-enable-that"></a>我希望能夠在進行推斷決策之前，從上游接收多個畫面格。 如何啟用此功能？ 

目前的 [預設範例](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) 會以「無狀態」模式運作。 這些範例不會保留先前呼叫的狀態，甚至是呼叫 (這表示多個拓撲實例可能會呼叫相同的推斷伺服器，而伺服器將無法區分呼叫者和每個呼叫者的狀態)  

#### <a name="http-protocol"></a>HTTP 通訊協定

使用 HTTP 通訊協定時： 

為保持狀態，每個呼叫端 (圖形拓樸實例) 都會使用呼叫端唯一的 HTTP 查詢參數來呼叫推斷伺服器。 例如，的推斷伺服器 URL 位址  

第1個拓撲實例 = `http://lvaExtension:44000/score?id=1`<br/>
第2個拓撲實例 = `http://lvaExtension:44000/score?id=2`

… 

在伺服器端，分數路由將知道呼叫者。 如果識別碼 = 1，則可以將該呼叫者的狀態分開， (graph 拓撲實例) 。 然後，您可以將接收到的影片框架保留在緩衝區中 (例如陣列或具有 DateTime 索引鍵的字典，而值則是框架) ，然後您可以定義要處理的伺服器 (在收到 x 個畫面格之後) 推斷。 

#### <a name="grpc-protocol"></a>GRPC 通訊協定 

使用 gRPC 通訊協定時： 

使用 gRPC 延伸模組時，每個會話都適用于單一攝影機摘要，因此不需要提供識別碼。 因此，現在使用 extensionConfiguration 屬性，您可以將影片框架儲存在緩衝區中，並定義要處理的伺服器 (在收到 x 個畫面格之後) 推斷。 

### <a name="do-all-processmediastreams-on-a-particular-container-run-the-same-ai-model"></a>特定容器上的所有 ProcessMediaStreams 都執行相同的 AI 模型嗎？ 

不正確。  

開始/停止來自圖形實例上終端使用者的呼叫會構成一個會話，或可能有相機中斷連線/重新連線。 目標是要在相機正在串流處理影片時保存一個會話。 

* 兩張相機傳送影片進行處理時，會建立兩個會話。 
* 一個相機進入具有兩個 gRPCExtension 節點的圖形會建立兩個會話。 

每個會話都是即時影片分析和 gRPC 伺服器之間的全雙工連線，而且每個會話可以有不同的模型/管線。 

> [!NOTE]
> 如果攝影機中斷連線/重新連線 (，且相機離線一段時間超過容錯限制) ，即時影片分析將會開啟 gRPC 伺服器的新會話。 伺服器不需要追蹤這些會話之間的狀態。 

Live Video Analytics 也為圖形實例中的單一相機新增了多個 gRPC 擴充功能的支援。 您將能夠使用這些 gRPC 延伸模組，以順序或平行方式執行 AI 處理，或甚至兩者的組合。 

> [!NOTE]
> 以平行方式執行多個擴充功能將會影響您的硬體資源，而且您必須在選擇符合您計算需求的硬體時記住這點。 

### <a name="what-is-the-max--of-simultaneous-processmediastreams"></a>同時 ProcessMediaStreams 的最大數目為何？ 

即時影片分析沒有適用的限制。  

### <a name="how-should-i-decide-if-my-inferencing-server-should-use-cpu-or-gpu-or-any-other-hardware-accelerator"></a>如何判斷我的推斷伺服器是否應該使用 CPU 或 GPU 或任何其他硬體加速器？ 

這完全取決於 AI 模型的開發複雜度，以及開發人員想要如何使用 CPU 和硬體加速器。 開發 AI 模型時，開發人員可以指定模型應該使用哪些資源來執行哪些動作。 

### <a name="how-do-i-store-images-with-bounding-boxes-post-processing"></a>如何? 使用周框方塊進行後置處理來儲存影像？ 

目前，我們只會將周框方塊座標提供為推斷訊息。 開發人員可以建立可使用這些訊息的自訂 MJPEG 流處理器，並在影片畫面上重迭周框方塊。  

## <a name="grpc-compatibility"></a>gRPC 相容性 

### <a name="how-will-i-know-what-the-mandatory-fields-for-the-media-stream-descriptor-are"></a>我要如何知道媒體串流描述項的強制欄位為何？ 

任何未提供的域值都會提供預設值 [，如 gRPC 所指定](https://developers.google.com/protocol-buffers/docs/proto3#default)。  

即時影片分析使用 **proto3** 版本的通訊協定緩衝區語言。 Live Video Analytics 合約所使用的所有通訊協定緩衝區資料都可在 [此處定義](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)的通訊協定緩衝區檔案中取得。 

### <a name="how-should-i-ensure-that-i-am-using-the-latest-protocol-buffer-files"></a>如何確保我使用的是最新的通訊協定緩衝區檔案？ 

您可以在 [這裡取得](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)最新的通訊協定緩衝區檔。 只要更新合約檔案，這些檔案就會出現在此位置。 雖然沒有立即更新通訊協定檔案的計畫，但請在檔案頂端尋找套件名稱以瞭解版本。 它必須是： 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

這些檔案的任何更新都會遞增名稱結尾的 "v-value"。 

> [!NOTE]
> 由於即時影片分析使用 proto3 版本的語言，因此欄位是選擇性的，因此可讓它回溯和向前相容。 

### <a name="what-grpc-features-are-available-for-me-to-use-with-live-video-analytics-which-features-are-mandatory-and-which-ones-are-optional"></a>我可以使用哪些 gRPC 功能來搭配即時影片分析？ 哪些是必要的功能，哪些是選擇性功能？ 

只要滿足 protobuf 合約，即可使用任何伺服器端 gRPC 功能。 

## <a name="monitoring-and-metrics"></a>監視和計量

### <a name="can-i-monitor-the-media-graph-on-the-edge-using-event-grid"></a>我可以使用事件方格在邊緣上監視媒體圖形嗎？

是。 您可以取用 prometheus 計量，並將其發佈至事件方格。 

### <a name="can-i-use-azure-monitor-to-view-the-health-metrics-and-performance-of-my-media-graphs-in-the-cloud-or-on-the-edge"></a>我可以使用 Azure 監視器來查看我在雲端或邊緣上媒體圖形的健康情況、計量和效能嗎？

是。 這有受到支援。 深入瞭解 [如何使用 Azure 監視器計量](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)。

### <a name="are-there-any-tools-to-make-it-easier-to-monitor-the-media-services-iot-edge-module"></a>是否有任何工具可讓您更輕鬆地監視 Media Services IoT Edge 模組？

Visual Studio Code 支援 "Azure IoT Tools" 延伸模組，可讓您輕鬆地監視 LVAEdge 模組端點。 您可以使用此工具來快速開始監視「事件」的 IoT 中樞內建端點，並查看從 edge 裝置路由傳送至雲端的推斷訊息。 

此外，您可以使用此擴充功能來編輯 LVAEdge 模組的模組對應項，以修改 media graph 設定。

如需詳細資訊，請參閱 [監視和記錄](monitoring-logging.md) 文章。

## <a name="billing-and-availability"></a>計費與可用性

### <a name="how-is-live-video-analytics-on-iot-edge-billed"></a>Live Video Analytics 如何計費 IoT Edge？

如需詳細資料，請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) 。

## <a name="next-steps"></a>後續步驟

[快速入門：開始使用 - IoT Edge 上的 Live Video Analytics](get-started-detect-motion-emit-events-quickstart.md)
