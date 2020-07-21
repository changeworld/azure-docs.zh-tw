---
title: Azure 監視器資源記錄支援的服務和類別
description: Azure 監視器的參考瞭解 Azure 資源記錄的支援服務和事件架構。
ms.subservice: logs
ms.topic: reference
ms.date: 06/03/2020
ms.openlocfilehash: ceca6b0a230de6d3eeab47e60d90767b33b5ac59
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515388"
---
# <a name="supported-categories-for-azure-resource-logs"></a>支援的 Azure 資源記錄類別

> [!NOTE]
> 資源記錄先前稱為診斷記錄。 名稱在2019年10月已變更，因為所收集的記錄類型 Azure 監視器轉移，而不只包含 Azure 資源。

[Azure 監視器資源記錄](../../azure-monitor/platform/platform-logs-overview.md)是由 Azure 服務發出的記錄，可描述這些服務或資源的操作。 透過 Azure 監視器所提供的所有資源記錄都會共用通用的最上層架構，而且每個服務都可以彈性地為自己的事件發出唯一的屬性。

資源類型 (適用於 `resourceId` 屬性) 與 `category` 的組合可唯一識別結構描述。 針對具有服務特定欄位的所有資源記錄，會有一個通用架構，然後針對不同的記錄類別新增。 如需詳細資訊，請參閱[適用于 Azure 資源記錄的通用和服務特定架構](resource-logs-categories.md)

## <a name="supported-log-categories-per-resource-type"></a>每個資源類型支援的記錄檔類別

以下是每個資源類型可用的記錄類型清單。 

某些類別目錄可能只支援特定類型的資源。 如果您覺得遺漏資源，請參閱資源特定的檔。 例如，所有資料庫類型都無法使用 [Microsoft .Sql/伺服器/資料庫] 類別。 如需詳細資訊，請參閱[SQL Database 診斷記錄](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)的相關資訊。 

如果您仍然遺失某個專案，可以在本文底部開啟 GitHub 留言。

## <a name="microsoftaadiamtenants"></a>microsoft.aadiam/tenants

|類別|類別顯示名稱|
|---|---|
|登入|登入|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|類別|類別顯示名稱|
|---|---|
|引擎|引擎|
|服務|服務|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|類別|類別顯示名稱|
|---|---|
|GatewayLogs|ApiManagement 閘道的相關記錄|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|類別|類別顯示名稱|
|---|---|
|ApplicationConsole|應用程式主控台|
|SystemLogs|系統記錄檔|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|類別|類別顯示名稱|
|---|---|
|JobLogs|作業記錄|
|JobStreams|作業串流|
|DscNodeStatus|Dsc 節點狀態|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|類別|類別顯示名稱|
|---|---|
|ServiceLog|服務記錄|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|類別|類別顯示名稱|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|類別|類別顯示名稱|
|---|---|
|BlockchainApplication|區塊鏈應用程式|
|Proxy|Proxy|


## <a name="microsoftblockchaincordamembers"></a>區塊鏈/cordaMembers

|類別|類別顯示名稱|
|---|---|
|BlockchainApplication|區塊鏈應用程式|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft Cdn/cdnwebapplicationfirewallpolicies

|類別|類別顯示名稱|
|---|---|
|WebApplicationFirewallLogs|Web 應用程式防火牆記錄|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|類別|類別顯示名稱|
|---|---|
|AzureCdnAccessLog|Azure Cdn 存取記錄|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

|類別|類別顯示名稱|
|---|---|
|CoreAnalytics|取得端點的計量，例如頻寬、輸出等。|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

|類別|類別顯示名稱|
|---|---|
|網路安全性群組規則流程事件|網路安全性群組規則流程事件|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|類別|類別顯示名稱|
|---|---|
|稽核|稽核記錄|
|RequestResponse|要求和回應記錄|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|類別|類別顯示名稱|
|---|---|
|ContainerRegistryLoginEvents|登入事件|
|ContainerRegistryRepositoryEvents|RepositoryEvent 記錄|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|類別|類別顯示名稱|
|---|---|
|cluster-autoscaler|Kubernetes 叢集自動調整程式|
|kube-apiserver|Kubernetes API 伺服器|
|kube-audit|Kubernetes Audit|
|kube-controller-manager|Kubernetes 控制器管理員|
|kube-scheduler|Kubernetes 排程器|


