---
title: 使用 Azure 診斷設定監視 Azure 宇宙資料庫資料
description: 瞭解如何使用 Azure 診斷設定來監視記憶體在 Azure Cosmos DB 中的數據的效能和可用性
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: f5a0b0f71a72ea76940450f73354fda230e09c5c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521054"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>使用 Azure 中的診斷設定監視 Azure 宇宙資料庫資料

Azure 中的診斷設置用於收集資源日誌。 Azure 資源日誌由資源發出,並提供有關該資源操作的豐富、頻繁的數據。 這些日誌是按請求捕獲的,它們也稱為「數據平面日誌」。。 數據平面操作的一些範例包括刪除、插入和讀取源。 這些記錄的內容會依資源類型而有所不同。

平台指標和活動日誌會自動收集,而您必須創建診斷設置以收集資源日誌或在 Azure 監視器之外轉發它們。 可以使用以下步驟開啟 Azure Cosmos 帳號的診斷設定:

1. 登入[Azure 門戶](https://portal.azure.com)。

1. 瀏覽至 Azure Cosmos 帳戶。 開啟 **「診斷設定」** 窗格,然後選擇 **「新增診斷設定」** 選項。

1. 在 **「診斷設定」** 窗格中,使用以下詳細資訊填寫表單: 

    * **名稱**：輸入要建立之記錄的名稱。

    * 您可以將紀錄儲存**到存檔到儲存帳戶**,**或是串流到事件中心**或**送出到紀錄分析**

1. 創建診斷設置時,可以指定要收集的日誌類別。 Azure Cosmos DB 支援的紀錄類別以及它們收集的範例紀錄如下:

 * **DataPlane 請求**:選擇此選項可將後端請求記錄到 Azure Cosmos DB 中的 SQL、圖形、MongoDB、Cassandra 和表 API 帳戶的所有 API。 需要注意的關鍵屬性是: `Requestcharge`、、`clientIPaddress``partitionID``statusCode`和 。

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **Mongo請求**:選擇此選項可記錄來自前端的用戶啟動的請求,以便為 MongoDB 提供 Azure Cosmos DB 的 API 請求。 此日誌類型不適用於其他 API 帳戶。 需要注意的關鍵屬性是: `Requestcharge` `opCode` 。 在診斷日誌中啟用 Mongo 請求時,請確保關閉 DataPlane 請求。 對於 API 上所做的每個請求,您將看到一個日誌。

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **Cassandra 請求**:選擇此選項可記錄來自前端的用戶啟動的請求,以為 Azure Cosmos DB 的 Cassandra API 提供請求。 此日誌類型不適用於其他 API 帳戶。 需要注意的關鍵屬性是`operationName` `requestCharge` `piiCommandText`。 在診斷日誌中啟用 Cassandra 請求時,請確保關閉 DataPlane 請求。 對於 API 上所做的每個請求,您將看到一個日誌。

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **查詢執行時統計資訊**:選擇此選項可記錄已執行的查詢文本。 此日誌類型僅適用於 SQL API 帳戶。

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **分區鍵統計資訊**:選擇此選項可記錄分區鍵的統計資訊。 這目前表示與分區鍵的存儲大小 (KB) 一起。 請參考使用本文的[Azure 診斷查詢部份的故障排除問題](#diagnostic-queries)。 例如,使用"分區鍵統計資訊"的查詢。 日誌將針對佔用大多數數據存儲的前三個分區密鑰發出。 此日誌包含訂閱 ID、區域名稱、資料庫名稱、集合名稱、分區密鑰和儲存大小等數據(以 KB 為單位)。

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **分區金鑰消耗**:此日誌報告分區密鑰每秒的聚合 RU/s 消耗。 目前,Azure Cosmos DB 僅報告 SQL API 帳戶以及點讀/寫和儲存過程操作的分區鍵。 不支援其他 API 和操作類型。 對於其他 API,診斷日誌表中的分區鍵列將為空。 此日誌包含訂閱 ID、區域名稱、資料庫名稱、集合名稱、分區鍵、操作類型和請求費用等數據。 請參考使用本文的[Azure 診斷查詢部份的故障排除問題](#diagnostic-queries)。 例如,使用"分區密鑰消耗"的查詢。 

* **ControlPlane 請求**:此日誌包含有關控制平面操作的詳細資訊,如創建帳戶、添加或刪除區域、更新帳戶複製設置等。此日誌類型可用於所有 API 類型,包括 SQL (核心)、蒙戈 DB、格雷姆林、卡桑德拉、表 API。

* **請求**:選擇此選項可從 Azure Cosmos DB 到診斷設置中的目標收集指標數據。 這與 Azure 指標中自動收集的數據相同。 使用資源日誌收集指標數據,以同時分析這兩種數據,並將指標數據發送到 Azure 監視器之外。

有關如何使用 Azure 門戶 CLI 或 PowerShell 建立診斷設定的詳細資訊,請參閱在 Azure 文章中[建立用於收集平台日誌和指標的診斷設定](../azure-monitor/platform/diagnostic-settings.md)。


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>診斷查詢故障

1. 如何獲取昂貴查詢的請求費用?

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

1. 如何查找哪些操作佔用了大多數 RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. 如何獲取不同操作的分佈?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. 分區使用的最大輸送量是多少?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. 如何取得有關分區金鑰 RU/s 每秒消耗量的資訊?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. 如何取得特定分割區金鑰的要求費用

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. 如何獲取在特定時間段內使用大多數 RU/s 的頂部分區鍵? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. 如何獲取存儲大小大於 8 GB 的分區密鑰的日誌?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. 如何獲取分區密鑰統計資訊以評估資料庫帳戶的前三個分區之間的偏差?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>後續步驟

* [Azure 宇宙資料庫的 Azure 監視器](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [使用 Azure Cosmos DB 中的計量進行監視及偵錯](use-metrics.md)
