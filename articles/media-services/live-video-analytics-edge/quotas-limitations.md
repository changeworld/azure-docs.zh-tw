---
title: IoT Edge 配額和限制的即時影片分析-Azure
description: 本文說明 IoT Edge 配額和限制的即時影片分析。
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 68c7b91bb1051348b5a8e52f841d443894f0a632
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400519"
---
# <a name="quotas-and-limitations"></a>配額和限制

本文列舉 IoT Edge 課程模組上即時影片分析的配額和限制。

## <a name="maximum-period-of-disconnected-use"></a>中斷使用的最長期間

Edge 模組可能會暫時失去網際網路連線。 如果模組保持中斷連線超過36小時，則會停用任何正在執行的圖形實例。 將會封鎖所有進一步的直接方法呼叫。

若要讓 edge 模組恢復運作狀態，您必須還原網際網路連線，才能讓模組成功與 Azure 媒體服務帳戶進行通訊。

## <a name="maximum-number-of-graph-instances"></a>圖形實例的最大數目

每個模組最多1000個圖形實例， (透過 GraphInstanceSet) 所建立的支援。

## <a name="maximum-number-of-graph-topologies"></a>圖形拓撲的最大數目

每個模組最多50個圖形拓撲 (可支援透過 GraphTopologySet) 建立。

## <a name="limitations-on-graph-topologies-at-preview"></a>預覽的圖表拓撲限制

在預覽版本中，您可以在 media graph 拓撲中將不同節點的限制連接在一起。

* RTSP 來源
   * 每個圖形拓撲只允許一個 RTSP 來源。
* 動作偵測處理器
   * 必須立即從 RTSP 來源下游。
   * 無法使用於 HTTP 或 gRPC 擴充處理器的下游。
* 信號閘道處理器
   * 必須立即從 RTSP 來源下游。
* 資產接收 
   * 必須立即從 RTSP 來源或信號閘道處理器下游。
* 檔案接收
   * 必須立即從信號閘道處理器立即下游。
   * 無法立即處於 HTTP 或 gRPC 擴充處理器或動作偵測處理器的下游
* IoT 中樞接收
   * 無法立即成為 IoT 中樞來源的下游。

## <a name="limitations-on-media-service-operations-at-preview"></a>預覽版媒體服務作業的限制

在預覽版本時，IoT Edge 上的即時影片分析不支援下列各項：

* 將媒體服務帳戶從一個訂用帳戶遷移至另一個訂用帳戶，而不會中斷。
* 使用多個儲存體帳戶搭配媒體服務帳戶的能力。
* 能夠以動態方式變更模組所需屬性中的服務主體資訊，而不需要重新開機。

您只能使用支援 RTSP 通訊協定的 IP 攝影機。 您可以在[符合 ONVIF 標準的](https://www.onvif.org/conformant-products)產品頁面上，尋找支援 RTSP 的 IP 攝影機。 尋找符合設定檔 G、S 或 T 的裝置。

此外，您應該將這些攝影機設定為使用 h.264 影片和 AAC 音訊。 目前不支援其他編解碼器。 

## <a name="next-steps"></a>後續步驟

[概觀](overview.md)
