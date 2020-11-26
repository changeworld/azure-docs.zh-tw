---
title: Azure 監視器監視的項目為何
description: Azure 監視器監視的所有服務和其他資源的參考資訊。
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 5be6a2d73be35b3d637df9364364784d373dabb3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186689"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Azure 監視器監視的項目為何？
本文說明 Azure 監視器監視的不同應用程式和服務。 

## <a name="insights-and-core-solutions"></a>深入解析與核心解決方案
核心深入解析和解決方案被視為是 Azure 監視器的一部分，並遵守 Azure 的支援和服務等級協定。 所有可使用 Azure 監視器的 Azure 區域都支援這些功能。

### <a name="insights"></a>深入解析

深入解析可為特定應用程式和服務提供自訂的監視體驗。 它們會收集並分析記錄和計量。

| 見解 | 描述 |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | 可擴充的應用程式效能管理 (APM) 服務，可在任何平台上監視您的即時 Web 應用程式。 |
| [適用於容器的 Azure 監視器](insights/container-insights-overview.md) | 會監視部署至 Azure Container Instances 或 Azure Kubernetes Service (AKS) 上所裝載受控 Kubernetes 叢集的容器工作負載效能。 |
| [適用於 Cosmos DB 的 Azure 監視器](insights/cosmosdb-insights-overview.md) | 可供您透過統一的互動式體驗，檢視所有 Azure Cosmos DB 資源的整體效能、失敗、容量及作業健康情況。 |
| [適用於網路的 Azure 監視器 (預覽)](insights/network-insights-overview.md) | 針對您所有的網路資源，提供健康情況和計量的全面性檢視。 進階搜尋功能可協助您識別資源相依性，藉由直接搜尋您的網站名稱，來實現不同案例，例如識別裝載網站的資源。 |
[適用於資源群組的 Azure 監視器 (預覽)](insights/resource-group-insights.md) |  將個別資源碰到的任何問題加以分級與診斷，同時就資源群組整體的健康狀態與效能提供內容。 |
| [適用於儲存體的 Azure 監視器](insights/storage-insights-overview.md) | 藉由提供 Azure 儲存體服務效能、容量和可用性的統一檢視，讓您能夠全面監視 Azure 儲存體帳戶。 |
| [適用於 VM 的 Azure 監視器](insights/vminsights-overview.md) | 會大規模監視您的 Azure 虛擬機器 (VM) 和虛擬機器擴展集。 此服務會分析您 Windows 和 Linux VM 的效能與健康情況，並且在其他資源和外部處理序上監視其處理序及相依性。 |
| [適用於 Key Vault 的 Azure 監視器 (預覽)](./insights/key-vault-insights-overview.md) | 藉由提供您的 Key Vault 要求、效能、失敗和延遲的統一觀點，提供金鑰保存庫的全面監視。 |
| [適用於 Azure Cache for Redis 的 Azure 監視器 (預覽)](insights/redis-cache-insights-overview.md) |  提供整體效能、失敗、容量、作業健康情況的統一、互動檢視。 |


### <a name="core-solutions"></a>核心解決方案

解決方案是以針對特定應用程式或服務自訂的記錄查詢和檢視為依據。 解決方法只會收集和分析記錄，並在一段時間後淘汰，以利深入解析。

| 解決方法 | 描述 |
|:---|:---|
| [代理程式健康情況](insights/solution-agenthealth.md) | 分析 Log Analytics 代理程式的健康情況和設定。 |
| [警示管理](platform/alert-management-solution.md) | 分析從 System Center Operations Manager、Nagios 或 Zabbix 收集的警示。 |
| [服務對應](insights/service-map.md) | 會自動在 Windows 及 Linux 系統上探索應用程式元件，並對應服務之間的通訊。 |



## <a name="azure-services"></a>Azure 服務
下表列出 Azure 服務及其收集並送到 Azure 監視器的資料。 