## <a name="microsoftcustomprovidersresourceproviders"></a>CustomProviders/resourceproviders

|類別|類別顯示名稱|
|---|---|
|AuditLogs|MiniRP 呼叫的 Audit 記錄|


## <a name="microsoftdatabricksworkspaces"></a>Databricks/工作區

|類別|類別顯示名稱|
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


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.datacatalog/datacatalogs

|類別|類別顯示名稱|
|---|---|
|ScanStatusLogEvent|ScanStatus|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|類別|類別顯示名稱|
|---|---|
|ActivityRuns|管線活動執行記錄|
|PipelineRuns|管線執行記錄|
|TriggerRuns|觸發程序執行記錄|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|類別|類別顯示名稱|
|---|---|
|稽核|稽核記錄|
|Requests|要求記錄|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|類別|類別顯示名稱|
|---|---|
|MySqlAuditLogs|適用于 mariadb Audit 記錄檔|
|MySqlSlowLogs|適用于 mariadb 伺服器記錄檔|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|類別|類別顯示名稱|
|---|---|
|MySqlAuditLogs|MySQL Audit 記錄|
|MySqlSlowLogs|MySQL 伺服器記錄|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|類別|類別顯示名稱|
|---|---|
|PostgreSQLLogs|PostgreSQL 伺服器記錄|
|QueryStoreRuntimeStatistics|于 postgresql 查詢存放區執行時間統計資料|
|QueryStoreWaitStatistics|于 postgresql 查詢存放區等候統計資料|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|類別|類別顯示名稱|
|---|---|
|PostgreSQLLogs|PostgreSQL 伺服器記錄|


## <a name="microsoftdbforpostgresqlsingleservers"></a>DBforPostgreSQL/singleservers

|類別|類別顯示名稱|
|---|---|
|PostgreSQLLogs|PostgreSQL 伺服器記錄|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>DesktopVirtualization/applicationgroups

|類別|類別顯示名稱|
|---|---|
|Checkpoint|Checkpoint|
|錯誤|錯誤|
|管理性|管理性|


## <a name="microsoftdesktopvirtualizationhostpools"></a>DesktopVirtualization/hostpools

|類別|類別顯示名稱|
|---|---|
|Checkpoint|Checkpoint|
|連線|連線|
|錯誤|錯誤|
|HostRegistration|HostRegistration|
|管理性|管理性|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>DesktopVirtualization/工作區

|類別|類別顯示名稱|
|---|---|
|Checkpoint|Checkpoint|
|錯誤|錯誤|
|摘要|摘要|
|管理性|管理性|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|類別|類別顯示名稱|
|---|---|
|C2DCommands|C2D 命令|
|C2DTwinOperations|C2D 對應項作業|
|組態|組態|
|連接|連接|
|D2C 對應項作業|D2C 對應項作業|
|DeviceIdentityOperations|裝置身分識別作業|
|DeviceStreams|裝置串流（預覽）|
|DeviceTelemetry|裝置遙測|
|DirectMethods|直接方法|
|DistributedTracing|分散式追蹤 (預覽)|
|FileUploadOperations|檔案上傳作業|
|JobsOperations|作業的操作|
|路由|路由|
|TwinQueries|對應項查詢|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|類別|類別顯示名稱|
|---|---|
|DeviceOperations|裝置作業|
|ServiceOperations|服務作業|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|類別|類別顯示名稱|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|類別|類別顯示名稱|
|---|---|
|AuditEvent|AuditEvent 記錄檔|
|DataIssue|DataIssue 記錄檔|
|Requests|設定記錄檔|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|類別|類別顯示名稱|
|---|---|
|DeliveryFailures|傳遞失敗記錄|
|PublishFailures|發行失敗記錄|


## <a name="microsofteventgridsystemtopics"></a>EventGrid/systemTopics

