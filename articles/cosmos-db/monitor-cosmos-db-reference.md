---
title: Azure Cosmos DB 監視資料參考 |Microsoft Docs
description: 從 Azure Cosmos DB 監視資料的記錄和計量參考。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: how-to
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: 446d876033b09728ebcbec43c6300884a5c29cd3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262730"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB 監視資料參考
本文提供所收集記錄和計量資料的參考，以分析 Azure Cosmos DB 的效能和可用性。 如需收集和分析 Azure Cosmos DB 的監視資料的詳細資訊，請參閱[監視 Cosmos DB](monitor-cosmos-db.md) 。


## <a name="resource-logs"></a>資源記錄
下表列出在 Azure 監視器記錄或 Azure 儲存體中收集 Azure Cosmos DB 資源記錄檔時的屬性。 在 Azure 監視器記錄檔中，它們會收集在**AzureDiagnostics**資料表中，其**ResourceProvider**值為*MICROSOFT.DOCUMENTDB*。 

| Azure 儲存體欄位或屬性 | Azure 監視器記錄檔屬性 | 描述 |
| --- | --- | --- |
| **time** | **TimeGenerated** | 作業發生的日期和時間 (UTC)。 |
| **resourceId** | **Resource** | 啟用記錄的 Azure Cosmos DB 帳戶。|
| **類別** | **類別** | 針對 Azure Cosmos DB 記錄檔， **DataPlaneRequests**、 **MongoRequests**、 **QueryRuntimeStatistics**、 **PartitionKeyStatistics**、 **PartitionKeyRUConsumption**、 **ControlPlaneRequests**是可用的記錄類型。 |
| **operationName** | **名稱** | 作業名稱。 這個值可以是下列任一作業：Create、Update、Read、ReadFeed、Delete、Replace、Execute、SqlQuery、Query、JSQuery、Head、HeadFeed 或 Upsert。   |
| **properties** | n/a | 此欄位的內容說明於下列資料列中。 |
| **activityId** | **activityId_g** | 所記錄作業的唯一 GUID。 |
| **userAgent** | **userAgent_s** | 此字串指定執行要求的用戶端使用者代理程式。 格式為 {使用者代理程式名稱}/{版本}。|
| **requestResourceType** | **requestResourceType_s** | 存取的資源類型。 這個值可以是下列任一資源類型：Database、Container、Document、Attachment、User、Permission、StoredProcedure、Trigger、UserDefinedFunction 或 Offer。 |
| **statusCode** | **statusCode_s** | 作業的回應狀態。 |
| **requestResourceId** | **ResourceId** | 關於要求的 resourceId。 根據執行的作業，此值可能表示 databaseRid、collectionRid 或 documentRid。|
| **clientIpAddress** | **clientIpAddress_s** | 用戶端的 IP 位址。 |
| **requestCharge** | **requestCharge_s** | 作業使用的 RU 數 |
| **collectionRid** | **collectionId_s** | 集合的唯一識別碼。|
| **duration** | **duration_s** | 作業的持續時間（以毫秒為單位）。 |
| **requestLength** | **requestLength_s** | 以位元組為單位的要求長度。 |
| **responseLength** | **responseLength_s** | 以位元組為單位的回應長度。|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | 使用[資源權杖](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens)進行驗證時，此值為非空白值。 此值表示使用者的資源識別碼。 |
| **responseLength** | **responseLength_s** | 以位元組為單位的回應長度。|

如需所有 Azure 監視器記錄類別的清單和相關聯架構的連結，請參閱[Azure 監視器記錄類別和架構](../azure-monitor/platform/diagnostic-logs-schema.md)。 

## <a name="metrics"></a>計量
下表列出為 Azure CosmOS DB 收集的平臺計量。 所有計量都會儲存在命名空間**Cosmos DB 標準計量**中。

如需所有 Azure 監視器支援計量（包括 CosmosDB）的清單，請參閱[Azure 監視器支援的計量](../azure-monitor/platform/metrics-supported.md)。 

#### <a name="request-metrics"></a>要求計量
            
|度量（度量顯示名稱）|單位（匯總類型） |Description|維度| 時間資料粒度| 舊版計量對應 | 使用方式 |
|---|---|---|---| ---| ---| ---|
| TotalRequests （要求總數） | 計數（計數） | 進行的要求數目| DatabaseName、CollectionName、Region、StatusCode| 全部 | TotalRequests、Http 2xx、Http 3xx、Http 400、Http 401、內部伺服器錯誤、可用的服務、已節流的要求、平均每秒鐘要求數 | 用來監視每個狀態碼的要求，每分鐘的資料細微性。 若要取得平均每秒要求數，請使用每分鐘 Count 彙總並除以 60。 |
| MetadataRequests （中繼資料要求） |計數（計數） | 中繼資料要求計數。 Azure Cosmos DB 會維護每個帳戶的系統中繼資料容器，讓您可以免費列舉集合、資料庫等和其設定。 | DatabaseName、CollectionName、Region、StatusCode| 全部| |用來依中繼資料要求而監視節流。|
| MongoRequests （Mongo 要求） | 計數（計數） | 已提出的 Mongo 要求數目 | DatabaseName、CollectionName、Region、CommandName、ErrorCode| 全部 |Mongo 查詢要求率、Mongo 更新要求率、Mongo 刪除要求率、Mongo 插入要求率，Mongo 計數要求速率| 用於監視 Mongo 要求錯誤、各命令類型的使用量。 |

#### <a name="request-unit-metrics"></a>要求單位計量

