---
title: Azure 監視器中計量警示所支援的資源
description: Azure 監視器中計量警示所支援計量與記錄的相關參考
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: c036fa3708d718d6199d27989e60b11015a1227e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585861"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Azure 監視器中計量警示所支援的資源

Azure 監視器現已可支援[新的計量警示類型](../../azure-monitor/platform/alerts-overview.md)，此類型具有遠優於[傳統計量警示](../../azure-monitor/platform/alerts-classic.overview.md)的優點。 計量適用於 [Azure 服務的大型清單](../../azure-monitor/platform/metrics-supported.md)。 新版警示則可支援該資源類型 (不斷增長) 的子集。 本文將列出該子集。

您還可以對存儲在日誌分析工作區中作為指標提取的常用日誌數據使用較新的指標警報。 如需詳細資訊，請檢視[記錄的計量警示](../../azure-monitor/platform/alerts-metric-logs.md)。

## <a name="portal-powershell-cli-rest-support"></a>入口網站、PowerShell、CLI、REST 支援
目前,只能在 Azure 門戶[、REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/)或[資源管理器範本](../../azure-monitor/platform/alerts-metric-create-templates.md)中創建較新的指標警報。 即將支援使用 PowerShell 和 Azure CLI 2.0 版與更新版本來設定新版警示。

## <a name="metrics-and-dimensions-supported"></a>支援的計量和維度
新版計量警示支援針對使用維度的計量發出警示。 您可以使用維度來將計量篩選到正確層級。 從 [Azure 監視器 - 計量瀏覽器](../../azure-monitor/platform/metrics-charts.md)，即可探索並以視覺化方式檢視所有支援的計量及適用的維度。

以下是新版警示所支援的 Azure 監視器計量來源完整清單：

|資源類型  |支援的維度 |多資源警報| 可用的計量|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | 是| 否 | [API 管理](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|微軟.AppPlatform/春天 |否| 是|
|Microsoft.Automation/automationAccounts | 是| 否 | [自動化帳戶](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/A| 否 | [批次帳戶](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|是| 否 |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|微軟.經典存儲/儲存帳戶/mmx 經典|否|是|
|微軟.經典存儲/存儲帳戶/mmx經典/blob服務|否|是|
|微軟.經典存儲/儲存帳戶/mmx經典/檔案服務|否|是|
|微軟.經典存儲/存儲帳戶/mmx經典/佇列服務|否|是|
|微軟.經典存儲/存儲帳戶/mmx經典/表服務|否|是| |
|Microsoft.CognitiveServices/accounts| N/A | 否 | [認知服務](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |是 | 是 | [虛擬機器](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |N/A | 是 |[虛擬機器擴展集](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | 是| 否 | [容器群組](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | 是 | 否 | [受控叢集](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|微軟.DataBoxEdge/DataBox邊緣設備 | 是 | 是 | |
|Microsoft.DataFactory/datafactories| 是| 否 | [資料處理站 V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories |是 | 否 |[資料處理站 (V2)](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|微軟.資料共享/帳戶 |否| 是|
|Microsoft.DBforMySQL/servers |N/A| 否 |[適用於 MySQL 的 DB](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |N/A | 否 | [適用於 PostgreSQL 的 DB](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | N/A | 否 |[IoT 中心指標](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| 是 | 否 |[DPS 指標](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|微軟.事件網格/域|否|是| |
|Microsoft.EventGrid/topics |是 | 否 |[事件格線主題](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |是| 否 |[事件中心群集](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |是| 否 |[事件中樞](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| 否 |否 |[金庫](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |N/A | 否 |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|是| 否 | [機器學習](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |是| 否 | [Azure NetApp 容量池](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|微軟.NetApp/NetApp帳戶/容量池/卷 |是| 否 | [Azure NetApp 卷](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|N/A| 否 |  |
|Microsoft.Network/dnsZones | N/A| 否 | [DNS 區域](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N/A | 否 |[快速路由線路](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (僅適用於標準 SKU)| 是| 否 | [負載均衡器](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|微軟.網路/natGateways|否|是|
|微軟.網路/私人終端機|否|是|
|微軟.網路/私人連結服務|否|是|
|Microsoft.Network/publicipaddresses |N/A | 否 |[公共 IP 位址](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | 是 | 否 | [流量管理員設定檔](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| 是 | 否 | [Log Analytics 工作區](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namespaces | 是 | 否 | [轉送](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|微軟.對等/對等服務|否|是|
|Microsoft.PowerBIDedicated/capacities | N/A | 否 | [能力](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |N/A|否 | [搜尋服務](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |是| 否 |[服務匯流排](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |    否 | 是 |
|Microsoft.Sql/servers/databases    | 否 | 是 |
|Microsoft.Storage/storageAccounts |是 | 否 | [儲存體帳戶](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | 是| 否 | [Blob 服務](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices)、[檔案服務](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices)、[佇列服務](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)及[資料表服務](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |N/A| 否 | [串流分析](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|微軟.微軟.VMWareCloud簡單/虛擬機器 |是|否 |[CloudSimple 虛擬機器](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingEnvironments/multiRolePools | 是 | 否 | [套用服務環境多角色池](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | 是 | 否 | [套用服務環境輔助池](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | 是 | 否 | [套用服務計劃](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | 是 | 否 | [App Service](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) 與[函式](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | 是 | 否 | [App Service 位置](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>承載結構描述

> [!NOTE]
> 您還可以使用[通用警報架構](https://aka.ms/commonAlertSchemaDocs),它提供了在 Azure 監視器中的所有警報服務中具有單個可擴展和統一警報負載的優勢,以便進行 Webhook 整合。 [瞭解常見的警報架構定義。](https://aka.ms/commonAlertSchemaDefinitions)


當使用已適當設定的[動作群組](../../azure-monitor/platform/action-groups.md)時，POST 作業會針對所有新版計量警示，包含下列 JSON 承載和結構描述：

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

* 深入了解新的[警示體驗](../../azure-monitor/platform/alerts-overview.md)。
* 了解 [Azure 中的記錄警示](../../azure-monitor/platform/alerts-unified-log.md)。
* 瞭解[Azure 中的警示](../../azure-monitor/platform/alerts-overview.md)。
