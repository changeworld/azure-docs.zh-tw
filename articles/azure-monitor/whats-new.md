---
title: Azure 監視器文件的最新動向
description: 每月更新的 Azure 監視器文件重大更新。
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/05/2020
ms.openlocfilehash: ecee13850e735f827a5465e0f49039f514afe233
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657736"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Azure 監視器文件的最新動向
本文提供新增或已大幅更新的 Azure 監視器文章清單。 該清單會在每個月的第一週重新整理，以納入上個月的文章更新。

## <a name="january-2020"></a>2020 年 1 月

### <a name="general"></a>一般
- [Azure 監視器監視的項目為何？](monitor-reference.md) - 新文章。

### <a name="agents"></a>代理程式
- [使用 Azure Log Analytics 代理程式收集記錄資料](platform/log-analytics-agent.md) - 已更新網路防火牆需求表。


### <a name="alerts"></a>警示
- [在 Azure 入口網站中建立和管理動作群組](platform/action-groups.md) - 針對不再需要的 v2 功能移除了設定。
- [使用 Resource Manager 範本建立計量警示](platform/alerts-metric-create-templates.md) - 已新增 *ignoreDataBefore* 參數的範例。  已新增多準則規則相關條件約束。
- [使用 Log Analytics 警示 REST API](platform/api-alerts.md) - 已更正 JSON 範例。


### <a name="application-insights"></a>Application Insights
- [Application Insights 和 Log Analytics 所使用的 IP 位址](app/ip-addresses.md) - 已更新「可用性測試」一節，其中包含如何新增輸入連接埠規則，以允許使用 Azure 網路安全性群組的流量。
- [針對 Azure Application Insights Profiler 的問題進行疑難排解](app/profiler-troubleshooting.md) - 已更新一般疑難排解。
- [Azure Application Insights 中的遙測取樣](app/sampling.md) - 已更新並重新建構，以根據客戶的意見反應改善可讀性。


### <a name="data-security"></a>資料安全性
- [Azure 監視器客戶管理的金鑰設定](platform/customer-managed-keys.md) - 新文章。

### <a name="insights-and-solutions"></a>見解和解決方案

#### <a name="azure-monitor-for-containers"></a>適用於容器的 Azure 監視器
- [設定適用於容器的 Azure 監視器代理程式資料收集](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config) - 已新增在 Azure Red Hat OpenShift 上升級代理程式的詳細資訊，並新增了其他資訊來區分升級代理程式的方法。
- [為適用於容器的 Azure 監視器建立效能警示](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-alerts) - 已修訂資訊及更新步驟，以便使用工作區內容警示，對工作區中儲存的效能資料建立警示。
- [使用適用於容器的 Azure 監視器進行 Kubernetes 監視](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-analyze) - 已更新有關 Windows Kubernetes 叢集支援的概觀文章和分析文章。
- [使用適用於容器的 Azure 監視器來設定 Azure Red Hat OpenShift 叢集](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-azure-redhat-setup) - 已新增在 Azure Red Hat OpenShift 上升級代理程式的詳細資訊，並新增了其他資訊來區分升級代理程式的方法。
- [使用適用於容器的 Azure 監視器來設定混合式 Kubernetes 叢集](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-hybrid-setup) - 已更新內容，以反映 Kubelet 的 cAdvisor 新增了安全連接埠 10250 支援。
- [如何管理適用於容器的 Azure 監視器代理程式](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-manage-agent) - 相較於其他類型的 Kubernetes 叢集，已更新透過 Azure Red Hat OpenShift 抓取計量的行為和設定相關詳細資料。
- [如何管理適用於容器的 Azure 監視器代理程式](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration) - 已更新透過 Azure Red Hat OpenShift (相較於其他類型的 Kubernetes 叢集) 抓取計量的行為和設定相關詳細資訊。
- [如何針對計量更新適用於容器的 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-update-metrics) - 相較於其他類型的 Kubernetes 叢集， Azure Red Hat OpenShift 的計量抓取的行為和設定相關的更新詳細資料。


#### <a name="azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器
- [適用於 VM 的 Azure 監視器 (GA) 常見問題集](insights/vminsights-ga-release-faq.md) - 已新增有關將工作區和代理程式升級至新版本的資訊。

