---
title: Azure 監視器資料平臺 |微軟文檔
description: Azure 監視器所收集的監視資料，會分為輕量型且能夠支援近乎即時之案例的計量，以及用於進階分析的記錄。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: a1b5859341237c1b177ee8deaf636a67f4824948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666542"
---
# <a name="azure-monitor-data-platform"></a>Azure 監視器資料平臺

在當今複雜的計算環境中實現可觀察性，這些環境運行著依賴于雲和本機服務的分散式應用程式，需要從分散式系統的每一層和每個元件收集運算元據。 您需要能夠對這些資料進行深入的洞察，並將其整合到具有不同視角的單一窗格中，以支援組織中的眾多利益相關者。

[Azure 監視器](../overview.md)會收集和聚合來自各種源的資料，並將其聚合到一個通用資料平臺中，該平臺可用於分析、視覺化和警報。 它提供了來自多個源的資料的一致體驗，使您能夠深入瞭解所有受監視的資源，甚至使用存儲在 Azure 監視器中的資料的其他服務的資料。


![Azure 監視器概觀](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Azure 監視器中的可觀測性資料
指標、日誌和分散式跟蹤通常稱為可觀測性的三大支柱。 這些是監視工具必須收集和分析的不同類型的資料，以便提供足夠的可觀測性。 可觀測性可以通過關聯來自多個支柱的資料和聚合資料來實現整個被監視的資源集。 由於 Azure 監視器將來自多個源的資料一起存儲，因此可以使用一組通用工具關聯和分析資料。 除了託管其他服務的資料外，它還將跨多個 Azure 訂閱和租戶的資料關聯。

Azure 資源生成大量監視資料。 Azure 監視器將此資料以及其他源的監視資料合併到指標或日誌平臺中。 每個功能都針對特定的監視方案進行了優化，並且每個功能都支援 Azure 監視器中的不同功能。 資料分析、視覺化或警報等功能要求您瞭解差異，以便以最高效、最具成本效益的方式實現所需的方案。 Azure 監視器中的見解（如[應用程式見解](../app/app-insights-overview.md)或[VM Azure 監視器）](../insights/vminsights-overview.md)具有分析工具，允許您專注于特定的監視方案，而無需瞭解兩種資料類型之間的差異。 


### <a name="metrics"></a>計量
[計量](data-platform-metrics.md)為數值，可描述系統在特定時間點的某個方面。 它們定期收集，並標識時間戳記、名稱、值和一個或多個定義標籤。 與其他指標相比，可以使用各種演算法聚合指標，並分析隨時間變化的趨勢。 

Azure 監視器中的指標存儲在時間序列資料庫中，該資料庫經過優化，用於分析時間戳記資料。 這使得指標特別適合警報和快速檢測問題。 它們可以告訴您系統如何運行，但通常需要與日誌結合以確定問題的根本原因。

指標可用於 Azure 門戶中的互動式分析，以及[Azure 指標資源管理器](../platform/metrics-getting-started.md)。 它們可以與其他資料一起添加到[Azure 儀表板](../learn/tutorial-app-dashboards.md)進行視覺化，並用於近即時[警報](alerts-metric.md)。

閱讀有關 Azure 監視器指標（包括其 Azure[監視器中的指標中](data-platform-metrics.md)的資料來源）的更多內容。

### <a name="logs"></a>記錄
[日誌](data-platform-logs.md)是系統中發生的事件。 它們可以包含不同類型的資料，並且可以是結構化或帶有時間戳記的自由形式文本。 當環境中的事件生成日誌條目時，可能會偶爾創建它們，而負載沉重的系統通常會生成更多的日誌卷。

Azure 監視器中的日誌存儲在基於[Azure 資料資源管理器](/azure/data-explorer/)的日誌分析工作區中，該工作區提供了強大的分析引擎和[豐富的查詢語言](/azure/kusto/query/)。 日誌通常提供足夠的資訊，以提供所識別問題的完整上下文，並且對於識別問題的根本原因很有價值。

> [!NOTE]
> 區分 Azure 監視器日誌和 Azure 中的日誌資料來源非常重要。 例如，Azure 中的訂閱級別事件將寫入活動[日誌](platform-logs-overview.md)，可以從 Azure 監視器功能表中查看。 大多數資源都會將操作資訊寫入[資源日誌](platform-logs-overview.md)，您可以將這些資訊轉發到不同的位置。 Azure 監視器日誌是一個日誌資料平臺，用於收集活動日誌和資源日誌以及其他監視資料，以便在整個資源集中提供深度分析。


 您可以在 Azure 門戶中與[日誌分析](../log-query/portals.md)交互處理[日誌查詢](../log-query/log-query-overview.md)，或將結果添加到[Azure 儀表板](../learn/tutorial-app-dashboards.md)，以便與其他資料一起進行視覺化。 您還可以創建[日誌警報](alerts-log.md)，該警報將基於計畫查詢的結果觸發警報。

閱讀有關 Azure 監視器日誌（包括[其 Azure 監視器中的日誌中](data-platform-logs.md)資料來源）的更多內容。

### <a name="distributed-traces"></a>分散式跟蹤
跟蹤是使用者請求通過分散式系統跟蹤的一系列相關事件。 它們可用於確定應用程式代碼的行為和不同事務的性能。 雖然日誌通常由分散式系統的單個元件創建，但跟蹤會測量應用程式在整個元件集中的操作和性能。

使用[應用程式見解 SDK](../app/distributed-tracing.md)啟用 Azure 監視器中的分散式跟蹤，跟蹤資料存儲與應用程式見解收集的其他應用程式日誌資料一起存儲。 這使得它可用於與其他日誌資料相同的分析工具，包括日誌查詢、儀表板和警報。

在[什麼是分散式跟蹤？中閱讀更多有關分散式跟蹤的內容](../app/distributed-tracing.md)。


## <a name="compare-azure-monitor-metrics-and-logs"></a>比較 Azure 監視器指標和日誌

下表比較了 Azure 監視器中的指標和日誌。

| 屬性  | 計量 | 記錄 |
|:---|:---|:---|
| 優點 | 重量輕，能夠進行近即時場景，如警報。 快速檢測問題的理想選擇。 | 使用豐富的查詢語言進行分析。 非常適合深入分析和識別根本原因。 |
| 資料 | 僅數值 | 文本或數位資料 |
| 結構 | 標準屬性集，包括採樣時間、正在監視的資源、數值。 某些指標包括多個維度，以便進一步定義。 | 根據日誌類型設置唯一的屬性集。 |
| 集合 | 定期收集。 | 當事件觸發要創建的記錄時，可能會偶爾收集。 |
| 在 Azure 入口網站中檢視 | 計量瀏覽器 | Log Analytics |
| 資料來源包括 | 從 Azure 資源收集的平臺指標。<br>應用程式見解監視的應用程式。<br>由應用程式或 API 定義的自訂。 | 應用程式和資源日誌。<br>監控解決方案。<br>代理和 VM 擴展。<br>應用程式請求和異常。<br>Azure 安全中心。<br>資料收集器 API。 |

## <a name="collect-monitoring-data"></a>收集監控資料
[Azure 監視器的不同資料來源](data-sources.md)將寫入日誌分析工作區（日誌）或 Azure 監視器指標資料庫（指標）或兩者。 某些源將直接寫入這些資料存儲，而其他源可能會寫入其他位置（如 Azure 存儲），並且需要一些配置來填充日誌或指標。 

有關填充每種類型的不同資料來源的清單，請參閱 Azure 監視器和[Azure 監視器中的日誌中的](data-platform-logs.md)[指標](data-platform-metrics.md)。


## <a name="stream-data-to-external-systems"></a>將資料串流處理到外部系統
除了使用 Azure 中的工具來分析監視資料，您可能也有將監視資料轉送到外部工具的需求，例如安全性資訊和事件管理 (SIEM) 產品。 此轉送通常是透過 [Azure 事件中樞](/azure/event-hubs/)，直接從受監視的資源來完成。 某些源可以配置為將資料直接發送到事件中心，同時可以使用其他進程（如邏輯應用）檢索所需的資料。 有關詳細資訊，請參閱[將 Azure 監視資料流程式傳輸到事件中心供外部工具使用](stream-monitoring-data-event-hubs.md)。



## <a name="next-steps"></a>後續步驟

- 閱讀 Azure[監視器 中有關指標的更多內容](data-platform-metrics.md)。
- 閱讀有關[Azure 監視器 中的日誌](data-platform-logs.md)的更多內容。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。
