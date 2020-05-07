---
title: Azure Cosmos DB：大量執行程式 JAVA API、SDK & 資源
description: 了解所有大量執行程式 Java API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos 大量執行程式 Java SDK 每個版本之間所做的變更。
author: milismsft
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/21/2018
ms.author: adrianmi
ms.openlocfilehash: 0030f974a36dc80dc8c4112000aa5934126a2482
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836439"
---
# <a name="java-bulk-executor-library-download-information"></a>Java 大量執行程式程式庫：下載資訊

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 變更摘要](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [非同步 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 資源提供者](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [大量執行程式-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [大量執行程式-JAVA](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**描述**|大量執行程式程式庫可讓用戶端應用程式在 Azure Cosmos DB 帳戶中執行大量作業。 大量執行程式程式庫提供 BulkImport 和 BulkUpdate 命名空間。 BulkImport 模組能夠以最佳化方式大量內嵌文件，如此就能充分取用為集合佈建的輸送量。 BulkUpdate 模組可以大量更新 Azure Cosmos 容器中的現有資料做為修補程式。|
|**SDK 下載**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**GitHub 中的大量執行程式程式庫**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API 檔**| [Java API 參考文件](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**開始使用**|[開始使用大量執行程式程式庫 Java SDK](bulk-executor-java.md)|
|**最低支援執行階段**|[JAVA 開發工具組（JDK） 7 +](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>版本資訊

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* 修正 DocumentAnalyzer，以正確地從 json 解壓縮嵌套分割區索引鍵值。

### <a name="294"></a><a name="2.9.4"/>2.9.4

* 新增 BulkDelete 作業中的功能，以在特定失敗時重試，並將失敗清單傳回給可重試的使用者。

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Cosmos SDK 版本2.4.7 版的更新。

### <a name="292"></a><a name="2.9.2"/>2.9.2

* 修正 ' mergeAll ' 以繼續進行 ' id ' 和分割區索引鍵值，以便在「識別碼」和資料分割索引鍵值之後放置的任何已修補的檔案屬性加入更新的專案清單中。

### <a name="291"></a><a name="2.9.1"/>2.9.1

* 將並行處理的開始程度更新為1，並新增迷你批次的偵錯工具記錄檔。