#### <a name="office-365"></a>Office 365
- [Azure 中的 office 365 管理解決方案](insights/solution-office-365.md) - 已新增在 Azure Sentinel 中遷移至 Office 365 解決方案的詳細資訊和常見問題集。 已移除上線區段。



### <a name="logs"></a>記錄
- [在 Azure 監視器中管理 Log Analytics 工作區](platform/manage-access.md)- 更新無法執行的動作。
- [管理 Azure 監視器記錄的使用量和成本](platform/manage-cost-storage.md) - 在「計價模式」一節中新增了資料量計算的詳細說明。
- [使用 Azure Resource Manager 範本建立和設定 Log Analytics 工作區](platform/template-workspace-configuration.md) - 已使用新定價層來更新範例。


### <a name="platform-logs"></a>平台記錄
- [使用診斷設定收集 Azure 活動記錄 - Azure 監視器](platform/diagnostic-settings-legacy.md) - 已變更屬性的其他資訊。
- [匯出 Azure 活動記錄](platform/activity-log-export.md) - 已針對 UI 變更進行更新。 





## <a name="december-2019"></a>2019 年 12 月

### <a name="agents"></a>代理程式
- [將 Linux 電腦連線至 Azure 監視器](platform/agent-linux.md) - 新文章。

### <a name="alerts"></a>警示
- [使用 Resource Manager 範本建立計量警示](platform/alerts-metric-create-templates.md) - 已新增自訂計量的範例。
- [在 Azure 監視器中建立具有動態閾值的警示](platform/alerts-dynamic-thresholds.md) - 已新增有關解讀動態閾值圖表的章節。
- [Azure 中的警示與通知監視概觀](platform/alerts-overview.md) - 已更新 Resource Graph 查詢。
- [Azure 監視器中計量警示支援的資源](platform/alerts-metric-near-real-time.md) - 更新支援的計量和維度。
- [從舊版 Log Analytics 警示 API 切換至新的 Azure 警示 API](platform/alerts-log-api-switch.md) - 新增了已修改警示名稱的附註。
- [了解計量警示在 Azure 監視器中的運作方式。](platform/alerts-metric-overview.md) - 已新增支援大規模監視的資源類型。

### <a name="application-insights"></a>Application Insights
- [適用於背景工作角色服務應用程式 (非 HTTP 應用程式) 的 Application Insights](app/worker-service.md) - 已新增 C# 程式碼的預設記錄層級。 已更新套件參考版本。
- [ApplicationInsights.config 參考 - Azure](app/configuration-with-applicationinsights-config.md)- 已更新範例程式碼。
- [使用 PowerShell 將 Azure Application Insights 自動化](app/powershell.md) - 更新 Resource Manager 範本。
- [Azure 監視器的 Application Insights NuGet 套件](app/nuget.md) - 已更新套件版本。
- [建立新的 Azure Application Insights 資源](app/create-new-resource.md) - 新增了全域唯一名稱的附註。
- [使用即時計量資料流進行診斷](app/live-stream.md) - 已更新 ASP.NET Core SDK 版本需求。
- [Application Insights 中的事件計數器](app/eventcounters.md) - 已更新 customMetrics 的類別和資料表。
- [探索 Azure Application Insights 中的 Java 追蹤記錄](app/java-trace-logs.md) - 已新增 Java 代理程式記錄閾值的設定。
- [Application Insights 和 Log Analytics 所使用的 IP 位址](app/ip-addresses.md) - 已更新即時計量資料流的 IP 位址。
- [監視 Azure 應用程式服務效能](app/azure-web-apps.md) - 已新增 ASP.NET Core 3.0 支援。 
- [使用 Azure 監視器來監視 Python 應用程式 (預覽)](app/opencensus-python.md) - 已新增 OpenCensus Python 結構描述對應至 Azure 監視器結構描述的詳細說明。
- [Azure Application Insights 的版本資訊](app/release-notes.md) - 新增了舊版的附註。
- [Azure Application Insights 中的遙測通道](app/telemetry-channels.md) - 已經更在長期中斷連線期間內已捨棄資料的持續時間。
- [Azure Application Insights 中的遙測取樣](app/sampling.md) - 已修正自訂 TelemetryInitializer 的程式碼片段。
- [針對 Java Web 專案中的 Application Insights 進行疑難排解](app/java-troubleshoot.md) - 已移除 JDK 9 中有關不支援相依性集合的陳述。

