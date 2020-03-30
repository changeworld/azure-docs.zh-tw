---
title: Azure 宇宙 DB 監視資料引用 |微軟文檔
description: 用於監視來自 Azure Cosmos DB 的資料的日誌和指標引用。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d243224192b5761af45d387690f5fb41b84481e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588717"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB 監視資料參考
本文提供所收集記錄和計量資料的參考，以分析 Azure Cosmos DB 的效能和可用性。 有關收集和分析 Azure Cosmos DB 的監視資料的詳細資訊，請參閱[監視宇宙資料庫](monitor-cosmos-db.md)。


## <a name="resource-logs"></a>資源記錄
下表列出了 Azure Cosmos DB 資源日誌在 Azure 監視器日誌或 Azure 存儲中收集時的屬性。 在 Azure 監視器日誌中，它們收集在**具有** **MICROSOFT 資來源提供者**值的 Azure 診斷表中 *。文檔 DB*. 

| Azure 儲存體欄位或屬性 | Azure 監視器日誌屬性 | 描述 |
| --- | --- | --- |
| **時間** | **TimeGenerated** | 作業發生的日期和時間 (UTC)。 |
| **資源 Id** | **資源** | 啟用記錄的 Azure Cosmos DB 帳戶。|
| **類別** | **類別目錄** | 對於 Azure Cosmos DB 日誌 **，DataPlane 請求****、Mongo 請求**、**查詢運行時統計資訊**、**分區鍵統計資訊**、**分區金鑰搜索**、**控制平面請求**是可用的日誌類型。 |
| **操作名稱** | **操作名稱** | 作業名稱。 這個值可以是下列任一作業：Create、Update、Read、ReadFeed、Delete、Replace、Execute、SqlQuery、Query、JSQuery、Head、HeadFeed 或 Upsert。   |
| **性能** | n/a | 此欄位的內容說明於下列資料列中。 |
| **活動Id** | **activityId_g** | 所記錄作業的唯一 GUID。 |
| **使用者代理** | **userAgent_s** | 此字串指定執行要求的用戶端使用者代理程式。 格式為 {使用者代理程式名稱}/{版本}。|
| **請求資源類型** | **requestResourceType_s** | 存取的資源類型。 這個值可以是下列任一資源類型：Database、Container、Document、Attachment、User、Permission、StoredProcedure、Trigger、UserDefinedFunction 或 Offer。 |
| **statusCode** | **statusCode_s** | 作業的回應狀態。 |
| **requestResourceId** | **資源 Id** | 關於要求的 resourceId。 根據執行的作業，此值可能表示 databaseRid、collectionRid 或 documentRid。|
| **clientIpAddress** | **clientIpAddress_s** | 用戶端的 IP 位址。 |
| **requestCharge** | **requestCharge_s** | 作業使用的 RU 數 |
| **collectionRid** | **collectionId_s** | 集合的唯一識別碼。|
| **時間** | **duration_s** | 操作的持續時間（以毫秒為單位）。 |
| **requestLength** | **requestLength_s** | 以位元組為單位的要求長度。 |
| **responseLength** | **responseLength_s** | 以位元組為單位的回應長度。|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | 使用[資源權杖](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens)進行驗證時，此值為非空白值。 此值表示使用者的資源識別碼。 |

有關所有 Azure 監視器日誌類別和指向關聯架構的連結的清單，請參閱[Azure 監視器日誌類別和架構](../azure-monitor/platform/diagnostic-logs-schema.md)。 

## <a name="metrics"></a>計量
下表列出了為 Azure CosmOS DB 收集的平臺指標。 所有指標都存儲在命名空間**Cosmos DB 標準指標中**。

有關所有 Azure 監視器支援指標（包括 CosmosDB）的清單，請參閱[Azure 監視器支援的指標](../azure-monitor/platform/metrics-supported.md)。 

#### <a name="request-metrics"></a>要求計量
            
|指標（指標顯示名稱）|單位（聚合類型） |描述|維度| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---| ---|
| 請求總數（請求總數） | 計數（計數） | 進行的要求數目| DatabaseName、CollectionName、Region、StatusCode| 全部 | TotalRequests、Http 2xx、Http 3xx、Http 400、Http 401、內部伺服器錯誤、可用的服務、已節流的要求、平均每秒鐘要求數 | 用於監視每個狀態碼的請求，容器細微性為分鐘。 若要取得平均每秒要求數，請使用每分鐘 Count 彙總並除以 60。 |
| 中繼資料請求（中繼資料請求） |計數（計數） | 中繼資料要求計數。 Azure Cosmos DB 為每個帳戶維護系統中繼資料容器，允許您免費枚舉集合、資料庫等及其配置。 | DatabaseName、CollectionName、Region、StatusCode| 全部| |用來依中繼資料要求而監視節流。|
| 蒙戈請求（蒙戈請求） | 計數（計數） | 已提出的 Mongo 要求數目 | DatabaseName、CollectionName、Region、CommandName、ErrorCode| 全部 |Mongo 查詢要求率、Mongo 更新要求率、Mongo 刪除要求率、Mongo 插入要求率，Mongo 計數要求速率| 用於監視 Mongo 要求錯誤、各命令類型的使用量。 |

#### <a name="request-unit-metrics"></a>要求單位計量

