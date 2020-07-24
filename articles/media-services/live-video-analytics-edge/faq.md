---
title: IoT Edge 常見問題的即時影片分析-Azure
description: 本主題提供有關 IoT Edge 常見問題的即時影片分析解答。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 705c74b1ba6d3a28911185b82a558099e87dc18d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011510"
---
# <a name="frequently-asked-questions-faqs"></a>常見問題集 (FAQ)

本主題提供有關 IoT Edge 常見問題的即時影片分析解答。

## <a name="general"></a>一般

可以在圖形拓撲定義中使用的系統變數有哪些？

|變數   |描述|
|---|---|
|[System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|表示時間的瞬間，通常以一天的日期和時間表示。|
|GraphTopologyName   |代表 media graph 拓撲，包含圖形的藍圖。|
|GraphInstanceName|  代表 media graph 實例，保留參數值並參考拓撲。|

## <a name="configuration-and-deployment"></a>設定和部署

我可以將媒體邊緣模組部署到 Windows 10 裝置嗎？
    * 是。 請參閱[Windows 10 上的 Linux 容器](/virtualization/windowscontainers/deploy-containers/linux-containers)文章。

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>從 IP 攝影機和 RTSP 設定進行捕獲

* 我需要在裝置上使用特殊的 SDK 才能傳送影片串流嗎？
    * 不可以。 IoT Edge 上的即時影片分析支援使用 RTSP 影片串流通訊協定（在大部分的 IP 攝影機上都支援）來捕獲媒體。
* 我可以使用 RTMP 或平滑方式（例如媒體服務實況活動），在 IoT Edge 上推送媒體到即時影片分析嗎？
    * 不可以。 LVA 只支援從 IP 攝影機捕捉影片的 RTSP。
    * 任何支援透過 TCP/HTTP 之 RTSP 串流的相機都應該能正常執行。 
* 我可以在圖形執行個體上重設或更新 RTSP 來源 URL 嗎？
    * 是，當圖形實例處於非作用中狀態時。  
* 在測試和開發期間是否有可使用的 RTSP 模擬器？
    * 是。 您可以在快速入門和教學課程中使用[RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)模擬器 edge 模組來支援學習程式。 該課程模組會盡可能提供，但不一定隨時可用。 強烈建議您不要使用此資訊超過幾個小時。 在規劃生產環境部署之前，您應該投入實際的 RTSP 來源來進行測試。
* 是否支援於邊緣 ONVIF 探索 IP 攝影機？
    * 否，不支援邊緣的 ONVIF 裝置探索。

## <a name="streaming-and-playback"></a>串流和播放

* 從邊緣記錄到 AMS 的資產，是否可以使用 HLS 或破折號之類的媒體服務串流技術來播放？
    * 是。 記錄的資產可以像 Azure 媒體服務中的任何其他資產一樣進行串流處理。 若要串流處理內容，您必須已建立串流端點並處於執行中狀態。 使用標準串流定位器建立程式，可讓您存取 HLS 或破折號資訊清單，以串流處理至任何支援的播放程式架構。 如需建立發行 HLS 或虛線資訊清單的詳細資訊，請參閱[動態封裝](../latest/dynamic-packaging-overview.md)。
* 我可以在封存的資產上使用媒體服務的標準內容保護和 DRM 功能嗎？
    * 是。 所有標準動態加密內容保護和 DRM 功能都可用於從 media graph 記錄的資產。
* 我可以使用哪些播放機來從已記錄的資產中查看內容？
   * 支援符合 Apple HTTP 即時串流（HLS）第3版或第4版的所有標準播放程式。 此外，也支援任何能夠符合相容 MPEG 破折號播放的播放程式。
    建議的測試播放機包括：

    * [Azure 媒體播放器](../latest/use-azure-media-player.md)
    * [HLS.js](https://hls-js.netlify.app/demo/)
    * [Video.js](https://videojs.com/)
    * [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
    * [Shaka Player](https://github.com/google/shaka-player)
    * [ExoPlayer](https://github.com/google/ExoPlayer)
    * [Apple native HTTP 即時串流](https://developer.apple.com/streaming/)
    * 邊緣、Chrome 或 Safari 內建的 HTML5 影片播放
    * 支援 HLS 或破折號播放的商業播放機
* 串流處理 media graph 資產的限制為何？
    * 從媒體圖形串流處理實況或已記錄的資產時，會使用相同的大規模基礎結構和串流端點，媒體服務支援媒體 & 娛樂、OTT 和廣播客戶的隨選和即時串流。 這表示您可以快速且輕鬆地啟用 Azure CDN、Verizon 或 Akamai，將您的內容傳遞給只有幾個檢視器的物件，或根據您的案例多達數百萬個。

    您可以使用 Apple HTTP 即時串流（HLS）或 MPEG 破折號來傳遞內容。

## <a name="monitoring-and-metrics"></a>監視和計量

* 我可以使用事件方格來監視邊緣上的媒體圖形嗎？
    * 不可以。 目前不支援事件方格。
* 我是否可以使用 Azure 監視器來查看雲端或邊緣上媒體圖形的健全狀況、計量和效能？
    * 不可以。
* 是否有任何工具可讓您更輕鬆地監視媒體服務 IoT Edge 模組？
    * Visual Studio Code 支援「Azure IoT Tools」延伸模組，可讓您輕鬆地監視 LVAEdge 模組端點。 您可以使用此工具快速開始監視「事件」的 IoT 中樞內建端點，並查看從 edge 裝置路由至雲端的推斷訊息。 

    此外，您可以使用此擴充功能來編輯 LVAEdge 模組的模組對應項，以修改 media graph 設定。

如需詳細資訊，請參閱[監視和記錄](monitoring-logging.md)一文。

## <a name="billing-and-availability"></a>計費與可用性

* IoT Edge 上的 LiveVideo 分析如何計費？
    * 如需詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="next-steps"></a>接下來的步驟

[快速入門：開始使用 - IoT Edge 上的 Live Video Analytics](get-started-detect-motion-emit-events-quickstart.md)
