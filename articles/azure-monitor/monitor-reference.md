---
title: Azure 監視器監視器
description: Azure 監視器監視的所有服務和其他資源的引用。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: e0e98b87cf3612bf01f90f806ea64ef06d08c60a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255323"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Azure 監視器監視的項目為何？
本文介紹了 Azure 監視器監視的不同應用程式和服務。 

## <a name="insights-and-core-solutions"></a>洞察和核心解決方案
核心見解和解決方案被視為 Azure 監視器的一部分,並遵循 Azure 的支援和服務級別協定。 它們在 Azure 監視器可用的所有 Azure 區域都支援。

### <a name="insights"></a>深入解析

見解為特定應用程式和服務提供客製的監控體驗。 它們收集和分析日誌和指標。

| 見解 | 描述 |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | 可擴充應用程式效能管理 (APM) 服務,用於在任何平台上監視即時 Web 應用程式。 |
| [容器的 Azure 監視器](insights/container-insights-overview.md) | 監視部署到 Azure 容器實例或託管在 Azure 庫伯奈斯服務 (AKS) 上託管的託管庫伯奈斯群集的容器工作負載的性能。 |
| [用於宇宙資料庫的 Azure 監視器(預覽)](insights/cosmosdb-insights-overview.md) | 在統一的互動式體驗中提供所有 Azure Cosmos DB 資源的總體性能、故障、容量和操作運行狀況視圖。 |
| [網路 Azure 監視器(預覽版)](insights/network-insights-overview.md) | 為所有網路資源提供運行狀況和指標的全面視圖。 高級搜索功能可説明您識別資源依賴項,通過搜索網站名稱,啟用標識託管網站的資源等方案。 |
[資源群組的 Azure 監視器(預覽)](insights/resource-group-insights.md) |  對單個資源遇到的任何問題進行會審和診斷,同時提供資源組整體運行狀況和性能的上下文。 |
| [用於儲存的 Azure 監視器(預覽)](insights/storage-insights-overview.md) | 通過提供 Azure 儲存服務性能、容量和可用性的統一視圖,全面監視 Azure 儲存帳戶。 |
| [虛擬機器的 Azure 監視器](insights/container-insights-overview.md) | 大規模監視 Azure 虛擬機器 (VM) 和虛擬機器縮放集。 此服務會分析您 Windows 和 Linux VM 的效能與健康情況，並且在其他資源和外部處理序上監視其處理序及相依性。 |

### <a name="core-solutions"></a>核心解決方案

解決方案基於為特定應用程式或服務自定義的日誌查詢和檢視。 他們只收集和分析日誌,並且隨著時間的推移被棄用,以獲得見解。

| 解決方法 | 描述 |
|:---|:---|
| [代理伺服器執行狀況](insights/solution-agenthealth.md) | 分析日誌分析代理的運行狀況和配置。 |
| [警示管理](platform/alert-management-solution.md) | 分析從系統中心運營經理、納吉奧斯或扎比克斯收集的警報。 |
| [服務地圖](insights/service-map.md) | 自動發現 Windows 和 Linux 系統上的應用程式元件,並映射服務之間的通信。 |



## <a name="azure-services"></a>Azure 服務
下表列出了 Azure 服務及其收集到 Azure 監視器中的數據。 

