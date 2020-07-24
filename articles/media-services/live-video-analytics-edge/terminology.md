---
title: IoT Edg 術語的即時影片分析-Azure
description: 本文提供有關 IoT Edge 術語的即時影片分析總覽。
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: 5d4eff506b2a6f51b9803f827379b9ba0c2b2ff6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011493"
---
# <a name="terminology"></a>詞彙

本文提供有關[IoT Edge 上即時影片分析](overview.md)的術語總覽。

## <a name="azure-media-services"></a>Azure 媒體服務

Azure 媒體服務是一種雲端媒體平臺，可讓您建立媒體解決方案。 您可以在[Azure 媒體服務](../latest/media-services-overview.md)檔中深入瞭解。

## <a name="asset"></a>資產

[資產](../latest/assets-concept.md)是 Azure 媒體服務中的實體，會對應到 Azure 儲存體帳戶中附加至媒體服務帳戶的 blob 容器。 與資產相關聯的所有檔案會儲存為該容器中的 blob，而媒體服務會保存與資產相關聯的中繼資料（例如名稱、描述、建立時間）。

IoT Edge 上的即時影片分析可以建立資產及/或將資料新增至現有的資產。 這可讓您進行連續和以事件為基礎的影片錄製和播放（透過邊緣裝置上的影片捕捉、記錄到 Azure 媒體服務，以及透過現有的 Azure 媒體服務串流功能播放）。

## <a name="streaming"></a>串流

如果您已從 Netflix、YouTube 等服務在行動裝置上觀看影片，就有經驗豐富的串流影片。 當您按下 [播放] （如果您有足夠的頻寬）時，就會開始播放，而且可以沿著影片的時間軸來回搜尋。 使用串流時，其概念是只提供正在監看的影片部分，並在資料仍從伺服器傳輸到播放用戶端時，讓檢視器開始播放影片。 在 Azure 媒體服務的內容中，[串流](https://en.wikipedia.org/wiki/Streaming_media)是指將媒體從[Azure 媒體服務](../azure-media-player/azure-media-player-overview.md)傳遞至串流用戶端的程式（例如，Azure 媒體播放機）。 您可以使用 Azure 媒體服務，使用業界標準的 HTTP 型媒體串流通訊協定[（例如 HTTP 即時串流（HLS）](https://developer.apple.com/streaming/)和[MPEG-破折號](https://dashif.org/about/)）將影片串流至用戶端。 HLS 受到 Azure 媒體播放機以及[JW Player](https://www.jwplayer.com/)、 [hls.js](https://github.com/video-dev/hls.js/)、 [VideoJS](https://videojs.com/)、 [Google Shaka Player](https://github.com/google/shaka-player)等 web 播放機的支援，您也可以使用 Android 的[Exoplayer](https://github.com/google/ExoPlayer)和 iOS 的[AV 基礎](https://developer.apple.com/av-foundation/)，在行動應用程式中以原生方式呈現。 Azure 媒體播放機，同樣支援 MPEG 虛線，請[在此頁面上尋找用戶端清單](https://dashif.org/clients/)。 

藉由使用[media graph](#media-graph)來錄製影片到 Azure 媒體服務中的資產，您可以使用媒體服務串流功能，以 HLS 和破折號傳遞串流。 您可以在[影片播放](video-playback-concept.md)文章中深入瞭解。

## <a name="recording"></a>記錄

在安全攝影機的影片管理系統內容中，影片錄製指的是從攝影機捕獲影片，並將其儲存在檔案（或檔案）中，以供後續透過行動和瀏覽器應用程式進行觀看的過程。 影片錄製可以分類成[持續的錄影](continuous-video-recording-concept.md)和以[事件為基礎的影片錄影](event-based-video-recording-concept.md)。 這些資訊會在[影片錄製](video-recording-concept.md)概念頁面中詳細說明。

## <a name="media-graph"></a>媒體圖表

[Media graph](media-graph-concept.md)可讓您定義應該從何處捕獲媒體、應該如何處理，以及應該在何處傳遞結果。 它可讓您定義由來源、處理器和接收節點組成的圖表，因此可讓您建立即時的影片分析應用程式。 Media graph 在 media graph 的概念頁面中會有詳細的說明。

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326)指的是即時串流通訊協定。 它是應用層級的通訊協定，可控制以即時屬性傳遞資料的方式。 RTSP 提供可擴充的架構，可讓您控制、依需求傳遞即時資料，例如音訊和影片。 

## <a name="vms"></a>VM

[Vm](https://en.wikipedia.org/wiki/Video_management_system)指的是影片管理系統。 這類系統是用來設定和控制 CCTV 攝影機、捕捉及錄製影片。 這些系統也會提供用戶端應用程式播放錄製的影片

## <a name="next-steps"></a>接下來的步驟

[媒體圖形](media-graph-concept.md)
