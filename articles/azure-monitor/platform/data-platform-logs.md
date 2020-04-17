---
title: Azure 監視器中的日誌 |微軟文件
description: 描述 Azure 監視器中用於進階分析監視數據的紀錄。
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 0b288bf6d987b9db682c8d1439879cf6b499f213
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457326"
---
# <a name="logs-in-azure-monitor"></a>Azure 監視器中的記錄

> [!NOTE]
> Azure 監視器收集的所有數據都適合兩種基本類型之一,即指標和日誌。 本文介紹日誌。 有關指標的詳細說明,請參閱[Azure 監視器中的指標](data-platform-metrics.md),以及監視[Azure 監視器收集的數據](data-platform.md),以便比較這兩個指標。

Azure 監視器中的日誌對於跨各種來源的數據執行複雜分析特別有用。 本文介紹了日誌在 Azure 監視器中的結構方式、如何處理數據以及標識在日誌中存儲數據的不同數據源。

> [!NOTE]
> 區分 Azure 監視器日誌和 Azure 中的日誌數據源非常重要。 例如,Azure 中的訂閱級別事件將寫入活動[日誌](platform-logs-overview.md),可以從 Azure 監視器功能表中查看。 大多數資源都會將操作資訊寫入[資源日誌](platform-logs-overview.md),您可以將這些資訊轉發到不同的位置。 Azure 監視器日誌是一個日誌數據平臺,用於收集活動日誌和資源日誌以及其他監視數據,以便在整個資源集中提供深度分析。

## <a name="what-are-azure-monitor-logs"></a>什麼是 Azure 監視器日誌?

Azure 監視器中的日誌包含不同類型的數據,這些數據組織到記錄中,每種類型的屬性集不同。 日誌可以包含數位值(如 Azure 監視器指標),但通常包含包含詳細說明的文本數據。 它們與指標數據略有不同,因為它們的結構各不相同,而且通常不會定期收集。 除了性能數據之外,還存儲 Azure 監視器日誌等遙測數據,以便將其全部組合用於分析。

日誌條目的常見類型是偶爾收集的事件。 事件由應用程式或服務創建,通常包含足夠的資訊來自行提供完整的上下文。 例如，事件可以表示已建立或修改特定資源、為了回應增加的流量而啟動新主機，或在應用程式中偵測到錯誤。

 資料的格式可能有所不同，應用程式可以使用其所需的結構建立自訂記錄。 指標數據甚至可以存儲在日誌中,將它們與其他監視數據合併,以便進行趨勢分析和其他數據分析。


## <a name="what-can-you-do-with-azure-monitor-logs"></a>使用 Azure 監視器日誌可以執行什麼操作?
下表列出了在 Azure 監視器中使用日誌的不同方法。


