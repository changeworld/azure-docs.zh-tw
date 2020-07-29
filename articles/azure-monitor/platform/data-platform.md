---
title: Azure 監視器資料平台 | Microsoft Docs
description: Azure 監視器所收集的監視資料，會分為輕量型且能夠支援近乎即時之案例的計量，以及用於進階分析的記錄。
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: e87ddd243aa248b896a26e6389ac1a219579a06d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325571"
---
# <a name="azure-monitor-data-platform"></a>Azure 監視器資料平台

若要能夠跨現今執行同時依賴雲端和內部部署服務的分散式應用程式其複雜運算環境進行檢視，則需要從分散式系統的每一層和每一個元件收集操作資料。 您必須能夠對這項資料執行見解，並將其合併到具有不同觀點的單一窗口中，以支援組織中的許多利害關係人。

[Azure 監視器](../overview.md)會收集各種來源的資料，並將其彙總到通用資料平台中，以便用於分析、視覺效果和警示。 這會在多個來源的資料上提供一致的體驗，其可供深入了解所有受監視的資源，甚至是將其資料儲存在 Azure 監視器中的其他服務資料。


![Azure 監視器概觀](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Azure 監視器中的可檢視性資料
可檢視性的三個要素通常是指計量、記錄和分散式追蹤。 這些是監視工具必須收集和分析的不同資料類型，以提供受監視系統足夠的可檢視性。 可檢視性可透過將多個要素的資料相互關聯，以及彙總整組受監視資源的資料來達成。 由於 Azure 監視器會將多個來源的資料儲存在一起，因此可使用一組通用的工具來相互關聯和分析資料。 除了裝載其他服務的資料之外，還可將多個 Azure 訂用帳戶和租用戶的資料相互關聯。

Azure 資源會產生大量的監視資料。 Azure 監視器會將此資料及其他來源的監視資料合併到計量或記錄平台中。 每項資料都會針對特定的監視案例進行最佳化，且每項資料都支援 Azure 監視器中的不同功能。 資料分析、視覺效果或警示等功能需要您了解差異，才能以最有效率且符合成本效益的方式來實作所需案例。 Azure 監視器中的見解 (例如 [Application Insights](../app/app-insights-overview.md) 或[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)) 具有分析工具，可供專注於特定的監視案例，而不需要了解這兩種資料類型之間的差異。 


### <a name="metrics"></a>計量
[計量](data-platform-metrics.md)為數值，可描述系統在特定時間點的某個方面。 這些計量會定期收集，並以時間戳記、名稱、值，以及一或多個定義標籤識別。 您可使用各種演算法來彙總計量、與其他計量進行比較，並分析一段時間的趨勢。 

Azure 監視器中的計量會儲存在時間序列資料庫中，其已進行最佳化而能分析具有時間戳記的資料。 這讓計量特別適用於警示和快速偵測問題。 這些計量可提供系統的執行效能資訊，但通常需要與記錄結合，以找出問題的根本原因。

您可在 Azure 入口網站中使用 [Azure 計量瀏覽器](./metrics-getting-started.md)來對計量互動式分析。 您可將其新增至 [Azure 儀表板](../learn/tutorial-app-dashboards.md)來與其他資料一起視覺化，並用於近乎即時的[警示](alerts-metric.md)。

若要深入了解 Azure 監視器計量 (包括其資料來源)，請參閱 [Azure 監視器中的計量](data-platform-metrics.md)。

### <a name="logs"></a>記錄
[記錄](data-platform-logs.md)是系統內發生的事件。 其可包含不同類型的資料，且可以是具有時間戳記的結構化或自由格式文字。 這些記錄可能會在環境中的事件產生記錄項目時斷斷續續建立，而負載過重的系統通常會產生較多的記錄量。

Azure 監視器中的記錄會儲存在 Log Analytics 工作區中，該工作區是以提供強大分析引擎和[豐富查詢語言](/azure/kusto/query/)的 [Azure 資料總管](/azure/data-explorer/)為基礎。 記錄通常包含足夠的資訊，可提供所發現問題的完整內容，其對於找出問題的根本原因很有幫助。

> [!NOTE]
> 請務必區分 Azure 監視器記錄和 Azure 中記錄資料的來源。 例如，Azure 中的訂用帳戶層級事件會寫入[活動記錄](platform-logs-overview.md)，您可從 [Azure 監視器] 功能表進行檢視。 大部分資源都會將作業資訊寫入[資源記錄](platform-logs-overview.md)，您可將其轉送至不同的位置。 Azure 監視器記錄是一種記錄資料平台，可收集活動記錄和資源記錄及其他監視資料，以提供整個資源集的深入分析。


 您可使用 Azure 入口網站中的 [Log Analytics](../log-query/log-query-overview.md)，以互動方式來處理[記錄查詢](../log-query/log-query-overview.md)，或將結果新增至 [Azure 儀表板](../learn/tutorial-app-dashboards.md)以與其他資料一起視覺化。 您也可以建立[記錄警示](alerts-log.md)，其會根據排程查詢的結果觸發警示。

若要深入了解 Azure 監視器記錄 (包括其資料來源)，請參閱 [Azure 監視器中的記錄](data-platform-logs.md)。

### <a name="distributed-traces"></a>分散式追蹤
追蹤是透過分散式系統追蹤使用者要求的一系列相關事件。 其可用來判斷應用程式程式碼的行為，以及不同交易的效能。 記錄通常是由分散式系統的個別元件所建立，而追蹤則會測量整組元件的應用程式作業和效能。

Azure 監視器中的分散式追蹤是透過 [Application Insights SDK](../app/distributed-tracing.md) 啟用，而追蹤資料會與 Application Insights 所收集的其他應用程式記錄檔資料一起儲存。 這可供與其他記錄資料 (包括記錄查詢、儀表板和警示) 相同的分析工具使用。

若要深入了解分散式追蹤，請參閱[什麼是分散式追蹤](../app/distributed-tracing.md)。


## <a name="compare-azure-monitor-metrics-and-logs"></a>比較 Azure 監視器計量和記錄

下表比較 Azure 監視器中的計量和記錄。

| 屬性  | 計量 | 記錄 |
|:---|:---|:---|
| 優點 | 屬於輕量型，且能夠支援近乎即時的案例 (例如警示)。 適用於快速偵測問題。 | 使用豐富的查詢語言進行分析。 適用於深入分析和找出根本原因。 |
| 資料 | 僅限數值 | 文字或數值資料 |
| 結構 | 一組標準屬性，包括取樣時間、受監視的資源、數值。 某些計量包含多個可進一步定義的維度。 | 一組取決於記錄類型的獨特屬性。 |
| 集合 | 定期收集。 | 可能會在事件觸發建立記錄時斷斷續續收集而來。 |
| 在 Azure 入口網站中檢視 | 計量瀏覽器 | Log Analytics |
| 資料來源包括 | 從 Azure 資源收集的平台計量。<br>Application Insights 所監視的應用程式。<br>應用程式或 API 所定義的自訂項目。 | 應用程式記錄檔和資源記錄。<br>監視解決方案。<br>代理程式和 VM 延伸模組。<br>應用程式要求和例外狀況。<br>Azure 資訊安全中心。<br>資料收集器 API。 |

## <a name="collect-monitoring-data"></a>收集監視資料
[Azure 監視器的不同資料來源](data-sources.md)會寫入 Log Analytics 工作區 (記錄)、Azure 監視器計量資料庫 (計量) 或兩者。 某些來源會直接寫入這些資料存放區，而其他來源則可能會寫入另一個位置 (例如 Azure 儲存體)，且需要一些設定才能填入記錄或計量。 

如需填入每種類型的不同資料來源清單，請參閱 [Azure 監視器中的計量](data-platform-metrics.md)和 [Azure 監視器中的記錄](data-platform-logs.md)。


## <a name="stream-data-to-external-systems"></a>將資料串流處理到外部系統
除了使用 Azure 中的工具來分析監視資料，您可能也有將監視資料轉送到外部工具的需求，例如安全性資訊和事件管理 (SIEM) 產品。 此轉送通常是透過 [Azure 事件中樞](../../event-hubs/index.yml)，直接從受監視的資源來完成。 某些來源可設定為直接將資料傳送至事件中樞，而您可使用另一個處理序 (例如邏輯應用程式) 來擷取所需資料。 如需詳細資料，請參閱[將 Azure 監視資料串流至事件中樞以供外部工具取用](stream-monitoring-data-event-hubs.md)。



## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器中的計量](data-platform-metrics.md)。
- 深入了解 [Azure 監視器中的記錄](data-platform-logs.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。

