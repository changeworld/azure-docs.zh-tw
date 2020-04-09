---
title: Azure 宇宙資料庫:SQL 非同步 JAva API、SDK &资源
description: 了解所有 SQL Async Java API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos DB SQL Async Java SDK 每個版本之間所做的變更。
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 7dad1e929c2beb32605f67219ed30d2fd9a3a394
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985468"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK for SQL API：版本資訊與資源
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改來源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [非同步 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 資源提供者](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [Sql](sql-api-query-reference.md)
> * [批次執行器 -.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批次執行器 -Java](sql-api-sdk-bulk-executor-java.md)

SQL API Async Java SDK 與 SQL API Java SDK 的不同之處在於會提供非同步作業，並且支援 [Netty 程式庫](https://netty.io/)。 預先存在的 [SQL API Java SDK](sql-api-sdk-java.md) 不支援非同步作業。 

| |  |
|---|---|
| **SDK 下載** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API 文件** |[Java API 參考文件](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**參與 SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**開始** | [開始使用 Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**程式碼範例** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **效能秘訣**| [GitHub 閱讀我](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **最低支援執行階段**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。

