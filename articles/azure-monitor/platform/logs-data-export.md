---
title: 'Azure 監視器 (預覽中的 Log Analytics 工作區資料匯出) '
description: Log Analytics 資料匯出可讓您將所選資料表的資料從 Log Analytics 工作區持續匯出至 Azure 儲存體帳戶，或在收集時 Azure 事件中樞。
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 10/14/2020
ms.openlocfilehash: 54d5fdf1f6bc905482186475302901c46de0d285
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380121"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Azure 監視器 (預覽中的 Log Analytics 工作區資料匯出) 
Azure 監視器中的 Log Analytics 工作區資料匯出可讓您從 Log Analytics 工作區中選取的資料表持續將資料匯出到 Azure 儲存體帳戶，或在收集時 Azure 事件中樞。 本文提供這項功能的詳細資料，以及在工作區中設定資料匯出的步驟。

## <a name="overview"></a>概觀
針對您的 Log Analytics 工作區設定資料匯出後，任何傳送至工作區中所選資料表的新資料都會以近乎即時的方式自動匯出到您的儲存體帳戶，或您的事件中樞。

![資料匯出總覽](media/logs-data-export/data-export-overview.png)

包含的資料表中的所有資料都會在沒有篩選的情況下匯出。 例如，當您設定 *SecurityEvent* 資料表的資料匯出規則時，傳送至 *SecurityEvent* 資料表的所有資料都會從設定時間開始匯出。


## <a name="other-export-options"></a>其他匯出選項
Log Analytics 工作區資料匯出會持續從 Log Analytics 工作區匯出資料。 針對特定案例匯出資料的其他選項包括下列各項：

