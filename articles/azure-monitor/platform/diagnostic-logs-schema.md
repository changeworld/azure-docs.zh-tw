---
title: Azure 資源紀錄支援的服務和架構
description: 瞭解 Azure 資源日誌的支援服務和事件架構。
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 8abd8767d9bb7e3c4336f6600b94f6b3f4ea48f1
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380512"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Azure 資源紀錄受支援的服務、架構和類別

> [!NOTE]
> 資源日誌以前稱為診斷日誌。

[Azure 監視器資源日誌](../../azure-monitor/platform/platform-logs-overview.md)是 Azure 服務發出的日誌,用於描述這些服務或資源的操作。 通過 Azure 監視器提供的所有資源日誌都共用一個通用的頂級架構,每個服務都靈活地為自己的事件發出唯一屬性。

資源類型 (適用於 `resourceId` 屬性) 與 `category` 的組合可唯一識別結構描述。 本文介紹了資源日誌的頂級架構以及每個服務到架構的連結。

## <a name="top-level-resource-logs-schema"></a>頂層資源記錄架構

| 名稱 | 必要/選用 | 描述 |
|---|---|---|
| time | 必要 | 事件的時間戳記 (UTC)。 |
| resourceId | 必要 | 發出事件之資源的資源識別碼。 對於租用戶服務，這是 /tenants/tenant-id/providers/provider-name 的格式。 |
| tenantId | 租用戶記錄所需的 | 此事件所繫結 Active Directory 租用戶的租用戶識別碼。 這個屬性只能用於租用戶層級記錄，並不會出現在資源層級記錄中。 |
| operationName | 必要 | 此事件所代表的作業名稱。 如果事件代表 RBAC 作業，則這是 RBAC 作業名稱 (例如 Microsoft.Storage/storageAccounts/BlobServices/Blobs/Read)。 通常以 Resource Manager 作業形式建模，即使它們不是實際記載的 Resource Manager 作業也是一樣 (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | 選用 | 與作業建立關聯的 api-version，如果使用 API 執行 operationName (例如 `http://myservice.windowsazure.net/object?api-version=2016-06-01`). 如果沒有對應至此作業的 API，則版本代表該作業的版本，以防與作業建立關聯的屬性在未來變更。 |
| category | 必要 | 事件的記錄類別。 類別是您可在特定資源上啟用或停用記錄的資料粒度。 事件之屬性 Blob 內顯示的屬性，會與特定記錄類別和資源類型內的屬性相同。 典型的日誌類別是"審核""操作""執行"和"請求"。 |
| resultType | 選用 | 事件的狀態。 一般值包括︰「已啟動」、「進行中」、「成功」、「失敗」、「作用中」和「已解決」。 |
| resultSignature | 選用 | 事件的子狀態。 如果此作業對應至 REST API 呼叫，則這是對應 REST 呼叫的 HTTP 狀態碼。 |
| resultDescription | 選用 | 此作業的靜態文字描述，例如 "獲取存儲檔"。 |
| durationMs | 選用 | 作業的持續時間 (以毫秒為單位)。 |
| callerIpAddress | 選用 | 呼叫端 IP 位址，如果作業對應至來自具有公開可用 IP 位址之實體的 API 呼叫。 |
| correlationId | 選用 | 用來將一組相關事件群組在一起的 GUID。 一般而言，如果兩個事件具有相同 operationName 但具有兩個不同狀態 (例如 它們共用相同的關聯 ID,「已啟動」和「成功」。 這也可能代表事件之間的其他關聯性。 |
| 身分識別 | 選用 | JSON Blob，描述已執行作業之使用者或應用程式的身分識別。 這通常包括來自 Active Directory 的授權和宣告/JWT 權杖。 |
| 層級 | 選用 | 事件的嚴重性層級。 必須是「資訊」、「警告」、「錯誤」或「嚴重」中的其中一個。 |
| location | 選用 | 發出事件之資源的區域，例如 "美國東部"或"法國南部" |
| properties | 選用 | 任何與此特定事件類別相關的擴充屬性。 所有自定義/唯一屬性都必須放在架構的"B 部分"中。 |

## <a name="service-specific-schemas-for-resource-logs"></a>資源記錄的服務特定架構
資源診斷記錄的結構描述會根據資源和記錄類別而有所不同。 此清單顯示提供可用資源日誌的所有服務,以及指向服務和特定於類別的架構的連結(如果可用)。

| 服務 | 結構描述與文件 |
| --- | --- |
| Azure Active Directory | [概述](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)、[稽核紀錄架構](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md)與[登入架構](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API 管理 | [API 管理資源紀錄](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| 應用程式閘道 |[應用程式閘道的紀錄記錄](../../application-gateway/application-gateway-diagnostics.md) |
| Azure 自動化 |[Azure 自動化的紀錄分析](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure 批次處理紀錄記錄](../../batch/batch-diagnostics.md) |
| 適用於 MySQL 的 Azure 資料庫 | [適用於 MySQL 的 Azure 資料庫診斷記錄](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| 適用於 PostgreSQL 的 Azure 資料庫 | [在後格雷SQL紀錄的 Azure 資料庫](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure 資料總管 | [Azure 資料資源管理員記錄](/azure/data-explorer/using-diagnostic-logs) |
| 認知服務 | [Azure 認知服務的紀錄記錄](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Azure 容器註冊表的紀錄記錄](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| 內容傳遞網路 | [CDN 的 Azure 紀錄](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB 記錄](../../cosmos-db/logging.md) |
| Data Factory | [使用 Azure 監視器監視資料工廠](../../data-factory/monitor-using-azure-monitor.md) |
| 資料湖分析 |[存取 Azure 資料湖分析的紀錄](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[存取 Azure 資料湖儲存的紀錄](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 事件中樞 |[Azure 事件中心紀錄](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | 無法使用結構描述。 |
| Azure 防火牆 | 無法使用結構描述。 |
| IoT 中樞 | [IoT 中樞作業](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure 金鑰保管庫紀錄記錄](../../key-vault/key-vault-logging.md) |
| Kubernetes 服務 |[Azure 庫伯內斯日誌記錄](../../aks/view-master-logs.md#log-event-schema) |
| 負載平衡器 |[Azure 負載平衡器的記錄檔分析](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B 自訂追蹤結構描述](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 網路安全性群組 |[網路安全性群組 (NSG) 的記錄檔分析](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS 保護 | [管理 Azure DDoS Protection Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI 專用 | [紀錄記錄在 Azure 內嵌入的電源 BI](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| [復原服務] | [Azure 備份的資料模型](../../backup/backup-azure-reports-data-model.md)|
| 搜尋 |[啟用和使用搜尋流量分析](../../search/search-traffic-analytics.md) |
| 服務匯流排 |[Azure 服務匯流排記錄](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL 資料庫紀錄記錄](../../sql-database/sql-database-metrics-diag-logging.md) |
| 串流分析 |[作業記錄](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| 流量管理員 | [流量管理員紀錄架構](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| 虛擬網路 | 無法使用結構描述。 |
| 虛擬網路閘道 | 無法使用結構描述。 |

## <a name="supported-log-categories-per-resource-type"></a>每個資源類型支援的記錄檔類別

某些類別可能僅支援特定類型的資源。 這是以某種形式提供的所有可用的清單。  例如,Microsoft.Sql/伺服器/資料庫類別不適用於所有類型的資料庫。 有關詳細資訊,請參閱有關[SQL 資料庫診斷紀錄的資訊](../../sql-database/sql-database-metrics-diag-logging.md)。 

|資源類型|類別|類別顯示名稱|
|---|---|---|
|微軟.AAD/域服務|系統安全|系統安全|
|微軟.AAD/域服務|AccountManagement|AccountManagement|
|微軟.AAD/域服務|登入日誌關閉|登入日誌關閉|
|微軟.AAD/域服務|物件存取|物件存取|
|微軟.AAD/域服務|原則變更|原則變更|
|微軟.AAD/域服務|權限使用|權限使用|
|微軟.AAD/域服務|詳細追蹤|詳細追蹤|
|微軟.AAD/域服務|目錄服務存取|目錄服務存取|
|微軟.AAD/域服務|帳號記錄|帳號記錄|
|microsoft.aadiam/tenants|登入|登入|
|Microsoft.AnalysisServices/servers|引擎|引擎|
|Microsoft.AnalysisServices/servers|服務|服務|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement 閘道的相關記錄|
|微軟.AppPlatform/春天|應用程式主控台|應用程式主控台|
|Microsoft.Automation/automationAccounts|JobLogs|作業記錄|
|Microsoft.Automation/automationAccounts|JobStreams|作業串流|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Dsc 節點狀態|
|Microsoft.Batch/batchAccounts|ServiceLog|服務記錄|
|微軟.BatchAI/工作區|百色事件|百色事件|
|微軟.BatchAI/工作區|百色節點事件|百色節點事件|
|微軟.BatchAI/工作區|拜約布事件|拜約布事件|
|微軟.區塊鏈/區塊鏈成員|區塊鏈應用|區塊鏈應用程式|
|微軟.區塊鏈/區塊鏈成員|Proxy|Proxy|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|取得端點的計量，例如頻寬、輸出等資訊。|
|Microsoft.ClassicNetwork/networksecuritygroups|網路安全性群組規則流程事件|網路安全性群組規則流程事件|
|Microsoft.CognitiveServices/accounts|稽核|稽核記錄|
|Microsoft.CognitiveServices/accounts|RequestResponse|要求和回應記錄|
|Microsoft.ContainerRegistry/registries|容器註冊儲存庫事件|儲存函式庫事件紀錄(預覽)|
|Microsoft.ContainerRegistry/registries|容器註冊登入事件|登入事件(預覽)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Kubernetes API 伺服器|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Kubernetes 控制器管理員|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Kubernetes 排程器|
|Microsoft.ContainerService/managedClusters|庫貝審計|庫伯內斯審計|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Kubernetes 叢集自動調整程式|
|微軟.數據磚/工作區|德布法斯|Databricks 檔案系統|
|微軟.數據磚/工作區|clusters|資料磚叢集|
|微軟.數據磚/工作區|accounts|資料磚帳戶|
|微軟.數據磚/工作區|jobs|資料磚工作|
|微軟.數據磚/工作區|notebook|Databricks Notebook|
|微軟.數據磚/工作區|ssh|資料磚 SSH|
|微軟.數據磚/工作區|工作區|資料磚工作區|
|微軟.數據磚/工作區|密碼|樞紐磚機密|
|微軟.數據磚/工作區|sql 權限|資料磚 SQL 權限|
|微軟.數據磚/工作區|實例池|實例池|
|微軟資料目錄/資料目錄|掃描狀態紀錄事件|掃描狀態|
|Microsoft.DataFactory/factories|ActivityRuns|管線活動執行記錄|
|Microsoft.DataFactory/factories|PipelineRuns|管線執行記錄|
|Microsoft.DataFactory/factories|TriggerRuns|觸發程序執行記錄|
|Microsoft.DataLakeAnalytics/accounts|稽核|稽核記錄|
|Microsoft.DataLakeAnalytics/accounts|Requests|要求記錄|
|Microsoft.DataLakeStore/accounts|稽核|稽核記錄|
|Microsoft.DataLakeStore/accounts|Requests|要求記錄|
|微軟.資料共享/帳戶|共用|共用|
|微軟.資料共享/帳戶|共用訂閱|共用訂閱|
|微軟.資料共享/帳戶|已送出分享快照|已送出的分享快照|
|微軟.資料共享/帳戶|已接收分享快照|已接收的分享快照|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|MySQL 伺服器記錄|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|MySQL 稽核紀錄|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL 伺服器記錄|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|後格雷SQL查詢儲存執行時統計資訊|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|後格雷SQL查詢儲存等待統計資訊|
|微軟.DBforPostgreSQL/伺服器2|PostgreSQLLogs|PostgreSQL 伺服器記錄|
|微軟.DBforPostgreSQL/伺服器2|QueryStoreRuntimeStatistics|後格雷SQL查詢儲存執行時統計資訊|
|微軟.DBforPostgreSQL/伺服器2|QueryStoreWaitStatistics|後格雷SQL查詢儲存等待統計資訊|
|微軟.桌面虛擬化/工作區|Checkpoint|Checkpoint|
|微軟.桌面虛擬化/工作區|錯誤|錯誤|
|微軟.桌面虛擬化/工作區|管理性|管理性|
|微軟.桌面虛擬化/工作區|摘要|摘要|
|微軟.桌面虛擬化/應用程式群組|Checkpoint|Checkpoint|
|微軟.桌面虛擬化/應用程式群組|錯誤|錯誤|
|微軟.桌面虛擬化/應用程式群組|管理性|管理性|
|微軟.桌面虛擬化/主機池|Checkpoint|Checkpoint|
|微軟.桌面虛擬化/主機池|錯誤|錯誤|
|微軟.桌面虛擬化/主機池|管理性|管理性|
|微軟.桌面虛擬化/主機池|Connection|Connection|
|微軟.桌面虛擬化/主機池|主機註冊|主機註冊|
|Microsoft.Devices/IotHubs|連接|連接|
|Microsoft.Devices/IotHubs|DeviceTelemetry|裝置遙測|
|Microsoft.Devices/IotHubs|C2DCommands|C2D 命令|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|裝置身分識別作業|
|Microsoft.Devices/IotHubs|FileUploadOperations|檔案上傳作業|
|Microsoft.Devices/IotHubs|路由|路由|
|Microsoft.Devices/IotHubs|D2C 對應項作業|D2C 對應項作業|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D 對應項作業|
|Microsoft.Devices/IotHubs|TwinQueries|對應項查詢|
|Microsoft.Devices/IotHubs|JobsOperations|作業的操作|
|Microsoft.Devices/IotHubs|DirectMethods|直接方法|
|Microsoft.Devices/IotHubs|DistributedTracing|分散式追蹤 (預覽)|
|Microsoft.Devices/IotHubs|組態|組態|
|Microsoft.Devices/IotHubs|設備流|裝置串流(預覽)|
|Microsoft.Devices/provisioningServices|DeviceOperations|裝置作業|
|Microsoft.Devices/provisioningServices|ServiceOperations|服務作業|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|分割區鍵統計資訊|分割區鍵統計資訊|
|Microsoft.DocumentDB/databaseAccounts|控制平面請求|控制平面請求|
|微軟.企業知識圖/服務|AuditEvent|稽核事件記錄|
|微軟.企業知識圖/服務|資料發佈|資料發貨紀錄|
|微軟.企業知識圖/服務|Requests|設定紀錄|
|Microsoft.EventHub/namespaces|ArchiveLogs|封存記錄|
|Microsoft.EventHub/namespaces|OperationalLogs|作業記錄|
|Microsoft.EventHub/namespaces|AutoScaleLogs|自動調整規模記錄|
|Microsoft.EventHub/namespaces|卡夫卡協調員日誌|卡夫卡協調員日誌|
|Microsoft.EventHub/namespaces|卡夫卡使用者錯誤紀錄|卡夫卡使用者錯誤紀錄|
|Microsoft.EventHub/namespaces|事件HubVNet連接事件|VNet/IP 篩選連線紀錄|
|Microsoft.EventHub/namespaces|客戶託管金鑰使用者紀錄|客戶託管金鑰紀錄|
|微軟.醫療保健/服務|AuditLogs|稽核記錄|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|自動調整評估|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|自動調整的調整動作|
|Microsoft.IoTSpaces/Graph|追蹤|追蹤|
|Microsoft.IoTSpaces/Graph|運作|運作|
|Microsoft.IoTSpaces/Graph|稽核|稽核|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|輸入|輸入|
|Microsoft.IoTSpaces/Graph|輸出|輸出|
|Microsoft.KeyVault/vaults|AuditEvent|稽核記錄|
|Microsoft.Kusto/Clusters|成功|成功引入操作|
|Microsoft.Kusto/Clusters|失敗|失敗的引入操作|
|Microsoft.Logic/workflows|WorkflowRuntime|工作流程執行階段診斷事件|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|整合帳戶追蹤事件|
|Microsoft.MachineLearningServices/workspaces|Aml 計算叢事件|Aml 計算叢事件|
|Microsoft.MachineLearningServices/workspaces|Aml 計算叢集節點事件|Aml 計算叢集節點事件|
|Microsoft.MachineLearningServices/workspaces|阿姆計算作業事件|阿姆計算作業事件|
|微軟.媒體/媒體服務|金鑰傳遞要求|金鑰交付要求|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|網路安全性群組事件|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|網路安全性群組規則計數器|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|網路安全性群組規則流程事件|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS 保護通知|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|DDoS 風險降低決策的流程記錄|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|DDoS 風險降低報告|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM 保護警示|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|應用程式閘道存取記錄檔|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|應用程式閘道效能記錄檔|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|應用程式閘道防火牆記錄檔|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Azure 防火牆應用程式規則|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Azure 防火牆網路規則|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|閘道診斷記錄|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|通道診斷記錄|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|路由診斷記錄|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE 診斷記錄|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S 診斷記錄|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|流量管理員探查健康情況結果事件|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|對等互連路由表記錄|
|微軟.網路/vpn閘道|GatewayDiagnosticLog|閘道診斷記錄|
|微軟.網路/vpn閘道|TunnelDiagnosticLog|通道診斷記錄|
|微軟.網路/vpn閘道|RouteDiagnosticLog|路由診斷記錄|
|微軟.網路/vpn閘道|IKEDiagnosticLog|IKE 診斷記錄|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|前門存取記錄|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|前門 Web 應用程式防火牆記錄|
|微軟.網路/p2sVpn閘道|GatewayDiagnosticLog|閘道診斷記錄|
|微軟.網路/p2sVpn閘道|IKEDiagnosticLog|IKE 診斷記錄|
|微軟.網路/p2sVpn閘道|P2SDiagnosticLog|P2S 診斷記錄|
|微軟.網路/堡壘主機|堡壘審核日誌|堡壘審核日誌|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|負載平衡器警示事件|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|負載平衡器探查健全狀況狀態|
|Microsoft.PowerBIDedicated/capacities|引擎|引擎|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure 備份報表資料|
|Microsoft.RecoveryServices/Vaults|核心Azure備份|核心 Azure 備份資料|
|Microsoft.RecoveryServices/Vaults|載入項目 Azure 備份工作|新增 Azure 備份工作資料|
|Microsoft.RecoveryServices/Vaults|載入項 Azure 備份警示|新增 Azure 備份警示資料|
|Microsoft.RecoveryServices/Vaults|附加 Azure 備份原則|新增 Azure 備份原則資料|
|Microsoft.RecoveryServices/Vaults|附加 Azure 備份儲存|附加 Azure 備份儲存資料|
|Microsoft.RecoveryServices/Vaults|新增 Azure 備份保護實體|新增 Azure 備份受保護實體資料|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery 作業|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery 事件|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery 複寫項目|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery 複寫統計資料|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 復原點|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery 複寫資料上傳速率|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery 受保護的磁碟資料變換|
|Microsoft.Search/searchServices|OperationLogs|作業記錄|
|Microsoft.ServiceBus/namespaces|OperationalLogs|作業記錄|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|AutomaticTuning|自動微調|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|查詢存放區執行階段統計資料|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|查詢存放區等候統計資料|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|資料庫等候統計資料|
|Microsoft.Sql/servers/databases|逾時|逾時|
|Microsoft.Sql/servers/databases|區塊|區塊|
|Microsoft.Sql/servers/databases|死結|死結|
|Microsoft.Sql/servers/databases|稽核|稽核記錄|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|SQL 安全性稽核事件|
|Microsoft.Sql/servers/databases|DmsWorkers|Dms 背景工作|
|Microsoft.Sql/servers/databases|ExecRequests|執行要求|
|Microsoft.Sql/servers/databases|RequestSteps|要求步驟|
|Microsoft.Sql/servers/databases|SqlRequests|Sql 要求|
|Microsoft.Sql/servers/databases|等候|等候|
|Microsoft.Sql/managedInstances|ResourceUsageStats|資源使用量統計資料|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|SQL 安全性稽核事件|
|Microsoft.Sql/managedInstances/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|查詢存放區執行階段統計資料|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|查詢存放區等候統計資料|
|Microsoft.Sql/managedInstances/databases|Errors|Errors|
|Microsoft.Storage/storageAccounts/tableServices|儲存讀取|儲存讀取|
|Microsoft.Storage/storageAccounts/tableServices|儲存寫入|儲存寫入|
|Microsoft.Storage/storageAccounts/tableServices|儲存刪除|儲存刪除|
|Microsoft.Storage/storageAccounts/blobServices|儲存讀取|儲存讀取|
|Microsoft.Storage/storageAccounts/blobServices|儲存寫入|儲存寫入|
|Microsoft.Storage/storageAccounts/blobServices|儲存刪除|儲存刪除|
|Microsoft.Storage/storageAccounts/fileServices|儲存讀取|儲存讀取|
|Microsoft.Storage/storageAccounts/fileServices|儲存寫入|儲存寫入|
|Microsoft.Storage/storageAccounts/fileServices|儲存刪除|儲存刪除|
|Microsoft.Storage/storageAccounts/queueServices|儲存讀取|儲存讀取|
|Microsoft.Storage/storageAccounts/queueServices|儲存寫入|儲存寫入|
|Microsoft.Storage/storageAccounts/queueServices|儲存刪除|儲存刪除|
|Microsoft.StreamAnalytics/streamingjobs|執行|執行|
|Microsoft.StreamAnalytics/streamingjobs|編寫|編寫|
|微軟.web/託管環境|套用服務環境平台日誌|套用服務環境平台日誌|
|microsoft.web/sites|功能應用紀錄|函數應用程式記錄|
|microsoft.web/sites|套用服務HTTPLogs|HTTP 紀錄|
|microsoft.web/sites|AppServiceConsoleLogs|套用服務主控台紀錄|
|microsoft.web/sites|AppServiceAppLogs|套用服務應用程式紀錄|
|microsoft.web/sites|套用服務檔案稽核紀錄|網站內容變更稽核紀錄|
|microsoft.web/sites|套用服務稽核紀錄|存取稽核紀錄|
|microsoft.web/sites/slots|功能應用紀錄|函數應用程式記錄|
|microsoft.web/sites/slots|套用服務HTTPLogs|HTTP 紀錄|
|microsoft.web/sites/slots|AppServiceConsoleLogs|主控台紀錄|
|microsoft.web/sites/slots|AppServiceAppLogs|應用程式記錄|
|microsoft.web/sites/slots|套用服務檔案稽核紀錄|網站內容變更稽核紀錄|
|microsoft.web/sites/slots|套用服務稽核紀錄|存取稽核紀錄|

## <a name="next-steps"></a>後續步驟

* [瞭解有關資源紀錄的更多資訊](../../azure-monitor/platform/platform-logs-overview.md)
* [將資源記錄串流式傳輸到**事件中心**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [使用 Azure 監視器 REST API 變更資源紀錄診斷設定](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [使用 Log Analytics 分析來自 Azure 儲存體的記錄](../../azure-monitor/platform/collect-azure-metrics-logs.md)
