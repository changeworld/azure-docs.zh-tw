---
title: Azure 監視器資源記錄支援的服務與類別
description: 參考 Azure 監視器瞭解 Azure 資源記錄的支援服務和事件架構。
ms.subservice: logs
ms.topic: reference
ms.date: 12/09/2020
ms.openlocfilehash: c7b2d48b40843930bba78f54d2294769d952daf6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931223"
---
# <a name="supported-categories-for-azure-resource-logs"></a>支援的 Azure 資源記錄類別

> [!NOTE]
> 資源記錄之前稱為診斷記錄。 此名稱已于2019年10月變更，因為 Azure 監視器改為包含不只包含 Azure 資源的記錄類型。

[Azure 監視器資源記錄](./platform-logs-overview.md) 是由 Azure 服務發出的記錄，這些服務會描述這些服務或資源的操作。 所有可透過 Azure 監視器共用的資源記錄都會共用通用的最上層架構，而且每個服務都有彈性地發出其本身事件的唯一屬性。

資源類型 (適用於 `resourceId` 屬性) 與 `category` 的組合可唯一識別結構描述。 針對不同的記錄類別，會針對具有服務特定欄位的所有資源記錄，提供一個通用的架構。 如需詳細資訊，請參閱 [適用于 Azure 資源記錄的通用和服務專屬架構]()


## <a name="costs"></a>成本

