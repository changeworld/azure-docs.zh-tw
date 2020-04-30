---
title: Azure 資源記錄支援的服務和架構
description: 瞭解 Azure 資源記錄的支援服務和事件架構。
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 7183c0b268342d08fe7c0ed79c7fa589e3e28afe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128476"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>適用于 Azure 資源記錄的支援服務、架構和類別

> [!NOTE]
> 資源記錄先前稱為診斷記錄。

[Azure 監視器資源記錄](../../azure-monitor/platform/platform-logs-overview.md)是由 Azure 服務發出的記錄，可描述這些服務或資源的操作。 透過 Azure 監視器所提供的所有資源記錄都會共用通用的最上層架構，而且每個服務都可以彈性地為自己的事件發出唯一的屬性。

資源類型 (適用於 `resourceId` 屬性) 與 `category` 的組合可唯一識別結構描述。 本文說明資源記錄的最上層架構，以及每個服務的架構連結。

## <a name="top-level-resource-logs-schema"></a>最上層資源記錄架構

| Name | 必要/選用 | 說明 |
|---|---|---|
| time | 必要 | 事件的時間戳記 (UTC)。 |
| resourceId | 必要 | 發出事件之資源的資源識別碼。 對於租用戶服務，這是 /tenants/tenant-id/providers/provider-name 的格式。 |
| tenantId | 租用戶記錄所需的 | 此事件所繫結 Active Directory 租用戶的租用戶識別碼。 這個屬性只能用於租用戶層級記錄，並不會出現在資源層級記錄中。 |
| operationName | 必要 | 此事件所代表的作業名稱。 如果事件代表 RBAC 作業，則這是 RBAC 作業名稱 (例如 Microsoft.Storage/storageAccounts/BlobServices/Blobs/Read)。 通常以 Resource Manager 作業形式建模，即使它們不是實際記載的 Resource Manager 作業也是一樣 (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | 選擇性 | 與作業建立關聯的 api-version，如果使用 API 執行 operationName (例如 `http://myservice.windowsazure.net/object?api-version=2016-06-01`). 如果沒有對應至此作業的 API，則版本代表該作業的版本，以防與作業建立關聯的屬性在未來變更。 |
| category | 必要 | 事件的記錄類別。 類別是您可在特定資源上啟用或停用記錄的資料粒度。 事件之屬性 Blob 內顯示的屬性，會與特定記錄類別和資源類型內的屬性相同。 一般記錄類別是「Audit」「作業」「執行」和「要求」。 |
| resultType | 選擇性 | 事件的狀態。 一般值包括︰「已啟動」、「進行中」、「成功」、「失敗」、「作用中」和「已解決」。 |
| resultSignature | 選擇性 | 事件的子狀態。 如果此作業對應至 REST API 呼叫，則這是對應 REST 呼叫的 HTTP 狀態碼。 |
| resultDescription | 選擇性 | 此作業的靜態文字描述，例如 「取得儲存體檔案」。 |
| durationMs | 選擇性 | 作業的持續時間 (以毫秒為單位)。 |
| callerIpAddress | 選擇性 | 呼叫端 IP 位址，如果作業對應至來自具有公開可用 IP 位址之實體的 API 呼叫。 |
| correlationId | 選擇性 | 用來將一組相關事件群組在一起的 GUID。 一般而言，如果兩個事件具有相同 operationName 但具有兩個不同狀態 (例如 「已啟動」和「成功」），它們共用相同的相互關聯識別碼。 這也可能代表事件之間的其他關聯性。 |
| 身分識別 | 選擇性 | JSON Blob，描述已執行作業之使用者或應用程式的身分識別。 這通常包括來自 Active Directory 的授權和宣告/JWT 權杖。 |
| 層級 | 選擇性 | 事件的嚴重性層級。 必須是「資訊」、「警告」、「錯誤」或「嚴重」中的其中一個。 |
| location | 選擇性 | 發出事件之資源的區域，例如 「美國東部」或「法國南部」 |
| properties | 選擇性 | 任何與此特定事件類別相關的擴充屬性。 所有自訂/唯一屬性都必須放在架構的此「部分 B」內。 |

## <a name="service-specific-schemas-for-resource-logs"></a>資源記錄的服務特定架構
資源診斷記錄的結構描述會根據資源和記錄類別而有所不同。 這份清單會顯示所有提供可用資源記錄檔的服務，以及可使用的服務和類別特定架構的連結。

| Service | 結構描述與文件 |
| --- | --- |
| Azure Active Directory | [總覽](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)， [Audit 記錄架構和登](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md)[入架構](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API 管理 | [API 管理資源記錄](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| 應用程式閘道 |[應用程式閘道的記錄](../../application-gateway/application-gateway-diagnostics.md) |
| Azure 自動化 |[Azure 自動化的 Log analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 記錄](../../batch/batch-diagnostics.md) |
| 適用於 MySQL 的 Azure 資料庫 | [適用於 MySQL 的 Azure 資料庫診斷記錄](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| 適用於 PostgreSQL 的 Azure 資料庫 | [適用於 PostgreSQL 的 Azure 資料庫記錄](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure 資料總管 | [Azure 資料總管記錄](/azure/data-explorer/using-diagnostic-logs) |
| 認知服務 | [Azure 認知服務的記錄](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Azure Container Registry 的記錄](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| 內容傳遞網路 | [適用于 CDN 的 Azure 記錄](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB 記錄](../../cosmos-db/logging.md) |
| Data Factory | [使用 Azure 監視器監視 Data factory](../../data-factory/monitor-using-azure-monitor.md) |
| 資料湖分析 |[存取 Azure Data Lake Analytics 的記錄](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[存取 Azure Data Lake 存放區的記錄](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 事件中樞 |[Azure 事件中樞記錄](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | 無法使用結構描述。 |
| Azure 防火牆 | 無法使用結構描述。 |
| IoT 中樞 | [IoT 中樞作業](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault 記錄](../../key-vault/general/logging.md) |
| Kubernetes 服務 |[Azure Kubernetes 記錄](../../aks/view-master-logs.md#log-event-schema) |
| 負載平衡器 |[Azure 負載平衡器的記錄檔分析](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B 自訂追蹤結構描述](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 網路安全性群組 |[網路安全性群組 (NSG) 的記錄檔分析](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS 保護 | [管理 Azure DDoS Protection Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI 專用 | [Azure 中的 Power BI Embedded 記錄](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| [復原服務] | [Azure 備份的資料模型](../../backup/backup-azure-reports-data-model.md)|
| 搜尋 |[啟用和使用搜尋流量分析](../../search/search-traffic-analytics.md) |
| 服務匯流排 |[Azure 服務匯流排記錄](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database 記錄](../../sql-database/sql-database-metrics-diag-logging.md) |
| 串流分析 |[作業記錄](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| 流量管理員 | [流量管理員記錄架構](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| 虛擬網路 | 無法使用結構描述。 |
| 虛擬網路閘道 | 無法使用結構描述。 |

## <a name="supported-log-categories-per-resource-type"></a>每個資源類型支援的記錄檔類別

某些類別目錄可能只支援特定類型的資源。 這是以某種形式提供的全部清單。  例如，所有資料庫類型都無法使用 [Microsoft .Sql/伺服器/資料庫] 類別。 如需詳細資訊，請參閱[SQL Database 診斷記錄](../../sql-database/sql-database-metrics-diag-logging.md)的相關資訊。 

|資源類型|類別|類別顯示名稱|
|---|---|---|
|Microsoft AAD/domainServices|SystemSecurity|SystemSecurity|
|Microsoft AAD/domainServices|AccountManagement|AccountManagement|
|Microsoft AAD/domainServices|LogonLogoff|LogonLogoff|
|Microsoft AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft AAD/domainServices|PolicyChange|PolicyChange|
|Microsoft AAD/domainServices|PrivilegeUse|PrivilegeUse|
|Microsoft AAD/domainServices|DetailTracking|DetailTracking|
|Microsoft AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft AAD/domainServices|AccountLogon|AccountLogon|
|microsoft.aadiam/tenants|登入|登入|
|Microsoft.AnalysisServices/servers|引擎|引擎|
|Microsoft.AnalysisServices/servers|Service|Service|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement 閘道的相關記錄|
|AppPlatform/春季|ApplicationConsole|應用程式主控台|
|Microsoft.Automation/automationAccounts|JobLogs|作業記錄|
|Microsoft.Automation/automationAccounts|JobStreams|作業串流|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Dsc 節點狀態|
|Microsoft.Batch/batchAccounts|ServiceLog|服務記錄|
|BatchAI/工作區|BaiClusterEvent|BaiClusterEvent|
|BatchAI/工作區|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BatchAI/工作區|BaiJobEvent|BaiJobEvent|
|區塊鏈/blockchainMembers|BlockchainApplication|區塊鏈應用程式|
|區塊鏈/blockchainMembers|Proxy|Proxy|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|取得端點的計量，例如頻寬、輸出等資訊。|
|Microsoft.ClassicNetwork/networksecuritygroups|網路安全性群組規則流程事件|網路安全性群組規則流程事件|
|Microsoft.CognitiveServices/accounts|稽核|稽核記錄|
|Microsoft.CognitiveServices/accounts|RequestResponse|要求和回應記錄|
|Microsoft.ContainerRegistry/registries|ContainerRegistryRepositoryEvents|RepositoryEvent 記錄（預覽）|
|Microsoft.ContainerRegistry/registries|ContainerRegistryLoginEvents|登入事件（預覽）|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Kubernetes API 伺服器|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Kubernetes 控制器管理員|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Kubernetes 排程器|
|Microsoft.ContainerService/managedClusters|kube-audit|Kubernetes Audit|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Kubernetes 叢集自動調整程式|
|Databricks/工作區|dbfs|Databricks 檔案系統|
|Databricks/工作區|clusters|Databricks 叢集|
|Databricks/工作區|帳戶|Databricks 帳戶|
|Databricks/工作區|jobs|Databricks 作業|
|Databricks/工作區|notebook|Databricks Notebook|
|Databricks/工作區|ssh|Databricks SSH|
|Databricks/工作區|工作區|Databricks 工作區|
|Databricks/工作區|密碼|Databricks 秘密|
|Databricks/工作區|.Sqlpermissions|Databricks .Sqlpermissions|
|Databricks/工作區|instancePools|實例集區|
|Microsoft.datacatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft.DataFactory/factories|ActivityRuns|管線活動執行記錄|
|Microsoft.DataFactory/factories|PipelineRuns|管線執行記錄|
|Microsoft.DataFactory/factories|TriggerRuns|觸發程序執行記錄|
|Microsoft.DataLakeAnalytics/accounts|稽核|稽核記錄|
|Microsoft.DataLakeAnalytics/accounts|Requests|要求記錄|
|Microsoft.DataLakeStore/accounts|稽核|稽核記錄|
|Microsoft.DataLakeStore/accounts|Requests|要求記錄|
|DataShare/accounts|共用|共用|
|DataShare/accounts|ShareSubscriptions|共用訂閱|
|DataShare/accounts|SentShareSnapshots|已傳送共用快照集|
|DataShare/accounts|ReceivedShareSnapshots|已收到共用快照集|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|MySQL 伺服器記錄|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|MySQL Audit 記錄|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL 伺服器記錄|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|于 postgresql 查詢存放區執行時間統計資料|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|于 postgresql 查詢存放區等候統計資料|
|DBforPostgreSQL/serversv2|PostgreSQLLogs|PostgreSQL 伺服器記錄|
|DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|于 postgresql 查詢存放區執行時間統計資料|
|DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|于 postgresql 查詢存放區等候統計資料|
|DesktopVirtualization/工作區|Checkpoint|Checkpoint|
|DesktopVirtualization/工作區|錯誤|錯誤|
|DesktopVirtualization/工作區|管理性|管理性|
|DesktopVirtualization/工作區|摘要|摘要|
|DesktopVirtualization/applicationGroups|Checkpoint|Checkpoint|
|DesktopVirtualization/applicationGroups|錯誤|錯誤|
|DesktopVirtualization/applicationGroups|管理性|管理性|
|DesktopVirtualization/hostPools|Checkpoint|Checkpoint|
|DesktopVirtualization/hostPools|錯誤|錯誤|
|DesktopVirtualization/hostPools|管理性|管理性|
|DesktopVirtualization/hostPools|Connection|Connection|
|DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
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
|Microsoft.Devices/IotHubs|設定|設定|
|Microsoft.Devices/IotHubs|DeviceStreams|裝置串流（預覽）|
|Microsoft.Devices/provisioningServices|DeviceOperations|裝置作業|
|Microsoft.Devices/provisioningServices|ServiceOperations|服務作業|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft.DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|EnterpriseKnowledgeGraph/服務|AuditEvent|AuditEvent 記錄檔|
|EnterpriseKnowledgeGraph/服務|DataIssue|DataIssue 記錄檔|
|EnterpriseKnowledgeGraph/服務|Requests|設定記錄檔|
|Microsoft.EventHub/namespaces|ArchiveLogs|封存記錄|
|Microsoft.EventHub/namespaces|OperationalLogs|作業記錄|
|Microsoft.EventHub/namespaces|AutoScaleLogs|自動調整規模記錄|
|Microsoft.EventHub/namespaces|KafkaCoordinatorLogs|Kafka 協調器記錄檔|
|Microsoft.EventHub/namespaces|KafkaUserErrorLogs|Kafka 使用者錯誤記錄檔|
|Microsoft.EventHub/namespaces|EventHubVNetConnectionEvent|VNet/IP 篩選連接記錄|
|Microsoft.EventHub/namespaces|CustomerManagedKeyUserLogs|客戶管理的金鑰記錄|
|HealthcareApis/服務|AuditLogs|稽核記錄|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|自動調整評估|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|自動調整的調整動作|
|Microsoft.IoTSpaces/Graph|追蹤|追蹤|
|Microsoft.IoTSpaces/Graph|作業|作業|
|Microsoft.IoTSpaces/Graph|稽核|稽核|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|輸入|輸入|
|Microsoft.IoTSpaces/Graph|輸出|輸出|
|Microsoft.KeyVault/vaults|AuditEvent|稽核記錄|
|Microsoft.Kusto/Clusters|SucceededIngestion|成功的內嵌作業|
|Microsoft.Kusto/Clusters|FailedIngestion|內嵌作業失敗|
|Microsoft.Logic/workflows|WorkflowRuntime|工作流程執行階段診斷事件|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|整合帳戶追蹤事件|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft Media/windowsazure.mediaservices.extensions|KeyDeliveryRequests|金鑰傳遞要求|
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
|Microsoft 網路/vpnGateways|GatewayDiagnosticLog|閘道診斷記錄|
|Microsoft 網路/vpnGateways|TunnelDiagnosticLog|通道診斷記錄|
|Microsoft 網路/vpnGateways|RouteDiagnosticLog|路由診斷記錄|
|Microsoft 網路/vpnGateways|IKEDiagnosticLog|IKE 診斷記錄|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|前門存取記錄|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|前門 Web 應用程式防火牆記錄|
|Microsoft 網路/p2sVpnGateways|GatewayDiagnosticLog|閘道診斷記錄|
|Microsoft 網路/p2sVpnGateways|IKEDiagnosticLog|IKE 診斷記錄|
|Microsoft 網路/p2sVpnGateways|P2SDiagnosticLog|P2S 診斷記錄|
|Microsoft 網路/bastionHosts|BastionAuditLogs|防禦審核記錄|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|負載平衡器警示事件|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|負載平衡器探查健全狀況狀態|
|Microsoft.PowerBIDedicated/capacities|引擎|引擎|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure 備份報表資料|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|核心 Azure 備份資料|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupJobs|Azure 備份工作資料的增益集|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlerts|Azure 備份警示資料的增益集|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupPolicy|Azure 備份原則資料的附件|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupStorage|載入項 Azure 備份儲存體資料|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupProtectedInstance|Azure 備份受保護實例資料的附件|
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
|Microsoft.Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft.StreamAnalytics/streamingjobs|執行|執行|
|Microsoft.StreamAnalytics/streamingjobs|編寫|編寫|
|microsoft web/hostingenvironments|AppServiceEnvironmentPlatformLogs|App Service 環境平臺記錄檔|
|microsoft.web/sites|FunctionAppLogs|函數應用程式記錄|
|microsoft.web/sites|AppServiceHTTPLogs|HTTP 記錄|
|microsoft.web/sites|AppServiceConsoleLogs|App Service 主控台記錄檔|
|microsoft.web/sites|AppServiceAppLogs|App Service 應用程式記錄檔|
|microsoft.web/sites|AppServiceFileAuditLogs|網站內容變更審核記錄|
|microsoft.web/sites|AppServiceAuditLogs|存取 Audit 記錄|
|microsoft.web/sites/slots|FunctionAppLogs|函數應用程式記錄|
|microsoft.web/sites/slots|AppServiceHTTPLogs|HTTP 記錄|
|microsoft.web/sites/slots|AppServiceConsoleLogs|主控台記錄檔|
|microsoft.web/sites/slots|AppServiceAppLogs|應用程式記錄|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|網站內容變更審核記錄|
|microsoft.web/sites/slots|AppServiceAuditLogs|存取 Audit 記錄|

## <a name="next-steps"></a>後續步驟

* [深入瞭解資源記錄](../../azure-monitor/platform/platform-logs-overview.md)
* [將資源資源記錄串流至**事件中樞**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [使用 Azure 監視器 REST API 變更資源記錄診斷設定](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [使用 Log Analytics 分析來自 Azure 儲存體的記錄](../../azure-monitor/platform/collect-azure-metrics-logs.md)
