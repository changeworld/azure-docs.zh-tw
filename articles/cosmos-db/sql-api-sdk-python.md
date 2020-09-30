---
title: Azure Cosmos DB SQL Python API、SDK 和資源
description: 了解所有 SQL Python API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos DB Python SDK 每個版本之間所做的變更。
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-python
ms.openlocfilehash: 03c8f5acfc10738401f61de099f946c33497d705
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569812"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>適用於 SQL API 的 Azure Cosmos DB Python SDK：版本資訊和資源

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 變更摘要 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [非同步 Java SDK v2](sql-api-sdk-async-java.md)
> * [同步 Java SDK v2](sql-api-sdk-java.md)
> * [春季資料 v2](sql-api-sdk-java-spring-v2.md)
> * [春季資料 v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 連接器](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 資源提供者](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [大量執行程式-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [大量執行程式 - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**下載 SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API 文件**|[Python API 參考文件](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python&preserve-view=true)|
|**SDK 安裝指示**|[SDK 安裝指示](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**開始使用**|[開始使用 Python SDK](create-sql-api-python.md)|
|**目前支援的平台**|[Python 2.7](https://www.python.org/downloads/) 和 [Python 3.5.3+](https://www.python.org/downloads/)|

## <a name="release-history"></a>版本歷程記錄

### <a name="410-2020-08-10"></a>4.1.0 (2020-08-10) 

- 已新增「延遲」索引模式的淘汰警告。 後端不再允許使用此模式建立容器，而是將它們設定為一致。

**新功能**
- 已新增在建立新容器時設定分析儲存體 TTL 的功能。

**Bug 修正**
- 已將 dicts 的支援修正為 get_client Api 的輸入。
- 修正查詢反覆運算器中的 Python 2/3 相容性。
- Fixed type 提示錯誤 (#12570) 的問題。
- 修正了選項標頭未新增至 upsert_item 函式的 bug。 問題 #11791-感謝您 @aalapatirvbd 。
- 修正在專案中使用非字串識別碼時引發的錯誤。 它現在會引發 TypeError，而非 AttributeError (#11793) 的問題。

### <a name="400"></a>4.0.0

* 穩定版本。
* 已將 HttpLoggingPolicy 新增至管線，以針對要求和回應標頭傳入自訂記錄器。

### <a name="400b6"></a>4.0.0b6

* 已修正媒體 API synchronized_request 中的錯誤。
* 已從 ConnectionPolicy 移除 MediaReadMode 和 MediaRequestTimeout，因為媒體要求不受支援。

### <a name="400b5"></a>4.0.0b5

* azure.cosmos.errors 模組已淘汰，由 azure.cosmos.exceptions 取代
* 存取條件參數 (`access_condition`、`if_match`、`if_none_match`) 已淘汰，改用個別的 `match_condition` 和 `etag` 參數。
* 已修正路由對應提供者中的錯誤。
* 已新增查詢 Distinct、Offset 和 Limit 支援。
* 預設文件查詢執行內容現在用於

  * ChangeFeed 查詢
  * 單一分割區查詢 (partitionkey、partitionKeyRangeId 存在於選項中)
  * 非文件查詢

* 多個分割區上的彙總發生錯誤，啟用跨分割區查詢設定為 true，但是沒有 "value" 關鍵字存在
* 針對其他案例叫用查詢計劃端點以提取查詢計劃
* 已新增 Cosmos 實體物件的 `__repr__` 支援。
* 更新文件。

### <a name="400b4"></a>4.0.0b4

* 已將 `timeout` 關鍵字引數的支援新增至所有作業，以指定必須完成作業的絕對逾時 (以秒為單位)。 如果超過逾時值，將會引發 `azure.cosmos.errors.CosmosClientTimeoutError`。

* 已新增新的 `ConnectionRetryPolicy` 來管理 HTTP 連線錯誤期間的重試行為。

* 已新增新的建構函式和每個作業的設定關鍵字引數：

  * `retry_total` - 重試次數上限。
  * `retry_backoff_max` - 最大重試等候時間 (秒)。
  * `retry_fixed_interval` - 固定重試間隔 (毫秒)。
  * `retry_read` - 通訊端讀取重試嘗試的次數上限。
  * `retry_connect` - 連線錯誤重試嘗試的次數上限。
  * `retry_status` - 錯誤狀態碼重試嘗試的次數上限。
  * `retry_on_status_codes` - 要重試的特定狀態碼清單。
  * `retry_backoff_factor` - 用來計算重試嘗試之間等候時間的因素。

### <a name="400b3"></a>4.0.0b3

* 將 `create_database_if_not_exists()` 和 `create_container_if_not_exists` 功能分別新增至 CosmosClient 和資料庫。

### <a name="400b2"></a>4.0.0b2

* 在我們致力於建置符合 Python 語言最佳做法的用戶端程式庫過程中，版本 4.0.0 b2 是第二個反覆項目。

**重大變更**

* 用戶端連線已調整為使用 `azure.core.pipeline` 中定義的 HTTP 管線。

* 互動式物件現在已重新命名為 Proxy。 這包括：

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* 已更新 `CosmosClient` 的建構函式：

  * `auth` 參數已重新命名為 `credential`，現在將直接採用驗證類型。 這表示主鍵值、資源標記的字典，或可傳入的許可權清單。 不過，仍然支援舊的字典格式。

  * `connection_policy` 參數已設為僅限關鍵字的參數，但是在仍受支援時，原則的每個個別屬性現在可以當做明確關鍵字引數傳入：

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* 已將新的建構函式新增至 `CosmosClient`，以便透過從 Azure 入口網站擷取的連接字串來建立。

* 某些 `read_all` 作業已重新命名為 `list` 作業：

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* 採用 `request_options` 或 `feed_options` 參數的所有作業，都已移至僅限關鍵字的參數。 此外，雖然仍支援這些選項字典，但是字典中的每個個別選項現在都支援當做明確關鍵字引數。

* 錯誤階層現在繼承自 `azure.core.AzureError` ：

  * `HTTPFailure` 已重新命名為 `CosmosHttpResponseError`
  * `JSONParseFailure` 已移除並由 `azure.core.DecodeError` 取代
  * 已針對特定回應碼新增額外的錯誤：
    * 狀態 404 的 `CosmosResourceNotFoundError`
    * 狀態 409 的 `CosmosResourceExistsError`
    * 狀態 412 的 `CosmosAccessConditionFailedError`

* `CosmosClient` 現在可以在內容管理員中執行，以處理關閉用戶端連線。

* 可反復執行回應 (例如，查詢回應和列出回應) 現在是 `azure.core.paging.ItemPaged` 的類型。 `fetch_next_block` 方法已由 `by_page` 方法所存取的次要迭代器所取代。

### <a name="400b1"></a>4.0.0b1

在我們致力於建置符合 Python 語言最佳做法的方便使用用戶端程式庫過程中，版本 4.0.0b1 是第一個預覽。 如需相關詳細資訊，以及其他 Azure SDK 程式庫的預覽版本，請造訪 https://aka.ms/azure-sdk-preview1-python 。

**重大變更：新的 API 設計**

* 作業現在的範圍為特定用戶端：

  * `CosmosClient`:此用戶端會處理帳戶層級作業。 這包括管理服務屬性，以及列出帳戶內的資料庫。
  * `Database`:這個用戶端會處理資料庫層級作業。 這包括建立和刪除容器、使用者和預存程序。 您可以依名稱從 cosmosClient 的執行個體進行存取。
  * `Container`:此用戶端會處理特定容器的作業。 這包括查詢和插入項目及管理屬性。
  * `User`:此用戶端會處理特定使用者的作業。 這包括新增和刪除權限，以及管理使用者屬性。

    您可以使用 `get_<child>_client` 方法瀏覽用戶端階層，以存取這些用戶端。 如需新 API 的完整詳細資料，請參閱[參考文件](https://aka.ms/azsdk-python-cosmos-ref)。

* 用戶端是依名稱存取，而不是依識別碼。 不需要串連字串來建立連結。

* 不需要再從個別模組匯入類型和方法。 公用 API 介面區可直接在 `azure.cosmos` 套件中取得。

* 個別要求屬性可以當做關鍵字引數提供，而不是建構不同的 `RequestOptions` 執行個體。

### <a name="302"></a>3.0.2

* 新增對 MultiPolygon 資料類型的支援
* 修正工作階段讀取重試原則中的錯誤 (bug)
* 修正在 Base 64 字串解碼期間造成錯誤填補問題的錯誤 (bug)

### <a name="301"></a>3.0.1

* 修正 LocationCache 中的錯誤 (bug)
* 修正端點重試邏輯的錯誤 (bug)
* 修正文件

### <a name="300"></a>3.0.0

* 已新增多區域寫入支援
* 命名變更
  * DocumentClient 至 CosmosClient
  * 集合至容器
  * 文件至項目
  * 套件名稱已更新為 "azure-cosmos"
  * 命名空間已更新為 "azure.cosmos"

### <a name="233"></a>2.3.3

* 新增對 Proxy 的支援
* 新增對讀取變更摘要的支援
* 新增對集合配額標頭的支援
* 修正造成大型工作階段權杖問題的錯誤 (bug)
* 修正 ReadMedia API 的錯誤 (bug)
* 修正分割區索引鍵範圍快取中的錯誤 (bug)

### <a name="232"></a>2.3.2

* 已新增對連線問題預設重試次數的支援。

### <a name="231"></a>2.3.1

* 更新參考 Azure Cosmos DB 而非 Azure DocumentDB 的文件。

### <a name="230"></a>2.3.0

* 此 SDK 版本需要使用從 https://aka.ms/cosmosdb-emulator 下載之最新版本的 Azure Cosmos DB 模擬器。

### <a name="221"></a>2.2.1

* 彙總 dict 的錯誤修正
* 資源連結中的修剪斜線錯誤修正
* Unicode 編碼的測試

### <a name="220"></a>2.2.0

* 已新增「每分鐘的要求單位 (RU/m)」功能支援。
* 已新增對名為 ConsistentPrefix 的新一致性層級的支援。

### <a name="210"></a>2.1.0

* 新增彙總查詢的支援 (COUNT、MIN、MAX、SUM 和 AVG)。
* 新增針對 DocumentDB 模擬器執行時停用 SSL 驗證的選項。
* 移除相依要求模組必須為 2.10.0 的限制。
* 已將分割區集合的最小輸送量從 10,100 RU/s 降低為 2500 RU/s。
* 在預存程序執行期間，加入支援指令碼記錄功能。
* REST API 版本在此版本中提升至 '2017-01-19'。

### <a name="201"></a>2.0.1

* 對文件註解進行編輯性的變更。

### <a name="200"></a>2.0.0

* 新增 Python 3.5 的支援。
* 新增使用要求模組的連線集區支援。
* 新增工作階段一致性的支援。
* 新增對已分割集合的 TOP/ORDERBY 查詢支援。

### <a name="190"></a>1.9.0

* 新加入已節流處理要求的重試原則支援。 (已節流處理的要求會收到要求率太大的例外狀況，即錯誤碼 429。)根據預設，發生錯誤碼 429 時，DocumentDB 會遵守回應標頭中的 retryAfter 時間，並針對每個要求重試九次。
  如果您想要忽略伺服器在多次重試之間傳回的 retryAfter 時間，現在可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中設定固定的重試間隔時間。
  DocumentDB 現在會針對每個節流處理中的要求等候最多 30 秒 (不論重試計數為何)，並傳回包含錯誤碼 429 的回應。
  您也可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中覆寫該時間。

* DocumentDB 現在會傳回 x-ms-throttle-retry-count 和 x-ms-throttle-retry-wait-time-ms 做為每個要求的回應標頭，其代表節流重試計數和要求歷經多次重試的累積時間。

* 移除 RetryPolicy 類別和 document_client 類別上公開的對應屬性 (retry_policy)，改為引進公開 ConnectionPolicy 類別上的 RetryOptions 屬性，它可以用來覆寫某些預設的重試選項。

### <a name="180"></a>1.8.0

* 新增對異地複寫資料庫帳戶的支援。
* 測試修正程式，將全域主機和 masterKey 移至個別的測試類別。

### <a name="170"></a>1.7.0

* 新加入文件的存留時間 (TTL) 功能支援。

### <a name="161"></a>1.6.1

* 伺服器端資料分割相關錯誤修正，允許在分割區索引鍵路徑中使用特殊字元。

### <a name="160"></a>1.6.0

* 已新增伺服器端資料分割集合功能的支援。

### <a name="150"></a>1.5.0

* 已將用戶端分區化架構新增至 SDK。 已實作 HashPartionResolver 和 RangePartitionResolver 類別。

### <a name="142"></a>1.4.2

* 實作 Upsert。 已新增新的 UpsertXXX 方法以支援 Upsert 功能。
* 實作以識別碼為基礎的路由。 不需變更公用 API，所有變更皆為內部變更。

### <a name="130"></a>1.3.0

* 已略過版本以配合其他 SDK 的版本號碼

### <a name="120"></a>1.2.0

* 支援地理空間索引。
* 驗證所有資源的識別碼屬性。 資源的識別碼不能包含 `?, /, #, \\` 字元，或以空格作為結尾。
* 將新標頭「索引轉換進度」加至 ResourceResponse。

### <a name="110"></a>1.1.0

* 實作 V2 索引原則

### <a name="101"></a>1.0.1

* 支援 Proxy 連線

## <a name="release--retirement-dates"></a>發行和停用日期

Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。 新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。

> [!WARNING]
> 2022年8月31日之後，Azure Cosmos DB 將不會再進行錯誤修正、新增功能，以及提供適用于 Azure Cosmos DB Python SDK for SQL API 1.x 或2.x 版的支援。 如果您不想要升級，Azure Cosmos DB 服務會繼續提供從1.x 版和2.x 版的 SDK 傳送的要求。

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [4.0.0](#400) |2020 年 5 月 20 日 |--- |
| [3.0.2](#302) |2018 年 11 月 15 日 |--- |
| [3.0.1](#301) |2018 年 10 月 4 日 |--- |
| [2.3.3](#233) |2018 年 9 月 8 日 |2020 年 8 月 30 日 |
| [2.3.2](#232) |2018 年 5 月 8 日 |2020 年 8 月 30 日 |
| [2.3.1](#231) |2017 年 12 月 21 日 |2020 年 8 月 30 日 |
| [2.3.0](#230) |2017 年 11 月 10 日 |2020 年 8 月 30 日 |
| [2.2.1](#221) |2017 年 9 月 29 日 |2020 年 8 月 30 日 |
| [2.2.0](#220) |2017 年 5 月 10 日 |2020 年 8 月 30 日 |
| [2.1.0](#210) |2017 年 5 月 1 日 |2020 年 8 月 30 日 |
| [2.0.1](#201) |2016 年 10 月 30 日 |2020 年 8 月 30 日 |
| [2.0.0](#200) |2016 年 9 月 29 日 |2020 年 8 月 30 日 |
| [1.9.0](#190) |2016 年 7 月 7 日 |2020 年 8 月 30 日 |
| [1.8.0](#180) |2016 年 6 月 14 日 |2020 年 8 月 30 日 |
| [1.7.0](#170) |2016 年 4 月 26 日 |2020 年 8 月 30 日 |
| [1.6.1](#161) |2016 年 4 月 8 日 |2020 年 8 月 30 日 |
| [1.6.0](#160) |2016 年 3 月 29 日 |2020 年 8 月 30 日 |
| [1.5.0](#150) |2016 年 1 月 3 日 |2020 年 8 月 30 日 |
| [1.4.2](#142) |2015 年 10 月 6 日 |2020 年 8 月 30 日 |
| 1.4.1 |2015 年 10 月 6 日 |2020 年 8 月 30 日 |
| [1.2.0](#120) |2015 年 8 月 6 日 |2020 年 8 月 30 日 |
| [1.1.0](#110) |2015 年 7 月 9 日 |2020 年 8 月 30 日 |
| [1.0.1](#101) |2015 年 5 月 25 日 |2020 年 8 月 30 日 |
| 1.0.0 |2015 年 4 月 7 日 |2020 年 8 月 30 日 |
| 0.9.4-prelease |2015 年 1 月 14 日 |2016 年 2 月 29 日 |
| 0.9.3-prelease |2014 年 12 月 9 日 |2016 年 2 月 29 日 |
| 0.9.2-prelease |2014 年 11 月 25 日 |2016 年 2 月 29 日 |
| 0.9.1-prelease |2014 年 9 月 23 日 |2016 年 2 月 29 日 |
| 0.9.0-prelease |2014 年 8 月 21 日 |2016 年 2 月 29 日 |

## <a name="faq"></a>常見問題集

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>後續步驟

若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。 
