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
ms.openlocfilehash: f0cb3d5f9184bacef42a0258add6dd2461a71dd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326655"
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
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
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
> 這些版本資訊適用於第 3 版的 Spring Data Azure Cosmos DB。 您可以在[這裡](sql-api-sdk-java-spring-v2.md)找到第 2 版的版本資訊。 
>
> Spring Data Azure Cosmos DB 僅支援 SQL API。
>
> 請參閱下列文章，以取得與其他 Azure Cosmos DB API 上的 Spring Data 有關的資訊：
> * [適用於 Apache Cassandra 的 Spring Data 與 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB 與 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin 與 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>從這裡開始

# <a name="explore"></a>[探討](#tab/explore)

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

---

## <a name="resources"></a>資源

* **參與 SDK**： [GitHub 上的春季資料 Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)存放庫

* **教學**課程： [GitHub 上的春季資料 Azure Cosmos DB 教學](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started)課程 

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="faq"></a>常見問題集

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)。

深入瞭解 [春季架構](https://spring.io/projects/spring-framework)。

深入瞭解 [春季開機](https://spring.io/projects/spring-boot)。

深入瞭解 [春季資料](https://spring.io/projects/spring-data)。