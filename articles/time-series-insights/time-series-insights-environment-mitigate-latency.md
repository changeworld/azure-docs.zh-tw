---
title: 如何監視和縮減節流設定-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解如何在 Azure 時間序列深入解析中監視、診斷和減少導致延遲和節流的效能問題。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: e89189b22b144d9e92ee8315bc6fd9aabe699eec
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531644"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights-gen1"></a>監視和減緩節流，以降低 Azure 時間序列深入解析 Gen1 中的延遲

> [!CAUTION]
> 這是 Gen1 文章。

當內送資料的數量超過您的環境設定時，可能會在 Azure 時間序列深入解析中發生延遲或節流。

您可以避免延遲和節流，方法為適當地設定您環境需要分析的資料量。

您在以下情況時，最可能會發生延遲和節流：

- 新增事件來源，其中包含可能超過您分配之輸入速率的舊資料 (Azure 時間序列深入解析需要趕上) 。
- 將更多事件來源新增至環境，從而導致其他事件高峰 (這可能會超出您環境的容量)。
- 將大量歷程記錄事件推送至事件來源，導致延遲 (Azure 時間序列深入解析需要趕上) 。
- 將參考資料與遙測聯結，從而導致較大的事件大小。 允許的封包大小上限為 32 KB;大於 32 KB 的資料封包會被截斷。

## <a name="video"></a>影片

### <a name="learn-about-azure-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>瞭解 Azure 時間序列深入解析資料輸入行為以及如何進行規劃。</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>使用警示來監視延遲和節流

警示可協助您診斷和減緩環境中發生的延遲問題。

1. 在 [Azure 入口網站中，選取您的 Azure 時間序列深入解析環境。 然後選取 [ **警示**]。

   [![在您的 Azure 時間序列深入解析環境中新增警示](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. 選取 [+ 新增警示規則]。 接著會顯示 [ **建立規則** ] 面板。 選取 [**條件**] 下的 [**新增**]。

   [![新增警示窗格](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. 接下來，為信號邏輯設定確切的條件。

   [![設定訊號邏輯](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   從該處，您可以使用下列部分條件來設定警示：

   |計量  |描述  |
   |---------|---------|
   |**輸入接收的位元組**     | 從事件來源讀取的未經處理位元組計數。 未經處理的計數通常會包含屬性名稱和值。  |  
   |**輸入接收的無效訊息**     | 從所有的 Azure 事件中樞或 Azure IoT 中樞事件來源讀取的無效訊息計數。      |
   |**輸入接收的訊息**   | 從所有事件中樞或 IoT 中樞事件來源讀取的訊息計數。        |
   |**輸入儲存的位元組**     | 已儲存且可供查詢的事件總大小。 只能計算屬性值的大小。        |
   |輸入**儲存的事件**    |   已儲存且可供查詢的壓平合併事件計數。      |
   |**輸入接收的訊息時間延遲**    |  訊息在事件來源中加入佇列的時間與在輸入中處理的時間之間的差異 (以秒為單位)。      |
   |**輸入接收的訊息計數延遲**    |  事件來源資料分割中最後加入佇列之訊息的序號與輸入中所處理訊息的序號之間的差異。      |

   選取 [完成]。

1. 設定所需的信號邏輯之後，請以視覺化方式檢查所選的警示規則。

   [![延遲視圖和圖表](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>節流與輸入管理

- 如果您正在進行節流處理，輸入接收的 *訊息時間延遲* 的值將會註冊，通知您 Azure 時間序列深入解析環境背後的秒數與訊息叫用事件來源的實際時間， (排除 appx 的索引時間。 30-60 秒)。  

  「輸入收到的訊息計數延遲」** 也應該有值，讓您可判斷您落後的訊息數目。  最簡單的趕上方式是將您環境的容量增加至可讓您克服差異的大小。  

  例如，如果您的 S1 環境示範5000000訊息的延隔時間，您可能會將環境的大小增加到大約一天的六個單位，以趕上。  您甚至可以更進一步加速趕上。 在一開始佈建環境時，有追趕期是常見的情況，特別是當您將其連線至已經有事件的事件來源，或是當您大量上傳許多歷史資料時。

- 另一種技術是將 [輸入儲存的事件]**** 警示設定為 >= 稍微小於 2 小時期間內您總環境容量的臨界值。  此警示可協助您了解是否經常符合容量，表示很有可能會延遲。

  例如，如果您佈建了三個 S1 單位 (或每分鐘輸入容量 2100 個事件)，就可以設定 [輸入儲存的事件]**** 警示 >= 2 小時 1900 個事件。 如果您經常會超過此臨界值，並因此觸發警示，您就可能佈建不足。  

- 如果您懷疑正在進行節流，可以將輸入接收的 **訊息** 與事件來源的輸出訊息進行比較。  如果您的事件中樞輸入大於輸入接收的 **訊息**，則您的 Azure 時間序列深入解析可能會受到節流。

## <a name="improving-performance"></a>改善效能

若要減少節流或發生延遲，最佳的修正方法是增加您環境的容量。

您可以避免延遲和節流，方法為適當地設定您環境需要分析的資料量。 如需如何在您的環境中新增容量的詳細資訊，請參閱 [調整您的環境](time-series-insights-how-to-scale-your-environment.md)。

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [診斷和解決 Azure 時間序列深入解析環境中的問題](time-series-insights-diagnose-and-solve-problems.md)。

- 瞭解 [如何調整您的 Azure 時間序列深入解析環境](time-series-insights-how-to-scale-your-environment.md)。
