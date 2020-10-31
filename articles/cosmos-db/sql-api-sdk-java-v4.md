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
ms.openlocfilehash: a70892a1900902cdf033434fe7edfed4c56f3b74
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097102"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core (SQL) API：版本資訊與資源
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

Azure Cosmos DB Java SDK v4 for Core (SQL) 會將非同步 API 與同步 API 結合成一個 Maven 成品。 V4 SDK 會根據 Project Reactor 和 [Netty 程式庫](https://netty.io/)，提供增強的效能、新的 API 功能和非同步支援。 使用者可以預期 Azure Cosmos DB Java SDK v4 與 [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) 和 [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md) 的改善效能。

> [!IMPORTANT]  
> 這些版本資訊僅適用於 Azure Cosmos DB Java SDK v4。 如果您目前使用的版本比 v4 舊，請參閱[遷移至 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南，以取得升級至 v4 的協助。
>
> 以下是快速開始進行的三個步驟！
> 1. 安裝[最低支援的 JAVA 執行階段，JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)，以便您可以使用 SDK。
> 2. 請逐步執行 [Azure Cosmos DB Java SDK v4 的快速入門手冊](./create-sql-api-java.md)，以取得 Maven 成品的存取權，並逐步解說 Azure Cosmos DB 的基本要求。
> 3. 閱讀 Azure Cosmos DB Java SDK v4 [效能秘訣](performance-tips-java-sdk-v4-sql.md)和[疑難排解](troubleshoot-java-sdk-v4-sql.md)指南，以最佳化應用程式的 SDK。
>
> [Azure Cosmos DB 的研討會和實驗室](https://aka.ms/cosmosworkshop)是了解如何使用 Azure Cosmos DB Java SDK v4 的另一個絕佳資源！
>

## <a name="helpful-content"></a>有用的內容

| Content | 連結 |
|---|---|
|**SDK 下載**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API 文件** | [Java API 參考文件](/java/api/overview/azure/cosmosdb/client?preserve-view=true&view=azure-java-stable) |
|**參與 SDK** | [GitHub 上的 Azure SDK for Java 中央存放庫](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**開始使用** | [快速入門：建置 JAVA 應用程式來管理 Azure Cosmos DB SQL API 資料](./create-sql-api-java.md) <br> [具有快速入門程式碼的 GitHub 存放庫](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**基本程式碼範例** | [Azure Cosmos DB：適用於 SQL API 的 Java 範例](sql-api-java-sdk-samples.md) <br> [包含範例程式碼的 GitHub 存放庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**具有變更摘要的主控台應用程式**| [變更摘要-JAVA SDK v4 範例](create-sql-api-java-changefeed.md) <br> [包含範例程式碼的 GitHub 存放庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Web 應用程式範例**| [使用 JAVA SDK v4 建立 web 應用程式](sql-api-java-application.md) <br> [包含範例程式碼的 GitHub 存放庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **效能秘訣**| [Java SDK v4 的效能秘訣](performance-tips-java-sdk-v4-sql.md)| 
| **疑難排解** | [針對 Java SDK v4 進行疑難排解](troubleshoot-java-sdk-v4-sql.md) |
| **從舊版 SDK 遷移至 v4** | [遷移至 JAVA V4 SDK](migrate-java-v4-sdk.md) |
| **最低支援執行階段**|[JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) | 
| **Azure Cosmos DB 研討會與實驗室** |[Cosmos DB 研討會首頁](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>後續步驟
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。