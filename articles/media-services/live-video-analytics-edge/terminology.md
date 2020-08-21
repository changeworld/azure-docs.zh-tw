---
title: IoT Edg 術語的即時影片分析-Azure
description: 本文提供 IoT Edge 術語的即時影片分析總覽。
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: e3a77b69adf2241a4af2652db4edb6673a63b4f0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690607"
---
# <a name="terminology"></a>詞彙

本文概述 [IoT Edge 上的即時影片分析](overview.md)相關術語。

## <a name="azure-media-services"></a>Azure 媒體服務

Azure 媒體服務是一種雲端媒體平臺，可讓您建立媒體解決方案。 您可以在 [Azure 媒體服務](../latest/media-services-overview.md) 檔中深入瞭解。

## <a name="asset"></a>資產

[資產](../latest/assets-concept.md) 是 Azure 媒體服務中的實體，會對應至附加至媒體服務帳戶的 Azure 儲存體帳戶中的 blob 容器。 與資產相關聯的所有檔案都會儲存為該容器中的 blob，而媒體服務會保存中繼資料 (例如，名稱、描述、建立時間) 與資產相關聯。

IoT Edge 上的即時影片分析可以建立資產及/或將資料新增至現有的資產。 這可讓您在邊緣裝置上使用影片捕捉、錄製到 Azure 媒體服務，以及透過現有的 Azure 媒體服務串流功能) 播放，以進行連續和事件的影片錄製和播放 (案例。

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) 是一種語言中立、高效能的遠端程序呼叫， (RPC) framework。 它使用以會話為基礎的結構化架構，透過通訊 [協定緩衝區 3](https://developers.google.com/protocol-buffers/docs/proto3) 作為其基礎訊息交換格式來進行通訊。

## <a name="media-graph"></a>媒體圖表

[Media graph](media-graph-concept.md) 可讓您定義媒體的捕獲來源、處理方式，以及應該傳遞結果的位置。 它可讓您定義由來源、處理器和接收節點組成的圖表，因而讓您能夠建立即時影片分析應用程式。 Media graph 的概念頁面會詳細說明 media graph。

## <a name="recording"></a>記錄

在安全攝影機的影片管理系統內容中，影片錄製是指從攝影機捕獲影片，並將它儲存在檔案中的程式 (或檔案) ，以透過行動和瀏覽器應用程式進行後續的觀看。 影片錄製可以分類成 [持續的影片錄製](continuous-video-recording-concept.md) 和以 [事件為基礎的影片錄製](event-based-video-recording-concept.md)。 這些將在 [影片錄製](video-recording-concept.md) 概念頁面中更詳細地說明。

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) 指的是即時串流通訊協定。 它是一種應用層級的通訊協定，可讓您控制即時屬性的資料傳遞。 RTSP 提供可延伸的架構，可讓您依需求提供即時資料的受控制、隨選傳遞，例如音訊和影片。 

## <a name="streaming"></a>資料流

如果您已在行動裝置上觀看 Netflix、YouTube 等服務的影片，您就有經驗的串流影片。 當您按下 [播放] 之後，就會開始播放 (如果您有足夠的頻寬) ，而且可以沿著影片的時間軸來回搜尋。 透過串流處理，概念是只傳遞正在監看的部分影片，並讓檢視器在資料仍從伺服器傳送到播放用戶端時開始播放影片。 在 Azure 媒體服務的內容中， [串流](https://en.wikipedia.org/wiki/Streaming_media) 指的是將媒體從 [Azure 媒體服務](../azure-media-player/azure-media-player-overview.md) 傳遞至串流用戶端的程式 (例如 Azure 媒體播放機) 。 您可以使用 Azure 媒體服務，使用業界標準、HTTP 型媒體串流通訊協定（例如 [HTTP 即時串流 (HLS) ](https://developer.apple.com/streaming/) 和 [MPEG 破折號](https://dashif.org/about/)）將影片串流至用戶端。 HLS 支援 Azure 媒體播放機和 web 播放機（例如 [JW Player](https://www.jwplayer.com/)、 [hls.js](https://github.com/video-dev/hls.js/)、 [VideoJS](https://videojs.com/)、 [Google 的 Shaka Player](https://github.com/google/shaka-player)），或者您可以使用 Android 的 [Exoplayer](https://github.com/google/ExoPlayer) 和 iOS [AV Foundation](https://developer.apple.com/av-foundation/)在行動應用程式中以原生方式轉譯。 Azure 媒體播放機同樣支援 MPEG 虛線，請 [在此頁面上尋找用戶端清單](https://dashif.org/clients/)。 

藉由使用 [media graph](#media-graph)將影片記錄到 Azure 媒體服務中的資產，您可以使用媒體服務串流功能，以 HLS 和破折號傳遞影片串流。 您可以在 [影片播放](video-playback-concept.md) 文章中深入瞭解。

## <a name="vms"></a>Vm

[Vm](https://en.wikipedia.org/wiki/Video_management_system) 是指影片管理系統。 這類系統用來設定和控制 CCTV 攝影機、抓住和錄製影片。 這些系統也會提供用戶端應用程式播放錄製的影片

## <a name="next-steps"></a>後續步驟

[媒體圖形](media-graph-concept.md)
