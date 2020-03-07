---
title: Azure 監視器中的記錄 |Microsoft Docs
description: 描述 Azure 監視器中用來進行監視資料之先進分析的記錄。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 06b35fdcd9da333c3c9d94d41c84a2f25ede59ad
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361833"
---
# <a name="logs-in-azure-monitor"></a>Azure 監視器中的記錄

> [!NOTE]
> Azure 監視器所收集的所有資料都適用于下列兩種基本類型的其中一種：計量和記錄。 本文說明記錄。 請參閱[Azure 監視器中的計量](data-platform-metrics.md)，以取得計量的詳細描述，以及[監視 Azure 監視器所收集的資料](data-platform.md)，以進行這兩項比較。

Azure 監視器中的記錄特別適用于跨各種來源的資料執行複雜的分析。 本文說明如何將記錄結構化在 Azure 監視器中、您可以如何處理資料，以及識別在記錄中儲存資料的不同資料來源。

> [!NOTE]
> 請務必區分 Azure 中 Azure 監視器記錄和記錄資料的來源。 例如，Azure 中的訂用帳戶層級事件會寫入 [[活動記錄](platform-logs-overview.md)] 中，您可以從 [Azure 監視器] 功能表進行查看。 大部分資源會將操作資訊寫入[資源記錄](platform-logs-overview.md)檔，您可以將其轉送至不同的位置。 Azure 監視器記錄檔是一種記錄資料平臺，會收集活動記錄和資源記錄以及其他監視資料，以提供整個資源集的深入分析。

## <a name="what-are-azure-monitor-logs"></a>什麼是 Azure 監視器記錄？

Azure 監視器中的記錄包含組織成記錄的不同類型資料，其中每個型別各有不同的屬性集。 記錄可以包含數值（例如 Azure 監視器計量），但通常包含具有詳細描述的文字資料。 它們會與計量資料進一步不同，因為它們的結構不同，通常不會定期收集。 除了效能資料以外，事件和追蹤等遙測會儲存 Azure 監視器記錄，讓它能夠全部結合以進行分析。

常見的記錄專案類型是事件，它會偶爾收集。 事件是由應用程式或服務所建立，而且通常包含足夠的資訊來提供自己的完整內容。 例如，事件可以表示已建立或修改特定資源、為了回應增加的流量而啟動新主機，或在應用程式中偵測到錯誤。

 資料的格式可能有所不同，應用程式可以使用其所需的結構建立自訂記錄。 計量資料甚至可以儲存在記錄中，以便將它們與其他監視資料結合，以進行趨勢和其他資料分析。


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Azure 監視器記錄可以做什麼？
下表列出您可以在 Azure 監視器中使用記錄的不同方式。


