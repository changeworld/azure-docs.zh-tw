---
title: Azure 監視器資料平臺 |Microsoft Docs
description: Azure 監視器所收集的監視資料，會分為輕量型且能夠支援近乎即時之案例的計量，以及用於進階分析的記錄。
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 58f542238c952088777ed9809b57dae3cdb9cf12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457258"
---
# <a name="azure-monitor-data-platform"></a>Azure 監視器資料平臺

跨現今執行同時依賴雲端和內部部署服務之分散式應用程式的複雜運算環境，啟用可檢視性，需要從每一層和分散式系統的每個元件收集運算元據。 您必須能夠對這項資料執行深入的深入解析，並將其合併到具有不同觀點的單一窗格中，以支援組織中的許多專案關係人。

[Azure 監視器](../overview.md)會收集各種來源的資料，並將其匯總成可用於分析、視覺化和警示的通用資料平臺。 它可為多個來源的資料提供一致的體驗，讓您深入瞭解所有受監視的資源，甚至是使用其他服務的資料，將其資料儲存在 Azure 監視器中。


![Azure 監視器概觀](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>可檢視性 Azure 監視器中的資料
計量、記錄和分散式追蹤通常稱為可檢視性的三個支柱。 這些是監視工具必須收集並分析的不同資料種類，以提供受監視系統的足夠可檢視性。 可檢視性可透過將多個支柱的資料相互關聯，並在受監視的整組資源之間匯總資料來達成。 因為 Azure 監視器會將多個來源的資料儲存在一起，所以可以使用一組常用的工具來相互關聯和分析資料。 除了裝載其他服務的資料之外，它也會將資料相互關聯到多個 Azure 訂用帳戶和租使用者。

Azure 資源會產生大量的監視資料。 Azure 監視器會合並此資料，並將資料從其他來源監視到計量或記錄平臺。 每個都會針對特定的監視案例進行優化，而且每個實例都支援 Azure 監視器中的不同功能。 資料分析、視覺效果或警示之類的功能都需要您瞭解差異，如此您才能以最有效率且符合成本效益的方式來執行所需的案例。 [Application Insights](../app/app-insights-overview.md)或[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)等 Azure 監視器中的深入解析具有分析工具，可讓您專注于特定的監視案例，而不需要瞭解這兩種資料類型之間的差異。 


### <a name="metrics"></a>計量
[計量](data-platform-metrics.md)為數值，可描述系統在特定時間點的某個方面。 它們會定期收集，並以時間戳記、名稱、值，以及一或多個定義標籤來識別。 您可以使用各種演算法來匯總計量（相較于其他計量），並分析一段時間的趨勢。 

Azure 監視器中的計量儲存在時間序列資料庫中，已針對分析時間戳記的資料進行優化。 這可讓計量特別適用于警示和快速偵測問題。 它們可以告訴您系統的執行方式，但通常需要與記錄結合，以識別問題的根本原因。

計量可供在 Azure 入口網站中使用[Azure 計量瀏覽器](../platform/metrics-getting-started.md)進行互動分析。 您可以將它們新增至[Azure 儀表板](../learn/tutorial-app-dashboards.md)，以與其他資料結合使用，並用於近乎即時的[警示](alerts-metric.md)。

深入瞭解 Azure 監視器計量，包括其在[Azure 監視器的計量中](data-platform-metrics.md)的資料來源。

### <a name="logs"></a>記錄
[記錄](data-platform-logs.md)是系統內發生的事件。 它們可以包含不同種類的資料，而且可以是具有時間戳記的結構化或自由格式文字。 這些事件可能會在環境中產生記錄專案時偶爾建立，而負載過重的系統通常會產生更多的記錄磁片區。

Azure 監視器中的記錄會儲存在以[Azure 資料總管](/azure/data-explorer/)為基礎的 Log Analytics 工作區中，這會提供強大的分析引擎和[豐富的查詢語言](/azure/kusto/query/)。 記錄通常會提供足夠的資訊，以提供所識別之問題的完整內容，而且對於識別問題的根本案例很有説明。

> [!NOTE]
> 請務必區分 Azure 中 Azure 監視器記錄和記錄資料的來源。 例如，Azure 中的訂用帳戶層級事件會寫入 [[活動記錄](platform-logs-overview.md)] 中，您可以從 [Azure 監視器] 功能表進行查看。 大部分資源會將操作資訊寫入[資源記錄](platform-logs-overview.md)檔，您可以將其轉送至不同的位置。 Azure 監視器記錄檔是一種記錄資料平臺，會收集活動記錄和資源記錄以及其他監視資料，以提供整個資源集的深入分析。


 您可以使用 Azure 入口網站中的[Log Analytics](../log-query/portals.md) ，以互動方式處理[記錄查詢](../log-query/log-query-overview.md)，或將結果新增至[Azure 儀表板](../learn/tutorial-app-dashboards.md)，以與其他資料組合使用視覺效果。 您也可以建立[記錄警示](alerts-log.md)，以根據排程查詢的結果來觸發警示。

深入瞭解 Azure 監視器記錄，包括其在[Azure 監視器的記錄](data-platform-logs.md)中的資料來源。

### <a name="distributed-traces"></a>分散式追蹤
追蹤是一系列的相關事件，會遵循透過分散式系統的使用者要求。 它們可以用來判斷應用程式代碼的行為，以及不同交易的效能。 雖然記錄通常是由分散式系統的個別元件所建立，但追蹤會測量整個元件集的應用程式作業和效能。

Azure 監視器中的分散式追蹤是透過[APPLICATION INSIGHTS SDK](../app/distributed-tracing.md)啟用，追蹤資料則是與 Application Insights 所收集的其他應用程式記錄檔資料一起儲存。 這可供與其他記錄資料（包括記錄查詢、儀表板和警示）相同的分析工具使用。

如需分散式追蹤的詳細資訊，請參閱[什麼是分散式追蹤？](../app/distributed-tracing.md)。


## <a name="compare-azure-monitor-metrics-and-logs"></a>比較 Azure 監視器計量和記錄

下表比較 Azure 監視器中的計量和記錄。

| 屬性  | 計量 | 記錄 |
|:---|:---|:---|
| 優點 | 輕量且能夠近乎即時的案例，例如警示。 適用于快速偵測問題。 | 使用豐富的查詢語言進行分析。 適用于深層分析和識別根本原因。 |
| 資料 | 僅限數值 | 文字或數值資料 |
| 結構 | 一組標準的屬性，包括取樣時間、受監視的資源、數值。 某些計量包含多個維度以供進一步定義。 | 一組唯一的屬性，視記錄類型而定。 |
| 集合 | 以固定間隔收集。 | 可能會因為事件觸發要建立的記錄而偶爾收集。 |
| 在 Azure 入口網站中檢視 | 計量瀏覽器 | Log Analytics |
| 資料來源包括 | 從 Azure 資源收集的平臺計量。<br>Application Insights 監視的應用程式。<br>由應用程式或 API 定義的自訂。 | 應用程式和資源記錄。<br>監視解決方案。<br>代理程式和 VM 擴充功能。<br>應用程式要求和例外狀況。<br>Azure 資訊安全中心。<br>資料收集器 API。 |

## <a name="collect-monitoring-data"></a>收集監視資料
Azure 監視器的不同[資料來源](data-sources.md)會寫入 Log Analytics 工作區（記錄）或 Azure 監視器計量資料庫（計量）或兩者。 有些來源會直接寫入這些資料存放區，有些則可以寫入另一個位置（例如 Azure 儲存體），而且需要一些設定來填入記錄或計量。 

請參閱[Azure 監視器中的計量](data-platform-metrics.md)和[Azure 監視器中的記錄](data-platform-logs.md)，以取得填入每個類型之不同資料來源的清單。


## <a name="stream-data-to-external-systems"></a>將資料串流處理到外部系統
除了使用 Azure 中的工具來分析監視資料，您可能也有將監視資料轉送到外部工具的需求，例如安全性資訊和事件管理 (SIEM) 產品。 此轉送通常是透過 [Azure 事件中樞](/azure/event-hubs/)，直接從受監視的資源來完成。 某些來源可以設定為直接將資料傳送至事件中樞，而您可以使用其他進程（例如邏輯應用程式）來抓取所需的資料。 如需詳細資訊，請參閱將[Azure 監視資料串流至事件中樞以供外部工具](stream-monitoring-data-event-hubs.md)取用。



## <a name="next-steps"></a>後續步驟

- [深入瞭解 Azure 監視器中的計量](data-platform-metrics.md)。
- [深入瞭解 Azure 監視器中的記錄](data-platform-logs.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。