### <a name="insights-and-solutions"></a>見解和解決方案
- [適用於容器的 Azure 監視器常見問題集](insights/container-insights-faq.md) - 已新增有關 [映像] 和 [名稱] 欄位的問題。
- [Azure 監視器中的 Azure SQL 分析解決方案](insights/azure-sql.md) - 已更新資料庫等候受控執行個體支援。
- [設定適用於容器的 Azure 監視器代理程式資料集合](insights/container-insights-agent-config.md) - 已新增 enrich_container_logs 的設定。
- [使用適用於容器的 Azure 監視器來設定混合式 Kubernetes 叢集](insights/container-insights-hybrid-setup.md) - 已新增疑難排解章節。
- [使用 Azure 監視器來監視 Active Directory 複寫狀態](insights/ad-replication-status.md) - 已更新 .NET Framework 必要條件。
- [Azure 中的網路效能監視器解決方案](insights/network-performance-monitor.md)- 已新增支援的區域。
- [使用 Azure 監視器將 Active Directory 環境最佳化](insights/ad-assessment.md) - 已更新 .NET Framework 必要條件。
- [使用 Azure 監視器將 SQL Server 環境最佳化](insights/sql-assessment.md) - 已更新 .NET Framework 必要條件。
- [使用 Azure Log Analytics 將 System Center Operations Manager 環境最佳化](insights/scom-assessment.md) - 已更新 .NET Framework 必要條件。
- [支援與 Azure Log Analytics 中的 IT Service Management Connector 連線](platform/itsmc-connections.md) - 已將 New York 新增至必要用戶端識別碼和用戶端密碼。

### <a name="logs"></a>記錄
- [刪除和復原 Azure Log Analytics 工作區](platform/delete-workspace.md) - 已新增 PowerShell 方法。
- [設計您的 Azure 監視器記錄部署](platform/design-logs-deployment.md) - 已提高工作區的擷取率。

### <a name="metrics"></a>計量
- [可透過診斷設定匯出的 Azure 監視器平台計量](platform/metrics-supported-export-diagnostic-settings.md) - 新文章。

### <a name="platform-logs"></a>平台記錄
根據使用診斷設定來設定活動記錄的新功能，在平台記錄內容的重新建構過程中更新了多篇文章。

- [將 Azure 資源記錄封存到儲存體帳戶](platform/resource-logs-collect-storage.md)
- [Azure 活動記錄事件結構描述](platform/activity-log-schema.md)
- [Azure 監視器服務限制](service-limits.md)
- [在 Log Analytics 工作區中收集和分析 Azure 活動記錄](platform/activity-log-collect.md)
- [使用診斷設定收集 Azure 活動記錄 (預覽) - Azure 監視器](platform/diagnostic-settings-legacy.md)
- [跨 Azure 租用戶將 Azure 活動記錄收集到 Log Analytics 工作區中](platform/activity-log-collect-tenants.md)
- [在 Log Analytics 工作區中收集 Azure 資源記錄](platform/resource-logs-collect-workspace.md)
- [使用 Resource Manager 範本在 Azure 中建立診斷設定](platform/diagnostic-settings-template.md)
- [建立診斷設定以收集 Azure 中的記錄和計量](platform/diagnostic-settings.md)
- [匯出 Azure 活動記錄](platform/activity-log-export.md)
- [Azure 平台記錄概觀](platform/platform-logs-overview.md)
- [將 Azure 監視資料串流至事件中樞](platform/stream-monitoring-data-event-hubs.md)
- [將 Azure 平台記錄串流至事件中樞](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>快速入門與教學課程

- [在 Azure 監視器中建立計量圖表](learn/tutorial-metrics-explorer.md) - 新文章。
- [從 Azure 資源收集資源記錄，並使用 Azure 監視器進行分析](learn/tutorial-resource-logs.md) - 新文章。
- [使用 Azure 監視器來監視 Azure 資源](learn/quick-monitor-azure-resource.md) - 新文章。
   
## <a name="next-steps"></a>後續步驟

- 如果您想要參與 Azure 監視器文件，請參閱[文件參與者指南](https://docs.microsoft.com/contribute/)。