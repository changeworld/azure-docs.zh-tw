---
title: SQL API 版本資訊和資源的春季資料 Azure Cosmos DB
description: 深入瞭解 SQL API 的春天資料 Azure Cosmos DB，包括發行日期、停用日期，以及 Azure Cosmos DB SQL 非同步 JAVA SDK 每個版本之間所做的變更。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: f94ea37966c4942f72ae2d7b701e742891fa6cd2
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854209"
---
# <a name="spring-data-azure-cosmos-db-for-core-sql-api-release-notes-and-resources"></a>核心 (SQL) API 的春季資料 Azure Cosmos DB：版本資訊與資源
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

適用于 Core (SQL) 的春天資料 Azure Cosmos DB 可讓開發人員在春季應用程式中使用 Azure Cosmos DB。 彈簧資料 Azure Cosmos DB 會公開用於運算元據庫和集合、使用檔和發出查詢的彈簧資料介面。 相同的 Maven 成品支援同步處理和非同步 (回應) Api。 

「[春季架構](https://spring.io/projects/spring-framework)」是一種程式設計和設定模型，可簡化 JAVA 應用程式開發。 為報價組織的網站，春季使用相依性插入來簡化應用程式的「配管」。 許多開發人員都很像春季，因為建立和測試應用程式會變得更簡單。 [春季開機](https://spring.io/projects/spring-boot)延伸了此概念，可讓您密切注意 web 應用程式和微服務開發，以處理配量。 「[春季資料](https://spring.io/projects/spring-data)」是一種程式設計模型，可存取資料存放區，例如來自春季或春季開機應用程式內容的 Azure Cosmos DB。 

您可以在[Azure 春季雲端](https://azure.microsoft.com/services/spring-cloud/)應用程式中使用春季資料 Azure Cosmos DB。

> [!IMPORTANT]  
> 這些版本資訊僅適用于 Azure Cosmos DB SQL API。 
>
> 下列指南支援其他 Azure Cosmos DB Api 的春季資料：
> * [具有 Azure Cosmos DB 的 Apache Cassandra 的春季資料](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [具有 Azure Cosmos DB 的春天資料 MongoDB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Azure Cosmos DB 的彈簧資料 Gremlin](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> 想要快速進入嗎？
> 1. 安裝[最低支援的 JAVA 執行階段，JDK 8](/java/azure/jdk/?view=azure-java-stable)，以便您可以使用 SDK。
> 2. 使用「入門」 Azure Cosmos DB 應用程式建立春季資料，[這很簡單](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)！
> 3. 逐步解說「[春季資料 Azure Cosmos DB 開發人員指南](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)」，其中會逐步解說基本的 Azure Cosmos DB 要求。
>
> 您可以使用[春季 Initializr](https://start.spring.io/)快速啟動春季開機入門應用程式！
>

## <a name="helpful-content"></a>有用的內容

| 內容 | 連結 |
|---|---|
| **SDK 下載** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API 文件** | [春季資料 Azure Cosmos DB 參考檔]() |
|**參與 SDK** | [GitHub 上的春季資料 Azure Cosmos DB 存放庫](https://github.com/microsoft/spring-data-cosmosdb) | 
|**春季開機 starter**| [適用于 JAVA 的 Azure Cosmos DB 春天開機入門用戶端程式庫](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**具有 Azure Cosmos DB 的春季待辦事項應用程式範例**| [App Service Linux 中的端對端 JAVA 體驗 (第2部分) ](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**開發人員指南** | [春季資料 Azure Cosmos DB 開發人員指南](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**使用 starter 指南** | [如何搭配 Azure Cosmos DB SQL API 使用 Spring Boot Starter](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [適用于 Azure 春季開機的 GitHub 存放庫入門 Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**應用程式服務的範例** | [如何在 Linux 上的 App Service 中使用 Spring 和 Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [待辦事項應用程式範例](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>版本歷程記錄

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21) 
#### <a name="new-features"></a>新功能
* 將春季開機版本更新為2.3。0 
#### <a name="key-bug-fixes"></a>金鑰錯誤修正

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19) 
#### <a name="new-features"></a>新功能
* 已將 Azure Cosmos DB 版本更新為 v 3.7。3
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 包含來自 Cosmos SDK v 3.7.3 的記憶體遺漏修正和 netty 版本升級 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06) 
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 已修正要從 CosmosDbConfig 納入考慮的 allowTelemetry 旗標
* 容器上的固定 TTL 屬性

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25) 
#### <a name="new-features"></a>新功能
* 已新增 findAll by 資料分割索引鍵 API
* 已將 azure cosmos 版本更新為3.7。0
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 已修正 collectionName-> 容器型
* 已修正 entityClass & domainClass-> domainType
* 已修正「傳回儲存機制所儲存的實體集合，而不是輸入實體」

### <a name="2110-2020-02-25"></a>2.1.10 (2020-02-25) 
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* Backport 修正「傳回儲存機制所儲存的實體集合，而不是輸入實體」

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15) 
#### <a name="new-features"></a>新功能
* 已將 azure cosmos 版本更新為 v 3.6。0
#### <a name="key-bug-fixes"></a>金鑰錯誤修正

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31) 
#### <a name="new-features"></a>新功能
* 已將 Cosmos DB SDK 版本更新為3.5。0
* 已新增批註欄位，以啟用/停用自動建立集合
* 更好的例外狀況處理，透過 CosmosDBAccessException 公開 CosmosClientException
* 透過 ResponseDiagnostics 公開 requestCharge 和 activityId
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* SDK 3.5.0 更新修正「當 Cosmos DB HTTP 回應標頭大於8192個位元組」時，"ConsistencyPolicy. defaultConsistencyLevel ( # A1 在限定過期和一致前置詞上失敗」
* 已修正 findById Api 行為，在找不到時傳回空的，而不是擲回例外狀況
* 已修正使用 CosmosPageRequest 時，不會在下一頁套用排序的錯誤（bug）

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26) 
#### <a name="new-features"></a>新功能
* 已新增批註欄位，以啟用/停用自動建立集合
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 已修正 findById Api 行為，在找不到時傳回空的，而不是擲回例外狀況

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21) 
#### <a name="new-features"></a>新功能
* 完成回應 Cosmos 存放庫支援
* Cosmos DB 要求診斷字串和查詢計量支援
* Cosmos DB SDK 版本更新為3.3。1
* 春季 Framework 版本升級至5.2.0。發行
* 春季資料 Commons 版本升級至2.2.0。發行
* 已新增 findByIdAndPartitionKey、deleteByIdAndPartitionKey Api
* 已從 azure 移除相依性-doumentdb
* 將 DocumentDb 更名至 Cosmos
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 已修正「當 pageSize 小於存放庫中的專案總數」時，排序會擲回例外狀況

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18) 
#### <a name="new-features"></a>新功能
* 取代 Document DB Api
* 已新增 findByIdAndPartitionKey、deleteByIdAndPartitionKey Api。
* 已新增以 _etag 為基礎的開放式鎖定
* 已啟用檔集合名稱的 SPeL 運算式
* ObjectMapper 改進。
#### <a name="key-bug-fixes"></a>金鑰錯誤修正

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18) 
#### <a name="new-features"></a>新功能
* 已新增 Cosmos SDK v3 相依性
* 已新增回應式 Cosmos 存放庫
* 已更新 DocumentDbTemplate 的執行，以使用 Cosmos SDK v3。
* 被動 Cosmos 存放庫支援的其他設定變更。
#### <a name="key-bug-fixes"></a>金鑰錯誤修正

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19) 
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 移除的 applicationInsights 相依性
    * 相依性污染的潛在風險
    * JAVA 11 不相容
    * 避免對 CPU 和/或記憶體造成潛在的效能影響。

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20) 
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 將移除的 applicationInsights 相依性
    * 相依性污染的潛在風險
    * JAVA 11 不相容
    * 避免對 CPU 和/或記憶體造成潛在的效能影響。

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07) 
#### <a name="new-features"></a>新功能
* 將主要版本更新為2.1。1
#### <a name="key-bug-fixes"></a>金鑰錯誤修正

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07) 
#### <a name="new-features"></a>新功能
* 忽略遙測中的所有例外狀況
#### <a name="key-bug-fixes"></a>金鑰錯誤修正

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17) 
#### <a name="new-features"></a>新功能
* 將版本更新為2.1.0 以解決問題
#### <a name="key-bug-fixes"></a>金鑰錯誤修正

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13) 
#### <a name="new-features"></a>新功能
* Add 關鍵字 exists，startsWith
* 更新讀我檔案
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 修正「無法直接呼叫實體的自我 href」
* 修正「如果未建立收集，findAll 會失敗」

### <a name="204-pre-release-2018-08-23"></a>2.0.4 版 (發行前版本)  (2018-08-23) 
#### <a name="new-features"></a>新功能
* 將套件從 documentdb 重新命名為 cosmosdb，
* 新增查詢方法關鍵字的新功能，支援來自 Sql API 的16個關鍵字。
* 使用分頁和排序來加入查詢的新功能。
* 簡化春季資料 cosmosdb 的設定。
* 新增 deleteCollection 和 deleteAll API。

#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* Bug 修正和瑕疵增強功能。

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>後續步驟
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。

若要深入瞭解春季架構，請參閱[專案首頁](https://spring.io/projects/spring-framework)。

若要深入瞭解春季開機，請參閱[專案首頁](https://spring.io/projects/spring-boot)。

若要深入瞭解春季資料，請參閱[專案首頁](https://spring.io/projects/spring-data)。