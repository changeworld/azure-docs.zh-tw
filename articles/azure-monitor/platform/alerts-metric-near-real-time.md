---
title: Azure 監視器中計量警示所支援的資源
description: Azure 監視器中計量警示所支援計量與記錄的相關參考
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 12/15/2020
ms.subservice: alerts
ms.openlocfilehash: 8f59f3488f6c8f5b35ec68d93db656447f882a92
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510677"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Azure 監視器中計量警示所支援的資源

Azure 監視器現已可支援[新的計量警示類型](./alerts-overview.md)，此類型具有遠優於[傳統計量警示](./alerts-classic.overview.md)的優點。 計量適用於 [Azure 服務的大型清單](./metrics-supported.md)。 新版警示則可支援該資源類型 (不斷增長) 的子集。 本文將列出該子集。

您也可以針對儲存在 Log Analytics 工作區中的熱門記錄資料使用較新的計量警示，並將其當作計量來解壓縮。 如需詳細資訊，請檢視[記錄的計量警示](./alerts-metric-logs.md)。

## <a name="portal-powershell-cli-rest-support"></a>入口網站、PowerShell、CLI、REST 支援
目前，您只能在 Azure 入口網站、 [REST API](/rest/api/monitor/metricalerts/)或 [Resource Manager 範本](./alerts-metric-create-templates.md)中建立較新的計量警示。 即將支援使用 PowerShell 和 Azure CLI 2.0 版與更新版本來設定新版警示。

## <a name="metrics-and-dimensions-supported"></a>支援的計量和維度
新版計量警示支援針對使用維度的計量發出警示。 您可以使用維度來將計量篩選到正確層級。 從 [Azure 監視器 - 計量瀏覽器](./metrics-charts.md)，即可探索並以視覺化方式檢視所有支援的計量及適用的維度。

以下是較新警示所支援之 Azure 監視器計量來源的完整清單：

