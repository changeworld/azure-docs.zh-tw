---
ms.openlocfilehash: 9c1b521a0f10da77295fd2457793566d787cb2cd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89570065"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs4.svg" alt-text="信號流動":::

上圖顯示此快速入門中的信號流動方式。 [邊緣模組](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)會模擬裝載即時串流通訊協定 (RTSP) 伺服器的 IP 攝影機。 [RTSP 來源](../../../media-graph-concept.md#rtsp-source)節點會從這部伺服器提取影片摘要，然後將影片畫面傳送至[動作偵測處理器](../../../media-graph-concept.md#motion-detection-processor)節點。 RTSP 來源會將相同的影片畫面傳送至[信號閘道處理器](../../../media-graph-concept.md#signal-gate-processor)節點，這會保持關閉，直到有事件觸發為止。

當動作偵測處理器偵測到影片中有動作時，就會將事件傳送至信號閘道處理器節點，並加以觸發。 閘道會在設定的持續時間內保持開啟，並將影片畫面傳送至[檔案接收](../../../media-graph-concept.md#file-sink)節點。 此接收節點會將影片以 MP4 檔案格式錄製到您邊緣裝置的本機檔案系統。 檔案會儲存在設定的位置。

在本快速入門中，您將：

1. 建立和部署媒體圖表。
1. 解譯解譯。
1. 清除資源。