|指標（指標顯示名稱）|單位（聚合類型）|描述|維度| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---| ---|
| Mongo請求收費（蒙戈請求費用） | 計數（總計） |已使用的 Mongo 要求單位| DatabaseName、CollectionName、Region、CommandName、ErrorCode| 全部 |Mongo 查詢要求費用、Mongo 更新要求費用、Mongo 刪除要求費用、Mongo 插入要求費用，Mongo 計數要求費用| 用來監視在一分鐘內的 Mongo 資源 RU。|
| 請求單位總數（請求單位總數）| 計數（總計） | 已使用的要求單位| DatabaseName、CollectionName、Region、StatusCode |全部| TotalRequestUnits| 用來監視每分鐘資料粒度的總 RU 使用量。 若要取得平均每秒耗用的 RU，請使用每分鐘 Total 彙總並除以 60。|
| 預配輸送量（預配輸送量）| 計數（最大值） |按容器細微性預配輸送量| 資料庫名稱，容器名稱| 5M| | 用於監視每個容器的預配輸送量。|

#### <a name="storage-metrics"></a>儲存體度量

|指標（指標顯示名稱）|單位（聚合類型）|描述|維度| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---| ---|
| 可用存儲（可用存儲） |位元組（總計） | 按每個區域 5 分鐘細微性報告的可用存儲總數| DatabaseName、CollectionName、Region| 5M| 可用的儲存體| 用於監視可用的儲存體容量 (僅適用於固定的儲存體集合) 最小資料粒度應為 5 分鐘。| 
| 資料使用（資料使用） |位元組（總計） |按每個區域 5 分鐘細微性報告的總數據使用量| DatabaseName、CollectionName、Region| 5M |資料大小 | 用於監視容器和地區的總數據使用方式，最小細微性應為 5 分鐘。|
| 索引使用（索引使用） | 位元組（總計） |按每個區域 5 分鐘細微性報告的索引總使用量| DatabaseName、CollectionName、Region| 5M| 索引大小| 用於監視容器和地區的總數據使用方式，最小細微性應為 5 分鐘。 |
| 文檔配額（文檔配額） | 位元組（總計） | 以每個區域 5 分鐘的細微性報告的總存儲配額。| DatabaseName、CollectionName、Region| 5M |儲存體容量| 用於監視容器和地區的總配額，最小細微性應為 5 分鐘。|
| 文檔計數（文檔計數） | 計數（總計） |按每個區域 5 分鐘細微性報告的文檔計數總數| DatabaseName、CollectionName、Region| 5M |文件計數|用於監視容器和地區的文檔計數，最小細微性應為 5 分鐘。|

#### <a name="latency-metrics"></a>延遲計量

|指標（指標顯示名稱）|單位（聚合類型）|描述|維度| 時間資料粒度| 使用量 |
|---|---|---|---| ---| ---|
| 複寫延遲（複寫延遲）| 毫秒（最小、最大、平均） | 異地複寫啟用的帳戶其跨來源和目標區域的 P99 複寫延遲| SourceRegion、TargetRegion| 全部 | 用來監視異地複寫的帳戶其任兩個區域之間的 P99 複寫延遲。 |
| 伺服器端延遲| 毫秒（平均） | 伺服器處理請求所花時間。 | 集合名稱、連接模式、資料庫名稱、操作類型、公共 API 類型、區域 | 全部 | 用於監視 Azure Cosmos DB 伺服器上的請求延遲。 |



#### <a name="availability-metrics"></a>可用性度量

|指標（指標顯示名稱） |單位（聚合類型）|描述| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---|
| 服務可用性（服務可用性）| 百分比（最小，最大） | 一小時資料粒度的帳戶要求可用性| 1H | 服務可用性 | 表示已傳遞請求總數的百分比。 如果狀態碼為 410、500 或 503，則要求會因為系統錯誤而視為失敗。用於以小時資料粒度監視帳戶的可用性。 |


#### <a name="cassandra-api-metrics"></a>Cassandra API 計量

|指標（指標顯示名稱）|單位（聚合類型）|描述|維度| 時間資料粒度| 使用量 |
|---|---|---|---| ---| ---|
| 卡珊多拉請求（卡珊多拉請求） | 計數（計數） | 已提出的 Cassandra API 要求數目| DatabaseName、CollectionName、ErrorCode、Region、OperationType、ResourceType| 全部| 用於監視每分鐘資料粒度的 Cassandra 要求數。 若要取得平均每秒要求數，請使用每分鐘 Count 彙總並除以 60。|
| 卡珊多拉請求費用（卡珊多拉請求收費） | 計數（總和、最小、最大、平均） | Cassandra API 要求所耗用的要求單位| DatabaseName、CollectionName、Region、OperationType、ResourceType| 全部| 用來監視 Cassandra API 帳戶每分鐘所使用的 RU 數。|
| 卡珊多拉連接關閉（卡珊多拉連接關閉） |計數（計數） |已終止的 Cassandra 連線數目| ClosureReason、Region| 全部 | 用於監視用戶端與 Azure Cosmos DB Cassandra API 之間的連線。|

## <a name="see-also"></a>另請參閱

- 有關監視 Azure 宇宙 DB 的說明，請參閱[監視 Azure 宇宙 DB。](monitor-cosmos-db.md)
- 有關監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器監視 Azure 資源](../azure-monitor/insights/monitor-azure-resource.md)。
