---
title: Azure Cosmos DB：大量執行工具 .NET API、SDK 和資源
description: 了解所有大量執行程式 .NET API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos DB 大量執行程式 .NET SDK 每個版本之間所做的變更。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: 6fa0b32d21c9a2172fa6093f2f7f57ccacf5e5d6
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92477754"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET 大量執行工具程式庫：下載資訊 

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

| |  |
|---|---|
| **說明**| .NET 大量執行工具程式庫可讓用戶端應用程式執行大量的 Azure Cosmos DB 帳戶作業。 此程式庫提供 BulkImport、BulkUpdate 和 BulkDelete 命名空間。 BulkImport 模組能夠以最佳化方式大量內嵌文件，如此就能充分取用為集合佈建的輸送量。 BulkUpdate 模組能夠以修補程式的形式，大量更新 Azure Cosmos 容器中的現有資料。 BulkDelete 模組能夠以最佳化方式大量刪除文件，如此就能充分取用為集合佈建的輸送量。|
|**SDK 下載**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub 中的大量執行工具程式庫**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API 文件**|[.NET API 參考文件](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?preserve-view=true&view=azure-dotnet)|
|**開始使用**|[開始使用大量執行程式程式庫 .NET SDK](bulk-executor-dot-net.md)|
| **目前支援的架構**| Microsoft .NET Framework 4.5.2, 4.6.1 與 .NET Standard 2.0 |

> [!NOTE]
> 如果您使用大量執行工具，請參閱最新 3.x 版的 [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md)，其已在 SDK 中內建大量執行工具。 

## <a name="release-notes"></a>版本資訊

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1-preview

* 已修正 BulkDelete 回應中的 TotalElapsedTime，以正確測量包含任何重試在內的總時間。

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0-preview

* 已將 SDK 相依性變更為 >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* 已新增圖形大量執行工具的支援，以接受頂點和邊緣的 ttl

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-preview2

* 已修正以下問題：以閘道模式執行時，在彈性調整 Azure Cosmos DB 期間造成例外狀況。 此修正使其在功能上相當於 1.4.1 版。

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-preview2

* 已新增 BulkDelete 支援，以便 SQL API 帳戶接受資料分割索引鍵 (要刪除的文件識別碼 Tuple)。 此變更使其在功能上相當於 1.4.0 版。

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-preview2

* 包括 MongoBulkExecutor 以支援 .NET Standard 2.0。 這項功能使其功能相當於 1.3.0 版本，加上支援 .NET Standard 2.0 目標架構。

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-preview

* 已新增 .NET Standard 2.0 為其中一個支援的目標架構，讓大量執行工具程式庫可以使用 .NET Core 應用程式。

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* 修正當以引號括住的值做為輸入參數傳遞時，BulkDeleteAsync 的問題。

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* 已修正由於增加邊框間距而非預期地增加檔案大小 (在某些情況下，超過文件大小上限) 的 MongoBulkExecutor 問題。

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* 已修正當集合具有巢狀分割區索引鍵路徑時的 BulkDeleteAsync 問題。

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* MongoBulkExecutor 現在會實作 IDisposable，且預期會在使用後予以處置。

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* 已移除 SDK 版本的鎖定。 套件現在相依於 SDK >= 2.5.1。

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* 已修正使用具有數值的 POCO 物件清單呼叫 BulkImport 時的識別碼處理。

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* 已修正 BulkDelete 回應中的 TotalElapsedTime，以正確測量包含任何重試在內的總時間。

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* 已修正在某些情況下的高 CPU 耗用量。
* 追蹤現在會使用 TraceSource。 使用者可以定義 `BulkExecutorTrace` 來源的接聽程式。
* 已修正在傳送接近 2Mb 大小的文件時，可能會造成鎖定的罕見案例。

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* 已將大量執行工具更新為現在使用最新版的 Azure Cosmos DB .NET SDK (2.4.0)

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* 已新增圖形大量執行工具的支援，以接受頂點和邊緣的 ttl

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* 已修正以下問題：以閘道模式執行時，在彈性調整 Azure Cosmos DB 期間造成例外狀況。

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* 已新增 BulkDelete 支援，以便 SQL API 帳戶接受資料分割索引鍵 (要刪除的文件識別碼 Tuple)。

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* 已修正以下問題：該問題導致大量執行工具所用的使用者代理程式發生格式化問題。

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* 改善大量執行工具匯入及更新 API，以在儲存體超過目前容量時順暢地適應 Cosmos 容器的彈性調整，而不擲回例外狀況。

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* 將 DocumentDB.NET SDK 相依性提高至版本 2.1.3。

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* 已修正導致大量執行工具匯入固定集合時擲回 JSRT 錯誤的問題。

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* 新增對 Azure Cosmos DB SQL API 帳戶之 BulkDelete 作業的支援。
* 若帳戶有適用於 MongoDB 的 Azure Cosmos DB API，可對其進行 BulkImport 作業。
* 將 DocumentDB.NET SDK 相依性提高至版本 2.0.0。 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* 新增對 Azure Cosmos DB Gremlin API 帳戶之 BulkImport 作業的支援。

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* 修正 Azure Cosmos DB SQL API 帳戶之 BulkImport 作業的次要錯誤。

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* 新增對 Azure Cosmos DB SQL API 帳戶之 BulkImport 和 BulkUpdate 作業的支援。

## <a name="next-steps"></a>後續步驟

若要深入了解大量執行工具的 Java 程式庫，請參閱下列文章：

[Java 大量執行工具程式庫 SDK 和版本資訊](sql-api-sdk-bulk-executor-java.md)