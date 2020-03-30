---
title: Azure 監視器中的日誌 |微軟文檔
description: 描述 Azure 監視器中用於高級分析監視資料的日誌。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: dd499a82ba1011d96772d6722e25a434d43a6bf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480193"
---
# <a name="logs-in-azure-monitor"></a>Azure 監視器中的記錄

> [!NOTE]
> Azure 監視器收集的所有資料都適合兩種基本類型之一，即指標和日誌。 本文介紹日誌。 有關指標的詳細說明，請參閱[Azure 監視器中的指標](data-platform-metrics.md)，以及監視[Azure 監視器收集的資料](data-platform.md)，以便比較這兩個指標。

Azure 監視器中的日誌對於跨各種來源的資料執行複雜分析特別有用。 本文介紹了日誌在 Azure 監視器中的結構方式、如何處理資料以及標識在日誌中存儲資料的不同資料來源。

> [!NOTE]
> 區分 Azure 監視器日誌和 Azure 中的日誌資料來源非常重要。 例如，Azure 中的訂閱級別事件將寫入活動[日誌](platform-logs-overview.md)，可以從 Azure 監視器功能表中查看。 大多數資源都會將操作資訊寫入[資源日誌](platform-logs-overview.md)，您可以將這些資訊轉發到不同的位置。 Azure 監視器日誌是一個日誌資料平臺，用於收集活動日誌和資源日誌以及其他監視資料，以便在整個資源集中提供深度分析。

## <a name="what-are-azure-monitor-logs"></a>什麼是 Azure 監視器日誌？

Azure 監視器中的日誌包含不同類型的資料，這些資料組織到記錄中，每種類型的屬性集不同。 日誌可以包含數位值（如 Azure 監視器指標），但通常包含包含詳細說明的文本資料。 它們與指標資料略有不同，因為它們的結構各不相同，而且通常不會定期收集。 除了效能資料之外，還存儲 Azure 監視器日誌等遙測資料，以便將其全部組合用於分析。

日誌條目的常見類型是偶爾收集的事件。 事件由應用程式或服務創建，通常包含足夠的資訊來自行提供完整的上下文。 例如，事件可以表示已建立或修改特定資源、為了回應增加的流量而啟動新主機，或在應用程式中偵測到錯誤。

 資料的格式可能有所不同，應用程式可以使用其所需的結構建立自訂記錄。 指標資料甚至可以存儲在日誌中，將它們與其他監視資料合併，以便進行趨勢分析和其他資料分析。


## <a name="what-can-you-do-with-azure-monitor-logs"></a>使用 Azure 監視器日誌可以執行什麼操作？
下表列出了在 Azure 監視器中使用日誌的不同方法。


