---
title: SQL API 版本資訊和資源的春季資料 Azure Cosmos DB v2
description: 深入瞭解 SQL API 的春季資料 Azure Cosmos DB v2，包括發行日期、停用日期，以及每個版本的 Azure Cosmos DB SQL Async JAVA SDK 之間所做的變更。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: d5c85095e767c0e92c22410054ac4f8fc5267660
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097136"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>適用于 Core (SQL) API 的春季資料 Azure Cosmos DB v2：版本資訊和資源
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 變更摘要 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [非同步 Java SDK v2](sql-api-sdk-async-java.md)
> * [同步 Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 連接器](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 資源提供者](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [大量執行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [大量執行程式 - Java](sql-api-sdk-bulk-executor-java.md)

 適用于 Core (SQL) 的春季資料 Azure Cosmos DB 第2版，可讓開發人員在春季應用程式中使用 Azure Cosmos DB。 彈簧資料 Azure Cosmos DB 會公開用於運算元據庫和集合、使用檔和發出查詢的彈簧資料介面。 在相同的 Maven 成品中，同步處理和非同步 (回應) Api 都受到支援。 

[春季架構](https://spring.io/projects/spring-framework)是一種可簡化 JAVA 應用程式開發的程式設計和設定模型。 春季使用相依性插入，簡化應用程式的「配管」。 許多開發人員喜歡春季，因為它可讓您以更簡單的方式建立和測試應用程式。 [彈簧開機](https://spring.io/projects/spring-boot) 將此處理程式延伸至 web 應用程式和微服務開發。 [彈簧資料](https://spring.io/projects/spring-data) 是一種用來存取資料存放區的程式設計模型，例如從春季或春季 Boot 應用程式的內容中 Azure Cosmos DB。 

您可以在 [Azure 春季雲端](https://azure.microsoft.com/services/spring-cloud/) 應用程式中使用春季資料 Azure Cosmos DB。

> [!IMPORTANT]  
> 這些版本資訊適用于第2版的春季資料 Azure Cosmos DB。 您可以在 [這裡找到第3版的](sql-api-sdk-java-spring-v3.md)版本資訊。 
>
> Spring Data Azure Cosmos DB 僅支援 SQL API。
>
> 請參閱下列文章，以取得其他 Azure Cosmos DB Api 上的春季資料相關資訊：
> * [適用於 Apache Cassandra 的 Spring Data 與 Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB 與 Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin 與 Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> 想要快速開始嗎？
> 1. 安裝 [最低支援的 JAVA 執行時間（JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)），以便您可以使用 SDK。
> 2. 使用 [starter](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)Azure Cosmos DB 應用程式建立春季資料。 就是這麼簡單！
> 3. 逐步解說《 [Azure Cosmos DB 開發人員指南》中的春季資料](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)，它會逐步解說基本的 Azure Cosmos DB 要求。
>
> 您可以使用 [春季 Initializr](https://start.spring.io/)快速啟動春季 Boot Starter 應用程式！
>

## <a name="resources"></a>資源

| 資源 | 連結 |
|---|---|
| **SDK 下載** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API 文件** | [彈簧資料 Azure Cosmos DB 參考檔]() |
|**參與 SDK** | [GitHub 上的春季資料 Azure Cosmos DB 存放庫](https://github.com/microsoft/spring-data-cosmosdb) | 
|**春季開機 Starter**| [適用于 JAVA 的 Azure Cosmos DB 春季 Boot Starter 用戶端程式庫](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**使用 Azure Cosmos DB 的春季 TODO 應用程式範例**| [App Service Linux 的端對端 JAVA 體驗 (第2部分) ](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**開發人員指南** | [Spring Data Azure Cosmos DB 開發人員指南](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**使用 Starter** | [如何搭配 Azure Cosmos DB SQL API 使用春季 Boot Starter](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [適用于 Azure Cosmos DB 春季 Boot Starter 的 GitHub 存放庫](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Azure App Service 的範例** | [如何在 Linux 上的 App Service 中使用 Spring 和 Cosmos DB](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO 應用程式範例](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>版本歷程記錄

### <a name="230-may-21-2020"></a>2020 (5 月21日 2.3.0) 
#### <a name="new-features"></a>新功能
* 將春季開機版本更新為2.3.0。


### <a name="225-may-19-2020"></a>2020 2.2.5 (5 月19日) 
#### <a name="new-features"></a>新功能
* 將 Azure Cosmos DB 版本更新為3.7.3。
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 包含 Azure Cosmos DB SDK 3.7.3 的記憶體流失修正和 Netty 版本升級。

### <a name="224-april-6-2020"></a>2.2.4 (2020 年4月6日) 
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正 `allowTelemetry` 要納入考慮的旗標 `CosmosDbConfig` 。
* 修正 `TTL` 容器的屬性。

### <a name="223-february-25-2020"></a>2020年2月25日 2.2.3 () 
#### <a name="new-features"></a>新功能
* `findAll`依分割區索引鍵 API 加入新的。
* 將 Azure Cosmos DB 版本更新為3.7.0。
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正 `collectionName`  ->  `containerName` 。
* 修正 `entityClass` 和 `domainClass`  ->  `domainType` 。
* 修正「傳回存放庫所儲存的實體集合，而非輸入實體」。

### <a name="2110-february-25-2020"></a>2020年2月25日 >2.1.10 () 
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 反向移植修正「由存放庫而非輸入實體儲存的傳回實體集合」。

### <a name="222-january-15-2020"></a>2.2.2 (2020 年1月15日) 
#### <a name="new-features"></a>新功能
* 將 Azure Cosmos DB 版本更新為3.6.0。
#### <a name="key-bug-fixes"></a>重要 bug 修正

### <a name="221-december-31-2019"></a>2.2.1 (2019 年12月31日) 
#### <a name="new-features"></a>新功能
* 將 Azure Cosmos DB SDK 版本更新為3.5.0。
* 新增批註欄位，以啟用或停用自動建立集合。
* 改善例外狀況處理。 公開 `CosmosClientException` `CosmosDBAccessException` 。
* 公開 `requestCharge` 和 `activityId` `ResponseDiagnostics` 。
#### <a name="key-bug-fixes"></a>重要 bug 修正
* SDK 3.5.0 更新修正」 Cosmos DB HTTP 回應標頭大於8192個位元組時的例外狀況：「ConsistencyPolicy. defaultConsistencyLevel ( # A1 在限定過期和一致前置詞上失敗。」
* 修正 `findById` 方法的行為。 先前，如果找不到實體，而不是擲回例外狀況，則此方法會傳回空白。
* 修正在使用時，不會在下一個頁面套用排序的 bug `CosmosPageRequest` 。

### <a name="219-december-26-2019"></a>2.1.9 (2019 年12月26日) 
#### <a name="new-features"></a>新功能
* 新增批註欄位，以啟用或停用自動建立集合。
#### <a name="key-bug-fixes"></a>重要 bug 修正
*  修正 `findById` 方法的行為。 先前，如果找不到實體，而不是擲回例外狀況，則此方法會傳回空白。

### <a name="220-october-21-2019"></a>2019年10月21日 2.2.0 () 
#### <a name="new-features"></a>新功能
* 完成回應 Cosmos 存放庫支援。
* Azure Cosmos DB 要求診斷字串和查詢計量支援。
* 3.3.1 的 Azure Cosmos DB SDK 版本更新。
* 將春季 Framework 版本升級為5.2.0。
* 春季資料 Commons 版本升級為2.2.0。
* 新增 `findByIdAndPartitionKey` `deleteByIdAndPartitionKey` api。
* 移除 azure-documentdb 的相依性。
* Rebrands DocumentDB 以 Azure Cosmos DB。
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正「當 pageSize 小於存放庫中的總專案時，排序會擲回例外狀況」。

### <a name="218-october-18-2019"></a>2.1.8 (2019 年10月18日) 
#### <a name="new-features"></a>新功能
* 淘汰 DocumentDB Api。
* 新增 `findByIdAndPartitionKey` `deleteByIdAndPartitionKey` api。
* 根據來加入開放式鎖定 `_etag` 。
* 啟用檔集合名稱的 SpEL 運算式。
* 新增 `ObjectMapper` 增強功能。

### <a name="217-october-18-2019"></a>2.1.7 (2019 年10月18日) 
#### <a name="new-features"></a>新功能
* 新增 Azure Cosmos DB SDK 第3版相依性。
* 新增回應的 Cosmos 存放庫。
* 將的執行更新 `DocumentDbTemplate` 為使用 AZURE COSMOS DB SDK 第3版。
* 新增回應 Cosmos 儲存機制支援的其他設定變更。

### <a name="212-march-19-2019"></a>2.1.2 (2019 年3月19日) 
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 移除 `applicationInsights` 下列各項的相依性：
    * 相依性污染的潛在風險。
    * JAVA 11 不相容。
    * 避免對 CPU 和/或記憶體造成潛在的效能影響。

### <a name="207-march-20-2019"></a>2.0.7 (2019 年3月20日) 
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 將會移除 `applicationInsights` 下列各項的相依性：
    * 相依性污染的潛在風險。
    * JAVA 11 不相容。
    * 避免對 CPU 和/或記憶體造成潛在的效能影響。

### <a name="211-march-7-2019"></a>2.1.1 (2019 年3月7日) 
#### <a name="new-features"></a>新功能
* 將主要版本更新為2.1.1。

### <a name="206-march-7-2019"></a>2.0.6 (2019 年3月7日) 
#### <a name="new-features"></a>新功能
* 略過遙測中的所有例外狀況。

### <a name="210-december-17-2018"></a>2.1.0 (2018 年12月17日) 
#### <a name="new-features"></a>新功能
* 將版本更新為2.1.0 以解決問題。

### <a name="205-september-13-2018"></a>2.0.5 (2018 年9月13日) 
#### <a name="new-features"></a>新功能
* 新增關鍵字 `exists` 和 `startsWith` 。
* 更新讀我檔案。
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正「無法直接呼叫實體的自助 href」。
* 修正「如果未建立集合，findAll 將會失敗」。

### <a name="204-prerelease-august-23-2018"></a>2.0.4 版 (發行前版本)  (2018 年8月23日) 
#### <a name="new-features"></a>新功能
* 將套件從 documentdb 重新命名為 cosmosdb。
* 加入 query method 關鍵字的新功能。 現在支援來自 SQL API 的16個關鍵字。
* 使用分頁和排序加入查詢的新功能。
* 簡化彈簧資料 cosmosdb 的設定。
* 新增 `deleteCollection` `deleteAll` api。

#### <a name="key-bug-fixes"></a>重要 bug 修正
* Bug 修正與瑕疵緩和措施。

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>後續步驟
深入了解 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)。

深入瞭解 [春季架構](https://spring.io/projects/spring-framework)。

深入瞭解 [春季開機](https://spring.io/projects/spring-boot)。

深入瞭解 [春季資料](https://spring.io/projects/spring-data)。