|類別|類別顯示名稱|
|---|---|
|DeliveryFailures|傳遞失敗記錄|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|類別|類別顯示名稱|
|---|---|
|DeliveryFailures|傳遞失敗記錄|
|PublishFailures|發行失敗記錄|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|類別|類別顯示名稱|
|---|---|
|ArchiveLogs|封存記錄|
|AutoScaleLogs|自動調整規模記錄|
|CustomerManagedKeyUserLogs|客戶管理的金鑰記錄|
|EventHubVNetConnectionEvent|VNet/IP 篩選連接記錄|
|KafkaCoordinatorLogs|Kafka 協調器記錄檔|
|KafkaUserErrorLogs|Kafka 使用者錯誤記錄檔|
|OperationalLogs|作業記錄|


## <a name="microsofthealthcareapisservices"></a>HealthcareApis/服務

|類別|類別顯示名稱|
|---|---|
|AuditLogs|稽核記錄|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|類別|類別顯示名稱|
|---|---|
|AutoscaleEvaluations|自動調整評估|
|AutoscaleScaleActions|自動調整的調整動作|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|類別|類別顯示名稱|
|---|---|
|AppAvailabilityResults|可用性結果|
|AppBrowserTimings|瀏覽器時間|
|AppDependencies|相依性|
|AppEvents|事件|
|AppExceptions|例外狀況|
|AppMetrics|計量|
|AppPageViews|網頁檢視|
|AppPerformanceCounters|效能計數器|
|AppRequests|Requests|
|AppSystemEvents|系統事件|
|AppTraces|追蹤|


## <a name="microsoftiotspacesgraph"></a>Microsoft.IoTSpaces/Graph

|類別|類別顯示名稱|
|---|---|
|稽核|稽核|
|輸出|輸出|
|輸入|輸入|
|運作|運作|
|追蹤|追蹤|
|UserDefinedFunction|UserDefinedFunction|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|類別|類別顯示名稱|
|---|---|
|AuditEvent|稽核記錄|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|類別|類別顯示名稱|
|---|---|
|FailedIngestion|內嵌作業失敗|
|SucceededIngestion|成功的內嵌作業|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

|類別|類別顯示名稱|
|---|---|
|IntegrationAccountTrackingEvents|整合帳戶追蹤事件|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|類別|類別顯示名稱|
|---|---|
|WorkflowRuntime|工作流程執行階段診斷事件|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|類別|類別顯示名稱|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|類別|類別顯示名稱|
|---|---|
|KeyDeliveryRequests|金鑰傳遞要求|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|類別|類別顯示名稱|
|---|---|
|ApplicationGatewayAccessLog|應用程式閘道存取記錄檔|
|ApplicationGatewayFirewallLog|應用程式閘道防火牆記錄檔|
|ApplicationGatewayPerformanceLog|應用程式閘道效能記錄檔|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|類別|類別顯示名稱|
|---|---|
|AzureFirewallApplicationRule|Azure 防火牆應用程式規則|
|AzureFirewallNetworkRule|Azure 防火牆網路規則|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft 網路/bastionHosts

|類別|類別顯示名稱|
|---|---|
|BastionAuditLogs|防禦審核記錄|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|類別|類別顯示名稱|
|---|---|
|PeeringRouteLog|對等互連路由表記錄|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|類別|類別顯示名稱|
|---|---|
|FrontdoorAccessLog|前門存取記錄|
|FrontdoorWebApplicationFirewallLog|前門 Web 應用程式防火牆記錄|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|類別|類別顯示名稱|
|---|---|
|LoadBalancerAlertEvent|負載平衡器警示事件|
|LoadBalancerProbeHealthStatus|負載平衡器探查健全狀況狀態|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|類別|類別顯示名稱|
|---|---|
|NetworkSecurityGroupEvent|網路安全性群組事件|
|NetworkSecurityGroupFlowEvent|網路安全性群組規則流程事件|
|NetworkSecurityGroupRuleCounter|網路安全性群組規則計數器|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|類別|類別顯示名稱|
|---|---|
|DDoSMitigationFlowLogs|DDoS 風險降低決策的流程記錄|
|DDoSMitigationReports|DDoS 風險降低報告|
|DDoSProtectionNotifications|DDoS 保護通知|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|類別|類別顯示名稱|
|---|---|
|ProbeHealthStatusEvents|流量管理員探查健康情況結果事件|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|類別|類別顯示名稱|
|---|---|
|GatewayDiagnosticLog|閘道診斷記錄|
|IKEDiagnosticLog|IKE 診斷記錄|
|P2SDiagnosticLog|P2S 診斷記錄|
|RouteDiagnosticLog|路由診斷記錄|
|TunnelDiagnosticLog|通道診斷記錄|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|類別|類別顯示名稱|
|---|---|
|VMProtectionAlerts|VM 保護警示|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|類別|類別顯示名稱|
|---|---|
|引擎|引擎|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|類別|類別顯示名稱|
|---|---|
|AddonAzureBackupAlerts|Azure 備份警示資料的增益集|
|AddonAzureBackupJobs|Azure 備份工作資料的增益集|
|AddonAzureBackupPolicy|Azure 備份原則資料的附件|
|AddonAzureBackupProtectedInstance|Azure 備份受保護實例資料的附件|
|AddonAzureBackupStorage|載入項 Azure 備份儲存體資料|
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

