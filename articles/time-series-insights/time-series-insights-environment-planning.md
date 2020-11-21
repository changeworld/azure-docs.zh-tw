---
title: 規劃您的 Gen1 環境-Azure 時間序列深入解析 |Microsoft Docs
description: 準備、設定和部署 Azure 時間序列深入解析 Gen1 環境的最佳作法。
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 5e0f1ea42aa2ba888b89dd652d3397a3a2163a3e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016202"
---
# <a name="plan-your-azure-time-series-insights-gen1-environment"></a>規劃您的 Azure 時間序列深入解析 Gen1 環境

> [!CAUTION]
> 這是 Gen1 文章。

本文說明如何根據預期的輸入速率和資料保留需求，規劃 Azure 時間序列深入解析 Gen1 環境。

## <a name="video"></a>影片

**觀看這段影片以深入瞭解 Azure 時間序列深入解析中的資料保留，以及如何進行規劃**：<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>最佳作法

若要開始使用 Azure 時間序列深入解析，最好是知道您預期要在幾分鐘內推送多少資料，以及您需要多少時間來儲存資料。  

如需 Azure 時間序列深入解析 Sku 的容量和保留的詳細資訊，請參閱 [Azure 時間序列深入解析定價](https://azure.microsoft.com/pricing/details/time-series-insights/)。

為了充分規劃您的 Azure 時間序列深入解析環境以進行長期成功，請考慮下列屬性：

- [儲存體容量](#storage-capacity)
- [資料保留期間](#data-retention)
- [輸入容量](#ingress-capacity)
- [塑造您的事件](#shape-your-events)
- [確保您已備妥參考資料](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>儲存體容量

根據預設，Azure 時間序列深入解析會根據您布建的儲存體數量（ (單位）保留資料，&#215;) 和輸入的儲存體數量。

## <a name="data-retention"></a>資料保留

您可以在 Azure 時間序列深入解析環境中變更 [ **資料保留時間** ] 設定。 您最多可以啟用400天的保留。

Azure 時間序列深入解析有兩種模式：

- 其中一個模式會針對最新的資料進行優化。 它會強制執行原則，以 **清除舊資料** ，使其可供實例使用最新的資料。 此模式預設為開啟。
- 另一個則會將資料優化，維持在設定的保留限制以下。 **暫停** 輸入會防止在選取新資料時輸入新資料，因為該資料已 **超過儲存空間限制的行為**。

您可以在 Azure 入口網站中環境設定頁面的兩個模式之間調整保留和切換。

> [!IMPORTANT]
> 您可以在 Azure 時間序列深入解析 Gen1 環境中設定最多400天的資料保留期。

### <a name="configure-data-retention"></a>設定資料保留期

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取您的時間序列深入解析環境。

1. 在 [**時間序列深入解析環境**] 窗格的 [設定] 底下，選取 [**儲存體****設定**]。

1. 在 [ **資料保留時間 (天)** ] 方塊中，輸入介於1到400之間的值。

   [![設定保留期](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> 若要深入瞭解如何執行適當的資料保留原則，請參閱 [如何設定保留期](./time-series-insights-how-to-configure-retention.md)。

## <a name="ingress-capacity"></a>輸入容量

[!INCLUDE [Azure Time Series Insights Gen1 limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>環境規劃

規劃 Azure 時間序列深入解析環境的第二個重點是輸入容量。 每日輸入儲存體和事件容量會以 1 KB 的區塊來測量。 允許的封包大小上限為 32 KB。 大於 32 KB 的資料封包會被截斷。

您可以在單一環境中將 S1 或 S2 SKU 的容量增加至 10 個單位。 您無法從 S1 環境遷移至 S2。 您無法從 S2 環境遷移至 S1。

針對輸入容量，請先判斷您需要以每個月為基礎的輸入總數。 接下來，判斷每分鐘的需求。

節流和延遲在每分鐘的容量中扮演著角色。 如果資料輸入的尖峰時間超過24小時，Azure 時間序列深入解析可以使用上表所列速率的兩倍輸入速率來「趕上」。

例如，如果您有單一 S1 SKU，則會以每分鐘720個事件的速率來輸入資料，而資料速率尖峰會以1440個事件或更少的速率進入小於一小時，您的環境就沒有明顯的延遲。 但是，如果您每分鐘超過1440個事件的時間超過一小時，您可能會遇到在環境中視覺化且可供查詢的資料延遲。

您可能不會事先知道預期要推送多少資料。 在此情況下，您可以在 Azure 入口網站訂用帳戶中找到 [Azure IoT 中樞](../iot-hub/monitor-iot-hub.md) 和 [Azure 事件中樞](/archive/blogs/cloud_solution_architect/using-the-azure-rest-apis-to-retrieve-event-hub-metrics) 的資料遙測。 遙測可協助您判斷如何布建您的環境。 使用個別事件來源 Azure 入口網站中的 [計量] 窗格來查看其遙測 **資料** 。 如果您瞭解您的事件來源計量，您可以更有效地規劃和布建您的 Azure 時間序列深入解析環境。

### <a name="calculate-ingress-requirements"></a>計算輸入需求

若要計算您的輸入需求：

- 確認您的輸入容量高於每分鐘平均速率，且您的環境夠大，足以處理您預期的輸入，相當於您的容量不到一小時的兩倍。

- 如果最近超過1小時的輸入尖峰發生，請使用尖峰率作為平均值。 布建容量來處理尖峰率的環境。

### <a name="mitigate-throttling-and-latency"></a>減少節流和延遲

如需有關如何避免節流和延遲的詳細資訊，請閱讀 [緩和延遲和節流](time-series-insights-environment-mitigate-latency.md)。

## <a name="shape-your-events"></a>塑形您的事件

請務必確定您將事件傳送至 Azure 時間序列深入解析的方式支援您要布建的環境大小。  (相反的，您可以將環境的大小對應到 Azure 時間序列深入解析讀取的事件數目和每個事件的大小。 ) 也請務必考慮您可能想要在查詢資料時用來配量及篩選的屬性。

> [!TIP]
> 查看傳送 [事件](time-series-insights-send-events.md)中的 JSON 成形檔。

## <a name="ensure-that-you-have-reference-data"></a>確定您有參考資料

*參考資料集* 是專案的集合，可從您的事件來源擴充事件。 Azure 時間序列深入解析輸入引擎會將事件來源中的每個事件，與參考資料集中的對應資料列聯結。 接著可使用增強的事件來進行查詢。 聯結是以參考資料集中定義的 **主鍵** 資料行為基礎。

> [!NOTE]
> 參考資料未聯結追溯。 在設定並上傳參考資料集之後，只會比對目前與未來的輸入資料並加入參考資料集。 如果您打算將大量歷程記錄資料傳送給 Azure 時間序列深入解析，且不會在 Azure 時間序列深入解析中第一次上傳或建立參考資料，您可能必須重做您的工作 (提示：不是有趣的) 。  

若要深入瞭解如何在 Azure 時間序列深入解析中建立、上傳及管理參考資料，請參閱我們的 [參考資料集檔](time-series-insights-add-reference-data-set.md)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>下一步

- [在 Azure 入口網站中建立新的 Azure 時間序列深入解析環境](time-series-insights-get-started.md)開始。

- 瞭解如何 [將事件中樞事件來源新增](./how-to-ingest-data-event-hub.md) 至 Azure 時間序列深入解析。

- 瞭解如何 [設定 IoT 中樞事件來源](./how-to-ingest-data-iot-hub.md)。