|度量（度量顯示名稱）|單位（匯總類型）|Description|維度| 時間資料粒度| 舊版計量對應 | 使用方式 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge （Mongo 要求費用） | 計數（總計） |已使用的 Mongo 要求單位| DatabaseName、CollectionName、Region、CommandName、ErrorCode| 全部 |Mongo 查詢要求費用、Mongo 更新要求費用、Mongo 刪除要求費用、Mongo 插入要求費用，Mongo 計數要求費用| 用來監視在一分鐘內的 Mongo 資源 RU。|
| TotalRequestUnits （要求單位總數）| 計數（總計） | 已使用的要求單位| DatabaseName、CollectionName、Region、StatusCode |全部| TotalRequestUnits| 用來監視每分鐘資料粒度的總 RU 使用量。 若要取得平均每秒耗用的 RU，請使用每分鐘 Total 彙總並除以 60。|
| ProvisionedThroughput （布建的輸送量）| 計數（最大值） |在容器資料細微性布建輸送量| DatabaseName、容器名稱| 5M| | 用來監視每個容器的布建輸送量。|

#### <a name="storage-metrics"></a>儲存體度量

|度量（度量顯示名稱）|單位（匯總類型）|Description|維度| 時間資料粒度| 舊版計量對應 | 使用方式 |
|---|---|---|---| ---| ---| ---|
| AvailableStorage （可用的存放裝置） |位元組（總計） | 每個區域每5分鐘資料細微性報告的可用儲存體總計| DatabaseName、CollectionName、Region| 5M| 可用的儲存體| 用於監視可用的儲存體容量 (僅適用於固定的儲存體集合) 最小資料粒度應為 5 分鐘。| 
| DataUsage （資料使用方式） |位元組（總計） |每個區域每5分鐘資料細微性報告的資料使用量總計| DatabaseName、CollectionName、Region| 5M |資料大小 | 用來監視容器和區域的總數據使用量，最小細微性應為5分鐘。|
| IndexUsage （索引使用方式） | 位元組（總計） |每個區域每5分鐘資料細微性報告的索引使用量總計| DatabaseName、CollectionName、Region| 5M| 索引大小| 用來監視容器和區域的總數據使用量，最小細微性應為5分鐘。 |
| DocumentQuota （檔配額） | 位元組（總計） | 每個區域每5分鐘資料細微性報告的總儲存體配額。| DatabaseName、CollectionName、Region| 5M |儲存體容量| 用來監視容器和區域的總配額，最小資料細微性應為5分鐘。|
| DocumentCount （檔計數） | 計數（總計） |每個區域每5分鐘資料細微性報告的總檔計數| DatabaseName、CollectionName、Region| 5M |文件計數|用來監視容器和區域的檔計數，最小資料細微性應為5分鐘。|

#### <a name="latency-metrics"></a>延遲計量

|度量（度量顯示名稱）|單位（匯總類型）|Description|維度| 時間資料粒度| 使用方式 |
|---|---|---|---| ---| ---|
| ReplicationLatency （複寫延遲）| 毫秒（最小值、最大值、平均） | 異地複寫啟用的帳戶其跨來源和目標區域的 P99 複寫延遲| SourceRegion、TargetRegion| 全部 | 用來監視異地複寫的帳戶其任兩個區域之間的 P99 複寫延遲。 |
| 伺服器端延遲| 毫秒（平均） | 伺服器處理要求所花費的時間。 | CollectionName、ConnectionMode、DatabaseName、OperationType、PublicAPIType、Region | 全部 | 用來監視 Azure Cosmos DB 伺服器上的要求延遲。 |



#### <a name="availability-metrics"></a>可用性度量

|度量（度量顯示名稱） |單位（匯總類型）|Description| 時間資料粒度| 舊版計量對應 | 使用方式 |
|---|---|---|---| ---| ---|
| ServiceAvailability （服務可用性）| 百分比（最小值，最大值） | 一小時資料粒度的帳戶要求可用性| 1H | 服務可用性 | 代表通過要求總數的百分比。 如果狀態碼為 410、500 或 503，則要求會因為系統錯誤而視為失敗。用於以小時資料粒度監視帳戶的可用性。 |


#### <a name="cassandra-api-metrics"></a>Cassandra API 計量

|度量（度量顯示名稱）|單位（匯總類型）|Description|維度| 時間資料粒度| 使用方式 |
|---|---|---|---| ---| ---|
| CassandraRequests （Cassandra 要求） | 計數（計數） | 已提出的 Cassandra API 要求數目| DatabaseName、CollectionName、ErrorCode、Region、OperationType、ResourceType| 全部| 用於監視每分鐘資料粒度的 Cassandra 要求數。 若要取得平均每秒要求數，請使用每分鐘 Count 彙總並除以 60。|
| CassandraRequestCharges （Cassandra 要求費用） | Count （Sum、Min、Max、Avg） | Cassandra API 要求所耗用的要求單位| DatabaseName、CollectionName、Region、OperationType、ResourceType| 全部| 用來監視 Cassandra API 帳戶每分鐘所使用的 RU 數。|
| CassandraConnectionClosures （Cassandra 連接閉） |計數（計數） |已終止的 Cassandra 連線數目| ClosureReason、Region| 全部 | 用於監視用戶端與 Azure Cosmos DB Cassandra API 之間的連線。|

## <a name="see-also"></a>另請參閱

- 如需監視 Azure Cosmos DB 的說明，請參閱[監視 Azure Cosmos DB](monitor-cosmos-db.md) 。
- 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器來監視 Azure 資源](../azure-monitor/insights/monitor-azure-resource.md)。
