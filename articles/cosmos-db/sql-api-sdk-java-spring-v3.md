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
ms.openlocfilehash: 9e5b92918d93109183740be555bb805877862407
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817865"
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

適用于 Core (SQL) 的春季資料 Azure Cosmos DB 第3版，可讓開發人員在春季應用程式中使用 Azure Cosmos DB。 彈簧資料 Azure Cosmos DB 會公開用於運算元據庫和集合、使用檔和發出查詢的彈簧資料介面。 在相同的 Maven 成品中，同步處理和非同步 (回應) Api 都受到支援。 

彈簧資料 Azure Cosmos DB 相依于彈簧資料架構。 Azure Cosmos DB SDK 小組會針對春季資料版本2.2 和2.3 發行 Maven 構件。

[春季架構](https://spring.io/projects/spring-framework)是一種可簡化 JAVA 應用程式開發的程式設計和設定模型。 春季使用相依性插入，簡化應用程式的「配管」。 許多開發人員喜歡春季，因為它可讓您以更簡單的方式建立和測試應用程式。 [彈簧開機](https://spring.io/projects/spring-boot) 將此處理程式延伸至 web 應用程式和微服務開發。 [彈簧資料](https://spring.io/projects/spring-data) 是用來存取資料存放區的程式設計模型和架構，例如從春季或春季 Boot 應用程式的內容中 Azure Cosmos DB。 

您可以在 [Azure 春季雲端](https://azure.microsoft.com/services/spring-cloud/) 應用程式中使用春季資料 Azure Cosmos DB。

> [!IMPORTANT]  
> 這些版本資訊適用于第3版的春季資料 Azure Cosmos DB。 您可以在 [這裡找到第2版的](sql-api-sdk-java-spring-v2.md)版本資訊。 
>
> 彈簧資料 Azure Cosmos DB 僅支援 SQL API。
>
> 請參閱下列文章，以取得其他 Azure Cosmos DB Api 上的春季資料相關資訊：
> * [使用 Azure Cosmos DB 的 Apache Cassandra 的彈簧資料](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [具有 Azure Cosmos DB 的春季資料 MongoDB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [使用 Azure Cosmos DB 的彈簧資料 Gremlin](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>從這裡開始

# <a name="explore"></a>[探索](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>這些索引標籤包含基本的彈簧資料 Azure Cosmos DB 範例。

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>設定相依性

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[[連接]](#tab/connect)

### <a name="connect"></a>連線

指定 Azure Cosmos DB 帳戶和容器詳細資料。 彈簧資料 Azure Cosmos DB 會自動建立用戶端，並連接至容器。

[應用程式。屬性](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties)：
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

建立：
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Create":::

刪除：
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Delete":::

# <a name="query"></a>[查詢](#tab/queries)

查詢：
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Query":::

---

## <a name="resources"></a>資源

* **參與 SDK**： [GitHub 上的春季資料 Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)存放庫

* **教學**課程： [GitHub 上的春季資料 Azure Cosmos DB 教學](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started)課程 

## <a name="release-history"></a>版本歷程記錄

### <a name="300-beta2-september-17-2020"></a>3.0.0-Beta. 2 (2020 年9月17日) 

#### <a name="new-features"></a>新功能

* 已將成品識別碼更新為 `azure-spring-data-cosmos` 。
* 已將 azure cosmos 相依性更新為 `4.5.0` 。
* `Query Annotation` 原生查詢的支援。
* JAVA 11 的支援。
* 藉由在批註中公開欄位來新增對嵌套分割區索引鍵的支援 `partitionKeyPath` `@Container` 。
* 已新增可 `limit` `top` `first` 在定義存放庫 api 時，允許和使用之查詢類型的支援。

#### <a name="key-bug-fixes"></a>重要 bug 修正

* 修正搭配注釋使用時的嵌套分割區索引鍵錯誤 `@GeneratedValue` 。

### <a name="300-beta1-august-17-2020"></a>3.0.0-Beta. 1 (2020 年8月17日) 

#### <a name="new-features"></a>新功能

* 將群組識別碼更新為 `com.azure` 。
* 將成品識別碼更新為 `azure-spring-data-2-3-cosmos` 。
* 將 azure cosmos SDK 相依性更新為 `4.3.2-beta.2` 。
* 新增對審核實體的支援：自動管理 `createdBy` 、 `createdDate` 、 `lastModifiedBy` 和 `lastModifiedDate` 批註欄位。
* 針對 `@GeneratedValue` 類型的識別碼欄位，加入自動識別碼產生的注釋支援 `String` 。
* 針對具有多個資料庫的單一 Azure Cosmos DB 帳戶，以及多個具有多個資料庫的 Azure Cosmos DB 帳戶，新增多資料庫設定支援。
* `@Version`在任何字串欄位上新增批註的支援。
* 將同步 API 傳回類型更新為 `Iterable` 類型，而不是 `List` 。
* `CosmosClientBuilder`從 AZURE COSMOS DB SDK 公開為類別的彈簧 bean `@Configuration` 。
* `CosmosConfig`包含查詢計量和回應診斷處理器實作為的更新。
* 新增針對 `Optional` 單一結果查詢傳回資料類型的支援。

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
* 修正對子句之轉義查詢的問題 `IN` 。
* 藉由允許 `long` 的資料類型來修正問題 `@Id` 。
* 藉由允許 `boolean` 、 `long` 、 `int` 和 `double` 做為批註的資料類型 `@PartitionKey` 來修正問題。
* `IgnoreCase` `AllIgnoreCase` 忽略 case 查詢的修正和關鍵字。
* 當自動建立容器時，移除預設的要求單位值4000。

## <a name="faq"></a>常見問題集

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)。

深入瞭解 [春季架構](https://spring.io/projects/spring-framework)。

深入瞭解 [春季開機](https://spring.io/projects/spring-boot)。

深入瞭解 [春季資料](https://spring.io/projects/spring-data)。