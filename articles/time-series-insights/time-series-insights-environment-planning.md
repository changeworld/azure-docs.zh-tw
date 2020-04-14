---
title: 規劃 GA 環境 - Azure 時間序列見解 |微軟文件
description: 準備、配置和部署 Azure 時間序列見解 GA 環境的最佳做法。
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 85910ee5467ecc9f4fe3c1a8bc13110b6f218e5c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272707"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>規劃 Azure 時間序列的見解 GA 環境

本文介紹如何根據預期的入侵率和數據保留要求規劃 Azure 時間序列見解通用性 (GA) 環境。

## <a name="video"></a>影片

**觀看此視訊,瞭解有關 Azure 時間序列的資料保留, 以及如何規劃資料保留的詳細資訊**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>最佳作法

要開始使用 Azure 時間序列見解,最好知道您希望按分鐘推送多少數據以及存儲數據所需的時間。  

有關兩個時間序列見解 SKU 的容量和保留的詳細資訊,請閱讀[時序見解定價](https://azure.microsoft.com/pricing/details/time-series-insights/)。

為了最好地規劃您的時間序列洞察環境,以便獲得長期成功,請考慮以下屬性:

- [儲存體容量](#storage-capacity)
- [資料保留期間](#data-retention)
- [輸入容量](#ingress-capacity)
- [塑造您的事件](#shape-your-events)
- [確保您有參考資料](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>儲存體容量

默認情況下,時序見解會根據您預配的存儲量(單位&#215;單位存儲量)和入口保留數據。

## <a name="data-retention"></a>資料保留

您可以在 Azure 時間序列見解環境中更改**資料保留時間**設定。 您可以啟用長達 400 天的保留時間。 

Azure 時間序列見解有兩種模式:

* 一種模式針對最新數據進行優化。 它強制實施策略**以清除舊資料**,使實例中提供了最近的數據。 默認情況下,此模式處於打開狀態。 
* 另一個優化數據以保持在配置的保留限制以下。 **暫停入入**入的資料時,**因為儲存限制 , 因為儲存限制 。**

您可以在 Azure 門戶中的環境配置頁上的兩種模式之間調整保留和切換。

> [!IMPORTANT]
> 您可以在 Azure 時間序列見解 GA 環境中設定最多 400 天的資料保留時間。

### <a name="configure-data-retention"></a>設定資料保留期

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取您的時間序列深入解析環境。

1. 在 **「時間序列洞察」環境**窗格中,**在「設定」** 下,選擇 **「存儲配置**」 。

1. 在 **「資料保留時間(以天)」** 框中,輸入介於 1 和 400 之間的值。

   [![設定保留期](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> 要瞭解有關如何實現適當的資料保留政策的更多內容,請閱讀[如何設定保留](./time-series-insights-how-to-configure-retention.md)。

## <a name="ingress-capacity"></a>輸入容量

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>環境規劃

規劃時序見解環境時要關注的第二個領域是入侵容量。 每日入口存儲和事件容量按每分鐘 1 KB 塊為單位進行測量。 允許的最大資料包大小為 32 KB。 大於 32 KB 的數據包將被截斷。

您可以在單一環境中將 S1 或 S2 SKU 的容量增加至 10 個單位。 不能從 S1 環境遷移到 S2。 不能從 S2 環境遷移到 S1。

對於入口容量,首先確定每月所需的入口總數。 接下來,確定每分鐘的需求是什麼。 

限制和延遲在每分鐘容量中起著一定的作用。 如果數據入口的峰值時間少於 24 小時,則時序見解可以「趕上」,入侵速率是上表中所列速率的兩倍。

例如,如果您有單個 S1 SKU,則以每分鐘 720 個事件的速率進入數據,並且數據速率以 1,440 個或更少事件的速度峰值不到一小時,則環境中沒有明顯的延遲。 但是,如果每分鐘超過 1,440 個事件超過 1 小時,則可能會遇到可視化且可用於環境中查詢的數據延遲。

您可能事先不知道您希望推送多少數據。 在這種情況下,您可以在 Azure 門戶訂閱中找到[Azure IoT 中心](../iot-hub/iot-hub-metrics.md)和 Azure[事件中心](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/)的數據遙測數據。 遙測可説明您確定如何預配環境。 使用 Azure 門戶中的 **「指標」** 窗格查看其遙測數據。 如果您了解事件來源的計量，就可以更有效地規劃並佈建時間序列深入解析環境。

### <a name="calculate-ingress-requirements"></a>計算輸入需求

要計算您的入口要求:

- 驗證您的入口容量是否高於每分鐘平均速率,以及您的環境是否足夠大,足以處理預期入口,相當於容量不足一小時的兩倍。

- 如果出現持續超過 1 小時的入口峰值,請使用峰值速率作為平均值。 預配具有處理峰值速率的能力的環境。

### <a name="mitigate-throttling-and-latency"></a>減少節流和延遲

有關如何防止限制和延遲的資訊,請閱讀[「緩解延遲和限制](time-series-insights-environment-mitigate-latency.md)」。。

## <a name="shape-your-events"></a>塑形您的事件

請務必確保向時間序列見解發送事件的方式支援要預配的環境的大小。 (相反,您可以將環境的大小映射到時間序列見解讀取的事件數和每個事件的大小。在查詢數據時,考慮可能要使用的屬性進行切片和篩選也很重要。

> [!TIP]
> 查看[「發送事件](time-series-insights-send-events.md)」中的 JSON 塑造文檔。

## <a name="ensure-that-you-have-reference-data"></a>確保您有參考資料

*引用數據集*是從事件源增強事件的專案的集合。 時序見解入口引擎將事件源中的每個事件與參考數據集中的相應數據行聯接。 然後,增強事件可供查詢。 聯接基於引用數據集中定義的**主鍵**列。

> [!NOTE]
> 引用數據不會追溯聯接。 在配置和上載當前和未來入口數據后,僅匹配這些數據並將其聯接到參考數據集。 如果您計劃向時序見解發送大量歷史數據,並且不首先在時間序列見解中上載或創建參考數據,則可能需要重做您的工作(提示:不有趣)。  

要瞭解有關如何在時間序列見解中建立、上傳和管理參考資料的更多內容,請閱讀我們的[參考資料集文件](time-series-insights-add-reference-data-set.md)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>後續步驟

- 通過在 Azure[門戶中創建新的時序見解環境](time-series-insights-get-started.md)開始。

- 瞭解如何[將事件中心事件源添加到](time-series-insights-how-to-add-an-event-source-eventhub.md)時間序列見解。

- 閱讀有關如何設定[IoT 中心事件來源](time-series-insights-how-to-add-an-event-source-iothub.md)。