- 計量 - 此服務會自動將計量收集到 Azure 監視器計量中。 
- 記錄 - 此服務支援的診斷設定可將平台記錄和計量收集到 Azure 監視器記錄。
- 深入解析 - 此服務的深入解析提供針對服務的自訂監視體驗。

| 服務 | 計量 | 記錄 | 見解 | 注意 |
|:---|:---|:---|:---|:---|
|Active Directory | 否 | 是 | [是](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | 否 | 否 | 否 |  |
|Active Directory Domain Services | 否 | 是 | 否 |  |
|活動記錄檔 | 否 | 是 | 否 | |
|進階威脅防護 | 否 | 否 | 否 |  |
|建議程式 | 否 | 否 | 否 |  |
|AI Builder | 否 | 否 | 否 |  |
|Analysis Services | 是 | 是 | 否 |  |
|API for FHIR | 否 | 否 | 否 |  |
|API 管理 | 是 | 是 | 否 |  |
|App Service 方案 | 是 | 是 | 否 |  |
|AppConfig | 否 | 否 | 否 |  |
|應用程式閘道 | 是 | 是 | 否 |  |
|證明服務 | 否 | 否 | 否 |  |
|自動化 | 是 | 是 | 否 |  |
|Azure Service Manager (RDFE) | 否 | 否 | 否 |  |
|Backup | 否 | 是 | 否 |  |
|Bastion | 否 | 否 | 否 |  |
|Batch | 是 | 是 | 否 |  |
|Batch AI | 否 | 否 | 否 |  |
|區塊鏈服務 | 否 | 是 | 否 |  |
|藍圖 | 否 | 否 | 否 |  |
|Bot 服務 | 否 | 否 | 否 |  |
|雲端服務 | 是 | 是 | 否 | 監視客體作業系統和工作流程所需的代理程式。  |
|Cloud Shell | 否 | 否 | 否 |  |
|認知服務 | 是 | 是 | 否 |  |
|Container Instances | 是 | 否 | 否 |  |
|Container Registry | 是 | 是 | 否 |  |
|內容傳遞網路 (CDN) | 否 | 是 | 否 |  |
|Cosmos DB | 是 | 是 | [是](insights/cosmosdb-insights-overview.md) |  |
|成本管理 | 否 | 否 | 否 |  |
|資料箱 | 否 | 否 | 否 |  |
|資料目錄 Gen2 | 否 | 否 | 否 |  |
|資料總管 | 是 | 是 | 否 |  |
|Data Factory | 是 | 是 | 否 |  |
|Data Factory V2 | 否 | 是 | 否 |  |
|資料共用 | 否 | 否 | 否 |  |
|適用於 MariaDB 的資料庫 | 是 | 是 | 否 |  |
|適用於 MySQL 的資料庫 | 是 | 是 | 否 |  |
|適用於 PostgreSQL 的資料庫 | 是 | 是 | 否 |  |
|Database Migration Service | 否 | 否 | 否 |  |
|Databricks | 否 | 是 | 否 |  |
|DDoS保護 | 是 | 是 | 否 |  |
|DevOps | 否 | 否 | 否 |  |
|DNS | 是 | 否 | 否 |  |
|網域名稱 | 否 | 否 | 否 |  |
|DPS | 否 | 否 | 否 |  |
|Dynamics 365 Customer Engagement | 否 | 否 | 否 |  |
|Dynamics 365 Finance and Operations | 否 | 否 | 否 |  |
|Event Grid | 是 | 否 | 否 |  |
|事件中樞 | 是 | 是 | 否 |  |
|ExpressRoute | 是 | 是 | 否 |  |
|防火牆 | 是 | 是 | 否 |  |
|Front Door | 是 | 是 | 否 |  |
|函式 | 是 | 是 | 否 |  |
|HDInsight | 否 | 是 | 否 |  |
|HPC Cache | 否 | 否 | 否 |  |
|資訊保護 | 否 | 是 | 否 |  |
|Intune | 否 | 是 | 否 |  |
|IoT 中心 | 否 | 否 | 否 |  |
|IoT 中樞 | 是 | 是 | 否 |  |
|Key Vault | 是 | 是 | [是](./insights/key-vault-insights-overview.md) |  |
|Kubernetes Service (AKS) | 否 | 否 | [是](insights/container-insights-overview.md)  |  |
|負載平衡器 | 是 | 否 | 否 |  |
|Logic Apps | 是 | 是 | 否 |  |
|機器學習服務 | 否 | 否 | 否 |  |
|受控應用程式  | 否 | 否 | 否 |  |
|地圖  | 否 | 否 | 否 |  |
|媒體服務 | 是 | 是 | 否 |  |
|Microsoft 受管理的電腦 | 否 | 否 | 否 |  |
|Microsoft PowerApps | 否 | 否 | 否 |  |
|Microsoft 社交參與 | 否 | 否 | 否 |  |
|Microsoft Stream | 是 | 是 | 否 |  |
|遷移 | 否 | 否 | 否 |  |
|Multi-Factor Authentication | 否 | 是 | 否 |  |
|網路監看員 | 是 | 是 | 否 |  |
|通知中樞 | 是 | 否 | 否 |  |
|Open Datasets | 否 | 否 | 否 |  |
|原則 | 否 | 否 | 否 |  |
|Power Automate | 否 | 否 | 否 |  |
|Power BI Embedded | 是 | 是 | 否 |  |
|私人連結 | 否 | 否 | 否 |  |
|Project Spool Communication Platform | 否 | 否 | 否 |  |
|Red Hat OpenShift | 否 | 否 | 否 |  |
|Redis 快取 | 是 | 是 | [是](insights/redis-cache-insights-overview.md) | |
|Resource Graph | 否 | 否 | 否 |  |
|Resource Manager | 否 | 否 | 否 |  |
|Retail Search – Bing 提供 | 否 | 否 | 否 |  |
|搜尋 | 是 | 是 | 否 |  |
|服務匯流排 | 是 | 是 | 否 |  |
|Service Fabric | 否 | 是 | 否 | 監視客體作業系統和工作流程所需的代理程式。  |
|註冊入口網站 | 否 | 否 | 否 |  |
|Site Recovery | 否 | 是 | 否 |  |
|Spring Cloud Service | 否 | 否 | 否 |  |
|Azure Synapse Analytics | 是 | 是 | 否 |  |
|SQL Database | 是 | 是 | 否 |  |
|SQL Server Stretch Database | 是 | 是 | 否 |  |
|Stack | 否 | 否 | 否 |  |
|儲存體 | 是 | 否 | [是](insights/storage-insights-overview.md) |  |
|儲存體快取 | 否 | 否 | 否 |  |
|儲存體同步服務 | 否 | 否 | 否 |  |
|串流分析 | 是 | 是 | 否 |  |
|時間序列深入解析 | 是 | 是 | 否 |  |
|TINA | 否 | 否 | 否 |  |
|流量管理員 | 是 | 是 | 否 |  |
|通用列印 | 否 | 否 | 否 |  |
|虛擬機器擴展集 | 否 | 是 | [是](insights/vminsights-overview.md) | 監視客體作業系統和工作流程所需的代理程式。 |
|虛擬機器 | 是 | 是 | [是](insights/vminsights-overview.md) | 監視客體作業系統和工作流程所需的代理程式。 |
|虛擬網路 | 是 | 是 | [是](insights/network-insights-overview.md) |  |
|虛擬網路 - NSG 流量記錄 | 否 | 是 | 否 |  |
|VPN 閘道 | 是 | 是 | 否 |  |
|Windows 虛擬桌面 | 否 | 否 | 否 |  |

## <a name="virtual-machine-agents"></a>虛擬機器代理程式
下表列出可從虛擬機器的客體作業系統收集資料，並將資料傳送至監視的代理程式。 每個代理程式都可以收集不同的資料，並將其傳送至 Azure 監視器中的計量或記錄。 

如需每個代理程式可收集之資料的詳細資訊，請參閱 [Azure 監視器代理](platform/agents-overview.md) 程式的總覽。

| 代理程式 |  計量 | 記錄 |
|:---|:---|:---|:---|
| [Azure 監視器代理程式 (預覽) ](platform/azure-monitor-agent-overview.md) | 是 | 是 |
| [Log Analytics 代理程式](platform/log-analytics-agent.md) | 否 | 是|
| [診斷擴充功能](platform/diagnostics-extension-overview.md) | 是 | 否 |
| [Telegraf 代理程式](platform/collect-custom-metrics-linux-telegraf.md) | 是 | 否 |
| [相依性代理程式](insights/vminsights-enable-overview.md) | 否 | 是 |


## <a name="product-integrations"></a>產品整合
下表中的服務和解決方案會將其資料儲存在 Log Analytics 工作區中，以便搭配 Azure 監視器收集的其他記錄資料進行分析。

| 產品/服務 | 描述 |
|:---|:---|
| [Azure 自動化](../automation/index.yml) | 管理作業系統更新，並追蹤 Windows 和 Linux 電腦上的變更。 請參閱[變更追蹤](../automation/change-tracking/overview.md)和[更新管理](../automation/update-management/overview.md)。 |
| [Azure 資訊保護](/azure/information-protection/) | 分類並選擇性地保護文件和電子郵件。 請參閱 [Azure 資訊保護的中央報告](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports)。 |
| [Azure 資訊安全中心](../security-center/index.yml) | 收集和分析安全性事件，並執行威脅分析。 請參閱 [Azure 資訊安全中心的資料收集](../security-center/security-center-enable-data-collection.md)。 |
| [Azure Sentinel](../sentinel/index.yml) | 連線到不同的來源，包括 Office 365 和 Amazon Web Services 雲端軌跡。 請參閱[連線資料來源](../sentinel/connect-data-sources.md)。 |
| [Microsoft Intune](/intune/) | 建立診斷設定，以將記錄檔傳送至 Azure 監視器。 請參閱[將記錄資料傳送至儲存體、事件中樞或 Intune 中的記錄分析 (預覽)](/intune/fundamentals/review-logs-using-azure-monitor)。  |
| 網路  | [網路效能監控](insights/network-performance-monitor.md) - 監視服務和應用程式端點的網路連線能力和效能。<br>[Azure 應用程式閘道](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) - 分析來自 Azure 應用程式閘道的記錄和計量。<br>[流量分析](../network-watcher/traffic-analytics.md) - 分析網路監看員網路安全性群組 (NSG) 流量記錄，讓您深入了解 Azure 雲端中的流量。 |
| [Office 365](insights/solution-office-365.md) | 監視您的 Office 365 環境。 已透過 Azure Sentinel 提供改善上線體驗的更新版本。 |
| [SQL Analytics](insights/azure-sql.md) | 跨多個訂用帳戶大規模監視 Azure SQL 資料庫和 SQL 受控實例的效能。 |
| [Surface Hub](insights/surface-hubs.md) | 追蹤 Surface Hub 裝置的健康情況和使用情況。 |
| [System Center Operations Manager](/system-center/scom) | 藉由將管理群組連線至 Azure 監視器，從 Operations Manager 代理程式收集資料。 請參閱[將 Operations Manager 連線至 Azure 監視器](platform/om-agents.md)。<br> 使用 [Operations Manager 評量](insights/scom-assessment.md)解決方案，評估 System Center Operations Manager 管理群組的風險和健康情況。 |
| [Microsoft Teams Rooms](/microsoftteams/room-systems/azure-monitor-deploy) | Microsoft Teams Rooms 裝置的整合端對端管理。 |
| [Visual Studio App Center](/appcenter/) | 建立、測試、散發應用程式，然後監視其狀態和使用情況。 請參閱[開始使用 App Center 和 Application Insights 分析您的行動應用程式](learn/mobile-center-quickstart.md)。 |
| Windows | [Windows Update 合規性](/windows/deployment/update/update-compliance-get-started) - 評估您的 Windows 桌面升級。<br>[電腦分析](/configmgr/desktop-analytics/overview) - 與 Configuration Manager 整合，提供深入解析和情報，以做出更明智的 Windows 用戶端更新準備決策。 |



## <a name="other-solutions"></a>其他解決方案
其他解決方案可用於監視不同的應用程式和服務，但已停止開發，而且可能無法在所有區域提供。 這些解決方案涵蓋在 Azure Log Analytics 資料擷取服務等級協定之下。

| 解決方法 | 描述 |
|:---|:---|
| [Active Directory 健康情況檢查](insights/ad-assessment.md) | 評估 Active Directory 環境的風險與健康情況。 |
| [Active Directory 複寫狀態](insights/ad-replication-status.md) | 定期監視您的 Active Directory 環境是否有任何複寫失敗。 |
| [活動記錄分析](platform/activity-log.md#activity-log-analytics-monitoring-solution) | 檢視活動記錄項目。 |
| [DNS 分析 (預覽)](insights/dns-analytics.md) | 收集、分析 Windows DNS 分析和稽核記錄，並將其與 DNS 伺服器中的其他相關資料關聯。 |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | 收集、檢視及分析多個部署的 Cloud Foundry 系統健康情況和效能計量。 |
| [容器](insights/containers.md) | 檢視及管理 Docker 和 Windows 容器主機。 |
| [隨選評量](/services-hub/health/getting_started_with_on_demand_assessments) | 評估和最佳化內部部署、混合式、雲端 Microsoft 技術環境的可用性、安全性及效能。 |
| [SQL 健康情況檢查](insights/sql-assessment.md) | 評估 SQL Server 環境的風險與健康情況。  |
| [連線資料](insights/wire-data.md) | 使用 Log Analytics 代理程式從 Windows 連線和 Linux 連線的電腦收集的匯總網路和效能資料。 |

## <a name="third-party-integration"></a>協力廠商整合

| 解決方法 | 描述 |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | IT Service Management Connector (ITSMC) 可讓您連線 Azure 和支援的 IT 服務管理 (ITSM) 產品/服務。  |


## <a name="resources-outside-of-azure"></a>Azure 外部的資源
Azure 監視器可以使用下表所列的方法，從 Azure 外部的資源收集資料。

| 資源 | 方法 |
|:---|:---|
| 應用程式 | 使用 Application Insights 監視 Azure 外部的 Web 應用程式。 請參閱[什麼是 Application Insights？](./app/app-insights-overview.md)。 |
| 虛擬機器 | 使用代理程式從其他雲端環境或內部部署虛擬機器的客體作業系統收集資料。 請參閱 [Azure 監視器代理程式的總覽](platform/agents-overview.md)。 |
| REST API 用戶端 | 有不同的 API 可用來將來自任何 REST API 用戶端的資料寫入 Azure 監視器記錄和計量。 針對記錄請參閱[使用 HTTP 資料收集器 API 將記錄資料傳送給 Azure 監視器](platform/data-collector-api.md)，針對計量請參閱[使用 REST API 將 Azure 資源的自訂計量傳送至 Azure 監視器計量的存放區](platform/metrics-store-custom-rest-api.md)。 |



## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 監視器資料平台](platform/data-platform.md)，其會儲存深入解析和解決方案所收集的記錄和計量。
- 完成[監視 Azure 資源的教學課程](learn/tutorial-resource-logs.md)。
- 完成[撰寫記錄查詢的教學課程](learn/tutorial-resource-logs.md)，以分析 Azure 監視器記錄中的資料。
- 完成[建立計量圖表的教學課程](learn/tutorial-metrics-explorer.md)，以分析 Azure 監視器計量中的資料。