將任何資料傳送至 Log Analytics、Azure 儲存體和/或事件中樞時，會產生相關費用。 您可以支付將資料放入這些位置並加以保留的成本。  資源記錄是您可以傳送至這些位置的一種資料類型。 將 [某些資源記錄類別匯出](https://azure.microsoft.com/pricing/details/monitor/) 至這些位置會產生額外的費用，而有些則是免費的匯出成本。 下表列出匯出的成本詳細資訊。

## <a name="supported-log-categories-per-resource-type"></a>每個資源類型支援的記錄檔類別

以下是每個資源類型可用的記錄類型清單。 

某些類別可能只支援特定的資源類型。 如果您覺得缺少資源，請參閱資源專屬檔。 例如，並非所有類型的資料庫都可以使用 Microsoft .Sql/servers/資料庫類別。 如需詳細資訊，請參閱 [SQL Database 診斷記錄](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)的相關資訊。 

如果您還沒有東西，可以在本文底部開啟 GitHub 批註。
## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|引擎|引擎|
|服務|服務|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|GatewayLogs|ApiManagement 閘道的相關記錄|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|ApplicationConsole|應用程式主控台|
|SystemLogs|系統記錄檔|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|DscNodeStatus|Dsc 節點狀態|
|JobLogs|作業記錄|
|JobStreams|作業串流|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|ServiceLog|服務記錄|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|BlockchainApplication|區塊鏈應用程式|
|FabricOrderer|網狀架構排序者|
|FabricPeer|網狀架構對等|
|Proxy|Proxy|


## <a name="microsoftblockchaincordamembers"></a>區塊鏈/cordaMembers

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|BlockchainApplication|區塊鏈應用程式|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft Cdn/cdnwebapplicationfirewallpolicies

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|WebApplicationFirewallLogs|Web 應用程式防火牆記錄檔|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|AzureCdnAccessLog|Azure Cdn 存取記錄|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|CoreAnalytics|取得端點的計量，例如頻寬、輸出等資訊。|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|網路安全性群組規則流程事件|網路安全性群組規則流程事件|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|稽核|稽核記錄|
|RequestResponse|要求和回應記錄|
|追蹤|追蹤記錄檔|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|ContainerRegistryLoginEvents|登入事件|
|ContainerRegistryRepositoryEvents|RepositoryEvent 記錄|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|cluster-autoscaler|Kubernetes 叢集自動調整程式|
|kube-apiserver|Kubernetes API 伺服器|
|kube-audit|Kubernetes Audit|
|kube-controller-manager|Kubernetes 控制器管理員|
|kube-scheduler|Kubernetes 排程器|


## <a name="microsoftcustomprovidersresourceproviders"></a>>microsoft.customproviders/resourceproviders

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|AuditLogs|MiniRP 呼叫的 Audit 記錄|


## <a name="microsoftdatabricksworkspaces"></a>Databricks/工作區

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|accounts|Databricks 帳戶|
|clusters|Databricks 叢集|
|dbfs|Databricks 檔案系統|
|instancePools|實例集區|
|jobs|Databricks 作業|
|notebook|Databricks Notebook|
|密碼|Databricks 秘密|
|.Sqlpermissions|Databricks .Sqlpermissions|
|ssh|Databricks SSH|
|工作區|Databricks 工作區|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|ActivityRuns|管線活動執行記錄|
|PipelineRuns|管線執行記錄|
|TriggerRuns|觸發程序執行記錄|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|稽核|稽核記錄|
|Requests|要求記錄|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|ReceivedShareSnapshots|收到的共用快照集|
|SentShareSnapshots|已傳送共用快照集|
|共用|共用|
|ShareSubscriptions|共用訂閱|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|MySqlAuditLogs|適用于 mariadb 審核記錄|
|MySqlSlowLogs|適用于 mariadb 伺服器記錄檔|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|MySqlAuditLogs|MySQL 審核記錄|
|MySqlSlowLogs|MySQL 慢速記錄|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|MySqlAuditLogs|MySQL 審核記錄|
|MySqlSlowLogs|MySQL 伺服器記錄|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|PostgreSQLLogs|PostgreSQL 伺服器記錄|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|PostgreSQLLogs|PostgreSQL 伺服器記錄|
|QueryStoreRuntimeStatistics|于 postgresql 查詢存放區執行時間統計資料|
|QueryStoreWaitStatistics|于 postgresql 查詢存放區等候統計資料|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|PostgreSQLLogs|PostgreSQL 伺服器記錄|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>DesktopVirtualization/applicationgroups

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|Checkpoint|Checkpoint|
|錯誤|錯誤|
|管理性|管理性|


## <a name="microsoftdesktopvirtualizationhostpools"></a>DesktopVirtualization/hostpools

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|Checkpoint|Checkpoint|
|連線|連線|
|錯誤|錯誤|
|HostRegistration|HostRegistration|
|管理性|管理性|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>DesktopVirtualization/工作區

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|Checkpoint|Checkpoint|
|錯誤|錯誤|
|摘要|摘要|
|管理性|管理性|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|C2DCommands|C2D 命令|
|C2DTwinOperations|C2D 對應項作業|
|組態|組態|
|連接|連接|
|D2C 對應項作業|D2C 對應項作業|
|DeviceIdentityOperations|裝置身分識別作業|
|DeviceStreams|裝置串流 (預覽) |
|DeviceTelemetry|裝置遙測|
|DirectMethods|直接方法|
|DistributedTracing|分散式追蹤 (預覽)|
|FileUploadOperations|檔案上傳作業|
|JobsOperations|作業的操作|
|路由|路由|
|TwinQueries|對應項查詢|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|DeviceOperations|裝置作業|
|ServiceOperations|服務作業|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|GremlinRequests|GremlinRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|DeliveryFailures|傳遞失敗記錄|
|PublishFailures|發行失敗記錄|


## <a name="microsofteventgridsystemtopics"></a>EventGrid/systemTopics

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|DeliveryFailures|傳遞失敗記錄|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|DeliveryFailures|傳遞失敗記錄|
|PublishFailures|發行失敗記錄|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|ArchiveLogs|封存記錄|
|AutoScaleLogs|自動調整規模記錄|
|CustomerManagedKeyUserLogs|客戶管理的金鑰記錄|
|EventHubVNetConnectionEvent|VNet/IP 篩選連接記錄|
|KafkaCoordinatorLogs|Kafka 協調器記錄|
|KafkaUserErrorLogs|Kafka 使用者錯誤記錄檔|
|OperationalLogs|作業記錄|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|AuditLogs|稽核記錄|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|AutoscaleEvaluations|自動調整評估|
|AutoscaleScaleActions|自動調整的調整動作|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|AppAvailabilityResults|可用性結果|
|AppBrowserTimings|瀏覽器計時|
|AppDependencies|相依性|
|AppEvents|事件|
|AppExceptions|例外狀況|
|AppMetrics|計量|
|AppPageViews|網頁檢視|
|AppPerformanceCounters|效能計數器|
|AppRequests|Requests|
|AppSystemEvents|系統事件|
|AppTraces|追蹤|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|AuditEvent|稽核記錄|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|命令|命令|
|FailedIngestion|內嵌作業失敗|
|IngestionBatching|內嵌批次處理|
|查詢|查詢|
|SucceededIngestion|成功的內嵌作業|
|TableDetails|資料表詳細資料|
|TableUsageStatistics|資料表使用統計資料|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|IntegrationAccountTrackingEvents|整合帳戶追蹤事件|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|WorkflowRuntime|工作流程執行階段診斷事件|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|KeyDeliveryRequests|金鑰傳遞要求|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|ApplicationGatewayAccessLog|應用程式閘道存取記錄檔|
|ApplicationGatewayFirewallLog|應用程式閘道防火牆記錄檔|
|ApplicationGatewayPerformanceLog|應用程式閘道效能記錄檔|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|AzureFirewallApplicationRule|Azure 防火牆應用程式規則|
|AzureFirewallNetworkRule|Azure 防火牆網路規則|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|BastionAuditLogs|防禦審核記錄|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|PeeringRouteLog|對等互連路由表記錄|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|FrontdoorAccessLog|前門存取記錄|
|FrontdoorWebApplicationFirewallLog|前門 Web 應用程式防火牆記錄|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|LoadBalancerAlertEvent|負載平衡器警示事件|
|LoadBalancerProbeHealthStatus|負載平衡器探查健全狀況狀態|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|NetworkSecurityGroupEvent|網路安全性群組事件|
|NetworkSecurityGroupFlowEvent|網路安全性群組規則流程事件|
|NetworkSecurityGroupRuleCounter|網路安全性群組規則計數器|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|DDoSMitigationFlowLogs|DDoS 風險降低決策的流程記錄|
|DDoSMitigationReports|DDoS 風險降低報告|
|DDoSProtectionNotifications|DDoS 保護通知|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|ProbeHealthStatusEvents|流量管理員探查健康情況結果事件|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|GatewayDiagnosticLog|閘道診斷記錄|
|IKEDiagnosticLog|IKE 診斷記錄|
|P2SDiagnosticLog|P2S 診斷記錄|
|RouteDiagnosticLog|路由診斷記錄|
|TunnelDiagnosticLog|通道診斷記錄|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|VMProtectionAlerts|VM 保護警示|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|引擎|引擎|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|AddonAzureBackupAlerts|增益集 Azure 備份警示資料|
|AddonAzureBackupJobs|作業資料的附加元件 Azure 備份|
|AddonAzureBackupPolicy|附加元件 Azure 備份原則資料|
|AddonAzureBackupProtectedInstance|附加元件 Azure 備份受保護的實例資料|
|AddonAzureBackupStorage|Azure 備份儲存體資料的增益集|
|AzureBackupReport|Azure 備份報表資料|
|AzureSiteRecoveryEvents|Azure Site Recovery 事件|
|AzureSiteRecoveryJobs|Azure Site Recovery 作業|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery 受保護的磁碟資料變換|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 復原點|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery 複寫項目|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery 複寫資料上傳速率|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery 複寫統計資料|
|CoreAzureBackup|核心 Azure 備份資料|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|HybridConnectionsEvent|HybridConnections 事件|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|OperationLogs|作業記錄|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|OperationalLogs|作業記錄|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|Alllogs.csv|Azure SignalR Service 記錄。|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|DevOpsOperationsAudit|Devops 作業審核記錄|
|ResourceUsageStats|資源使用量統計資料|
|SQLSecurityAuditEvents|SQL 安全性稽核事件|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|Errors|Errors|
|QueryStoreRuntimeStatistics|查詢存放區執行階段統計資料|
|QueryStoreWaitStatistics|查詢存放區等候統計資料|
|SQLInsights|SQL Insights|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|AutomaticTuning|自動微調|
|區塊|區塊|
|DatabaseWaitStatistics|資料庫等候統計資料|
|死結|死結|
|DevOpsOperationsAudit|Devops 作業審核記錄|
|DmsWorkers|Dms 背景工作|
|Errors|Errors|
|ExecRequests|執行要求|
|QueryStoreRuntimeStatistics|查詢存放區執行階段統計資料|
|QueryStoreWaitStatistics|查詢存放區等候統計資料|
|RequestSteps|要求步驟|
|SQLInsights|SQL Insights|
|SqlRequests|Sql 要求|
|SQLSecurityAuditEvents|SQL 安全性稽核事件|
|逾時|逾時|
|等候|等候|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

成本：依[Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)的 [平臺記錄] 區段中所述付費。 

|類別 |類別顯示名稱|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

成本：依[Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)的 [平臺記錄] 區段中所述付費。 

|類別 |類別顯示名稱|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

成本：依[Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)的 [平臺記錄] 區段中所述付費。 
 
|類別 |類別顯示名稱|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

成本：依[Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)的 [平臺記錄] 區段中所述付費。 
 
|類別 |類別顯示名稱|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|編寫|編寫|
|執行|執行|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|BuiltinSqlReqsEnded|內建的 Sql 集區要求已結束|
|GatewayApiRequests|Synapse 閘道 Api 要求|
|SQLSecurityAuditEvents|SQL 安全性稽核事件|
|SynapseRbacOperations|Synapse RBAC 作業|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Synapse/workspace/bigDataPools

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|BigDataPoolAppsEnded|大型資料集區應用程式已結束|


## <a name="microsoftsynapseworkspacessqlpools"></a>Synapse/workspace/sqlPools

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|DmsWorkers|Dms 背景工作|
|ExecRequests|執行要求|
|RequestSteps|要求步驟|
|SqlRequests|Sql 要求|
|SQLSecurityAuditEvents|Sql 安全性審核事件|
|等候|等候|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

成本：免費 

|類別 |類別顯示名稱|
|---|---|
|AppServiceEnvironmentPlatformLogs|App Service 環境平臺記錄|


## <a name="microsoftwebsites"></a>microsoft.web/sites

成本：免費 


|類別 |類別顯示名稱|
|---|---|
|AppServiceAppLogs|App Service 應用程式記錄檔|
|AppServiceAuditLogs|存取 Audit 記錄|
|AppServiceConsoleLogs|App Service 主控台記錄|
|AppServiceFileAuditLogs|網站內容變更審核記錄|
|AppServiceHTTPLogs|HTTP 記錄|
|FunctionAppLogs|函數應用程式記錄檔|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

成本：免費 


|類別 |類別顯示名稱|
|---|---|
|AppServiceAppLogs|App Service 應用程式記錄檔|
|AppServiceAuditLogs|存取 Audit 記錄|
|AppServiceConsoleLogs|App Service 主控台記錄|
|AppServiceFileAuditLogs|網站內容變更審核記錄|
|AppServiceHTTPLogs|HTTP 記錄|
|FunctionAppLogs|函數應用程式記錄檔|


## <a name="next-steps"></a>後續步驟

* [深入瞭解資源記錄](./platform-logs-overview.md)
* [將資源資源記錄串流至 **事件中樞**](./resource-logs.md#send-to-azure-event-hubs)
* [使用 Azure 監視器 REST API 變更資源記錄診斷設定](/rest/api/monitor/diagnosticsettings)
* [使用 Log Analytics 分析來自 Azure 儲存體的記錄](./resource-logs.md#send-to-log-analytics-workspace)