|類別|類別顯示名稱|
|---|---|
|HybridConnectionsEvent|HybridConnections 事件|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|類別|類別顯示名稱|
|---|---|
|OperationLogs|作業記錄|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|類別|類別顯示名稱|
|---|---|
|OperationalLogs|作業記錄|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|類別|類別顯示名稱|
|---|---|
|Alllogs.csv|Azure SignalR Service 記錄檔。|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|類別|類別顯示名稱|
|---|---|
|DevOpsOperationsAudit|Devops 作業審核記錄|
|ResourceUsageStats|資源使用量統計資料|
|SQLSecurityAuditEvents|SQL 安全性稽核事件|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|類別|類別顯示名稱|
|---|---|
|錯誤|錯誤|
|QueryStoreRuntimeStatistics|查詢存放區執行階段統計資料|
|QueryStoreWaitStatistics|查詢存放區等候統計資料|
|SQLInsights|SQL Insights|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|類別|類別顯示名稱|
|---|---|
|AutomaticTuning|自動微調|
|區塊|區塊|
|DatabaseWaitStatistics|資料庫等候統計資料|
|死結|死結|
|DevOpsOperationsAudit|Devops 作業審核記錄|
|DmsWorkers|Dms 背景工作|
|錯誤|錯誤|
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

|類別|類別顯示名稱|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|類別|類別顯示名稱|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|類別|類別顯示名稱|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|類別|類別顯示名稱|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|類別|類別顯示名稱|
|---|---|
|編寫|編寫|
|執行|執行|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

|類別|類別顯示名稱|
|---|---|
|AppServiceEnvironmentPlatformLogs|App Service 環境平臺記錄檔|


## <a name="microsoftwebsites"></a>microsoft.web/sites

|類別|類別顯示名稱|
|---|---|
|AppServiceAppLogs|App Service 應用程式記錄檔|
|AppServiceAuditLogs|存取 Audit 記錄|
|AppServiceConsoleLogs|App Service 主控台記錄檔|
|AppServiceFileAuditLogs|網站內容變更審核記錄|
|AppServiceHTTPLogs|HTTP 記錄|
|FunctionAppLogs|函數應用程式記錄|
|ScanLogs|防毒軟體掃描記錄|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots


|類別|類別顯示名稱|
|---|---|
|AppServiceAppLogs|App Service 應用程式記錄檔|
|AppServiceAuditLogs|存取 Audit 記錄|
|AppServiceConsoleLogs|App Service 主控台記錄檔|
|AppServiceFileAuditLogs|網站內容變更審核記錄|
|AppServiceHTTPLogs|HTTP 記錄|
|FunctionAppLogs|函數應用程式記錄|
|ScanLogs|防毒軟體掃描記錄|


## <a name="next-steps"></a>後續步驟

* [深入瞭解資源記錄](../../azure-monitor/platform/platform-logs-overview.md)
* [將資源資源記錄串流至**事件中樞**](./resource-logs.md#send-to-azure-event-hubs)
* [使用 Azure 監視器 REST API 變更資源記錄診斷設定](/rest/api/monitor/diagnosticsettings)
* [使用 Log Analytics 分析來自 Azure 儲存體的記錄](./resource-logs.md#send-to-log-analytics-workspace)
