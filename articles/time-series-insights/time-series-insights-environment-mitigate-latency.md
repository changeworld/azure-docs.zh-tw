---
title: 如何監視和減少限制 - Azure 時間序列見解 |微軟文件
description: 瞭解如何監視、診斷和緩解在 Azure 時間序列見解中導致延遲和限制的性能問題。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 2812b535c7aef7987db7106bfa6b07e15a1b61c7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263381"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>監視與降低節流，以減少 Azure 時間序列深入解析中的延遲

當內送資料的數量超過您的環境設定時，可能會在 Azure 時間序列深入解析中發生延遲或節流。

您可以避免延遲和節流，方法為適當地設定您環境需要分析的資料量。

您在以下情況時，最可能會發生延遲和節流：

- 新增事件來源，其中包含的舊資料可能會超過您的配置輸入率 (時間序列深入解析必須趕上)。
- 將更多事件來源新增至環境，從而導致其他事件高峰 (這可能會超出您環境的容量)。
- 將大量的歷程記錄事件推送至事件來源，從而導致延隔時間 (時間序列深入解析必須趕上)。
- 將參考資料與遙測聯結，從而導致較大的事件大小。 允許的最大資料包大小為 32 KB;大於 32 KB 的數據包將被截斷。

## <a name="video"></a>影片

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>了解時間序列洞察數據入侵行為以及如何規劃它。</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>使用警示來監視延遲和節流

警報可以幫助您診斷和緩解環境中發生的延遲問題。

1. 在 Azure 入口網站中，選取您的時間序列深入解析環境。 然後選擇 **「警報**」。

   [![向時序見解環境加入警報](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. 選取 [+ 新增警示規則]****。 然後將顯示「**創建規則**」 面板。 選擇 **「在條件**」 選**加**。

   [![新增警示窗格](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. 接下來,配置信號邏輯的確切條件。

   [![設定訊號邏輯](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   從那裡,您可以使用以下一些條件設定警報:

   |計量  |描述  |
   |---------|---------|
   |**輸入接收的位元組**     | 從事件來源讀取的未經處理位元組計數。 未經處理的計數通常會包含屬性名稱和值。  |  
   |**輸入接收的無效訊息**     | 從所有的 Azure 事件中樞或 Azure IoT 中樞事件來源讀取的無效訊息計數。      |
   |**輸入接收的訊息**   | 從所有事件中樞或 IoT 中樞事件來源讀取的訊息計數。        |
   |**輸入儲存的位元組**     | 已儲存且可供查詢的事件總大小。 只能計算屬性值的大小。        |
   |**入口儲存事件**    |   已儲存且可供查詢的壓平合併事件計數。      |
   |**輸入接收的訊息時間延遲**    |  訊息在事件來源中加入佇列的時間與在輸入中處理的時間之間的差異 (以秒為單位)。      |
   |**輸入接收的訊息計數延遲**    |  事件來源資料分割中最後加入佇列之訊息的序號與輸入中所處理訊息的序號之間的差異。      |

   選取 [完成]  。

1. 配置所需的信號邏輯后,目視查看所選警報規則。

   [![延遲檢視與圖表](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>限制並入口管理

* 如果被限制,將註冊 *「入口接收消息時滯」* 的值,通知您 TIme 系列見解環境從消息到達事件源的實際時間(不包括 appx 的索引時間)後有多少秒。 30-60 秒)。  

  「輸入收到的訊息計數延遲」** 也應該有值，讓您可判斷您落後的訊息數目。  最簡單的趕上方式是將您環境的容量增加至可讓您克服差異的大小。  

  例如,如果您的 S1 環境顯示延遲 5,000,000 條消息,則可以將環境的大小增加到六個單位,大約一天才能趕上來。  您甚至可以更進一步加速趕上。 在一開始佈建環境時，有追趕期是常見的情況，特別是當您將其連線至已經有事件的事件來源，或是當您大量上傳許多歷史資料時。

* 另一種技術是將 [輸入儲存的事件]**** 警示設定為 >= 稍微小於 2 小時期間內您總環境容量的臨界值。  此警示可協助您了解是否經常符合容量，表示很有可能會延遲。 

  例如，如果您佈建了三個 S1 單位 (或每分鐘輸入容量 2100 個事件)，就可以設定 [輸入儲存的事件]**** 警示 >= 2 小時 1900 個事件。 如果您經常會超過此臨界值，並因此觸發警示，您就可能佈建不足。  

* 如果您懷疑自己被限制,則可以將 **「入口接收消息」** 與事件源的傳出消息進行比較。  如果事件中樞的輸入大於您 [輸入接收的訊息]****，時間序列深入解析就可能會受到節流。

## <a name="improving-performance"></a>改善效能

若要減少節流或發生延遲，最佳的修正方法是增加您環境的容量。

您可以避免延遲和節流，方法為適當地設定您環境需要分析的資料量。 關於如何向環境加入容量的詳細資訊,請閱讀[縮放環境](time-series-insights-how-to-scale-your-environment.md)。

## <a name="next-steps"></a>後續步驟

- 閱讀有關[診斷與解決時間序列的解環境中的問題](time-series-insights-diagnose-and-solve-problems.md)。

- [瞭解如何延伸時間序列的解環境](time-series-insights-how-to-scale-your-environment.md)。