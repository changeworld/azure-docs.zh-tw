---
title: IoT Edge 配額的即時影片分析-Azure
description: 本文說明有關 IoT Edge 配額和限制的即時影片分析。
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 9b01db8f1120174806f4b687f7e9ebc4e2386f3d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260341"
---
# <a name="quotas-and-limitations"></a>配額和限制

本文列舉 IoT Edge 模組上即時影片分析的配額和限制。

## <a name="maximum-period-of-disconnected-use"></a>中斷連線使用的最長期間

邊緣模組可以承受暫時遺失網路連線。 如果模組保持中斷連接超過36小時，它會停用任何正在執行的圖形實例，而進一步的直接方法呼叫將會遭到封鎖。

若要將 edge 模組恢復為操作狀態，您必須還原網路連線，而且模組必須能夠成功與 Azure 媒體服務帳戶進行通訊。

## <a name="maximum-number-of-graph-instances"></a>圖形實例的最大數目

每個模組最多可以有1000個圖形實例（透過 GraphInstanceSet 建立）。

## <a name="maximum-number-of-graph-topologies"></a>圖形拓撲的最大數目

每個模組最多可以有50個圖形拓撲（透過 GraphTopologySet 建立）。

## <a name="limitations-on-graph-topologies-at-preview"></a>預覽時的圖形拓撲限制

在預覽版本中，不同節點的限制可以在 media graph 拓撲中連接在一起。

* RTSP 來源
   * 每個圖形拓撲只允許一個 RTSP 來源。
* 畫面播放速率篩選處理器
   * 必須立即從 RTSP 來源或動作偵測處理器下游。
   * 無法用於 HTTP 擴充處理器的下游。
   * 無法從動作偵測處理器進行上游。
* HTTP 擴充處理器
   * 每個圖形拓撲最多隻能有一個此類處理器。
* 動作偵測處理器
   * 必須立即從 RTSP 來源進行下游。
   * 每個圖形拓撲最多隻能有一個此類處理器。
   * 無法用於 HTTP 擴充處理器的下游。
* 信號閘道處理器
   * 必須立即從 RTSP 來源進行下游。
* 資產接收 
   * 每個圖形拓撲最多隻能有一個此類節點。
      * 如果使用了資產接收，則不能有 file 接收，反之亦然。
   * 必須立即從 RTSP 來源或信號閘道處理器下游。
* 檔接收
   * 每個圖形拓撲最多隻能有一個此類節點（請參閱關於資產接收的相關注意事項）。
   * 必須是來自信號閘道處理器的立即下游。
   * 無法立即成為 HTTP 延伸模組處理器或動作偵測處理器的下游
* IoT 中樞接收
   * 不能是 IoT 中樞來源的立即下游。

如果使用 [動作偵測] 和 [篩選速率] 處理器節點，它們應該位於通向 RTSP 來源節點的相同節點鏈中。

## <a name="limitations-on-media-service-operations-at-preview"></a>預覽時的媒體服務作業限制

在預覽版本期間，IoT Edge 上的即時影片分析不支援下列各項：

* 能夠將媒體服務帳戶從一個訂用帳戶遷移至另一個，而不會中斷。
* 能夠以媒體服務帳戶使用一個以上的儲存體帳戶。
* 不需要重新開機，就能動態變更模組所需屬性中的服務主體資訊。

## <a name="next-steps"></a>後續步驟

[概觀](overview.md)