- 指標 - 服務會自動將指標收集到 Azure 監視器指標中。 
- 日誌 - 該服務支援診斷設置,這些設置可以將平臺日誌和指標收集到 Azure 監視器日誌。
- Insight - 該服務有可供洞察,為服務提供自定義的監視體驗。

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
|Azure 服務管理員 (RDFE) | 否 | 否 | 否 |  |
|Backup | 否 | 是 | 否 |  |
|Bastion | 否 | 否 | 否 |  |
|Batch | 是 | 是 | 否 |  |
|Batch AI | 否 | 否 | 否 |  |
|區塊鏈服務 | 否 | 是 | 否 |  |
|藍圖 | 否 | 否 | 否 |  |
|Bot 服務 | 否 | 否 | 否 |  |
|雲端服務 | 是 | 是 | 否 | 監控來賓操作系統和工作流所需的代理。  |
|Cloud Shell | 否 | 否 | 否 |  |
|認知服務 | 是 | 是 | 否 |  |
|Container Instances | 是 | 否 | 否 |  |
|Container Registry | 是 | 是 | 否 |  |
|內容傳遞網路 (CDN) | 否 | 是 | 否 |  |
|Cosmos DB | 是 | 是 | [是](insights/cosmosdb-insights-overview.md) |  |
|成本管理 | 否 | 否 | 否 |  |
|資料箱 | 否 | 否 | 否 |  |
|資料目錄第 2 代 | 否 | 否 | 否 |  |
|資料總管 | 是 | 是 | 否 |  |
|Data Factory | 是 | 是 | 否 |  |
|資料工廠 v2 | 否 | 是 | 否 |  |
|Data Share | 否 | 否 | 否 |  |
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
|動態 365 客戶互動 | 否 | 否 | 否 |  |
|動態 365 財務和營運 | 否 | 否 | 否 |  |
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
|Key Vault | 是 | 是 | 否 |  |
|Kubernetes Service (AKS) | 否 | 否 | [是](insights/container-insights-overview.md)  |  |
|負載平衡器 | 是 | 是 | 否 |  |
|Logic Apps | 是 | 是 | 否 |  |
|機器學習服務 | 否 | 否 | 否 |  |
|受控應用程式  | 否 | 否 | 否 |  |
|地圖  | 否 | 否 | 否 |  |
|媒體服務 | 是 | 是 | 否 |  |
|Microsoft Flow | 否 | 否 | 否 |  |
|Microsoft 受管理的電腦 | 否 | 否 | 否 |  |
|Microsoft PowerApps | 否 | 否 | 否 |  |
|Microsoft Social Engagement | 否 | 否 | 否 |  |
|Microsoft Stream | 是 | 是 | 否 |  |
|遷移 | 否 | 否 | 否 |  |
|Multi-Factor Authentication | 否 | 是 | 否 |  |
|網路監看員 | 是 | 是 | 否 |  |
|通知中樞 | 是 | 否 | 否 |  |
|Open Datasets | 否 | 否 | 否 |  |
|原則 | 否 | 否 | 否 |  |
|Power BI | 是 | 是 | 否 |  |
|Power BI Embedded | 否 | 否 | 否 |  |
|私人連結 | 否 | 否 | 否 |  |
|專案後臺通訊平臺 | 否 | 否 | 否 |  |
|Red Hat OpenShift | 否 | 否 | 否 |  |
|Redis 快取 | 是 | 是 | 否 |  |
|Resource Graph | 否 | 否 | 否 |  |
|Resource Manager | 否 | 否 | 否 |  |
|零售搜尋 - 由必應 | 否 | 否 | 否 |  |
|搜尋 | 是 | 是 | 否 |  |
|服務匯流排 | 是 | 是 | 否 |  |
|Service Fabric | 否 | 是 | 否 | 監控來賓操作系統和工作流所需的代理。  |
|註冊門戶 | 否 | 否 | 否 |  |
|Site Recovery | 否 | 是 | 否 |  |
|春雲服務 | 否 | 否 | 否 |  |
|SQL 資料倉儲 | 是 | 是 | 否 |  |
|SQL Database | 是 | 是 | 否 |  |
|SQL Server Stretch Database | 是 | 是 | 否 |  |
|Stack | 否 | 否 | 否 |  |
|儲存體 | 是 | 否 | [是](insights/storage-insights-overview.md) |  |
|儲存快取 | 否 | 否 | 否 |  |
|儲存同步服務 | 否 | 否 | 否 |  |
|串流分析 | 是 | 是 | 否 |  |
|時間序列深入解析 | 是 | 是 | 否 |  |
|TINA | 否 | 否 | 否 |  |
|流量管理員 | 是 | 是 | 否 |  |
|一般列印 | 否 | 否 | 否 |  |
|虛擬機器擴展集 | 否 | 是 | [是](insights/vminsights-overview.md) | 監控來賓操作系統和工作流所需的代理。 |
|虛擬機器 | 是 | 是 | [是](insights/vminsights-overview.md) | 監控來賓操作系統和工作流所需的代理。 |
|虛擬網路 | 是 | 是 | [是](insights/network-insights-overview.md) |  |
|虛擬網路 - NSG 串流紀錄 | 否 | 是 | 否 |  |
|VPN 閘道 | 是 | 是 | 否 |  |
|Windows 虛擬桌面 | 否 | 否 | 否 |  |


## <a name="product-integrations"></a>產品整合
下表中的服務和解決方案將其數據存儲在日誌分析工作區中,以便可以使用 Azure 監視器收集的其他日誌數據對其進行分析。

