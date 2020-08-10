---
title: Azure Cosmos DB Java SDK v4 for SQL API：版本資訊與資源
description: 了解所有 Azure Cosmos DB Java SDK v4 for SQL API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos DB SQL Async Java SDK 每個版本之間所做的變更。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 0d1845c06c1f0373ffd4be43c104889a7327c3ac
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035768"
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
> * [Spring Data](sql-api-sdk-java-spring.md)
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
|**開始使用** | [快速入門：建置 JAVA 應用程式來管理 Azure Cosmos DB SQL API 資料](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) <br> [含有快速入門程式碼的 GitHub 存放庫](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**基本程式碼範例** | [Azure Cosmos DB：適用於 SQL API 的 Java 範例](sql-api-java-sdk-samples.md) <br> [包含範例程式碼的 GitHub 存放庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**具有變更摘要的主控台應用程式**| [變更摘要-JAVA SDK v4 範例](create-sql-api-java-changefeed.md) <br> [包含範例程式碼的 GitHub 存放庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Web 應用程式範例**| [使用 JAVA SDK v4 建立 web 應用程式](sql-api-java-application.md) <br> [包含範例程式碼的 GitHub 存放庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **效能秘訣**| [Java SDK v4 的效能秘訣](performance-tips-java-sdk-v4-sql.md)| 
| **疑難排解** | [針對 Java SDK v4 進行疑難排解](troubleshoot-java-sdk-v4-sql.md) |
| **從舊版 SDK 遷移至 v4** | [遷移至 JAVA V4 SDK](migrate-java-v4-sdk.md) |
| **最低支援執行階段**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB 研討會與實驗室** |[Cosmos DB 研討會首頁](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>版本歷程記錄

### <a name="440-beta1-unreleased"></a>4.4.0-搶鮮版（Beta） 1 (建置) 

### <a name="430-2020-07-29"></a>4.3.0 (2020-07-29) 
#### <a name="new-features"></a>新功能
* 已將 reactor 核心程式庫版本更新為 `3.3.8.RELEASE` 。 
* 已將 reactor-netty 程式庫版本更新為 `0.9.10.RELEASE` 。 
* 已將 netty 程式庫版本更新為 `4.1.51.Final` 。 
* 已為新增新的多載 Api `upsertItem` `partitionKey` 。 
* 已新增開啟的遙測追蹤支援。 
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 已修正在閘道模式中取消要求時，會擲回 SSLException 的問題。
* 已修正預存程式執行的資源節流重試原則。
* 已修正 SDK 在記錄層級的 DEBUG 模式中停止回應的問題。 
* 已修正直接模式延遲的定期尖峰。 
* 已修正高用戶端初始化時間問題。 
* 已修正自訂具有直接模式和閘道模式的用戶端時的 HTTP proxy 錯誤。 
* 已修正使用者中的潛在 NPE 傳遞 null 選項。 
* 已 `requestLatency` 在診斷字串中將 timeUnit 新增至。
* 已從診斷字串中移除重複的 uri 字串。 
* 已修正適用于點作業的適當 JSON 格式的診斷字串。
* 已修正運算子的問題， `.single()` 導致 reactor 鏈在找不到例外狀況時出現。 

### <a name="420-2020-07-14"></a>4.2.0 (2020-07-14) 
#### <a name="new-features"></a>新功能
* 已將腳本記錄啟用的 API 新增至 `CosmosStoredProcedureRequestOptions` 。
* 已 `DirectConnectionConfig` 將預設值更新 `idleEndpointTimeout` 為1h，並預設 `connectTimeout` 為5秒。
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 已修正覆寫的問題 `GatewayConnectionConfig` `idleConnectionTimeout` `DirectConnectionConfig` `idleConnectionTimeout` 。
* 已修正 `responseContinuationTokenLimitInKb` 中的 get 和 Set api `CosmosQueryRequestOptions` 。
* 已修正重新建立具有相同名稱的集合時，查詢和變更摘要中的問題。
* 已修正 top 查詢擲回發生 classcastexception 的問題。
* 已修正 order by 查詢擲回發生 nullpointerexception 的問題。
* 已修正在直接模式中處理已取消要求而造成呼叫 reactor 的問題 `onErrorDropped` 。 

### <a name="410-2020-06-25"></a>4.1.0 (2020-06-25) 
#### <a name="new-features"></a>新功能
* 已新增查詢的支援 `GROUP BY` 。
* 在 DirectConnectionConfig 中，將 maxConnectionsPerEndpoint 的預設值增加為130。
* 在 DirectConnectionConfig 中，將 maxRequestsPerConnection 的預設值增加為30。
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 已修正使用接續 token 繼續時，order by 查詢傳回重複結果的問題。 
* 已修正值查詢針對嵌套物件傳回 null 值的問題。
* 已修正 RntbdClientChannelPool 中要求管理員的 null 指標例外狀況。

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10) 
#### <a name="new-features"></a>新功能
* 已將 `QueryRequestOptions` 重新命名為 `CosmosQueryRequestOptions`。
* 已更新 `ChangeFeedProcessorBuilder` 為 builder 模式。
* 已更新 `CosmosPermissionProperties` 新的容器名稱和子資源 api。
* 已將更多範例 & 擴充的檔新增至 `CosmosClientBuilder` 。 
* 已 `CosmosDatabase`  &  `CosmosContainer` 使用 throughputProperties 更新 api 以進行自動調整/autopilot 支援。 
* 已將 `CosmosClientException` 重新命名為 `CosmosException`。 
* 已 `AccessCondition`  &  `AccessConditionType` 由 `ifMatchETag()`  &  `ifNoneMatchETag()` api 取代。 
* 將所有 `Cosmos*AsyncResponse`  &  `CosmosResponse` 類型合併成單一 `CosmosResponse` 類型。
* 已將 `CosmosResponseDiagnostics` 重新命名為 `CosmosDiagnostics`。  
* 包裝 `FeedResponseDiagnostics` 在中 `CosmosDiagnostics` 。 
* 已 `jackson` 從 azure cosmos & 依賴 azure 核心來移除相依性。 
* 已取代 `CosmosKeyCredential` 為 `AzureKeyCredential` 類型。 
* 已 `ProxyOptions` 將 api 新增至 `GatewayConnectionConfig` 。 
* 已更新 SDK 以使用 `Instant` 類型，而不是 `OffsetDateTime` 。 
* 已加入新的列舉類型 `OperationKind` 。 
* 已將 `FeedOptions` 重新命名為 `QueryRequestOptions`。 
* 已 `getETag()`  &  `getTimestamp()` 將 api 新增至 `Cosmos*Properties` 類型。 
* 已 `userAgent` 在中新增資訊 `CosmosException`  &  `CosmosDiagnostics` 。 
* 已將中的新行字元更新 `Diagnostics` 為系統換行字元。 
* 已移除 `readAll*` api，請改用查詢選取所有 api。
* 已新增 `ChangeFeedProcessor` 估計延遲 API。   
* 已將自動調整/autopilot 輸送量布建支援新增至 SDK。  
* 已取代 `ConnectionPolicy` 為新的連線建立。 `DirectConnectionConfig`  &  `GatewayConnectionConfig` `CosmosClientBuilder` 針對直接 & 閘道模式連線設定，透過公開的 api。
* 已移 `JsonSerializable`  &  `Resource` 至執行封裝。 
* 已 `contentResponseOnWriteEnabled` 將 API 新增至 CosmosClientBuilder，這會停用寫入作業的完整回應內容。
* 已公開 `getETag()` 回應類型的 api。
* 已移 `CosmosAuthorizationTokenResolver` 至 [執行中]。 
* API 已重新命名 `preferredLocations`  &  `multipleWriteLocations` 為 `preferredRegions`  &  `multipleWriteRegions` 。 
* 已更新 `reactor-core` 為3.3.5， `reactor-netty` 0.9.7 版發行 & `netty` 至4.1.49。最終版本。 
* 已 `analyticalStoreTimeToLive` 在 SDK 中新增的支援。     
* `CosmosClientException`擴充 `AzureException` 。 
* 已 `maxItemCount`  &  `requestContinuationToken` 從移除 api `FeedOptions` ，改為使用 `byPage()` 來自的 api `CosmosPagedFlux`  &  `CosmosPagedIterable` 。
* `CosmosPermissionProperties`在 api 的公用介面上引進 `Permission` 。
* 移除 `SqlParameterList` 的類型 & 取代為`List`
* 已修正直接 TCP 用戶端中的多個記憶體流失。 
* 已新增查詢的支援 `DISTINCT` 。 
* 已移除的外部相依性 `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` 。  
* 已移 `CosmosPagedFlux`  &  `CosmosPagedIterable` 至 `utils` 套件。 
* 已將 netty 更新為4.1.45。最終 & 專案 reactor 至3.3.3 版本。
* 已將公用 rest 合約更新為 `Final` 類別。
* 已新增對點作業的先進診斷支援。
* 已將套件更新為`com.azure.cosmos`
* 已新增 `models` 模型/rest 合約的封裝
* 已新增 `utils` 類型的封裝 `CosmosPagedFlux`  &  `CosmosPagedIterable` 。 
* 已更新公用 Api 以在 `Duration` SDK 中使用。
* 已將所有 rest 合約新增至 `models` 封裝。
* `RetryOptions` 已重新命名為 `ThrottlingRetryOptions`。
* 已加入 `CosmosPagedFlux`  &  `CosmosPagedIterable` 查詢 api 的分頁類型。 
* 已新增在中使用新的 API，在多個 CosmosClients 實例間共用 TransportClient 的支援`CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* 藉由移除雙重序列化/還原序列化來查詢優化。 
* 回應標頭會藉由移除不必要的來回複製來進行優化。 
* 藉 `ByteBuffer` 由移除中繼字串具現化來優化序列化/還原序列化。

#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 已修正 ConnectionPolicy `toString()` Null 指標例外狀況。
* 已修正在值排序依據查詢的情況下，剖析查詢結果的問題。 
* 已修正直接 TCP 用戶端的通訊端流失問題。
* 已修正 `orderByQuery` 接續 token bug。
* `ChangeFeedProcessor`修正在找不到分割區時 & 處理分割區分割的 bug。
* `ChangeFeedProcessor`跨不同執行緒同步處理租用更新時的錯誤修正。
* 已修正競爭條件導致 `ArrayIndexOutOfBound` StoreReader 中發生例外狀況

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>後續步驟
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。