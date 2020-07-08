---
title: 使用 Azure 診斷設定來監視 Azure Cosmos DB 資料
description: 瞭解如何使用 Azure 診斷設定來監視儲存在 Azure Cosmos DB 中的資料效能和可用性
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/05/2020
ms.author: sngun
ms.openlocfilehash: 881ddfec587df61201f2c251fd0dd0a8164496c3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85549967"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>使用 Azure 中的診斷設定來監視 Azure Cosmos DB 資料

Azure 中的診斷設定可用來收集資源記錄。 資源會發出 Azure 資源記錄，並提供有關該資源之作業的豐富、經常性資料。 這些記錄會針對每個要求而捕獲，而且也稱為「資料平面記錄」。 資料平面作業的一些範例包括 delete、insert 和 readFeed。 這些記錄的內容會依資源類型而有所不同。

系統會自動收集平臺計量和活動記錄，而您必須建立診斷設定來收集資源記錄，或將它們轉送到 Azure 監視器外部。 您可以使用下列步驟來開啟 Azure Cosmos 帳戶的診斷設定：

1. 登入[Azure 入口網站](https://portal.azure.com)。

1. 瀏覽至 Azure Cosmos 帳戶。 開啟 [**診斷設定**] 窗格，然後選取 [**新增診斷設定**] 選項。

1. 在 [**診斷設定**] 窗格中，填寫表單的下列詳細資料： 

    * **名稱**：輸入要建立之記錄的名稱。

    * 您可以儲存記錄以封存**至儲存體帳戶**、**串流至事件中樞**，或**傳送至 Log Analytics**

1. 當您建立診斷設定時，可以指定要收集的記錄類別。 下面列出 Azure Cosmos DB 所支援的記錄類別，以及它們所收集的範例記錄檔：

 * **DataPlaneRequests**：選取此選項可將後端要求記錄到所有 api，包括 SQL、Graph、MongoDB、Cassandra，以及 Azure Cosmos DB 中的資料表 API 帳戶。 要注意的重要屬性包括： `Requestcharge` 、 `statusCode` 、 `clientIPaddress` 和 `partitionID` 。

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**：選取此選項可從前端記錄使用者起始的要求，以向 Azure Cosmos DB 的 MongoDB API 提供要求。 其他 API 帳戶無法使用此記錄類型。 要注意的重要屬性包括： `Requestcharge` 、 `opCode` 。 當您在診斷記錄中啟用 MongoRequests 時，請務必關閉 DataPlaneRequests。 針對 API 提出的每個要求，您會看到一個記錄檔。

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **CassandraRequests**：選取此選項可從前端記錄使用者起始的要求，以提供要求給 Cassandra AZURE COSMOS DB 的 API。 其他 API 帳戶無法使用此記錄類型。 要注意的索引鍵屬性是 `operationName` 、 `requestCharge` 、 `piiCommandText` 。 當您在診斷記錄中啟用 CassandraRequests 時，請務必關閉 DataPlaneRequests。 針對 API 提出的每個要求，您會看到一個記錄檔。

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **QueryRuntimeStatistics**：選取此選項以記錄已執行的查詢文字。 此記錄類型僅適用于 SQL API 帳戶。

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**：選取此選項可記錄分割區索引鍵的統計資料。 這目前是以資料分割索引鍵的儲存體大小（KB）來表示。 請參閱本文的[使用 Azure 診斷查詢來疑難排解問題](#diagnostic-queries)一節。 例如，使用 "PartitionKeyStatistics" 的查詢。 記錄會針對佔用大部分資料儲存體的前三個分割區索引鍵來發出。 此記錄包含訂用帳戶識別碼、區功能變數名稱稱、資料庫名稱、集合名稱、資料分割索引鍵和儲存體大小（以 KB 為單位）的資料。

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**：此記錄檔會報告資料分割索引鍵的每秒匯總每秒耗用量。 目前，Azure Cosmos DB 只會報告 SQL API 帳戶的資料分割索引鍵，以及用於點讀取/寫入和預存程式作業。 不支援其他 Api 和操作類型。 若是其他 Api，診斷記錄資料表中的分割區索引鍵資料行將會是空的。 此記錄包含訂用帳戶識別碼、區功能變數名稱稱、資料庫名稱、集合名稱、資料分割索引鍵、作業類型和要求費用等資料。 請參閱本文的[使用 Azure 診斷查詢來疑難排解問題](#diagnostic-queries)一節。 例如，使用 "PartitionKeyRUConsumption" 的查詢。 

* **ControlPlaneRequests**：此記錄包含控制平面作業的詳細資料，例如建立帳戶、新增或移除區域、更新帳戶複寫設定等。此記錄類型適用于包含 SQL （Core）、MongoDB、Gremlin、Cassandra 資料表 API 的所有 API 類型。

* **要求**：選取此選項可從 Azure Cosmos DB 收集計量資料到診斷設定中的目的地。 這是在 Azure 計量中自動收集的相同資料。 使用資源記錄收集計量資料，以同時分析兩種資料，並在 Azure 監視器外部傳送計量資料。

如需如何使用 Azure 入口網站、CLI 或 PowerShell 建立診斷設定的詳細資訊，請參閱[建立診斷設定以收集 Azure 中的平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)文章。


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>針對診斷查詢的問題進行疑難排解

1. 如何查詢執行時間超過3毫秒的作業：

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

1. 如何查詢正在執行作業的使用者代理程式：

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

1. 如何查詢長時間執行的作業：

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
1. 如何取得分割區索引鍵統計資料，以評估資料庫帳戶的前3個磁碟分割的扭曲：

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
   ```

1. 如何取得昂貴查詢的要求費用？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. 如何找出最多 RU/秒所佔用的作業？

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

1. 如何取得所有耗用超過 100 RU/s 的查詢，並與**DataPlaneRequests**和**QueryRunTimeStatistics**中的資料聯結。

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. 如何取得查詢的要求費用和執行持續時間？

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
   ```


1. 如何取得不同作業的散發？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. 分割區已耗用的輸送量上限為何？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. 如何取得每秒分割區索引鍵 RU/秒耗用量的相關資訊？

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. 如何取得特定分割區索引鍵的要求費用

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. 如何取得在特定期間內耗用最多 RU/秒的前幾個資料分割索引鍵？ 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. 如何取得儲存體大小大於 8 GB 的資料分割索引鍵的記錄？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. 如何取得分割區索引鍵統計資料，以評估資料庫帳戶的前三個磁碟分割之間的扭曲？

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId
   ```

1. 如何取得作業、要求費用或回應長度的 P99 或 P50 複寫延遲？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize
   percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s),
   percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s),
   percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s),
   count()
   by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
1. 如何取得 Controlplane 記錄？
 
   請記得依照[停用金鑰型中繼資料寫入存取](audit-control-plane-logs.md#disable-key-based-metadata-write-access)articleand 中所述的方式來開啟旗標，並透過 AZURE POWERSHELL、CLI 或 ARM 來執行作業。
 
   ```Kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```


## <a name="next-steps"></a>後續步驟

* [適用於 Azure Cosmos DB 的 Azure 監視器](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [使用 Azure Cosmos DB 中的計量進行監視及偵錯](use-metrics.md)
