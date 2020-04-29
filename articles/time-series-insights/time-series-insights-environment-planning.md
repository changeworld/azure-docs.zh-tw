---
title: 規劃您的 GA 環境-Azure 時間序列深入解析 |Microsoft Docs
description: 準備、設定和部署 Azure 時間序列深入解析 GA 環境的最佳做法。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272707"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>規劃您的 Azure 時間序列深入解析 GA 環境

本文說明如何根據預期的輸入速率和資料保留需求，規劃您的 Azure 時間序列深入解析公開上市（GA）環境。

## <a name="video"></a>影片

**觀看這段影片以深入瞭解 Azure 時間序列深入解析中的資料保留以及如何進行規劃**：<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>最佳作法

若要開始使用 Azure 時間序列深入解析，最好是知道您預期每分鐘推送的資料量，以及儲存資料所需的時間。  

如需時間序列深入解析 Sku 的容量和保留的詳細資訊，請參閱[時間序列深入解析定價](https://azure.microsoft.com/pricing/details/time-series-insights/)。

若要為您的時間序列深入解析環境進行長期成功的規劃，請考慮下列屬性：

- [儲存體容量](#storage-capacity)
- [資料保留期間](#data-retention)
- [輸入容量](#ingress-capacity)
- [塑造您的事件](#shape-your-events)
- [確保您已備妥參考資料](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>儲存體容量

根據預設，時間序列深入解析會依據您布建的儲存體數量（單位 &#215; 每個單位的儲存體數量）和輸入來保留資料。

## <a name="data-retention"></a>資料保留

您可以變更 Azure 時間序列深入解析環境中的 [**資料保留時間**] 設定。 您最多可以啟用400天的保留期。 

Azure 時間序列深入解析有兩種模式：

* 其中一種模式會針對最新的資料進行優化。 它會強制執行原則，以**清除舊**資料，讓實例能夠使用最新的資料。 此模式預設為開啟。 
* 另一個則會將資料優化，維持在設定的保留限制以下。 **暫停**輸入會防止新資料在選取為**超出儲存體限制的行為**時被輸入。

您可以在 [Azure 入口網站] 環境的 [設定] 頁面上，調整兩種模式的保留期和切換。

> [!IMPORTANT]
> 您可以在 Azure 時間序列深入解析 GA 環境中設定最多400天的資料保留期。

### <a name="configure-data-retention"></a>設定資料保留期

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取您的時間序列深入解析環境。

1. 在 [**時間序列深入解析環境**] 窗格的 [**設定**] 底下，選取 [**存放裝置**設定]。

1. 在 [**資料保留時間（以天**為單位）] 方塊中，輸入介於1到400之間的值。

   [![設定保留期](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> 若要深入瞭解如何執行適當的資料保留原則，請參閱[如何設定保留期](./time-series-insights-how-to-configure-retention.md)。

## <a name="ingress-capacity"></a>輸入容量

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>環境規劃

要專注于規劃時間序列深入解析環境的第二個區域是輸入容量。 每日輸入儲存體和事件容量的測量單位為 1 KB 區塊中的每分鐘。 允許的封包大小上限為 32 KB。 大於 32 KB 的資料封包會被截斷。

您可以在單一環境中將 S1 或 S2 SKU 的容量增加至 10 個單位。 您無法從 S1 環境遷移到 S2。 您無法從 S2 環境遷移至 S1。

針對輸入容量，請先以每個月為基礎來決定您需要的總輸入。 接下來，判斷您每分鐘的需求為何。 

節流和延遲會以每分鐘的容量來播放角色。 如果您的資料輸入突然增加了24小時，時間序列深入解析可以使用上表所列費率的兩倍輸入速率來「趕上」。

例如，如果您有單一 S1 SKU，您會以每分鐘720個事件的速率來輸入資料，而且資料速率會尖峰超過一小時（以1440事件或更少的速率），在您的環境中不會有明顯的延遲。 不過，如果您超過一小時的每分鐘1440事件，您可能會在環境中視覺化且可供查詢的資料中遇到延遲。

您可能事先不知道預期會推送多少資料。 在此情況下，您可以在 Azure 入口網站訂用帳戶中找到[Azure IoT 中樞](../iot-hub/iot-hub-metrics.md)和[Azure 事件中樞](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/)的資料遙測。 遙測可協助您判斷如何布建您的環境。 使用個別事件來源的 Azure 入口網站中的 [**計量**] 窗格來查看其遙測。 如果您了解事件來源的計量，就可以更有效地規劃並佈建時間序列深入解析環境。

### <a name="calculate-ingress-requirements"></a>計算輸入需求

若要計算您的輸入需求：

- 請確認您的輸入容量高於平均每分鐘速率，且您的環境夠大，可處理您預期的輸入，相當於您容量的兩倍（不到一小時）。

- 如果過去超過1小時的輸入尖峰發生，請使用尖峰速率作為您的平均值。 布建具有容量的環境，以處理尖峰速率。

### <a name="mitigate-throttling-and-latency"></a>減少節流和延遲

如需如何避免節流和延遲的相關資訊，請參閱[減輕延遲和節流](time-series-insights-environment-mitigate-latency.md)。

## <a name="shape-your-events"></a>塑形您的事件

請務必確定您將事件傳送至時間序列深入解析的方式，可以支援您所布建的環境大小。 （相反地，您可以將環境的大小對應至時間序列深入解析讀取的事件數目，以及每個事件的大小）。當您查詢資料時，也必須考慮您可能想要使用的屬性來進行配量及篩選。

> [!TIP]
> 請參閱傳送[事件](time-series-insights-send-events.md)中的 JSON 成形檔。

## <a name="ensure-that-you-have-reference-data"></a>確定您有參考資料

*參考資料集*是從事件來源擴充事件的專案集合。 時間序列深入解析輸入引擎會將事件來源中的每個事件與參考資料集中的對應資料列聯結在一起。 然後，擴充的事件便可供查詢。 聯結是以參考資料集中定義的**主要索引鍵**資料行為基礎。

> [!NOTE]
> 參考資料未聯結追溯。 只有在設定並上傳之後，才會比對目前和未來的輸入資料並聯結至參考資料集。 如果您打算將大量歷程記錄資料傳送至時間序列深入解析，而不要在時間序列深入解析中第一次上傳或建立參考資料，您可能必須重做工作（提示：不有趣）。  

若要深入瞭解如何在時間序列深入解析中建立、上傳及管理參考資料，請閱讀我們的[參考資料集檔](time-series-insights-add-reference-data-set.md)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>後續步驟

- [在 Azure 入口網站中建立新的時間序列深入解析環境以](time-series-insights-get-started.md)開始使用。

- 瞭解如何[將事件中樞的事件來源新增](time-series-insights-how-to-add-an-event-source-eventhub.md)至時間序列深入解析。

- 瞭解如何[設定 IoT 中樞事件來源](time-series-insights-how-to-add-an-event-source-iothub.md)。
