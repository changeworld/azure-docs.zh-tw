---
title: Azure Cosmos DB：大量執行程式 .NET API、SDK & 資源
description: 了解所有大量執行程式 .NET API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos DB 大量執行程式 .NET SDK 每個版本之間所做的變更。
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169407"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET 大量執行程式程式庫：下載資訊 

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
| **說明**| .Net 大量執行程式程式庫可讓用戶端應用程式對 Azure Cosmos DB 帳戶執行大量作業。 此程式庫提供 BulkImport、BulkUpdate 和 BulkDelete 命名空間。 BulkImport 模組能夠以最佳化方式大量內嵌文件，如此就能充分取用為集合佈建的輸送量。 BulkUpdate 模組可以大量更新 Azure Cosmos 容器中的現有資料做為修補程式。 BulkDelete 模組能夠以最佳化方式大量刪除文件，如此就能充分取用為集合佈建的輸送量。|
|**SDK 下載**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub 中的大量執行程式程式庫**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API 檔**|[.NET API 參考文件](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**開始使用**|[開始使用大量執行程式程式庫 .NET SDK](bulk-executor-dot-net.md)|
| **目前支援的架構**| Microsoft .NET Framework 4.5.2, 4.6.1 與 .NET Standard 2.0 |

> [!NOTE]
> 如果您使用大量執行程式，請參閱最新版本3.x 的[.NET sdk](tutorial-sql-api-dotnet-bulk-import.md)，其已內建于 SDK 中的大量執行程式。 

## <a name="release-notes"></a>版本資訊

### <a name="241-preview"></a><a name="2.4.1-preview"/>2.4.1-預覽

* 已修正 BulkDelete 回應中的 TotalElapsedTime，以正確測量總時間，包括任何重試次數。

### <a name="240-preview"></a><a name="2.4.0-preview"/>2.4.0-預覽

* 已將 SDK 相依性變更為 >= 2.5。1

### <a name="230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* 已新增圖形大量執行程式的支援，以接受頂點和邊緣的 ttl

### <a name="220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* 已修正問題，這會在以閘道模式執行時，于彈性調整 Azure Cosmos DB 時造成例外狀況。 此修正讓它在功能上相當於1.4.1 版本。

### <a name="210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* 已新增 SQL API 帳戶的 BulkDelete 支援，以接受要刪除的資料分割索引鍵、檔識別碼元組。 這項變更使其在功能上相當於1.4.0 版本。

### <a name="200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* 包括 MongoBulkExecutor 以支援 .NET Standard 2.0。 這項功能使其功能相當於 1.3.0 版本，加上支援 .NET Standard 2.0 目標架構。

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* 已將 .NET Standard 2.0 新增為其中一個支援的目標架構，讓大量執行程式程式庫可與 .NET Core 應用程式搭配使用。

### <a name="188"></a><a name="1.8.8"/>1.8.8

* 已修正在 MongoBulkExecutor 上，藉由新增填補而非預期地增加檔案大小的問題，在某些情況下，會超過最大檔案大小限制。

### <a name="187"></a><a name="1.8.7"/>1.8.7

* 已修正當集合具有嵌套資料分割索引鍵路徑時，BulkDeleteAsync 的問題。

### <a name="186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor 現在會執行 IDisposable，而且預期會在使用之後加以處置。

### <a name="185"></a><a name="1.8.5"/>1.8.5 版

* 已移除 SDK 版本的鎖定。 套件現在相依于 SDK >= 2.5.1。

### <a name="184"></a><a name="1.8.4"/>1.8.4

* 已修正使用具有數值的 POCO 物件清單呼叫 BulkImport 時的識別碼處理。

### <a name="183"></a><a name="1.8.3"/>1.8.3

* 已修正 BulkDelete 回應中的 TotalElapsedTime，以正確測量總時間，包括任何重試次數。

### <a name="182"></a><a name="1.8.2"/>1.8.2

* 已修正在某些情況下的高 CPU 耗用量。
* 追蹤現在會使用 TraceSource。 使用者可以定義來源的`BulkExecutorTrace`接聽程式。
* 已修正在傳送接近2Mb 大小的檔時，可能會造成鎖定的罕見案例。

### <a name="160"></a><a name="1.6.0"/>1.6.0

* 已更新大量執行程式，現在使用最新版的 Azure Cosmos DB .NET SDK （2.4.0）

### <a name="150"></a><a name="1.5.0"/>1.5.0

* 已新增圖形大量執行程式的支援，以接受頂點和邊緣的 ttl

### <a name="141"></a><a name="1.4.1"/>1.4.1

* 已修正問題，這會在以閘道模式執行時，于彈性調整 Azure Cosmos DB 時造成例外狀況。

### <a name="140"></a><a name="1.4.0"/>1.4.0

* 已新增 SQL API 帳戶的 BulkDelete 支援，以接受要刪除的資料分割索引鍵、檔識別碼元組。

### <a name="130"></a><a name="1.3.0"/>1.3.0

* 已修正在大量執行程式所使用的使用者代理程式中造成格式化問題的問題。

### <a name="120"></a><a name="1.2.0"/>1.2.0

* 改善大量執行程式的匯入和更新 Api，以在儲存體超過目前的容量而不擲回例外狀況時，以透明的方式調整 Cosmos 容器的彈性調整。

### <a name="112"></a><a name="1.1.2"/>1.1.2

* 將 DocumentDB.NET SDK 相依性提高至版本 2.1.3。

### <a name="111"></a><a name="1.1.1"/>1.1.1

* 已修正問題，這會導致大量執行程式在匯入至固定集合時擲回 JSRT 錯誤。

### <a name="110"></a><a name="1.1.0"/>1.1.0

* 新增對 Azure Cosmos DB SQL API 帳戶之 BulkDelete 作業的支援。
* 若帳戶有適用於 MongoDB 的 Azure Cosmos DB API，可對其進行 BulkImport 作業。
* 將 DocumentDB.NET SDK 相依性提高至版本 2.0.0。 

### <a name="102"></a><a name="1.0.2"/>1.0.2

* 新增對 Azure Cosmos DB Gremlin API 帳戶之 BulkImport 作業的支援。

### <a name="101"></a><a name="1.0.1"/>1.0.1

* 修正 Azure Cosmos DB SQL API 帳戶之 BulkImport 作業的次要錯誤。

### <a name="100"></a><a name="1.0.0"/>1.0.0

* 新增對 Azure Cosmos DB SQL API 帳戶之 BulkImport 和 BulkUpdate 作業的支援。

## <a name="next-steps"></a>後續步驟

若要深入瞭解大量執行程式 JAVA 程式庫，請參閱下列文章：

[JAVA 大量執行程式程式庫 SDK 和發行資訊](sql-api-sdk-bulk-executor-java.md)
