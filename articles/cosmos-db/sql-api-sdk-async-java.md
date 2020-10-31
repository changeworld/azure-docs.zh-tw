---
title: Azure Cosmos DB：SQL Async JAVA API、SDK 和資源
description: 了解所有 SQL Async Java API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos DB SQL Async Java SDK 每個版本之間所做的變更。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: b5ae48e5dab6b56ea2b22d99e9126238be4d5b78
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098615"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>適用於 SQL API 的 Azure Cosmos DB Async JAVA SDK：版本資訊和資源
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
> * [REST](/rest/api
> * [REST 資源提供者](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-api-query-reference.md)
> * [大量執行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [大量執行程式 - Java](sql-api-sdk-bulk-executor-java.md)

SQL API Async Java SDK 與 SQL API Java SDK 的不同之處在於會提供非同步作業，並且支援 [Netty 程式庫](https://netty.io/)。 預先存在的 [SQL API Java SDK](sql-api-sdk-java.md) 不支援非同步作業。 

> [!IMPORTANT]  
> 這「不是」適用於 Azure Cosmos DB 的最新 Java SDK！ 請考慮針對您的專案使用 [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md)。 若要升級，請遵循[移轉到 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南及 [Reactor 與 RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) 指南中的指示。 
>

| |  |
|---|---|
| **SDK 下載** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API 文件** |[Java API 參考文件](/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?preserve-view=true&view=azure-java-stable) | 
|**參與 SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**開始使用** | [開始使用 Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**程式碼範例** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **效能秘訣**| [GitHub 讀我檔案](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **最低支援執行階段**|[JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。