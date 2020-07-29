---
title: Azure 監視器中的記錄 | Microsoft Docs
description: 描述 Azure 監視器中用來進行監視資料進階分析的記錄。
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 413616034dfe7d1f13612ba12ba86014af62c704
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325622"
---
# <a name="logs-in-azure-monitor"></a>Azure 監視器中的記錄

> [!NOTE]
> Azure 監視器所收集的全部資料均符合下列兩個基本類型之一：計量和記錄。 本文說明記錄。 請參閱 [Azure 監視器中的計量](data-platform-metrics.md)來取得計量的詳細描述，以及參閱 [Azure 監視器所收集的監視資料](data-platform.md)以進行兩者的比較。

Azure 監視器中的記錄特別適合用於對各種來源的資料執行複雜的分析。 本文說明如何在 Azure 監視器中將記錄結構化、如何處理資料，以及識別在記錄中儲存資料的不同資料來源。

> [!NOTE]
> 請務必區分 Azure 監視器記錄和 Azure 中記錄資料的來源。 例如，Azure 中的訂用帳戶層級事件會寫入[活動記錄](platform-logs-overview.md)，您可從 [Azure 監視器] 功能表進行檢視。 大部分資源都會將作業資訊寫入[資源記錄](platform-logs-overview.md)，您可將其轉送至不同的位置。 Azure 監視器記錄是一種記錄資料平台，可收集活動記錄和資源記錄及其他監視資料，以提供整個資源集的深入分析。

## <a name="what-are-azure-monitor-logs"></a>什麼是 Azure 監視器記錄？

Azure 監視器中的記錄包含不同類型的資料，而資料會針對每個類型組織成不同的屬性集。 記錄可以包含數值 (例如 Azure 監視器計量)，但通常會包含具有詳細說明的文字資料。 其在結構方面進一步與計量資料不同，而且通常不會定期收集。 除了效能資料，還會將事件和追蹤之類的遙測儲存為 Azure 監視器記錄，使其能夠全部組合在一起進行分析。

常見的記錄項目類型是偶爾會收集的事件。 事件是由應用程式或服務所建立，而且通常包含足夠的資訊來自行提供完整的內容。 例如，事件可以表示已建立或修改特定資源、為了回應增加的流量而啟動新主機，或在應用程式中偵測到錯誤。

 資料的格式可能有所不同，應用程式可以使用其所需的結構建立自訂記錄。 計量資料甚至可以儲存在記錄中，將此資料與其他監視資料結合，以便進行趨勢分析和其他資料分析。


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Azure 監視器記錄有何功用？
下表列出您可在 Azure 監視器中使用記錄的各種方式。