- 使用邏輯應用程式從記錄查詢排程匯出。 這類似于資料匯出功能，但可讓您將已篩選或匯總的資料傳送至 Azure 儲存體。 不過，此方法受限於 [記錄查詢限制](../service-limits.md#log-analytics-workspaces)  ，請參閱 [使用邏輯應用程式將資料從 log Analytics 工作區封存至 Azure 儲存體](logs-export-logic-app.md)。
- 使用邏輯應用程式進行一次匯出。 請參閱 [Logic Apps 和 Power Automate 的 Azure 監視器記錄連接器](logicapp-flow-connector.md)。
- 使用 PowerShell 腳本一次匯出到本機電腦。 請參閱 [AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)。


## <a name="current-limitations"></a>目前的限制

- 目前只能使用 CLI 或 REST 要求來執行設定。 您無法使用 Azure 入口網站或 PowerShell。
- ```--export-all-tables```CLI 和 REST 中的選項不受支援，將會移除。 您應該明確地提供匯出規則中的資料表清單。
- 支援的資料表目前僅限於以下支援的 [資料表](#supported-tables) 區段中的特定資料表。 如果資料匯出規則包含不支援的資料表，作業將會成功，但不會針對該資料表匯出任何資料。 如果資料匯出規則包含不存在的資料表，它將會失敗並出現錯誤 ```Table <tableName> does not exist in the workspace.```
- 您的 Log Analytics 工作區可以位於下列任何區域中：
  - 瑞士北部
  - 瑞士西部
  - Azure 政府區域
- 目的地儲存體帳戶或事件中樞必須位於與 Log Analytics 工作區相同的區域中。
- 儲存體帳戶的資料表名稱不能超過60個字元，且事件中樞不可超過47個字元。 系統將不會匯出名稱較長的資料表。

> [!NOTE]
> Log Analytics 資料匯出會將資料寫入為附加 blob，其目前為 Azure Data Lake Storage Gen2 的預覽狀態。 您必須在設定匯出至此儲存體之前，先開啟支援要求。 針對此要求，請使用下列詳細資料。
> - 問題類型：技術
> - 訂用帳戶：您的訂用帳戶
> - 服務： Data Lake Storage Gen2
> - 資源：您的資源名稱
> - 摘要：要求訂用帳戶註冊以接受來自 Log Analytics 資料匯出的資料。
> - 問題類型：連線能力
> - 問題子類型：連線能力問題

## <a name="data-completeness"></a>資料完整性
當目的地無法使用時，資料匯出會繼續重試傳送資料最多30分鐘。 如果在30分鐘後仍無法使用，則會捨棄資料，直到目的地變成可用為止。

## <a name="cost"></a>成本
資料匯出功能目前沒有額外的費用。 未來將會宣佈資料匯出的定價，以及開始計費之前所提供的通知。 如果您選擇在通知期間之後繼續使用資料匯出，將會以適用的費率向您收費。

## <a name="export-destinations"></a>匯出目的地

### <a name="storage-account"></a>儲存體帳戶
每小時都會將資料傳送至儲存體帳戶。 資料匯出設定會為儲存體帳戶中的每個資料表建立一個容器，並 *在後面加* 上該資料表的名稱。 例如，資料表 *SecurityEvent* 會傳送至名為 *am-SecurityEvent* 的容器。

儲存體帳戶 blob 路徑為 *WorkspaceResourceId =/subscriptions/subscription-id/resourcegroups/ \<resource-group\> /providers/microsoft.operationalinsights/workspaces/ \<workspace\> /y =/m =/d =/h = \<four-digit numeric year\> \<two-digit numeric month\> \<two-digit numeric day\> \<two-digit 24-hour clock hour\> 00/PT1H.js開啟* 。 因為附加 blob 僅限於儲存體中的50K 寫入，所以如果附加的數目很高，匯出的 blob 數目可能會擴充。 在這種情況下，blob 的命名模式會是 PT1H_ #，其中 # 是增量 blob 計數。

儲存體帳戶資料格式為 [JSON 行](diagnostic-logs-append-blobs.md)。 這表示每筆記錄都是以一個新行分隔，而且沒有外部記錄陣列，而 JSON 記錄之間沒有逗號。 

[![儲存體範例資料](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

當以時間為基礎的保留原則啟用 *allowProtectedAppendWrites* 設定時，Log Analytics 資料匯出可以將附加 blob 寫入不可變的儲存體帳戶。 這可讓您將新的區塊寫入附加 blob，同時保有永久性的保護和合規性。 請參閱 [允許受保護的附加 blob 寫入](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes)。

### <a name="event-hub"></a>事件中樞
資料會在接近 Azure 監視器時，以近乎即時的方式傳送至您的事件中樞。 系統會為您匯出的每個資料類型建立事件中樞，並 *在後面加上資料表名稱。* 例如，資料表 *SecurityEvent* 會傳送至名為 *SecurityEvent* 的事件中樞。 如果您想要匯出的資料到達特定的事件中樞，或資料表的名稱超過47個字元的限制，您可以提供自己的事件中樞名稱，並將已定義資料表的所有資料匯出至該名稱。

匯出的資料量通常會隨著時間增加，而且必須增加事件中樞規模以處理較大的傳輸速率，並避免節流案例和資料延遲。 您應該使用事件中樞的自動擴充功能，自動擴大並增加輸送量單位的數目，並符合使用量需求。 如需詳細資料，請參閱 [自動擴大 Azure 事件中樞輸送量單位](../../event-hubs/event-hubs-auto-inflate.md) 。


## <a name="prerequisites"></a>Prerequisites
以下是在設定 Log Analytics 資料匯出之前必須完成的必要條件。

- 儲存體帳戶和事件中樞必須已建立，且必須與 Log Analytics 工作區位於相同的區域。 如果您需要將資料複寫至其他儲存體帳戶，您可以使用任何 [Azure 儲存體的冗余選項](../../storage/common/storage-redundancy.md)。  
- 儲存體帳戶必須是 StorageV1 或 StorageV2。 不支援傳統儲存體  
- 如果您已將儲存體帳戶設定為允許從選取的網路進行存取，您必須在儲存體帳戶設定中新增例外狀況，以允許 Azure 監視器寫入您的儲存體。

## <a name="enable-data-export"></a>啟用資料匯出
您必須執行下列步驟，以啟用 Log Analytics 資料匯出。 如需各項的詳細資訊，請參閱下列各節。

- 註冊資源提供者。
- 允許信任的 Microsoft 服務。
- 建立一或多個資料匯出規則，以定義要匯出的資料表及其目的地。

### <a name="register-resource-provider"></a>註冊資源提供者
下列 Azure 資源提供者必須註冊您的訂用帳戶，才能啟用 Log Analytics 資料匯出。 

- Microsoft.Insights

此資源提供者可能已經註冊給大部分的 Azure 監視器使用者。 若要確認，請移至 Azure 入口網站中的 [ **訂閱** ]。 選取您的訂用帳戶，然後按一下功能表的 [ **設定** ] 區段中的 [ **資源提供者** ]。 找出 [ **Microsoft Insights** ]。 如果其狀態為 [已 **註冊** ]，表示它已註冊。 如果沒有，請按一下 [ **註冊** ] 進行註冊。

您也可以使用任何可用的方法來註冊資源提供者 [，如 Azure 資源提供者和類型](../../azure-resource-manager/management/resource-providers-and-types.md)中所述。 以下是使用 PowerShell 的範例命令：

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>允許信任的 Microsoft 服務
如果您已將儲存體帳戶設定為允許從選取的網路進行存取，您需要新增例外狀況，以允許 Azure 監視器寫入至帳戶。 從儲存體帳戶的 **防火牆和虛擬網路** ，選取 [ **允許信任的 Microsoft 服務存取此儲存體帳戶** ]。

[![儲存體帳戶防火牆和虛擬網路](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)


### <a name="create-or-update-data-export-rule"></a>建立或更新資料匯出規則
資料匯出規則會定義要針對一組資料表匯出到單一目的地的資料。 您可以為每個目的地建立規則。

使用下列 CLI 命令來查看工作區中的資料表。 它有助於複製您想要的資料表，並包含在資料匯出規則中。
```azurecli
az monitor log-analytics workspace table list -resource-group resourceGroupName --workspace-name workspaceName --query [].name --output table
```

使用下列命令，以使用 CLI 來建立儲存體帳戶的資料匯出規則。

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountId
```

使用下列命令，使用 CLI 建立事件中樞的資料匯出規則。

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesId
```

使用下列要求，利用 REST API 建立資料匯出規則。 要求應使用持有人權杖授權和內容類型 application/json。

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

要求的主體會指定資料表目的地。 以下是儲存體帳戶 REST 要求的範例主體。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

以下是事件中樞 REST 要求的範例主體。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

以下是提供事件中樞名稱的事件中樞 REST 要求的範例主體。 在此情況下，所有匯出的資料都會傳送到此事件中樞。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

## <a name="view-data-export-configuration"></a>查看資料匯出設定
使用下列命令，以使用 CLI 來查看資料匯出規則的設定。

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

您可以使用下列要求，利用 REST API 來查看資料匯出規則的設定。 要求應使用持有人權杖授權。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

## <a name="disable-an-export-rule"></a>停用匯出規則
您可以停用匯出規則，讓您在不需要保留特定期間的資料（例如執行測試時）時停止匯出。 使用下列命令，以使用 CLI 來停用資料匯出規則。

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

使用下列要求，利用 REST API 停用資料匯出規則。 要求應使用持有人權杖授權。

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

## <a name="delete-an-export-rule"></a>刪除匯出規則
使用下列命令，以使用 CLI 來刪除資料匯出規則。

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

使用下列要求，利用 REST API 刪除資料匯出規則。 要求應使用持有人權杖授權。

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

## <a name="view-all-data-export-rules-in-a-workspace"></a>查看工作區中的所有資料匯出規則
使用下列命令，以使用 CLI 來查看工作區中的所有資料匯出規則。

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

您可以使用下列要求，使用 REST API 來查看工作區中的所有資料匯出規則。 要求應使用持有人權杖授權。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

## <a name="unsupported-tables"></a>不支援的資料表
如果資料匯出規則包含不支援的資料表，設定將會成功，但不會針對該資料表匯出任何資料。 如果稍後支援資料表，則會在該時間匯出其資料。

如果資料匯出規則包含不存在的資料表，它將會失敗並出現錯誤 ```Table <tableName> does not exist in the workspace.```


## <a name="supported-tables"></a>支援的資料表
支援的資料表目前僅限於以下指定的資料表。 除非指定了限制，否則將匯出資料表中的所有資料。 這份清單會隨著加入其他資料表的支援而更新。


| Table | 限制 |
|:---|:---|
| AADDomainServicesAccountLogon | |
| AADDomainServicesAccountManagement | |
| AADDomainServicesDirectoryServiceAccess | |
| AADDomainServicesLogonLogoff | |
| AADDomainServicesPolicyChange | |
| AADDomainServicesPrivilegeUse | |
| AADManagedIdentitySignInLogs | |
| AADNonInteractiveUserSignInLogs | |
| AADProvisioningLogs | |
| AADServicePrincipalSignInLogs | |
| ADAssessmentRecommendation | |
| ADFActivityRun | |
| ADFPipelineRun | |
| ADFTriggerRun | |
| ADReplicationResult | |
| ADSecurityAssessmentRecommendation | |
| ADTDigitalTwinsOperation | |
| ADTEventRoutesOperation | |
| ADTModelsOperation | |
| ADTQueryOperation | |
| ADXCommand | |
| ADXQuery | |
| AegDeliveryFailureLogs | |
| AegPublishFailureLogs | |
| 警示 |部分支援。 此資料表的部分資料是透過儲存體帳戶內嵌。 這項資料目前不會匯出。 |
| 異常 | |
| ApiManagementGatewayLogs | |
| AppCenterError | |
| AppPlatformSystemLogs | |
| AppServiceAppLogs | |
| AppServiceAuditLogs | |
| AppServiceConsoleLogs | |
| AppServiceFileAuditLogs | |
| AppServiceHTTPLogs | |
| AppServiceIPSecAuditLogs | |
| AppServicePlatformLogs | |
| AuditLogs | |
| AutoscaleEvaluationsLog | |
| AutoscaleScaleActionsLog | |
| AWSCloudTrail | |
| AzureAssessmentRecommendation | |
| AzureDevOpsAuditing | |
| BehaviorAnalytics | |
| BlockchainApplicationLog | |
| BlockchainProxyLog | |
| BlockchainProxyLog | |
| CommonSecurityLog | |
| CommonSecurityLog | |
| ComputerGroup | |
| ConfigurationData | 部分支援。 某些資料是透過不支援匯出的內部服務所內嵌。 這項資料目前不會匯出。 |
| ContainerImageInventory | |
| ContainerInventory | |
| ContainerLog | |
| ContainerNodeInventory | |
| ContainerServiceLog | |
| CoreAzureBackup | |
| DatabricksAccounts | |
| DatabricksClusters | |
| DatabricksDBFS | |
| DatabricksInstancePools | |
| DatabricksJobs | |
| DatabricksNotebook | |
| DatabricksSecrets | |
| DatabricksSQLPermissions | |
| DatabricksSSH | |
| DatabricksWorkspace | |
| DnsEvents | |
| DnsInventory | |
| Dynamics365Activity | |
| 事件 | 部分支援。 此資料表的部分資料是透過儲存體帳戶內嵌。 這項資料目前不會匯出。 |
| ExchangeAssessmentRecommendation | |
| FailedIngestion | |
| FunctionAppLogs | |
| HDInsightAmbariClusterAlerts | |
| HDInsightAmbariSystemMetrics | |
| HDInsightGatewayAuditLogs | |
| HDInsightHadoopAndYarnLogs | |
| HDInsightHadoopAndYarnMetrics | |
| HDInsightHBaseLogs | |
| HDInsightHBaseMetrics | |
| HDInsightHiveAndLLAPLogsSample | |
| HDInsightKafkaLogs | |
| HDInsightKafkaMetrics | |
| HDInsightOozieLogs | |
| HDInsightSecurityLogs | |
| HDInsightSparkApplicationEvents | |
| HDInsightSparkBlockManagerEvents | |
| HDInsightSparkEnvironmentEvents | |
| HDInsightSparkEventsLog | |
| HDInsightSparkExecutorEvents | |
| HDInsightSparkExtraEvents | |
| HDInsightSparkJobEvents | |
| HDInsightSparkLogs | |
| HDInsightSparkSQLExecutionEvents | |
| HDInsightSparkStageEvents | |
| HDInsightSparkStageTaskAccumulables | |
| HDInsightSparkTaskEvents | |
| HDInsightStormLogs | |
| HDInsightStormMetrics | |
| HDInsightStormTopologyMetrics | |
| 活動訊號 | |
| HuntingBookmark | |
| InsightsMetrics | 部分支援。 某些資料是透過不支援匯出的內部服務所內嵌。 目前匯出中缺少此部分。 |
| IntuneAuditLogs | |
| IntuneDeviceComplianceOrg | |
| IntuneOperationalLogs | |
| KubeEvents | |
| KubeHealth | |
| KubeMonAgentEvents | |
| KubeNodeInventory | |
| KubePodInventory | |
| KubeServices | |
| KubeServices | |
| LAQueryLogs | |
| McasShadowItReporting | |
| MicrosoftAzureBastionAuditLogs | |
| MicrosoftDataShareReceivedSnapshotLog | |
| MicrosoftDataShareSentSnapshotLog | |
| MicrosoftDataShareShareLog | |
| MicrosoftHealthcareApisAuditLogs | |
| NWConnectionMonitorDestinationListenerResult | |
| NWConnectionMonitorDNSResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorTestResult | |
| NWConnectionMonitorTestResult | |
| OfficeActivity | 部分支援。 透過 webhook 從 Office 365 內嵌至 Log Analytics 的部分資料。 這項資料目前不會匯出。 |
| 作業 | 部分支援。 某些資料是透過不支援匯出的內部服務所內嵌。 這項資料目前不會匯出。 |
| Perf | 部分支援。 目前只支援 windows 效能資料。 Linux 效能資料目前不會匯出。 |
| ProtectionStatus | |
| SCCMAssessmentRecommendation | |
| SCOMAssessmentRecommendation | |
| SecurityAlert | |
| SecurityBaseline | |
| SecurityBaselineSummary | |
| SecurityDetection | |
| SecurityEvent | |
| SecurityIncident | |
| SecurityIoTRawEvent | |
| SecurityNestedRecommendation | |
| SecurityRecommendation | |
| SfBAssessmentRecommendation | |
| SfBOnlineAssessmentRecommendation | |
| SharePointOnlineAssessmentRecommendation | |
| SignalRServiceDiagnosticLogs | |
| SigninLogs | |
| SPAssessmentRecommendation | |
| SQLAssessmentRecommendation | |
| SucceededIngestion | |
| SynapseGatewayEvents | |
| SynapseRBACEvents | |
| syslog | 部分支援。 此資料表的部分資料是透過儲存體帳戶內嵌。 這項資料目前不會匯出。 |
| ThreatIntelligenceIndicator | |
| 更新 | 部分支援。 某些資料是透過不支援匯出的內部服務所內嵌。 這項資料目前不會匯出。 |
| UpdateRunProgress | |
| UpdateSummary | |
| 使用方式 | |
| UserAccessAnalytics | |
| UserPeerAnalytics | |
| 關注清單 | |
| Windowsevent 進行篩選 | |
| WindowsFirewall | |
| WireData | 部分支援。 某些資料是透過不支援匯出的內部服務所內嵌。 這項資料目前不會匯出。 |
| WorkloadMonitoringPerf | |
| WVDAgentHealthStatus | |
| WVDCheckpoints | |
| WVDConnections | |
| WVDErrors | |
| WVDFeeds | |
| WVDManagement | |


## <a name="next-steps"></a>後續步驟

- [從 Azure 資料總管查詢匯出的資料](azure-data-explorer-query-storage.md)。