|  |  |
|:---|:---|
| 分析 | 使用 Azure 門戶中的[日誌分析](../log-query/get-started-portal.md)編寫[日誌查詢](../log-query/log-query-overview.md)，並使用強大的資料資源管理器分析引擎以對話模式分析日誌資料。<br>使用 Azure 門戶中[的應用程式見解分析主控台](../app/analytics.md)編寫日誌查詢，並互動式分析來自應用程式見解的日誌資料。 |
| 視覺化 | 將呈現為表或圖表的查詢結果固定到[Azure 儀表板](../../azure-portal/azure-portal-dashboards.md)。<br>創建[活頁簿](../app/usage-workbooks.md)以在互動式報表中與多組資料組合。 <br>將查詢的結果匯出到 [Power BI](powerbi.md) 以使用不同的視覺效果，並與 Azure 外部的使用者共用。<br>將查詢結果匯出到[Grafana](grafana-plugin.md)以利用其儀表板並與其他資料來源結合使用。|
| 警示 | 設定[記錄警示規則](alerts-log.md)，在查詢結果符合特定結果時，傳送通知或採取[自動化動作](action-groups.md)。<br>在作為指標提取的某些日誌資料日誌上配置[指標警報規則](alerts-metric-logs.md)。 |
| 擷取 | 使用[Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)訪問命令列的日誌查詢結果。<br>使用[PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.operationalinsights)訪問命令列的日誌查詢結果。<br>使用[REST API](https://dev.loganalytics.io/)訪問來自自訂應用程式的日誌查詢結果。 |
| 匯出 | 構建一個工作流來檢索日誌資料，並使用[邏輯應用](~/articles/logic-apps/index.yml)將其複製到外部位置。 |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Azure 監視器日誌中的資料是如何構造的？
Azure 監視器日誌收集的資料存儲在[日誌分析工作區](../platform/design-logs-deployment.md)中。 每個工作區包含多個表，每個表都存儲來自特定源的資料。 雖然所有表都共用[一些公共屬性](log-standard-properties.md)，但每個表都有一組唯一的屬性，具體取決於它存儲的資料類型。 新的工作區將具有標準表集，並且將添加更多表，這些表將由不同的監視解決方案和寫入工作區的其他服務添加。

來自應用程式見解的日誌資料使用與工作區相同的日誌分析引擎，但它為每個受監視的應用程式單獨存儲。 每個應用程式都有一組標準表來保存資料，如應用程式請求、異常和網頁檢視。

日誌查詢將使用來自日誌分析工作區或應用程式見解應用程式的資料。 可以使用[跨資源查詢](../log-query/cross-workspace-query.md)將應用程式資料與其他日誌資料一起分析，或創建包含多個工作區或應用程式的查詢。

![工作區](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>記錄查詢
使用使用[Kusto 查詢語言](../log-query/get-started-queries.md)編寫的[日誌查詢](../log-query/log-query-overview.md)檢索 Azure 監視器日誌中的資料，該查詢允許您快速檢索、合併和分析收集的資料。 使用[日誌分析](../log-query/portals.md)在 Azure 門戶中編寫和測試日誌查詢。 它允許您以對話模式處理結果，或將它們固定到儀表板上，以便與其他視覺化效果一起查看結果。

![Log Analytics](media/data-platform-logs/log-analytics.png)

[從應用程式見解打開日誌分析](../app/analytics.md)以分析應用程式見解資料。

![Application Insights 分析](media/data-platform-logs/app-insights-analytics.png)

您還可以使用[日誌分析 API](https://dev.loganalytics.io/documentation/overview)和[應用程式見解 REST API](https://dev.applicationinsights.io/documentation/overview)檢索日誌資料。


## <a name="sources-of-azure-monitor-logs"></a>Azure 監視器日誌的來源
Azure 監視器可以在 Azure 中及內部部署資源的各種來源收集資料。 下表列出了從將資料寫入 Azure 監視器日誌的不同資源中可用的不同資料來源。 每個都有指向任何所需配置的詳細資訊的連結。

### <a name="azure-tenant-and-subscription"></a>Azure 租戶和訂閱

| 資料 | 描述 |
|:---|:---|
| Azure 活動目錄稽核記錄 | 通過每個目錄的診斷設置進行配置。 請參閱[將 Azure AD 日誌與 Azure 監視器日誌集成](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。  |
| 活動記錄 | 預設情況下單獨存儲，可用於近即時警報。 安裝活動日誌分析解決方案以寫入日誌分析工作區。 請參閱[在日誌分析中收集和分析 Azure 活動日誌](activity-log-collect.md)。 |

### <a name="azure-resources"></a>Azure 資源

| 資料 | 描述 |
|:---|:---|
| 資源診斷 | 將診斷設置配置為寫入診斷資料，包括日誌分析工作區的指標。 請參閱[將 Azure 資源日誌資料流到日誌分析](resource-logs-collect-workspace.md)。 |
| 監視解決方案 | 監視解決方案將收集的資料寫入日誌分析工作區。 有關解決方案清單，請參閱[Azure 中管理解決方案的資料收集詳細資訊](../insights/solutions-inventory.md)。 有關安裝和使用解決方案的詳細資訊，請參閱[Azure 監視器中的監視解決方案](../insights/solutions.md)。 |
| 計量 | 將 Azure 監視器資源的平臺指標發送到日誌分析工作區，以保留日誌資料更長時間，並使用[Kusto 查詢語言](/azure/kusto/query/)與其他資料類型執行複雜的分析。 請參閱[將 Azure 資源日誌資料流到日誌分析](resource-logs-collect-storage.md)。 |
| Azure 表格儲存體 | 從 Azure 存儲收集資料，其中某些 Azure 資源寫入監視資料。 請參閱[使用 Azure blob 存儲進行 IIS 和 Azure 表存儲的事件與日誌分析](diagnostics-extension-logs.md)。 |

### <a name="virtual-machines"></a>虛擬機器

| 資料 | 描述 |
|:---|:---|
|  代理程式資料來源 | 從[Windows](agent-windows.md)和[Linux](../learn/quick-collect-linux-computer.md)代理收集的資料來源包括事件、效能資料和自訂日誌。 有關資料來源的清單和配置的詳細資訊，請參閱[Azure 監視器中的代理資料來源](data-sources.md)。 |
| 監視解決方案 | 監視解決方案將資料從代理收集到的日誌分析工作區寫入。 有關解決方案清單，請參閱[Azure 中管理解決方案的資料收集詳細資訊](../insights/solutions-inventory.md)。 有關安裝和使用解決方案的詳細資訊，請參閱[Azure 監視器中的監視解決方案](../insights/solutions.md)。 |
| System Center Operations Manager | 將操作管理器管理組連接到 Azure 監視器，以便從本地代理收集事件和效能資料到日誌中。 有關此配置的詳細資訊[，請參閱將操作管理器連接到日誌分析](om-agents.md)。 |


### <a name="applications"></a>應用程式

| 資料 | 描述 |
|:---|:---|
| 請求和例外 | 有關應用程式請求和異常的詳細資料位於_請求_、_網頁檢視_和_異常_表中。 對[外部元件](../app/asp-net-dependencies.md)的調用位於_依賴項_表中。 |
| 使用和性能 | 應用程式的性能在_請求_、_瀏覽器計時__和效能計數器_表中可用。 [自訂指標](../app/api-custom-events-metrics.md#trackevent)的資料位於_自訂指標_表中。|
| 跟蹤資料 | [分散式跟蹤](../app/distributed-tracing.md)的結果存儲在_跟蹤_表中。 |
| 可用性集合 | [可用性測試](../app/monitor-web-app-availability.md)的摘要資料存儲在_可用性結果_表中。 這些測試的詳細資料位於單獨的存儲中，並從 Azure 門戶中的應用程式見解訪問。 |

### <a name="insights"></a>深入解析

| 資料 | 描述 |
|:---|:---|
| 適用於容器的 Azure 監視器 | [Azure 監視器收集的容器的](../insights/container-insights-overview.md)清單和效能資料。 有關表的清單，請參閱[容器資料收集詳細資訊](../insights/container-insights-log-search.md#container-records)。 |
| 適用於 VM 的 Azure 監視器 | [Azure 監視器為 VM](../insights/vminsights-overview.md)收集的映射和效能資料。 有關如何[查詢 VM 的 Azure 監視器日誌](../insights/vminsights-log-search.md)的詳細資訊，請參閱如何查詢日誌。 |

### <a name="custom"></a>Custom 

| 資料 | 描述 |
|:---|:---|
| REST API | 將資料從任何 REST 用戶端寫入日誌分析工作區。 有關詳細資訊[，請參閱使用 HTTP 資料收集器 API 將日誌資料發送到 Azure 監視器](data-collector-api.md)。
| 邏輯應用程式 | 使用**Azure 日誌分析資料收集器**操作，從邏輯應用工作流將任何資料寫入日誌分析工作區。 |

### <a name="security"></a>安全性

| 資料 | 描述 |
|:---|:---|
| Azure 資訊安全中心 | [Azure 安全中心](/azure/security-center/)存儲它在日誌分析工作區中收集的資料，以便使用其他日誌資料對其進行分析。 有關工作區配置的詳細資訊，請參閱[Azure 安全中心中的資料收集](../../security-center/security-center-enable-data-collection.md)。 |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/)將資料從資料來源存儲到日誌分析工作區中。 請參閱[連接資料來源](/azure/sentinel/connect-data-sources)。  |


## <a name="next-steps"></a>後續步驟

- 瞭解有關 Azure[監視器資料平臺](data-platform.md)的更多資訊。
- 瞭解[Azure 監視器 中的指標](data-platform-metrics.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。