|  | 說明 |
|:---|:---|
| **分析** | 在 Azure 入口網站中使用 [Log Analytics](../log-query/get-started-portal.md) 來撰寫 [記錄查詢](../log-query/log-query-overview.md)，並使用強大的資料總管分析引擎以互動方式分析記錄資料。<br>在 Azure 入口網站中使用 [Application Insights 分析主控台](../log-query/log-query-overview.md)來撰寫記錄查詢，並以互動方式分析來自 Application Insights 的記錄資料。 |
| **視覺化** | 將轉譯為表格或圖表的查詢結果釘選到 [Azure 儀表板](../../azure-portal/azure-portal-dashboards.md)。<br>建立[活頁簿](./workbooks-overview.md)，以在互動式報告中與多組資料結合。 <br>將查詢的結果匯出到 [Power BI](powerbi.md) 以使用不同的視覺效果，並與 Azure 外部的使用者共用。<br>將查詢的結果匯出至 [Grafana](grafana-plugin.md) 以利用其儀表板功能，並與其他資料來源結合。|
| **警示** | 設定[記錄警示規則](alerts-log.md)，在查詢結果符合特定結果時，傳送通知或採取[自動化動作](action-groups.md)。<br>針對擷取為計量的某些記錄資料記錄，設定[計量警示規則](alerts-metric-logs.md)。 |
| **擷取** | 使用 [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)，從命令列存取記錄查詢結果。<br>使用 [PowerShell Cmdlet](/powershell/module/az.operationalinsights)，從命令列存取記錄查詢結果。<br>使用 [REST API](https://dev.loganalytics.io/)，從自訂應用程式存取記錄查詢結果。 |
| **匯出** | 建立工作流程來擷取記錄資料，並使用 [Logic Apps](../../logic-apps/index.yml) 將其複製到外部位置。 |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>如何在 Azure 監視器記錄中將資料結構化？
Azure 監視器記錄收集的資料都會儲存於 [Log Analytics 工作區](./design-logs-deployment.md)中。 每個工作區都包含多個資料表，而每個資料表會儲存來自特定來源的資料。 雖然所有資料表會共用[一些通用屬性](log-standard-properties.md)，但每個資料表都有一組獨特的屬性 (視其儲存的資料類型而定)。 新工作區會有一組標準的資料表，而寫入工作區的各種監視解決方案和其他服務將會新增更多資料表。

來自 Application Insights 的記錄資料會使用與工作區相同的 Log Analytics 引擎，但該資料會針對每個受監視的應用程式分開儲存。 每個應用程式都有一組標準的資料表可保存資料，例如應用程式要求、例外狀況和頁面檢視。

記錄查詢會使用 Log Analytics 工作區或 Application Insights 應用程式中的資料。 您可以使用[跨資源查詢](../log-query/cross-workspace-query.md)，同時分析應用程式資料與其他記錄資料，或建立包含多個工作區或應用程式的查詢。

![工作區](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>記錄查詢
Azure 監視器記錄中的資料都是使用以 [Kusto 查詢語言](../log-query/get-started-queries.md)撰寫的[記錄查詢](../log-query/log-query-overview.md)來擷取，可讓您快速擷取、彙總及分析收集的資料。 使用 [Log Analytics](../log-query/log-query-overview.md)，在 Azure 入口網站中撰寫及測試查詢。 其可讓您以互動方式使用結果，或將結果釘選到儀表板，利用其他視覺效果進行檢視。

![Log Analytics](media/data-platform-logs/log-analytics.png)

[從 Application Insights 開啟 Log Analytics](../log-query/log-query-overview.md)，以分析 Application Insights 資料。

![Application Insights 分析](media/data-platform-logs/app-insights-analytics.png)

您也可以使用 [Log Analytics API](https://dev.loganalytics.io/documentation/overview) 和 [Application Insights REST API](https://dev.applicationinsights.io/documentation/overview) 來擷取記錄資料。


## <a name="sources-of-azure-monitor-logs"></a>Azure 監視器記錄的來源
Azure 監視器可以在 Azure 中及內部部署資源的各種來源收集資料。 下表列出可從將資料寫入 Azure 監視器記錄的不同資源中取得的不同資料來源。 每個資料來源都有一個連結，可提供任何必要設定的詳細資料。

### <a name="azure-tenant-and-subscription"></a>Azure 租用戶與訂用帳戶

| 資料 | 描述 |
|:---|:---|
| Azure Active Directory 稽核記錄 | 透過每個目錄的診斷設定進行設定。 請參閱[整合 Azure AD 記錄與 Azure 監視器記錄](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。  |
| 活動記錄 | 預設會分開儲存，並可用於近乎即時的警示。 安裝活動記錄分析解決方案以寫入 Log Analytics 工作區。 請參閱[在 Log Analytics 中收集並分析 Azure 活動記錄](./activity-log.md)。 |

### <a name="azure-resources"></a>Azure 資源

| 資料 | 描述 |
|:---|:---|
| 資源診斷 | 設定診斷設定以寫入診斷資料，包括將計量寫入 Log Analytics 工作區。 請參閱[將 Azure 資源記錄串流至 Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)。 |
| 監視解決方案 | 監視解決方案會將其收集的資料寫入其 Log Analytics 工作區。 請參閱 [Azure 中管理解決方案的資料收集詳細資料](../monitor-reference.md)，以取得解決方案清單。 如需安裝和使用解決方案的詳細資訊，請參閱 [Azure 監視器中的監視解決方案](../insights/solutions.md)。 |
| 計量 | 將 Azure 監視器資源的平台計量傳送到 Log Analytics 工作區，可將記錄資料保留較長的時間，以及使用 [Kusto 查詢語言](/azure/kusto/query/)來執行其他資料類型的複雜分析。 請參閱[將 Azure 資源記錄串流至 Log Analytics](./resource-logs.md#send-to-azure-storage)。 |
| Azure 表格儲存體 | 從 Azure 儲存體收集資料，其中有些 Azure 資源會寫入監視資料。 請參閱[針對 Log Analytics 的事件，使用 IIS 和 Azure 表格儲存體的 Azure blob 儲存體](diagnostics-extension-logs.md)。 |

### <a name="virtual-machines"></a>虛擬機器

| 資料 | 描述 |
|:---|:---|
|  代理程式資料來源 | 從 [Windows](agent-windows.md) 和 [Linux](../learn/quick-collect-linux-computer.md) 代理程式收集到的資料來源包含事件、效能資料和自訂記錄。 如需資料來源清單和設定詳細資訊，請參閱 [Azure 監視器中的代理程式資料來源](data-sources.md)。 |
| 監視解決方案 | 監視解決方案會將從代理程式收集的資料寫入其 Log Analytics 工作區。 請參閱 [Azure 中管理解決方案的資料收集詳細資料](../monitor-reference.md)，以取得解決方案清單。 如需安裝和使用解決方案的詳細資訊，請參閱 [Azure 監視器中的監視解決方案](../insights/solutions.md)。 |
| System Center Operations Manager | 將 Operations Manager 管理群組連線至 Azure 監視器，進而將內部部署代理程式中的事件和效能資料收集到記錄中。 如需此設定的詳細資訊，請參閱[將 Operations Manager 連線到 Log Analytics](om-agents.md)。 |


### <a name="applications"></a>應用程式

| 資料 | 描述 |
|:---|:---|
| 要求和例外狀況 | 有關應用程式要求和例外狀況的詳細資料位於 _requests_、_pageViews_及 _exceptions_ 資料表中。 [外部元件](../app/asp-net-dependencies.md) 的呼叫位於 _dependencies_資料表中。 |
| 使用方式和效能 | 在 _requests_、_browserTimings_ 和 _performanceCounters_ 資料表中可取得應用程式的效能。 [自訂計量](../app/api-custom-events-metrics.md#trackevent)的資料位於 _customMetrics_ 資料表中。|
| 追蹤資料 | [分散式追蹤](../app/distributed-tracing.md)的結果會儲存在 _traces_ 資料表中。 |
| 可用性集合 | [可用性測試](../app/monitor-web-app-availability.md)的摘要資料會儲存在 _availabilityResults_ 資料表中。 這些測試的詳細資料位於不同的儲存體，並從 Azure 入口網站中的 Application Insights 存取。 |

### <a name="insights"></a>深入解析

| 資料 | 描述 |
|:---|:---|
| 適用於容器的 Azure 監視器 | [適用於容器的 Azure 監視器](../insights/container-insights-overview.md)所收集的清查和效能資料。 如需資料表清單，請參閱[容器資料收集詳細資訊](../insights/container-insights-log-search.md#container-records)。 |
| 適用於 VM 的 Azure 監視器 | [適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)所收集的地圖和效能資料。 請參閱[如何從適用於 VM 的 Azure 監視器查詢記錄](../insights/vminsights-log-search.md)，以取得查詢此資料的詳細資料。 |

### <a name="custom"></a>Custom 

| 資料 | 描述 |
|:---|:---|
| REST API | 將資料從任何 REST 用戶端寫入 Log Analytics 工作區。 請參閱[使用 HTTP 資料收集器 API 將記錄資料傳送給 Azure 監視器](data-collector-api.md)，以取得詳細資料。
| 邏輯應用程式 | 使用 **Azure Log Analytics 資料收集器**動作，從邏輯應用程式工作流程將任何資料寫入 Log Analytics 工作區。 |

### <a name="security"></a>安全性

| 資料 | 描述 |
|:---|:---|
| Azure 資訊安全中心 | [Azure 資訊安全中心](../../security-center/index.yml)會將其收集的資料儲存在 Log Analytics 工作區中，以便與其他記錄資料一起進行分析。 請參閱 [Azure 資訊安全中心的資料收集](../../security-center/security-center-enable-data-collection.md)，以取得工作區設定的詳細資料。 |
| Azure Sentinel | [Azure Sentinel](../../sentinel/index.yml) 會將資料來源的資料儲存到 Log Analytics 工作區。 請參閱[連線資料來源](../../sentinel/connect-data-sources.md)。  |


## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器資料平台](data-platform.md)。
- 了解 [Azure 監視器中的計量](data-platform-metrics.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。