|  |  |
|:---|:---|
| 分析 | 使用 Azure 門戶中的[日誌分析](../log-query/get-started-portal.md)編寫[紀錄查詢](../log-query/log-query-overview.md),並使用強大的資料資源管理器分析引擎以交互方式分析日誌數據。<br>使用 Azure 門戶中的[應用程式見解分析主控台](../app/analytics.md)編寫日誌查詢,並互動式分析來自應用程式見解的日誌數據。 |
| 視覺化 | 將呈現為表表或圖表的查詢結果固定到[Azure 儀表板](../../azure-portal/azure-portal-dashboards.md)。<br>創建[工作簿](../app/usage-workbooks.md)以在互動式報表中與多組數據組合。 <br>將查詢的結果匯出到 [Power BI](powerbi.md) 以使用不同的視覺效果，並與 Azure 外部的使用者共用。<br>將查詢結果匯出到[Grafana](grafana-plugin.md)以利用其儀表板並與其他數據源結合使用。|
| 警示 | 設定[記錄警示規則](alerts-log.md)，在查詢結果符合特定結果時，傳送通知或採取[自動化動作](action-groups.md)。<br>在此資料編取的某些紀錄資料紀錄上設定[指標警示規則](alerts-metric-logs.md)。 |
| 擷取 | 使用[Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)訪問命令列的紀錄查詢結果。<br>使用[PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.operationalinsights)訪問命令列的日誌查詢結果。<br>使用[REST API](https://dev.loganalytics.io/)訪問來自自訂應用程式的日誌查詢結果。 |
| 匯出 | 構建一個工作流來檢索日誌數據,並使用[邏輯應用](~/articles/logic-apps/index.yml)將其複製到外部位置。 |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Azure 監視器日誌中的數據是如何構造的?
Azure 監視器日誌收集的數據存儲在[日誌分析工作區](../platform/design-logs-deployment.md)中。 每個工作區包含多個表,每個表都存儲來自特定源的數據。 雖然所有表都共用[一些公共屬性](log-standard-properties.md),但每個表都有一組唯一的屬性,具體取決於它存儲的數據類型。 新的工作區將具有標準表集,並且將添加更多表,這些表將由不同的監視解決方案和寫入工作區的其他服務添加。

來自應用程式見解的日誌數據使用與工作區相同的日誌分析引擎,但它為每個受監視的應用程式單獨存儲。 每個應用程式都有一組標準表來保存數據,如應用程式請求、異常和頁面檢視。

日誌查詢將使用來自日誌分析工作區或應用程式見解應用程式的數據。 可以使用[跨資源查詢](../log-query/cross-workspace-query.md)將應用程式數據與其他日誌數據一起分析,或創建包含多個工作區或應用程式的查詢。

![工作區](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>記錄查詢
使用使用[Kusto 查詢語言](../log-query/get-started-queries.md)編寫的[紀錄查詢](../log-query/log-query-overview.md)檢索 Azure 監視器日誌中的數據,該查詢允許您快速檢索、合併和分析收集的數據。 使用[日誌分析](../log-query/portals.md)在 Azure 門戶中編寫和測試日誌查詢。 它允許您以交互方式處理結果,或將它們固定到儀錶板上,以便與其他可視化效果一起查看結果。

![Log Analytics](media/data-platform-logs/log-analytics.png)

[從應用程式見解打開日誌分析](../app/analytics.md)以分析應用程式見解數據。

![Application Insights 分析](media/data-platform-logs/app-insights-analytics.png)

您還可以使用[日誌分析 API](https://dev.loganalytics.io/documentation/overview)和[應用程式見解 REST API](https://dev.applicationinsights.io/documentation/overview)檢索日誌數據。


## <a name="sources-of-azure-monitor-logs"></a>Azure 監視器紀錄的來源
Azure 監視器可以在 Azure 中及內部部署資源的各種來源收集資料。 下表列出了從將數據寫入 Azure 監視器日誌的不同資源中可用的不同數據來源。 每個都有指向任何所需配置的詳細資訊的連結。

### <a name="azure-tenant-and-subscription"></a>Azure 租戶和訂閱

| 資料 | 描述 |
|:---|:---|
| Azure 活動目錄稽核紀錄 | 通過每個目錄的診斷設置進行配置。 請參考[Azure AD 紀錄與 Azure 監視器紀錄整合](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。  |
| 活動記錄 | 默認情況下單獨存儲,可用於近即時警報。 安裝活動日誌分析解決方案以寫入日誌分析工作區。 請參考[在紀錄分析中收集與分析 Azure 活動紀錄](activity-log-collect.md)。 |

### <a name="azure-resources"></a>Azure 資源

| 資料 | 描述 |
|:---|:---|
| 資源診斷 | 將診斷設置配置為寫入診斷數據,包括日誌分析工作區的指標。 請參考[Azure 資源紀錄串流式傳輸到紀錄分析](resource-logs-collect-workspace.md)。 |
| 監視解決方案 | 監視解決方案將收集的數據寫入日誌分析工作區。 有關解決方案清單,請參閱[Azure 中管理解決方案的資料收集詳細資訊](../insights/solutions-inventory.md)。 有關安裝和使用解決方案的詳細資訊,請參閱[Azure 監視器中的監視解決方案](../insights/solutions.md)。 |
| 計量 | 將 Azure 監視器資源的平台指標發送到日誌分析工作區,以保留日誌數據更長時間,並使用[Kusto 查詢語言](/azure/kusto/query/)與其他數據類型執行複雜的分析。 請參考[Azure 資源紀錄串流式傳輸到紀錄分析](resource-logs-collect-storage.md)。 |
| Azure 表格儲存體 | 從 Azure 儲存收集數據,其中某些 Azure 資源寫入監視數據。 請參考[Azure blob 儲存的 IIS 與 Azure 儲存的事件與紀錄分析](diagnostics-extension-logs.md)。 |

### <a name="virtual-machines"></a>虛擬機器

| 資料 | 描述 |
|:---|:---|
|  代理程式資料來源 | 從[Windows](agent-windows.md)和[Linux](../learn/quick-collect-linux-computer.md)代理收集的數據來源包括事件、性能數據和自訂日誌。 關於資料來源的清單和設定的詳細資訊,請參閱[Azure 監視器中的代理資料來源](data-sources.md)。 |
| 監視解決方案 | 監視解決方案將數據從代理收集到的日誌分析工作區寫入。 有關解決方案清單,請參閱[Azure 中管理解決方案的資料收集詳細資訊](../insights/solutions-inventory.md)。 有關安裝和使用解決方案的詳細資訊,請參閱[Azure 監視器中的監視解決方案](../insights/solutions.md)。 |
| System Center Operations Manager | 將操作管理員管理組連接到 Azure 監視器,以便從本地代理收集事件和性能數據到日誌中。 有關此設定的詳細資訊[,請參閱將操作管理員連接到日誌分析](om-agents.md)。 |


### <a name="applications"></a>應用程式

| 資料 | 描述 |
|:---|:---|
| 要求與例外 | 有關應用程式請求和異常的詳細數據位於_請求_、_頁面檢視_和_異常_表中。 對[外部元件](../app/asp-net-dependencies.md)的調用位於_依賴項_表中。 |
| 使用與效能 | 應用程式的性能在_請求_、_瀏覽器計時__和性能計數器_表中可用。 [自定義指標](../app/api-custom-events-metrics.md#trackevent)的數據位於_自定義指標_表中。|
| 追蹤資料 | [分散式跟蹤](../app/distributed-tracing.md)的結果存儲在_跟蹤_表中。 |
| 可用性集合 | [可用性測試](../app/monitor-web-app-availability.md)的摘要數據存儲在_可用性結果_表中。 這些測試的詳細數據位於單獨的存儲中,並從 Azure 門戶中的應用程式見解訪問。 |

### <a name="insights"></a>深入解析

| 資料 | 描述 |
|:---|:---|
| 適用於容器的 Azure 監視器 | [Azure 監視器收集的容器的](../insights/container-insights-overview.md)清單和性能數據。 有關表的清單,請參考[容器資料收集詳細資訊](../insights/container-insights-log-search.md#container-records)。 |
| 適用於 VM 的 Azure 監視器 | [Azure 監視器為 VM](../insights/vminsights-overview.md)收集的映射和性能數據。 有關如何[查詢 VM 的 Azure 監視器日誌](../insights/vminsights-log-search.md)的詳細資訊,請參閱如何查詢日誌。 |

### <a name="custom"></a>Custom 

| 資料 | 描述 |
|:---|:---|
| REST API | 將數據從任何 REST 用戶端寫入日誌分析工作區。 關於詳細資訊[,請參考使用 HTTP 資料收集器 API 將紀錄資料傳送到 Azure 監視器](data-collector-api.md)。
| 邏輯應用程式 | 使用**Azure 日誌分析數據收集器**操作,從邏輯應用工作流將任何數據寫入日誌分析工作區。 |

### <a name="security"></a>安全性

| 資料 | 描述 |
|:---|:---|
| Azure 資訊安全中心 | [Azure 安全中心](/azure/security-center/)存儲它在日誌分析工作區中收集的數據,以便使用其他日誌數據對其進行分析。 有關工作區設定的詳細資訊,請參閱[Azure 安全中心中的資料收集](../../security-center/security-center-enable-data-collection.md)。 |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/)將數據從資料來源儲存到日誌分析工作區中。 請參考[連線資料來源](/azure/sentinel/connect-data-sources)。  |


## <a name="next-steps"></a>後續步驟

- 瞭解有關 Azure[監視器數據平臺](data-platform.md)的更多資訊。
- 瞭解[Azure 監視器 中的指標](data-platform-metrics.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。
