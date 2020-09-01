---
title: SQL API 版本資訊和資源的春季資料 Azure Cosmos DB v3
description: 深入瞭解 SQL API 的春季資料 Azure Cosmos DB v3，包括發行日期、停用日期，以及每個版本的 Azure Cosmos DB SQL Async JAVA SDK 之間所做的變更。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: e67e6911eeac29036dee2b68c19395b34e1d11da
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228030"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>適用于 Core (SQL) API 的春季資料 Azure Cosmos DB v3：版本資訊和資源
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

適用于 Core (SQL) 的春季資料 Azure Cosmos DB v3 可讓開發人員在春季應用程式中使用 Azure Cosmos DB。 彈簧資料 Azure Cosmos DB 會公開用於運算元據庫和集合、使用檔和發出查詢的彈簧資料介面。 在相同的 Maven 成品中，同步處理和非同步 (回應) Api 都受到支援。 

彈簧資料 Azure Cosmos DB 會相依于彈簧資料架構。 Azure Cosmos DB SDK 小組會針對春季 Data 2.2 和2.3 版 Maven 成品。

[春季架構](https://spring.io/projects/spring-framework)是一種可簡化 JAVA 應用程式開發的程式設計和設定模型。 若要為組織的網站加上引號，請使用相依性插入，簡化應用程式的「配管」。 許多像是春天的開發人員，因為組建和測試應用程式變得更簡單。 [春季 Boot](https://spring.io/projects/spring-boot) 擴充了這種處理常式，並留意 web 應用程式和微服務開發的概念。 [彈簧資料](https://spring.io/projects/spring-data) 是一種程式設計模型和架構，可用於存取資料存放區，例如從春季或春季 Boot 應用程式的內容中進行 Azure Cosmos DB。 

您可以在 [Azure 春季雲端](https://azure.microsoft.com/services/spring-cloud/) 應用程式中使用春季資料 Azure Cosmos DB。

> [!IMPORTANT]  
> 這些版本資訊適用于彈簧資料 Azure Cosmos DB 的 v3。 您可以在 [這裡](sql-api-sdk-java-spring-v2.md)找到 v2 版本資訊。 
>
> 彈簧資料 Azure Cosmos DB 僅支援 SQL API。
>
> 下列指南支援其他 Azure Cosmos DB Api 上的春季資料：
> * [使用 Azure Cosmos DB 的 Apache Cassandra 的彈簧資料](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [具有 Azure Cosmos DB 的春季資料 MongoDB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [使用 Azure Cosmos DB 的彈簧資料 Gremlin](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>從這裡開始

# <a name="explore"></a>[探索](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

### <a name="navigate-the-tabs-above-for-basic-spring-data-azure-cosmos-db-samples"></a>流覽上面的索引標籤，以取得基本的彈簧資料 Azure Cosmos DB 範例。

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>設定相依性

有兩個彈簧資料 Azure Cosmos DB v3 Maven 成品可供使用。

相依于春季 Data framework 2.2 的成品：
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-2-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

相依于春季 Data framework 2.3 的成品：
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-3-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

# <a name="connect"></a>[[連接]](#tab/connect)

### <a name="connect"></a>連線

指定 Azure Cosmos DB 帳戶和容器詳細資料。 彈簧資料 Azure Cosmos DB 會自動建立用戶端，並連接至容器。

[應用程式。屬性](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-2-cosmos-java-getting-started/src/main/resources/application.properties)：
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Doc ops](#tab/docs)

### <a name="document-operations"></a>文件作業

[建立](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java)： [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]

[刪除](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java)： [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

# <a name="query"></a>[查詢](#tab/queries)

### <a name="query"></a>查詢

[查詢](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java)： [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="helpful-content"></a>有用的內容

| Content | 春季 Data framework 2。2 | 春季 Data framework 2.3 版 |
|---|---|
| **SDK 下載** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**參與 SDK** | [GitHub 上的春季資料 Azure Cosmos DB 存放庫](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [GitHub 上的春季資料 Azure Cosmos DB 存放庫](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**教學課程**| [GitHub 上的春季資料 Azure Cosmos DB 教學課程](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [GitHub 上的春季資料 Azure Cosmos DB 教學課程](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>版本歷程記錄

### <a name="300-beta2-unreleased"></a>3.0.0-Beta. 2 (未發行) 

### <a name="300-beta1-2020-08-17"></a>3.0.0-Beta. 1 (2020-08-17) 
#### <a name="new-features"></a>新功能
* 已將群組識別碼更新為 `com.azure` 。
* 已將成品識別碼更新為 `azure-spring-data-2-3-cosmos` 。
* 已將 azure cosmos SDK 相依性更新為 `4.3.2-beta.2` 。
* 支援審核實體-自動管理 createdBy、createdDate、lastModifiedBy 和 lastModifiedDate 批註欄位。
* `@GeneratedValue` 批註支援類型的識別碼欄位的自動識別碼產生 `String` 。
* 針對具有多個資料庫的單一 cosmos 帳戶，以及多個具有多個資料庫的 cosmos 帳戶的多資料庫設定支援。
* 支援 `@Version` 任何字串欄位上的批註。
* 更新的同步 Api 會將型別傳回 `Iterable` 類型，而不是 `List` 。
* `CosmosClientBuilder`從 COSMOS SDK 以彈簧 bean 的形式公開給 `@Configuration` 類別。
* 已更新 `CosmosConfig` 為包含查詢計量和回應診斷處理器實作為。
* 支援傳回 `Optional` 單一結果查詢的資料類型。
#### <a name="renames"></a>重 命名
* `CosmosDbFactory` 至 `CosmosFactory` 。
* `CosmosDBConfig` 至 `CosmosConfig` 。
* `CosmosDBAccessException` 至 `CosmosAccessException` 。
* `Document` 批註的批註 `Container` 。
* `DocumentIndexingPolicy` 批註的批註 `CosmosIndexingPolicy` 。
* `DocumentQuery` 至 `CosmosQuery` 。
* 的應用程式屬性旗標 `populateQueryMetrics` `queryMetricsEnabled` 。
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 將診斷記錄工作排程到 `Parallel` 執行緒，以避免封鎖 Netty i/o 執行緒。
* 修正刪除操作的開放式鎖定。
* 修正了針對子句進行的「轉義查詢」問題 `IN` 。
* 藉由允許 `long` 的資料類型來修正問題 `@Id` 。
* 藉由允許 `boolean` 、 `long` 、和 `int` `double` 注釋的資料類型 `@PartitionKey` 來修正問題。
* 已修正 `IgnoreCase`  &  `AllIgnoreCase` 忽略 case 查詢的關鍵字。
* 自動建立容器時，移除預設的要求單位值4000。

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>後續步驟
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。

若要深入瞭解春季架構，請參閱 [專案首頁](https://spring.io/projects/spring-framework)。

若要深入瞭解彈簧開機，請參閱 [專案首頁](https://spring.io/projects/spring-boot)。

若要深入瞭解春季資料，請參閱 [專案首頁](https://spring.io/projects/spring-data)。