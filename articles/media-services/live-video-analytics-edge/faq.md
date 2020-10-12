---
title: IoT Edge 常見問題的即時影片分析-Azure
description: 本主題提供 IoT Edge 常見問題的即時影片分析解答。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 705c74b1ba6d3a28911185b82a558099e87dc18d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011510"
---
# <a name="frequently-asked-questions-faqs"></a>常見問題集 (FAQ)

本主題提供 IoT Edge 常見問題的即時影片分析解答。

## <a name="general"></a>一般

可以在圖形拓撲定義中使用的系統變數有哪些？

|變數   |描述|
|---|---|
|[System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|表示時間的瞬間，通常以一天的日期和時間表示。|
|System. GraphTopologyName   |代表 media graph 拓撲，其中包含圖形的藍圖。|
|System. GraphInstanceName|  代表 media graph 實例、保存參數值，並參考拓撲。|

## <a name="configuration-and-deployment"></a>設定和部署

我可以將 media edge 模組部署到 Windows 10 裝置嗎？
    * 是。 請參閱 Windows 10 上的 [Linux 容器](/virtualization/windowscontainers/deploy-containers/linux-containers)文章。

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>從 IP 相機和 RTSP 設定進行捕捉

* 我需要在裝置上使用特殊的 SDK 才能傳送影片串流嗎？
    * 否。 IoT Edge 上的即時影片分析支援使用 RTSP 影片串流通訊協定來捕捉媒體， (在大部分的 IP 攝影機) 中都有支援。
* 我可以使用 RTMP 或平滑 (（像是媒體服務實況活動) ）將媒體推送至 IoT Edge 的媒體到即時影片分析嗎？
    * 否。 LVA 僅支援從 IP 攝影機捕獲影片的 RTSP。
    * 任何透過 TCP/HTTP 支援 RTSP 串流的攝影機都應可運作。 
* 我可以在圖形執行個體上重設或更新 RTSP 來源 URL 嗎？
    * 是，當圖形實例處於非使用中狀態時。  
* 在測試和開發期間是否可使用 RTSP 模擬器？
    * 是。 您可以在快速入門和教學課程中使用 [RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) 模擬器 edge 模組，以支援學習流程。 該課程模組會盡可能提供，但不一定隨時可用。 強烈建議您不要使用此功能超過數小時。 在規劃生產環境部署的計畫之前，您應該先投資實際的 RTSP 來源進行測試。
* 是否支援於邊緣 ONVIF 探索 IP 攝影機？
    * 否，不支援邊緣的 ONVIF 裝置探索。

## <a name="streaming-and-playback"></a>串流和播放

* 是否可以使用媒體服務串流技術（例如 HLS 或虛線）來播放從 edge 記錄到 AMS 的資產？
    * 是。 記錄的資產可以像 Azure 媒體服務中的任何其他資產一樣進行串流處理。 若要串流處理內容，您必須建立串流端點並處於執行中狀態。 使用標準串流定位器建立程式，將可讓您存取 HLS 或虛線資訊清單，以串流至任何可用的播放程式架構。 如需建立發行 HLS 或虛線資訊清單的詳細資訊，請參閱 [動態封裝](../latest/dynamic-packaging-overview.md)。
* 我可以在封存的資產上，使用媒體服務的標準內容保護和 DRM 功能嗎？
    * 是。 所有標準動態加密內容保護和 DRM 功能都可用於從媒體圖形記錄的資產。
* 我可以使用哪些玩家來查看所記錄資產的內容？
   * 支援符合規範的 Apple HTTP 即時串流 (HLS) 第3版或第4版的所有標準播放程式都受到支援。 此外，也支援任何能夠符合標準之 MPEG 播放的播放機。
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
* 串流 media graph 資產有哪些限制？
    * 從 media graph 串流處理實況或已錄製的資產，會使用媒體服務支援的相同高規模基礎結構和串流端點，以供媒體 & 娛樂、OTT 和廣播客戶的隨選和即時串流使用。 這表示您可以快速且輕鬆地啟用 Azure CDN、Verizon 或 Akamai，將您的內容傳遞給使用者，只要使用幾個檢視器，或根據您的案例，最多可達百萬。

    您可以使用 Apple HTTP 即時串流 (HLS) 或 MPEG 虛線來傳遞內容。

## <a name="monitoring-and-metrics"></a>監視和計量

* 我可以使用事件方格在邊緣上監視媒體圖形嗎？
    * 否。 目前不支援事件方格。
* 我可以使用 Azure 監視器來查看我在雲端或邊緣上媒體圖形的健康情況、計量和效能嗎？
    * 否。
* 是否有任何工具可讓您更輕鬆地監視 Media Services IoT Edge 模組？
    * Visual Studio Code 支援 "Azure IoT Tools" 延伸模組，可讓您輕鬆地監視 LVAEdge 模組端點。 您可以使用此工具來快速開始監視「事件」的 IoT 中樞內建端點，並查看從 edge 裝置路由傳送至雲端的推斷訊息。 

    此外，您可以使用此擴充功能來編輯 LVAEdge 模組的模組對應項，以修改 media graph 設定。

如需詳細資訊，請參閱 [監視和記錄](monitoring-logging.md) 文章。

## <a name="billing-and-availability"></a>計費與可用性

* LiveVideo Analytics 如何計費 IoT Edge？
    * 如需詳細資料，請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) 。

## <a name="next-steps"></a>接下來的步驟

[快速入門：開始使用 - IoT Edge 上的 Live Video Analytics](get-started-detect-motion-emit-events-quickstart.md)