|  |  |
|:---|:---|
| 分析 | 使用 Azure 入口網站中的[Log Analytics](../log-query/get-started-portal.md)來撰寫[記錄查詢](../log-query/log-query-overview.md)，並使用強大的資料總管分析引擎以互動方式分析記錄資料。<br>使用 Azure 入口網站中的[Application Insights 分析主控台](../app/analytics.md)來撰寫記錄查詢，並以互動方式從 Application Insights 分析記錄資料。 |
| 視覺化 | 將呈現為數據表或圖表的查詢結果釘選到[Azure 儀表板](../../azure-portal/azure-portal-dashboards.md)。<br>建立活頁[簿](../app/usage-workbooks.md)，以結合互動式報表中的多個資料集。 <br>將查詢的結果匯出到 [Power BI](powerbi.md) 以使用不同的視覺效果，並與 Azure 外部的使用者共用。<br>將查詢的結果匯出至[Grafana](grafana-plugin.md) ，以利用其儀表板管理並與其他資料來源結合。|
| 警示 | 設定[記錄警示規則](alerts-log.md)，在查詢結果符合特定結果時，傳送通知或採取[自動化動作](action-groups.md)。<br>針對已解壓縮為計量的特定記錄資料記錄，設定[度量警示規則](alerts-metric-logs.md)。 |
| 擷取 | 使用[Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)從命令列存取記錄查詢結果。<br>使用[PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.operationalinsights)從命令列存取記錄查詢結果。<br>使用[REST API](https://dev.loganalytics.io/)，從自訂應用程式存取記錄查詢結果。 |
| 匯出 | 建立工作流程來抓取記錄資料，並使用[Logic Apps](~/articles/logic-apps/index.yml)將它複製到外部位置。 |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Azure 監視器記錄中的資料如何結構化？
Azure 監視器記錄收集的資料會儲存在[Log Analytics 工作區](../platform/design-logs-deployment.md)中。 每個工作區都包含多個資料表，其中每個都會儲存來自特定來源的資料。 雖然所有資料表都共用[一些通用屬性](log-standard-properties.md)，但每個都有一組唯一的屬性，視它所儲存的資料類型而定。 新的工作區會有一組標準的資料表，而其他的監視解決方案和寫入工作區的其他服務將會新增更多資料表。

來自 Application Insights 的記錄資料會使用與工作區相同的 Log Analytics 引擎，但它會針對每個受監視的應用程式分別儲存。 每個應用程式都有一組標準的資料表來保存資料，例如應用程式要求、例外狀況和頁面流覽。

記錄查詢會使用 Log Analytics 工作區或 Application Insights 應用程式中的資料。 您可以使用[跨資源查詢](../log-query/cross-workspace-query.md)，同時分析應用程式資料與其他記錄資料，或建立包含多個工作區或應用程式的查詢。

![工作區](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>記錄查詢
系統會使用以[Kusto 查詢語言](../log-query/get-started-queries.md)撰寫的[記錄查詢](../log-query/log-query-overview.md)來抓取 Azure 監視器記錄中的資料，這可讓您快速地抓取、合併和分析所收集的資料。 使用[Log Analytics](../log-query/portals.md)在 Azure 入口網站中撰寫及測試記錄查詢。 它可讓您以互動方式處理結果，或將其釘選到儀表板，以透過其他視覺效果來觀看。

![Log Analytics](media/data-platform-logs/log-analytics.png)

[從 Application Insights 開啟 Log Analytics](../app/analytics.md)來分析 Application Insights 資料。

![Application Insights 分析](media/data-platform-logs/app-insights-analytics.png)

您也可以使用[Log ANALYTICS API](https://dev.loganalytics.io/documentation/overview)和[Application Insights REST API](https://dev.applicationinsights.io/documentation/overview)來取出記錄資料。


## <a name="sources-of-azure-monitor-logs"></a>Azure 監視器記錄的來源
Azure 監視器可以在 Azure 中及內部部署資源的各種來源收集資料。 下表列出可從將資料寫入 Azure 監視器記錄的不同資源中，可用的不同資料來源。 每個都有一個連結，可提供任何必要設定的詳細資料。

### <a name="azure-tenant-and-subscription"></a>Azure 租使用者和訂用帳戶

| 資料 | 描述 |
|:---|:---|
| Azure Active Directory audit 記錄檔 | 透過每個目錄的診斷設定來設定。 請參閱[整合 Azure AD 記錄與 Azure 監視器記錄](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。  |
| 活動記錄 | 預設會個別儲存，並可用於近乎即時的警示。 安裝活動記錄分析解決方案以寫入 Log Analytics 工作區。 請參閱[在 Log Analytics 中收集並分析 Azure 活動記錄](activity-log-collect.md)。 |

### <a name="azure-resources"></a>Azure 資源

| 資料 | 描述 |
|:---|:---|
| 資源診斷 | 設定診斷設定以寫入診斷資料，包括對 Log Analytics 工作區的計量。 請參閱[將 Azure 資源記錄串流至 Log Analytics](resource-logs-collect-storage.md)。 |
| 監視解決方案 | 監視解決方案會將他們收集的資料寫入其 Log Analytics 工作區。 如需解決方案清單，請參閱[Azure 中管理解決方案的資料收集詳細資料](../insights/solutions-inventory.md)。 如需安裝和使用解決方案的詳細資訊，請參閱[Azure 監視器中的監視解決方案](../insights/solutions.md)。 |
| 度量 | 將 Azure 監視器資源的平臺計量傳送到 Log Analytics 工作區，以保留記錄資料較長的時間，以及使用[Kusto 查詢語言](/azure/kusto/query/)來執行其他資料類型的複雜分析。 請參閱[將 Azure 資源記錄串流至 Log Analytics](resource-logs-collect-storage.md)。 |
| Azure 表格儲存體 | 從 Azure 儲存體收集資料，其中某些 Azure 資源會寫入監視資料。 如需 Log Analytics 的事件，請參閱[使用適用于 IIS 的 azure blob 儲存體和 azure 資料表儲存體](diagnostics-extension-logs.md)。 |

### <a name="virtual-machines"></a>虛擬機器

| 資料 | 描述 |
|:---|:---|
|  代理程式資料來源 | 從[Windows](agent-windows.md)和[Linux](../learn/quick-collect-linux-computer.md)代理程式收集的資料來源包含事件、效能資料和自訂記錄。 如需資料來源的清單和設定的詳細資訊，請參閱[Azure 監視器中的代理程式資料來源](data-sources.md)。 |
| 監視解決方案 | 監視解決方案會將他們從代理程式收集到的資料寫入其 Log Analytics 工作區。 如需解決方案清單，請參閱[Azure 中管理解決方案的資料收集詳細資料](../insights/solutions-inventory.md)。 如需安裝和使用解決方案的詳細資訊，請參閱[Azure 監視器中的監視解決方案](../insights/solutions.md)。 |
| System Center Operations Manager | 將 Operations Manager 管理群組連線至 Azure 監視器，將內部部署代理程式的事件和效能資料收集到記錄中。 如需此設定的詳細資訊，請參閱[將 Operations Manager 連接到 Log Analytics](om-agents.md) 。 |


### <a name="applications"></a>應用程式

| 資料 | 描述 |
|:---|:---|
| 要求和例外狀況 | 有關應用程式要求和例外狀況的詳細資料位於 [_要求_]、[ _pageViews_] 和 [_例外_狀況] 資料表中。 [外部元件](../app/asp-net-dependencies.md)的_呼叫位於相依性資料表中_。 |
| 使用方式和效能 | 應用程式的效能可在_requests_、 _browserTimings_和_performanceCounters_資料表中取得。 [自訂計量](../app/api-custom-events-metrics.md#trackevent)的資料位於_customMetrics_資料表中。|
| 追蹤資料 | [分散式追蹤](../app/distributed-tracing.md)的結果會儲存在_追蹤_資料表中。 |
| 可用性集合 | 來自[可用性測試](../app/monitor-web-app-availability.md)的摘要資料會儲存在_availabilityResults_資料表中。 這些測試中的詳細資料位於不同的儲存體，並從 Azure 入口網站中的 Application Insights 存取。 |

### <a name="insights"></a>深入解析

| 資料 | 描述 |
|:---|:---|
| 適用於容器的 Azure 監視器 | [Azure 監視器針對容器](../insights/container-insights-overview.md)收集的清查和效能資料。 如需資料表的清單，請參閱[容器資料收集詳細資料](../insights/container-insights-log-search.md#container-records)。 |
| 適用於 VM 的 Azure 監視器 | [適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)所收集的對應和效能資料。 如需查詢此資料的詳細資訊，請參閱[如何從適用於 VM 的 Azure 監視器查詢記錄](../insights/vminsights-log-search.md)。 |

### <a name="custom"></a>Custom 

| 資料 | 描述 |
|:---|:---|
| REST API | 將資料從任何 REST 用戶端寫入 Log Analytics 工作區。 如需詳細資訊，請參閱[使用 HTTP 資料收集器 API 將記錄資料傳送至 Azure 監視器](data-collector-api.md)。
| 邏輯應用程式 | 使用**Azure Log Analytics 資料收集器**動作，從邏輯應用程式工作流程將任何資料寫入 Log Analytics 工作區。 |

### <a name="security"></a>安全性

| 資料 | 描述 |
|:---|:---|
| Azure 資訊安全中心 | [Azure 資訊安全中心](/azure/security-center/)會將它收集的資料儲存在 Log Analytics 工作區中，以便使用其他記錄資料進行分析。 如需工作區設定的詳細資訊，請參閱[Azure 資訊安全中心中的資料收集](../../security-center/security-center-enable-data-collection.md)。 |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/)會將資料來源中的資料儲存至 Log Analytics 工作區。 請參閱[連接資料來源](/azure/sentinel/connect-data-sources)。  |


## <a name="next-steps"></a>後續步驟

- 深入瞭解[Azure 監視器資料平臺](data-platform.md)。
- 深入瞭解[Azure 監視器中的計量](data-platform-metrics.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。