|資源類型  |支援的維度 |多資源警示| 可用的計量|
|---------|---------|-----|----------|
|Aadiam/azureADMetrics | 是 | 否 | |
|Microsoft.ApiManagement/service | 是 | 否 | [API 管理](./metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |是 | 否 | [應用程式組態](./metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/Spring | 是 | 否 | [Azure Spring Cloud](./metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automationAccounts | 是| 否 | [自動化帳戶](./metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft AVS/privateClouds | 否 | 否 | |
|Microsoft.Batch/batchAccounts | 是 | 否 | [批次帳戶](./metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis | 是 | 是 | [Azure Cache for Redis](./metrics-supported.md#microsoftcacheredis) |
|Microsoft.ClassicCompute/domainNames/slots/roles | 否 | 否 | [傳統雲端服務](./metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft.ClassicCompute/virtualMachines | 否 | 否 | [傳統虛擬機器](./metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft.ClassicStorage/storageAccounts | 是 | 否 | [ (傳統) 的儲存體帳戶 ](./metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | 是 | 否 | [ (傳統) Blob 的儲存體帳戶](./metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft.ClassicStorage/storageAccounts/fileServices | 是 | 否 | [儲存體帳戶 (傳統) 檔案](./metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft.ClassicStorage/storageAccounts/queueServices | 是 | 否 | [儲存體帳戶 (傳統) -佇列](./metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft.ClassicStorage/storageAccounts/tableServices | 是 | 否 | [ (傳統) 資料表的儲存體帳戶](./metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft.CognitiveServices/accounts | 是 | 否 | [認知服務](./metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | 是 | 是<sup>1</sup> | [虛擬機器](./metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | 是 | 否 |[虛擬機器擴展集](./metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ContainerInstance/containerGroups | 是| 否 | [容器群組](./metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | 否 | 否 | [容器登錄](./metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | 是 | 否 | [受控叢集](./metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | 是 | 是 | [資料箱](./metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| 是| 否 | [資料處理站 V1](./metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/factories |是 | 否 | [資料處理站 (V2)](./metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | 是 | 否 | |
|Microsoft.DBforMariaDB/servers | 否 | 否 | [適用于 mariadb 的 DB](./metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | 否 | 否 |[適用於 MySQL 的 DB](./metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers | 否 | 否 | [適用於 PostgreSQL 的 DB](./metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | 否 | 否 | [適用于于 postgresql V2 的 DB](./metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.DBforPostgreSQL/flexibleServers | 是 | 否 | [DB for 于 postgresql (彈性的伺服器) ](./metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft.Devices/IotHubs | 是 | 否 |[IoT 中心](./metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| 是 | 否 | [裝置布建服務](./metrics-supported.md#microsoftdevicesprovisioningservices) |
|DigitalTwins/digitalTwinsInstances | 是 | 否 | |
|Microsoft.DocumentDB/databaseAccounts | 是 | 否 | [Cosmos DB](./metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domains | 是 | 否 | 「事件方格網域」 |
|EventGrid/systemTopics | 是 | 否 | [事件方格系統主題](./metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/topics |是 | 否 | 「事件方格主題」 |
|Microsoft.EventHub/clusters |是| 否 | [事件中樞叢集](./metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |是| 否 | [事件中樞](./metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | 是 | 否 | [HDInsight 叢集](./metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Components | 是 | 否 | [Application Insights](./metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | 是 |是 |[保存庫](./metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | 是 |否 |[資料總管叢集](./metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/integrationServiceEnvironments | 是 | 否 |[整合服務環境](./metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | 否 | 否 |[Logic Apps](./metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | 是 | 否 | [Machine Learning](./metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Maps/accounts | 是 | 否 | [Maps 帳戶](./metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | 否 | 否 | [媒體服務](./metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | 是 | 否 | [媒體服務串流端點](./metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | 是 | 是 | [Azure NetApp 容量集區](./metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | 是 | 是 | [Azure NetApp 磁片區](./metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | 是 | 否 | [應用程式閘道](./metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | 是 | 否 | [防火牆](./metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | 否 | 否 | [DNS 區域](./metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | 是 | 否 |[ExpressRoute 線路](./metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/expressRoutePorts | 是 | 否 |[ExpressRoute Direct](./metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft.Network/loadBalancers (僅適用於標準 SKU)| 是| 否 | [負載平衡器](./metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| 否 | 否 | |
|Microsoft. Network/privateEndpoints| 否 | 否 | |
|Microsoft.Network/privateLinkServices| 否 | 否 |
|Microsoft.Network/publicipaddresses | 否 | 否 |[公用 IP 位址](./metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | 是 | 否 | [流量管理員設定檔](./metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| 是 | 否 | [Log Analytics 工作區](./metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft 對等互連/對等互連 | 是 | 否 | [對等互連](./metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft 對等互連/peeringServices | 是 | 否 | [對等互連服務](./metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | 否 | 否 | [容量](./metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.Relay/namespaces | 是 | 否 | [轉送](./metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | 否 | 否 | [搜尋服務](./metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | 是 | 否 | [服務匯流排](./metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | 否 | 是 | [SQL 受控執行個體](./metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | 否 | 是 | [SQL Databases](./metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servers/elasticPools | 否 | 是 | [SQL 彈性集區](./metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |是 | 否 | [儲存體帳戶](./metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/blobServices | 是| 否 | [儲存體帳戶-Blob](./metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft.Storage/storageAccounts/fileServices | 是| 否 | [儲存體帳戶-檔案](./metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft.Storage/storageAccounts/queueServices | 是| 否 | [儲存體帳戶-佇列](./metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft.Storage/storageAccounts/tableServices | 是| 否 | [儲存體帳戶-資料表](./metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft.StorageCache/caches | 是 | 否 | |
|Microsoft.storagesync/storageSyncServices | 是 | 否 | [儲存體同步服務](./metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | 是 | 否 | [串流分析](./metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft.Synapse/workspaces | 是 | 否 | [Synapse Analytics](./metrics-supported.md#microsoftsynapseworkspaces) |
|Synapse/workspace/bigDataPools | 是 | 否 | [Synapse Analytics Apache Spark 集區](./metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Synapse/workspace/sqlPools | 是 | 否 | [Synapse Analytics SQL 集區](./metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|VMWareCloudSimple/virtualMachines | 是 | 否 | [CloudSimple 虛擬機器](./metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | 是 | 否 | [App Service 環境多角色集區](./metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | 是 | 否 | [App Service 環境背景工作集區](./metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | 是 | 否 | [App Service 方案](./metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | 是 | 否 | [App Service 與函式](./metrics-supported.md#microsoftwebsites)|
|Microsoft.Web/sites/slots | 是 | 否 | [App Service 位置](./metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> 不支援虛擬機器網路計量 (網路總計、網路輸出、輸入流量、輸出流量、輸入流量最大建立速率、輸出流量最大建立速率) 和自訂計量。

## <a name="payload-schema"></a>承載結構描述

> [!NOTE]
> 您也可以使用 [常見的警示架構](./alerts-common-schema.md)，讓您能夠在 Azure 監視器中的所有警示服務上擁有單一可延伸和整合的警示承載，以供您的 webhook 整合使用。 [深入瞭解常見的警示架構定義。](./alerts-common-schema-definitions.md)


當使用已適當設定的[動作群組](./action-groups.md)時，POST 作業會針對所有新版計量警示，包含下列 JSON 承載和結構描述：

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>後續步驟

* 深入了解新的[警示體驗](./alerts-overview.md)。
* 了解 [Azure 中的記錄警示](./alerts-unified-log.md)。
* 瞭解 [Azure 中的警示](./alerts-overview.md)。
