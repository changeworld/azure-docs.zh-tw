---
title: Azure Cosmos DB 監視資料參考 |Microsoft Docs
description: 用於監視 Azure Cosmos DB 中資料的記錄和計量參考。
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 21e1d93e206751b5a55b0b3549e8bd566612ddbe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88080448"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB 監視資料參考

本文提供所收集記錄和計量資料的參考，以分析 Azure Cosmos DB 的效能和可用性。 請參閱 [監視 Azure Cosmos DB](monitor-cosmos-db.md) 文章，以瞭解如何收集和分析監視資料以進行 Azure Cosmos DB。

## <a name="resource-logs"></a>資源記錄

下表列出 Azure Cosmos DB 中資源記錄的屬性。 資源記錄會收集到 Azure 監視器記錄檔或 Azure 儲存體。 在 Azure 監視器中，會在 [資源提供者 * * 的名稱] 底下的 [ **AzureDiagnostics** ] 資料表中收集記錄 `MICROSOFT.DOCUMENTDB` 。

| Azure 儲存體欄位或屬性 | Azure 監視器 Logs 屬性 | 描述 |
| --- | --- | --- |
| **time** | **TimeGenerated** | 作業發生的日期和時間 (UTC)。 |
| **resourceId** | **Resource** | 啟用記錄的 Azure Cosmos DB 帳戶。|
| **類別** | **類別** | 針對 Azure Cosmos DB， **DataPlaneRequests**、 **MongoRequests**、 **QueryRuntimeStatistics**、 **PartitionKeyStatistics**、 **PartitionKeyRUConsumption**、 **ControlPlaneRequests** 是可用的記錄檔類型。 |
| **operationName** | **OperationName** | 作業名稱。 作業名稱可以是  `Create` 、、、、、、、、、、、 `Update` `Read` `ReadFeed` `Delete` `Replace` `Execute` `SqlQuery` `Query` `JSQuery` `Head` `HeadFeed` 或 `Upsert` 。   |
| **properties** | n/a | 此欄位的內容說明於下列資料列中。 |
| **activityId** | **activityId_g** | 所記錄作業的唯一 GUID。 |
| **userAgent** | **userAgent_s** | 字串，指定傳送要求的來源用戶端使用者代理程式。 使用者代理程式的格式為 `{user agent name}/{version}` 。|
| **requestResourceType** | **requestResourceType_s** | 存取的資源類型。 此值可以是資料庫、容器、檔、附件、使用者、許可權、預存程式、觸發程式、使用者定義函數或供應專案。 |
| **statusCode** | **statusCode_s** | 作業的回應狀態。 |
| **requestResourceId** | **ResourceId** | 關於要求的 resourceId。 視執行的作業而定，這個值可能會指向 `databaseRid` 、 `collectionRid` 或 `documentRid` 。|
| **clientIpAddress** | **clientIpAddress_s** | 用戶端的 IP 位址。 |
| **requestCharge** | **requestCharge_s** | 作業所使用的 RU/秒數目 |
| **collectionRid** | **collectionId_s** | 集合的唯一識別碼。|
| **duration** | **duration_d** | 作業的持續時間（以毫秒為單位）。 |
| **requestLength** | **requestLength_s** | 以位元組為單位的要求長度。 |
| **responseLength** | **responseLength_s** | 以位元組為單位的回應長度。|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | 使用[資源權杖](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens)進行驗證時，此值為非空白值。 此值表示使用者的資源識別碼。 |
| **responseLength** | **responseLength_s** | 以位元組為單位的回應長度。|

如需所有 Azure 監視器記錄類別的清單和相關聯架構的連結，請參閱 [Azure 監視器記錄類別和架構](../azure-monitor/platform/diagnostic-logs-schema.md)。 

## <a name="metrics"></a>計量
下表列出針對 Azure CosmOS DB 收集的平臺計量。 所有計量都會儲存在命名空間 **Cosmos DB 標準計量**中。

如需所有 Azure 監視器支援計量的清單 (包含 Azure Cosmos DB) ，請參閱 [Azure 監視器支援的度量](../azure-monitor/platform/metrics-supported.md)。 

#### <a name="request-metrics"></a>要求計量
            
|度量 (計量顯示名稱) |單位 (匯總類型)  |描述|維度| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---| ---|
| TotalRequests (要求總數)  | 計數 (計數)  | 進行的要求數目| DatabaseName、CollectionName、Region、StatusCode| 全部 | TotalRequests、Http 2xx、Http 3xx、Http 400、Http 401、內部伺服器錯誤、可用的服務、已節流的要求、平均每秒鐘要求數 | 用來監視每個狀態碼、容器（以分鐘為單位）的要求。 若要取得平均每秒要求數，請使用每分鐘 Count 彙總並除以 60。 |
| MetadataRequests (中繼資料要求)  |計數 (計數)  | 中繼資料要求計數。 Azure Cosmos DB 會維護每個帳戶的系統中繼資料容器，可讓您免費列舉集合、資料庫等設定。 | DatabaseName、CollectionName、Region、StatusCode| 全部| |用來依中繼資料要求而監視節流。|
| MongoRequests (Mongo 要求)  | 計數 (計數)  | 已提出的 Mongo 要求數目 | DatabaseName、CollectionName、Region、CommandName、ErrorCode| 全部 |Mongo 查詢要求率、Mongo 更新要求率、Mongo 刪除要求率、Mongo 插入要求率，Mongo 計數要求速率| 用於監視 Mongo 要求錯誤、各命令類型的使用量。 |

