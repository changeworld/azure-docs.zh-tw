---
title: Azure Cosmos DB：大量執行工具 Java API、SDK 和資源
description: 了解所有大量執行程式 Java API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos 大量執行程式 Java SDK 每個版本之間所做的變更。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 1a88e33c523ff3bcd9f23767597232af0319ab57
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323837"
---
# <a name="java-bulk-executor-library-download-information"></a>Java 大量執行程式程式庫：下載資訊

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 變更摘要 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [非同步 Java SDK v2](sql-api-sdk-async-java.md)
> * [同步 Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 資源提供者](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [大量執行程式-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [大量執行工具 - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**說明**|大量執行程式程式庫可讓用戶端應用程式在 Azure Cosmos DB 帳戶中執行大量作業。 大量執行程式程式庫提供 BulkImport 和 BulkUpdate 命名空間。 BulkImport 模組能夠以最佳化方式大量內嵌文件，如此就能充分取用為集合佈建的輸送量。 BulkUpdate 模組能夠以修補程式的形式，大量更新 Azure Cosmos 容器中的現有資料。|
|**SDK 下載**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**GitHub 中的大量執行工具程式庫**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API 文件**| [Java API 參考文件](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**開始使用**|[開始使用大量執行程式程式庫 Java SDK](bulk-executor-java.md)|
|**最低支援執行階段**|[Java 開發套件 (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>版本資訊

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* 修正 DocumentAnalyzer.java，以便正確地從 json 解壓縮巢狀分割區索引鍵值。

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* 新增 BulkDelete 作業中的功能，以在特定失敗時重試，同時也會將失敗清單傳回給可重試的使用者。

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Cosmos SDK 2.4.7 版的更新。

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* 修正 'mergeAll' 以繼續處理 'id' 和分割區索引鍵值，如此一來，就能將放置於 'id' 和資料分割索引鍵值之後的任何已修補文件屬性新增至更新的項目清單。

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* 將並行處理的開始程度更新為 1，並為迷你批次新增了偵錯工具記錄。


