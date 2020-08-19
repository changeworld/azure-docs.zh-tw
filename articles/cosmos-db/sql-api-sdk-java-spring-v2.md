---
title: SQL API 版本資訊和資源的春季資料 Azure Cosmos DB v2
description: 深入瞭解 SQL API 的春季資料 Azure Cosmos DB v2，包括發行日期、停用日期，以及每個 Azure Cosmos DB SQL Async JAVA SDK 版本之間所做的變更。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 47d9a8ff884e29dc5692c97d5e7867a856d01063
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590590"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>適用于 Core (SQL) API 的春季資料 Azure Cosmos DB v2：版本資訊和資源
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

適用于 Core (SQL) 的春季資料 Azure Cosmos DB v2，可讓開發人員在春季應用程式中使用 Azure Cosmos DB。 彈簧資料 Azure Cosmos DB 會公開用於運算元據庫和集合、使用檔和發出查詢的彈簧資料介面。 在相同的 Maven 成品中，同步處理和非同步 (回應) Api 都受到支援。 

[春季架構](https://spring.io/projects/spring-framework)是一種可簡化 JAVA 應用程式開發的程式設計和設定模型。 若要為組織的網站加上引號，請使用相依性插入，簡化應用程式的「配管」。 許多像是春天的開發人員，因為組建和測試應用程式變得更簡單。 [春季 Boot](https://spring.io/projects/spring-boot) 擴充了這種處理常式，並留意 web 應用程式和微服務開發的概念。 [彈簧資料](https://spring.io/projects/spring-data) 是從春季或春季 Boot 應用程式的內容存取資料存放區（例如 Azure Cosmos DB）的程式設計模型。 

您可以在 [Azure 春季雲端](https://azure.microsoft.com/services/spring-cloud/) 應用程式中使用春季資料 Azure Cosmos DB。

> [!IMPORTANT]  
> 這些版本資訊適用于第2版的春季資料 Azure Cosmos DB。 您可以在 [這裡](sql-api-sdk-java-spring-v3.md)找到 v3 版本資訊。 
>
> 彈簧資料 Azure Cosmos DB 僅支援 SQL API。
>
> 下列指南支援其他 Azure Cosmos DB Api 上的春季資料：
> * [使用 Azure Cosmos DB 的 Apache Cassandra 的彈簧資料](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [具有 Azure Cosmos DB 的春季資料 MongoDB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [使用 Azure Cosmos DB 的彈簧資料 Gremlin](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> 想要快速開始嗎？
> 1. 安裝[最低支援的 JAVA 執行階段，JDK 8](/java/azure/jdk/?view=azure-java-stable)，以便您可以使用 SDK。
> 2. 使用 starter Azure Cosmos DB 應用程式建立春季資料-很 [簡單](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)！
> 3. 逐步解說 [資料 Azure Cosmos DB 開發人員指南](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) ，其中會逐步解說基本的 Azure Cosmos DB 要求。
>
> 您可以使用 [春季 Initializr](https://start.spring.io/)快速啟動春季 Boot Starter 應用程式！
>

## <a name="helpful-content"></a>有用的內容

| 內容 | 連結 |
|---|---|
| **SDK 下載** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API 文件** | [彈簧資料 Azure Cosmos DB 參考檔]() |
|**參與 SDK** | [GitHub 上的春季資料 Azure Cosmos DB 存放庫](https://github.com/microsoft/spring-data-cosmosdb) | 
|**春季開機 starter**| [適用于 JAVA 的 Azure Cosmos DB 春季 Boot Starter 用戶端程式庫](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**使用 Azure Cosmos DB 的春季 TODO 應用程式範例**| [App Service Linux 的端對端 JAVA 體驗 (第2部分) ](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**開發人員指南** | [春季資料 Azure Cosmos DB 開發人員指南](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**使用 starter 指南** | [如何搭配 Azure Cosmos DB SQL API 使用 Spring Boot Starter](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [適用于 Azure Boot Boot Starter Cosmos DB 的 GitHub 存放庫](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**使用應用程式服務的範例** | [如何在 Linux 上的 App Service 中使用 Spring 和 Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO 應用程式範例](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>版本歷程記錄

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21) 
#### <a name="new-features"></a>新功能
* 將春季開機版本更新為2.3。0 
#### <a name="key-bug-fixes"></a>重要 bug 修正

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19) 
#### <a name="new-features"></a>新功能
* Azure Cosmos DB 版本更新為 v 3.7。3
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 包含來自 Cosmos SDK v 3.7.3 的記憶體流失修正和 netty 版本升級 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06) 
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 已修正 allowTelemetry 旗標以納入 CosmosDbConfig 的考慮
* 已修正容器的 TTL 屬性

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25) 
#### <a name="new-features"></a>新功能
* 依資料分割索引鍵 API 新增 findAll
* 已將 azure cosmos 版本更新為3.7。0
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 已修正 collectionName-> 容器
* 已修正 entityClass & domainClass-> domainType
* 已修正「儲存機制所儲存的傳回實體集合，而非輸入實體」

### <a name="2110-2020-02-25"></a>>2.1.10 (2020-02-25) 
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>重要 bug 修正
* Backport 修正「存放庫所儲存的傳回實體集合，而非輸入實體」

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15) 
#### <a name="new-features"></a>新功能
* 已將 azure cosmos 版本更新為 v 3.6。0
#### <a name="key-bug-fixes"></a>重要 bug 修正

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31) 
#### <a name="new-features"></a>新功能
* 已將 Cosmos DB SDK 版本更新為3.5。0
* 已新增批註欄位以啟用/停用自動建立集合
* 更好的例外狀況處理，透過 CosmosDBAccessException 公開 CosmosClientException
* 透過 ResponseDiagnostics 公開 requestCharge 和 activityId
#### <a name="key-bug-fixes"></a>重要 bug 修正
* SDK 3.5.0 更新修正「Cosmos DB HTTP 回應標頭大於8192個位元組時的例外狀況」、「ConsistencyPolicy. defaultConsistencyLevel ( # A1 在限定過期和一致前置詞上失敗」
* 已修正 findById Api 行為，在找不到時傳回空白，而不是擲回例外狀況
* 修正使用 CosmosPageRequest 時，不會在下一頁套用排序的 bug

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26) 
#### <a name="new-features"></a>新功能
* 已新增批註欄位以啟用/停用自動建立集合
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 已修正 findById Api 行為，在找不到時傳回空白，而不是擲回例外狀況

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21) 
#### <a name="new-features"></a>新功能
* 完成回應 Cosmos 存放庫支援
* Cosmos DB 要求診斷字串和查詢計量支援
* 3.3.1 的 Cosmos DB SDK 版本更新
* 將春季 Framework 版本升級為5.2.0 版
* 春季資料 Commons 版本升級為2.2.0。發行
* 已新增 findByIdAndPartitionKey、deleteByIdAndPartitionKey Api
* 已移除 azure 的相依性-doumentdb
* 將 DocumentDb 更名至 Cosmos
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 已修正「當 pageSize 小於存放庫中的總專案時，排序會擲回例外狀況」

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18) 
#### <a name="new-features"></a>新功能
* 取代檔 DB Api
* 已新增 findByIdAndPartitionKey、deleteByIdAndPartitionKey Api。
* 新增以 _etag 為基礎的開放式鎖定
* 已啟用檔集合名稱的 SPeL 運算式
* ObjectMapper 改進。
#### <a name="key-bug-fixes"></a>重要 bug 修正

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18) 
#### <a name="new-features"></a>新功能
* 已新增 Cosmos SDK v3 相依性
* 已新增回應 Cosmos 儲存機制
* 已將 DocumentDbTemplate 的執行更新為使用 Cosmos SDK v3。
* 回應 Cosmos 儲存機制支援的其他設定變更。
#### <a name="key-bug-fixes"></a>重要 bug 修正

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19) 
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 移除 applicationInsights 相依性
    * 相依性的潛在風險污染
    * JAVA 11 不相容
    * 避免對 CPU 和/或記憶體造成潛在的效能影響。

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20) 
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 將會移除的 applicationInsights 相依性
    * 相依性的潛在風險污染
    * JAVA 11 不相容
    * 避免對 CPU 和/或記憶體造成潛在的效能影響。

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07) 
#### <a name="new-features"></a>新功能
* 將主要版本更新為2.1。1
#### <a name="key-bug-fixes"></a>重要 bug 修正

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07) 
#### <a name="new-features"></a>新功能
* 略過遙測中的所有例外狀況
#### <a name="key-bug-fixes"></a>重要 bug 修正

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17) 
#### <a name="new-features"></a>新功能
* 將版本更新為2.1.0 以解決問題
#### <a name="key-bug-fixes"></a>重要 bug 修正

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13) 
#### <a name="new-features"></a>新功能
* Add 關鍵字 exists，startsWith
* 更新讀我檔案
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正「無法直接呼叫實體的自助 href」
* 修正「如果未建立集合，findAll 將會失敗」

### <a name="204-pre-release-2018-08-23"></a>2.0.4 版 (發行前版本)  (2018-08-23) 
#### <a name="new-features"></a>新功能
* 將套件從 documentdb 重新命名為 cosmosdb
* 新增 query method 關鍵字的新功能，支援 Sql API 中的16個關鍵字。
* 使用分頁和排序加入查詢的新功能。
* 簡化彈簧資料 cosmosdb 的設定。
* 新增 deleteCollection 和 deleteAll API。

#### <a name="key-bug-fixes"></a>重要 bug 修正
* Bug 修正和瑕疵增強功能。

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>後續步驟
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。

若要深入瞭解春季架構，請參閱 [專案首頁](https://spring.io/projects/spring-framework)。

若要深入瞭解彈簧開機，請參閱 [專案首頁](https://spring.io/projects/spring-boot)。

若要深入瞭解春季資料，請參閱 [專案首頁](https://spring.io/projects/spring-data)。