#### <a name="request-unit-metrics"></a>要求單位計量

|度量 (計量顯示名稱) |單位 (匯總類型) |描述|維度| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo 要求費用)  | 計數 (總計)  |已使用的 Mongo 要求單位| DatabaseName、CollectionName、Region、CommandName、ErrorCode| 全部 |Mongo 查詢要求費用、Mongo 更新要求費用、Mongo 刪除要求費用、Mongo 插入要求費用，Mongo 計數要求費用| 用來監視在一分鐘內的 Mongo 資源 RU。|
| TotalRequestUnits (總要求單位) | 計數 (總計)  | 已使用的要求單位| DatabaseName、CollectionName、Region、StatusCode |全部| TotalRequestUnits| 用來監視每分鐘資料粒度的總 RU 使用量。 若要取得平均每秒耗用的 RU，請使用每分鐘 Total 彙總並除以 60。|
| ProvisionedThroughput (布建的輸送量) | 計數 (最大值)  |在容器細微性布建的輸送量| DatabaseName、容器名稱| 5M| | 用來監視每個容器的布建輸送量。|

#### <a name="storage-metrics"></a>儲存體度量

|度量 (計量顯示名稱) |單位 (匯總類型) |描述|維度| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (可用的儲存體)  | (總計) 的位元組 | 每個區域每5分鐘資料細微性報告的可用儲存體總計| DatabaseName、CollectionName、Region| 5M| 可用的儲存體| 用於監視可用的儲存體容量 (僅適用於固定的儲存體集合) 最小資料粒度應為 5 分鐘。| 
| DataUsage (資料使用量)  | (總計) 的位元組 |每個區域每5分鐘資料細微性報告的總數據使用量| DatabaseName、CollectionName、Region| 5M |資料大小 | 用來監視容器和區域的總數據使用量，最小資料細微性應為5分鐘。|
| IndexUsage (索引使用方式)  |  (總計) 的位元組 |每個區域每5分鐘資料細微性報告的索引使用量總計| DatabaseName、CollectionName、Region| 5M| 索引大小| 用來監視容器和區域的總數據使用量，最小資料細微性應為5分鐘。 |
| DocumentQuota (檔配額)  |  (總計) 的位元組 | 每個區域每5分鐘資料細微性報告的總儲存配額。| DatabaseName、CollectionName、Region| 5M |儲存體容量| 用來監視容器和區域的總配額，最小資料細微性應為5分鐘。|
| DocumentCount (檔計數)  | 計數 (總計)  |每個區域每5分鐘資料細微性報告的檔計數總計| DatabaseName、CollectionName、Region| 5M |文件計數|用來監視容器和區域的檔計數，最小資料細微性應為5分鐘。|

#### <a name="latency-metrics"></a>延遲計量

|度量 (計量顯示名稱) |單位 (匯總類型) |描述|維度| 時間資料粒度| 使用量 |
|---|---|---|---| ---| ---|
| ReplicationLatency (複寫延遲) | 毫秒 (最小值、最大值、平均)  | 異地複寫啟用的帳戶其跨來源和目標區域的 P99 複寫延遲| SourceRegion、TargetRegion| 全部 | 用來監視異地複寫的帳戶其任兩個區域之間的 P99 複寫延遲。 |
| 伺服器端延遲| 平均) 毫秒 ( | 伺服器處理要求所花費的時間。 | CollectionName、ConnectionMode、DatabaseName、OperationType、PublicAPIType、Region | 全部 | 用來監視 Azure Cosmos DB 伺服器上的要求延遲。 |



#### <a name="availability-metrics"></a>可用性度量

|度量 (計量顯示名稱)  |單位 (匯總類型) |描述| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---|
| ServiceAvailability (服務可用性) | 百分比 (最小值，最大值)  | 一小時資料粒度的帳戶要求可用性| 1H | 服務可用性 | 表示已通過要求總數的百分比。 如果狀態碼為 410、500 或 503，則要求會因為系統錯誤而視為失敗。用於以小時資料粒度監視帳戶的可用性。 |


#### <a name="cassandra-api-metrics"></a>Cassandra API 計量

|度量 (計量顯示名稱) |單位 (匯總類型) |描述|維度| 時間資料粒度| 使用量 |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra 要求)  | 計數 (計數)  | 已提出的 Cassandra API 要求數目| DatabaseName、CollectionName、ErrorCode、Region、OperationType、ResourceType| 全部| 用於監視每分鐘資料粒度的 Cassandra 要求數。 若要取得平均每秒要求數，請使用每分鐘 Count 彙總並除以 60。|
| CassandraRequestCharges (Cassandra 要求費用)  | Count (Sum、Min、Max、Avg)  | Cassandra API 所耗用的要求單位 | DatabaseName、CollectionName、Region、OperationType、ResourceType| 全部| 用來監視 Cassandra API 帳戶每分鐘所使用的 RU 數。|
| CassandraConnectionClosures (Cassandra 連接關閉)  |計數 (計數)  |已終止的 Cassandra 連線數目| ClosureReason、Region| 全部 | 用於監視用戶端與 Azure Cosmos DB Cassandra API 之間的連線。|

## <a name="see-also"></a>另請參閱

- 如需監視 Azure Cosmos DB 的說明，請參閱 [監視 Azure Cosmos DB](monitor-cosmos-db.md) 。
- 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器來監視 Azure 資源](../azure-monitor/insights/monitor-azure-resource.md)。
