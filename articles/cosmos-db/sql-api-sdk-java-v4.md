---
title: Azure Cosmos DB Java SDK v4 for SQL API：版本資訊與資源
description: 了解所有 Azure Cosmos DB Java SDK v4 for SQL API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos DB SQL Async Java SDK 每個版本之間所做的變更。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: af0964dceca8b862d0008d878045203983a96bda
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586210"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core (SQL) API：版本資訊與資源
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

Azure Cosmos DB Java SDK v4 for Core (SQL) 會將非同步 API 與同步 API 結合成一個 Maven 成品。 V4 SDK 會根據 Project Reactor 和 [Netty 程式庫](https://netty.io/)，提供增強的效能、新的 API 功能和非同步支援。 使用者可以預期 Azure Cosmos DB Java SDK v4 與 [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) 和 [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md) 的改善效能。

> [!IMPORTANT]  
> 這些版本資訊僅適用於 Azure Cosmos DB Java SDK v4。 如果您目前使用的版本比 v4 舊，請參閱[遷移至 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南，以取得升級至 v4 的協助。
>
> 以下是快速開始進行的三個步驟！
> 1. 安裝[最低支援的 JAVA 執行階段，JDK 8](/java/azure/jdk/?view=azure-java-stable)，以便您可以使用 SDK。
> 2. 請逐步執行 [Azure Cosmos DB Java SDK v4 的快速入門手冊](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java)，以取得 Maven 成品的存取權，並逐步解說 Azure Cosmos DB 的基本要求。
> 3. 閱讀 Azure Cosmos DB Java SDK v4 [效能秘訣](performance-tips-java-sdk-v4-sql.md)和[疑難排解](troubleshoot-java-sdk-v4-sql.md)指南，以最佳化應用程式的 SDK。
>
> [Azure Cosmos DB 的研討會和實驗室](https://aka.ms/cosmosworkshop)是了解如何使用 Azure Cosmos DB Java SDK v4 的另一個絕佳資源！
>

## <a name="helpful-content"></a>有用的內容

| 內容 | 連結 |
|---|---|
|**SDK 下載**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API 文件** | [Java API 參考文件](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**參與 SDK** | [GitHub 上的 Azure SDK for Java 中央存放庫](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**開始使用** | [快速入門：建置 JAVA 應用程式來管理 Azure Cosmos DB SQL API 資料](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) <br> [具有快速入門程式碼的 GitHub 存放庫](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**基本程式碼範例** | [Azure Cosmos DB：適用於 SQL API 的 Java 範例](sql-api-java-sdk-samples.md) <br> [包含範例程式碼的 GitHub 存放庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**具有變更摘要的主控台應用程式**| [變更摘要-JAVA SDK v4 範例](create-sql-api-java-changefeed.md) <br> [包含範例程式碼的 GitHub 存放庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Web 應用程式範例**| [使用 JAVA SDK v4 建立 web 應用程式](sql-api-java-application.md) <br> [包含範例程式碼的 GitHub 存放庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **效能秘訣**| [Java SDK v4 的效能秘訣](performance-tips-java-sdk-v4-sql.md)| 
| **疑難排解** | [針對 Java SDK v4 進行疑難排解](troubleshoot-java-sdk-v4-sql.md) |
| **從舊版 SDK 遷移至 v4** | [遷移至 JAVA V4 SDK](migrate-java-v4-sdk.md) |
| **最低支援執行階段**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB 研討會與實驗室** |[Cosmos DB 研討會首頁](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>版本歷程記錄

### <a name="440-beta1-unreleased"></a>4.4.0-Beta. 1 (未發行) 

### <a name="430-2020-07-29"></a>4.3.0 (2020-07-29) 
#### <a name="new-features"></a>新功能
* 已將 reactor 核心程式庫版本更新為 `3.3.8.RELEASE` 。 
* 已將 reactor-netty 程式庫版本更新為 `0.9.10.RELEASE` 。 
* 已將 netty 程式庫版本更新為 `4.1.51.Final` 。 
* 已加入的新多載 Api `upsertItem` `partitionKey` 。 
* 已新增開啟的遙測追蹤支援。 
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 已修正 SSLException 在閘道模式中取消要求時擲回的問題。
* 已修正預存程式執行的資源節流重試原則。
* 修正了 SDK 在記錄層級偵測模式中停止回應的問題。 
* 修正直接模式延遲的定期尖峰。 
* 修正高用戶端初始化時間問題。 
* 修正了使用直接模式和閘道模式自訂用戶端時的 HTTP proxy 錯誤。 
* 已修正使用者傳遞 null 選項的潛在 NPE。 
* 已 `requestLatency` 在診斷字串中新增 timeUnit。
* 已從診斷字串中移除重複的 uri 字串。 
* 修正適用于 point 作業的診斷字串（適用于正確的 JSON 格式）。
* 修正因找 `.single()` 不到例外狀況而導致 reactor 鏈出現的運算子問題。 

### <a name="420-2020-07-14"></a>4.2.0 (2020-07-14) 
#### <a name="new-features"></a>新功能
* 已將啟用腳本記錄的 API 新增至 `CosmosStoredProcedureRequestOptions` 。
* 已 `DirectConnectionConfig` 將預設值更新 `idleEndpointTimeout` 為1h，預設值 `connectTimeout` 為5秒。
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 已修正覆寫的問題 `GatewayConnectionConfig` `idleConnectionTimeout` `DirectConnectionConfig` `idleConnectionTimeout` 。
* 修正 `responseContinuationTokenLimitInKb` 中的 get 和 Set api `CosmosQueryRequestOptions` 。
* 修正了使用相同名稱重新建立集合時，查詢和變更摘要中的問題。
* 修正 top 查詢擲回發生 classcastexception 的問題。
* 修正了 order by 查詢擲回發生 nullpointerexception 的問題。
* 修正在直接模式中處理已取消要求的問題，導致 `onErrorDropped` 呼叫 reactor。 

### <a name="410-2020-06-25"></a>4.1.0 (2020-06-25) 
#### <a name="new-features"></a>新功能
* 已新增 `GROUP BY` 查詢支援。
* 在 DirectConnectionConfig 中，將 maxConnectionsPerEndpoint 的預設值增加為130。
* 在 DirectConnectionConfig 中，將 maxRequestsPerConnection 的預設值增加為30。
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正了 order by 查詢在使用接續 token 繼續進行時，傳回重複結果的問題。 
* 修正了值查詢傳回嵌套物件之 null 值的問題。
* 修正 RntbdClientChannelPool 中要求管理員的 null 指標例外狀況。

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10) 
#### <a name="new-features"></a>新功能
* 已將 `QueryRequestOptions` 重新命名為 `CosmosQueryRequestOptions`。
* 已更新 `ChangeFeedProcessorBuilder` 為 builder 模式。
* 已更新 `CosmosPermissionProperties` 新的容器名稱和子資源 api。
* 已將更多範例 & 擴充的檔新增至 `CosmosClientBuilder` 。 
* 已更新 `CosmosDatabase`  &  `CosmosContainer` throughputProperties 的 api，以提供自動調整/autopilot 支援。 
* 已將 `CosmosClientException` 重新命名為 `CosmosException`。 
* `AccessCondition`  &  `AccessConditionType` 由 `ifMatchETag()`  &  `ifNoneMatchETag()` api 取代。 
* 將所有 `Cosmos*AsyncResponse`  &  `CosmosResponse` 類型合併為單一 `CosmosResponse` 類型。
* 已將 `CosmosResponseDiagnostics` 重新命名為 `CosmosDiagnostics`。  
* 包裝 `FeedResponseDiagnostics` 在中 `CosmosDiagnostics` 。 
* 已移除 `jackson` azure cosmos & 依賴 azure 核心的相依性。 
* 取代 `CosmosKeyCredential` 為 `AzureKeyCredential` 類型。 
* 已 `ProxyOptions` 將 api 新增至 `GatewayConnectionConfig` 。 
* 已將 SDK 更新為使用 `Instant` 類型，而不是 `OffsetDateTime` 。 
* 已加入新的列舉類型 `OperationKind` 。 
* 已將 `FeedOptions` 重新命名為 `QueryRequestOptions`。 
* `getETag()`  &  `getTimestamp()` 將 api 新增至 `Cosmos*Properties` 類型。 
* `userAgent`中的新增資訊 `CosmosException`  &  `CosmosDiagnostics` 。 
* 已將新的行字元更新 `Diagnostics` 為系統換行字元。 
* 已移除 `readAll*` api，請使用查詢來選取所有 api。
* 已新增 `ChangeFeedProcessor` 估價延遲 API。   
* 已將自動調整/autopilot 輸送量布建支援新增至 SDK。  
* `ConnectionPolicy`以新的連線進行取代。 `DirectConnectionConfig`  &  `GatewayConnectionConfig` 透過 `CosmosClientBuilder` 直接 & 閘道模式連線設定來公開 api。
* 已移 `JsonSerializable`  &  `Resource` 至 [執行封裝]。 
* 已 `contentResponseOnWriteEnabled` 將 API 新增至 CosmosClientBuilder，以停用寫入作業的完整回應內容。
* 公開 `getETag()` api 的回應類型。
* 已移 `CosmosAuthorizationTokenResolver` 至 [執行]。 
* `preferredLocations`  &  `multipleWriteLocations` 將 API 重新命名為 `preferredRegions`  &  `multipleWriteRegions` 。 
* 已更新 `reactor-core` 為3.3.5， `reactor-netty` 至0.9.7 版. release & `netty` 到 4.1.49. 最終版本。 
* 已新增 SDK 的支援 `analyticalStoreTimeToLive` 。     
* `CosmosClientException` 擴充 `AzureException` 。 
* `maxItemCount`  &  `requestContinuationToken` 改為從 `FeedOptions` 使用中的 api 移除 api `byPage()` `CosmosPagedFlux`  &  `CosmosPagedIterable` 。
* `CosmosPermissionProperties`在公用介面上引進 `Permission` api。
* 移除 `SqlParameterList` 的類型 & 取代為 `List`
* 修正了直接 TCP 用戶端中的多個記憶體流失。 
* 已新增 `DISTINCT` 查詢支援。 
* 已移除外部相依性 `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` 。  
* 已移 `CosmosPagedFlux`  &  `CosmosPagedIterable` 至 `utils` 套件。 
* 已將 netty 更新為4.1.45。最終 & 專案 reactor 至3.3.3 版本。
* 已將公用 rest 合約更新為 `Final` 類別。
* 已新增對點作業之 advanced Diagnostics 的支援。
* 已將封裝更新為 `com.azure.cosmos`
* 已新增 `models` 模型/rest 合約的套件
* 已新增 `utils` 類型的封裝 `CosmosPagedFlux`  &  `CosmosPagedIterable` 。 
* 已更新公用 Api 以 `Duration` 在 SDK 中使用。
* 已將所有 rest 合約新增至 `models` 套件。
* `RetryOptions` 已重新命名為 `ThrottlingRetryOptions`。
* 已新增 `CosmosPagedFlux`  &  `CosmosPagedIterable` 查詢 api 的分頁類型。 
* 已新增在中使用新的 API 跨多個 CosmosClients 實例共用 TransportClient 的支援 `CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* 藉由移除雙重序列化/還原序列化來查詢優化。 
* 藉由移除不必要的來回複製來優化回應標頭。 
* 藉 `ByteBuffer` 由移除中繼字串具現化來優化序列化/還原序列化。

#### <a name="key-bug-fixes"></a>重要 bug 修正
* Fixed ConnectionPolicy `toString()` Null 指標例外狀況。
* 修正了查詢結果在依查詢值順序的情況下，剖析查詢結果的問題。 
* 已修正直接 TCP 用戶端的通訊端洩漏問題。
* 修正 `orderByQuery` 接續 token bug。
* `ChangeFeedProcessor` 在找不到分割區時，修正處理分割區分割 & 的錯誤。
* `ChangeFeedProcessor` 在跨不同執行緒同步處理租用更新時的 bug 修正。
* 修正 `ArrayIndexOutOfBound` 在 StoreReader 中造成例外狀況的競爭情形

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>後續步驟
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。