| 產品/服務 | 描述 |
|:---|:---|
| [Azure 自動化](/azure/automation/) | 管理作業系統更新並跟蹤 Windows 和 Linux 電腦上的更改。 請參考[追蹤](../automation/change-tracking.md)並[更新管理](../automation/automation-update-management.md)。 |
| [Azure 資訊保護](https://docs.microsoft.com/azure/information-protection/) | 對文件和電子郵件進行分類並選擇性地保護文檔和電子郵件。 請參閱[Azure 資訊保護的中央報告](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports)。 |
| [Azure 資訊安全中心](/azure/security-center/) | 收集和分析安全事件並執行威脅分析。 請參閱[Azure 安全中心中的數據收集](/azure/security-center/security-center-enable-data-collection) |
| [Azure Sentinel](/azure/sentinel/) | 連接到不同的來源,包括 Office 365 和 Amazon Web 服務雲端追蹤。 請參考[連線資料來源](/azure/sentinel/connect-data-sources)。 |
| [金鑰保存庫分析](insights/azure-key-vault.md) | 分析 Azure 金鑰保管庫審核事件日誌。 |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | 創建診斷設置以將日誌發送到 Azure 監視器。 請參考[在 Intune(預覽)中將紀錄資料傳送到儲存、事件中心或紀錄分析](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor)。  |
| 網路  | [網路效能監視器](insights/network-performance-monitor.md)- 監控與服務和應用程式終結點的網路連接和性能。<br>[Azure 應用程式閘道](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor)- 分析 Azure 應用程式閘道的紀錄和指標。<br>[流量分析](/azure/network-watcher/traffic-analytics)- 分析網路觀察程式網路安全組 (NSG) 流日誌,以便深入瞭解 Azure 雲中的流量流。 |
| [Office 365](insights/solution-office-365.md) | 監視 Office 365 環境。 更新的版本,可通過 Azure Sentinel 提供改進的載入服務。 |
| [SQL Analytics](insights/azure-sql.md) | 大規模監視 Azure SQL 資料庫、彈性池和託管實例的性能,並跨多個訂閱。 |
| [Surface Hub](insights/surface-hubs.md) | 跟蹤曲面集線器設備的運行狀況和使用。 |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | 通過將管理組連接到 Azure 監視器,從操作管理器代理收集數據。 請參考[操作管理員連接到 Azure 監視器](platform/om-agents.md)<br> 使用[運營經理評估](insights/scom-assessment.md)解決方案評估系統中心運營經理管理團隊的風險和運行狀況。 |
| [微軟團隊會議室](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | 微軟團隊會議室設備的集成端到端管理。 |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | 生成、測試和分發應用程式,然後監視其狀態和使用方式。 請參考[您的移動應用程式與應用程式中心與應用程式來見解](learn/mobile-center-quickstart.md)。 |
| Windows | [Windows 更新合規性](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started)- 評估 Windows 桌面升級。<br>[桌面分析](https://docs.microsoft.com/configmgr/desktop-analytics/overview)- 與設定管理器集成,提供見解和智慧,以便對 Windows 用戶端的更新就緒情況做出更明智的決策。 |



## <a name="other-solutions"></a>其他解決方案
其他解決方案可用於監視不同的應用程式和服務,但主動開發已經停止,並且可能無法在所有區域可用。 它們由 Azure 日誌分析數據引入服務級別協議涵蓋。

| 解決方法 | 描述 |
|:---|:---|
| [動作目錄執行狀況檢查](insights/ad-assessment.md) | 評估活動目錄環境的風險和運行狀況。 |
| [動作目錄複製狀態](insights/ad-replication-status.md) | 定期監視活動目錄環境,以監視任何複製失敗。 |
| [活動紀錄分析](platform/activity-log-view.md#azure-portal) | 查看活動日誌條目。 |
| [DNS 分析(預覽)](insights/dns-analytics.md) | 從 DNS 伺服器收集、分析和關聯 Windows DNS 分析和審核日誌以及其他相關數據。 |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | 跨多個部署收集、查看和分析雲鑄造系統運行狀況和性能指標。 |
| [容器](insights/containers.md) | 檢視和管理 Docker 和 Windows 容器主機。 |
| [依需評估](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | 評估和優化本地、混合和雲端Microsoft技術環境的可用性、安全性和性能。 |
| [SQL 執行狀況檢查](insights/sql-assessment.md) | 評估 SQL Server 環境的風險和運行狀況。  |
| [連線資料](insights/wire-data.md) | 使用日誌分析代理從 Windows 連接和 Linux 連接的電腦收集的整合網路和性能數據。 |


## <a name="third-party-integration"></a>第三方整合

| 解決方法 | 描述 |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | IT Service Management Connector (ITSMC) 可讓您連線 Azure 和支援的 IT 服務管理 (ITSM) 產品/服務。  |


## <a name="resources-outside-of-azure"></a>Azure 以外的資源
Azure 監視器可以使用下表中列出的方法從 Azure 外部的資源收集數據。

| 資源 | 方法 |
|:---|:---|
| 應用程式 | 使用應用程式見解監視 Azure 外部的 Web 應用程式。 請參閱[什麼是應用程式見解?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) |
| 虛擬機器 | 使用日誌分析代理從其他雲端環境或本地虛擬機器的來賓作業系統收集數據。 請參考[您的紀錄分析代理的文章資料](platform/log-analytics-agent.md)。 |
| REST API 用戶端 | 單獨的 API 可用於從任何 REST API 用戶端將資料寫入 Azure 監視器日誌和指標。 請參考[使用以紀錄的 HTTP 資料收集器 API 向 Azure 監視器傳送紀錄資料](platform/data-collector-api.md),以及使用 REST API 將[Azure 資源的自訂指標傳送到 Azure 監視器指標儲存](platform/metrics-store-custom-rest-api.md)。 |



## <a name="next-steps"></a>後續步驟

- 閱讀有關[Azure 監視器資料平臺的更多內容,該平臺儲存了見解和解決方案收集的紀錄和指標](platform/data-platform.md)。
- 完成[有關監視 Azure 資源的教學](learn/tutorial-resource-logs.md)。
- 完成[關於撰寫紀錄查詢以分析 Azure 監視器紀錄中的教學](learn/tutorial-resource-logs.md)。
- 完成[建立式指標圖表以分析 Azure 監視器指標中的教學](learn/tutorial-metrics-explorer.md)。

 
