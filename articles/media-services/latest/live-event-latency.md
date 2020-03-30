---
title: Azure 媒體服務中的 LiveEvent 低延遲設置 |微軟文檔
description: 本主題概述了 LiveEvent 低延遲設置，並演示如何設置低延遲。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: a82a0644fac099b568ab86ea213b98cd8e7d5c22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199643"
---
# <a name="live-event-low-latency-settings"></a>即時事件低延遲設置

本文說明如何在[實況活動](https://docs.microsoft.com/rest/api/media/liveevents)上設定低延遲。 它也會討論您在各種播放器中使用低延遲設定時會看到的典型結果。 結果會隨著 CDN 和網路延遲而有所不同。

若要使用新的 **LowLatency** 功能，您可以在 **LiveEvent** 上將 **StreamOptionsFlag** 設定為 **LowLatency**。 在建立 [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) 以便播放 HLS 時，請將 [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) 設定為 1。 啟動並運行流後，可以使用[Azure 媒體播放機](https://ampdemo.azureedge.net/)（AMP 演示頁），並將播放選項設置為使用"低延遲啟發式設定檔"。

> [!NOTE]
> 目前，Azure 媒體播放機中的低延遲啟發式設定檔設計用於在 MPEG-DASH 協定中播放流，其格式為 CSF 或 CMAF 格式`format=mdp-time-csf`（`format=mdp-time-cmaf`例如，或 ）。 

下列 .NET 範例會示範如何在 **LiveEvent** 上設定 **LowLatency**：

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

參閱完整範例：[MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126)。

## <a name="live-events-latency"></a>實況活動延遲

下表會顯示媒體服務中延遲 (啟用 LowLatency 旗標時) 的典型結果，其測量方式為從發佈摘要抵達服務到檢視器在播放器上看見播放之間的時間。 若要以最佳方式使用低延遲，您應該將您的編碼器設定下調為 1 秒「圖片群組」(GOP) 長度。 使用較高的 GOP 長度時，您可在相同的畫面播放速率下，將頻寬耗用量降至最低並減少位元速率。 這對動作較少的影片特別有幫助。

### <a name="pass-through"></a>傳遞 

||已啟用 2 秒 GOP 低延遲|已啟用 1 秒 GOP 低延遲|
|---|---|---|
|AMP 中的 DASH|10 秒|8 秒|
|原生 iOS 播放程式上的 HLS|14 秒|10 秒|

### <a name="live-encoding"></a>即時編碼

||已啟用 2 秒 GOP 低延遲|已啟用 1 秒 GOP 低延遲|
|---|---|---|
|AMP 中的 DASH|14 秒|10 秒|
|原生 iOS 播放程式上的 HLS|18 秒|13 秒|

> [!NOTE]
> 端對端延遲可能會因區域網路狀況或引進 CDN 快取層而有所不同。 建議您測試實際的設定。

## <a name="next-steps"></a>後續步驟

- [即時串流概觀](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)

