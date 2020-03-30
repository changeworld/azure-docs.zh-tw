---
title: 使用 Azure 診斷設置監視 Azure 宇宙資料庫資料
description: 瞭解如何使用 Azure 診斷設置來監視存儲在 Azure Cosmos DB 中的資料的性能和可用性
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: 184fc65dae57292243be9abdca71a129512b3d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252061"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>使用 Azure 中的診斷設置監視 Azure 宇宙資料庫資料

Azure 中的診斷設置用於收集資源日誌。 Azure 資源日誌由資源發出，並提供有關該資源操作的豐富、頻繁的資料。 這些日誌是按請求捕獲的，它們也稱為"資料平面日誌"。 資料平面操作的一些示例包括刪除、插入和讀取源。 這些記錄的內容會依資源類型而有所不同。

平臺指標和活動日誌會自動收集，而您必須創建診斷設置以收集資源日誌或在 Azure 監視器之外轉發它們。 可以使用以下步驟打開 Azure Cosmos 帳戶的診斷設置：

1. 登錄到[Azure 門戶](https://portal.azure.com)。

1. 瀏覽至 Azure Cosmos 帳戶。 打開 **"診斷設置"** 窗格，然後選擇 **"添加診斷設置"** 選項。

1. 在 **"診斷設置"** 窗格中，使用以下詳細資訊填寫表單： 

    * **名稱**：輸入要建立之記錄的名稱。

    * 您可以將日誌存儲**到存檔到存儲帳戶**、**流到事件中心**或**發送到日誌分析**

1. 創建診斷設置時，可以指定要收集的日誌類別。 Azure Cosmos DB 支援的日誌類別以及它們收集的示例日誌如下：

 * **DataPlane 請求**：選擇此選項可將後端請求記錄到 Azure Cosmos DB 中的 SQL、圖形、MongoDB、Cassandra 和表 API 帳戶的所有 API。 需要注意的關鍵屬性是： `Requestcharge`、、`clientIPaddress``partitionID``statusCode`和 。

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **Mongo請求**：選擇此選項可記錄來自前端的使用者啟動的請求，以便為 MongoDB 提供 Azure Cosmos DB API 的請求，此日誌類型不適用於其他 API 帳戶。 MongoDB 請求將顯示在蒙戈請求和 DataPlane 請求中。 需要注意的關鍵屬性是： `Requestcharge`。 `opCode`

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **查詢運行時統計資訊**：選擇此選項可記錄已執行的查詢文本。 此日誌類型僅適用于 SQL API 帳戶。

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **分區鍵統計資訊**：選擇此選項可記錄分區鍵的統計資訊。 這當前表示與分區鍵的存儲大小 （KB） 一起。 請參閱使用本文的[Azure 診斷查詢部分的故障排除問題](#diagnostic-queries)。 例如，使用"分區鍵統計資訊"的查詢。 日誌將針對佔用大多數資料存儲的前三個分區金鑰發出。 此日誌包含訂閱 ID、區功能變數名稱稱、資料庫名稱、集合名稱、分區金鑰和存儲大小等資料（以 KB 為單位）。

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **分區金鑰消耗**：此日誌報告分區金鑰每秒的聚合 RU/s 消耗。 目前，Azure Cosmos DB 僅報告 SQL API 帳戶以及點讀/寫和預存程序操作的分區鍵。 不支援其他 API 和操作類型。 對於其他 API，診斷日誌表中的分區鍵列將為空。 此日誌包含訂閱 ID、區功能變數名稱稱、資料庫名稱、集合名稱、分區鍵、操作類型和請求費用等資料。 請參閱使用本文的[Azure 診斷查詢部分的故障排除問題](#diagnostic-queries)。 例如，使用"分區金鑰消耗"的查詢。 

* **ControlPlane 請求**：此日誌包含有關控制平面操作的詳細資訊，如創建帳戶、添加或刪除區域、更新帳戶複製設置等。此日誌類型可用於所有 API 類型，包括 SQL （核心）、蒙戈DB、格雷姆林、卡珊多拉、表 API。

* **請求**：選擇此選項可從 Azure Cosmos DB 到診斷設置中的目標收集指標資料。 這與 Azure 指標中自動收集的資料相同。 使用資源日誌收集指標資料，以同時分析這兩種資料，並將指標資料發送到 Azure 監視器之外。

有關如何使用 Azure 門戶 CLI 或 PowerShell 創建診斷設置的詳細資訊，請參閱在 Azure 文章中[創建用於收集平臺日誌和指標的診斷設置](../azure-monitor/platform/diagnostic-settings.md)。


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>診斷查詢故障

1. 如何獲取昂貴查詢的請求費用？

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

1. 如何查找哪些操作佔用了大多數 RU/s？

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. 如何獲取不同操作的分佈？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. 分區使用的最大輸送量是多少？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. 如何獲取有關分區金鑰 RU/s 每秒消耗量的資訊？

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. 如何獲取特定分區金鑰的請求費用

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. 如何獲取在特定時間段內使用大多數 RU/s 的頂部分區鍵？ 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. 如何獲取存儲大小大於 8 GB 的分區金鑰的日誌？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. 如何獲取分區金鑰統計資訊以評估資料庫帳戶的前三個分區之間的偏差？

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>後續步驟

* [Azure 宇宙資料庫的 Azure 監視器](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [使用 Azure Cosmos DB 中的計量進行監視及偵錯](use-metrics.md)
