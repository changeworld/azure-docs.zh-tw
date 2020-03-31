---
title: Azure 宇宙 DB：大批量執行器 .NET API、SDK &資源
description: 了解所有大量執行程式 .NET API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos DB 大量執行程式 .NET SDK 每個版本之間所做的變更。
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169407"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET 大量執行程式程式庫：下載資訊 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [非同步 Java](sql-api-sdk-async-java.md)
> * [JAVA](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [休息](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 資源提供者](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [批量執行器 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量執行器 - JAVA](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **描述**| .Net 批量執行器庫允許用戶端應用程式對 Azure Cosmos DB 帳戶執行批量操作。 此庫提供大量匯入、批量更新和大量刪除命名空間。 BulkImport 模組能夠以最佳化方式大量內嵌文件，如此就能充分取用為集合佈建的輸送量。 BulkUpdate 模組可以將 Azure Cosmos 容器中的現有資料批量更新為修補程式。 BulkDelete 模組能夠以最佳化方式大量刪除文件，如此就能充分取用為集合佈建的輸送量。|
|**SDK 下載**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub 中的批量執行者庫**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API 文檔**|[.NET API 參考文件](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**開始使用**|[開始使用大量執行程式程式庫 .NET SDK](bulk-executor-dot-net.md)|
| **目前支援的架構**| Microsoft .NET Framework 4.5.2, 4.6.1 與 .NET Standard 2.0 |

> [!NOTE]
> 如果您使用的是批量執行器，請參閱[.NET SDK](tutorial-sql-api-dotnet-bulk-import.md)的最新版本 3.x，該版本在 SDK 中內置了批量執行器。 

## <a name="release-notes"></a>版本資訊

### <a name="241-preview"></a><a name="2.4.1-preview"/>2.4.1 預覽

* 在 BulkDelete 的回應中固定總時間，以正確測量總時間，包括任何重試。

### <a name="240-preview"></a><a name="2.4.0-preview"/>2.4.0 預覽版

* 將 SDK 依賴項更改為 >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-預覽2

* 添加了對圖形批量執行器的支援，以在頂點和邊上接受 ttl

### <a name="220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-預覽2

* 修復了在閘道模式下運行時 Azure Cosmos DB 的彈性縮放期間導致異常的問題。 此修復程式使其在功能上等效于 1.4.1 版本。

### <a name="210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-預覽2

* 添加了對 SQL API 帳戶接受分區金鑰、文件識別碼 副本以刪除的支援。 此更改使其在功能上等效于 1.4.0 版本。

### <a name="200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* 包括 MongoBulkExecutor 以支援 .NET Standard 2.0。 這項功能使其功能相當於 1.3.0 版本，加上支援 .NET Standard 2.0 目標架構。

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* 添加了 .NET 標準 2.0 作為支援的目標框架之一，以使批量執行器庫與 .NET Core 應用程式一起工作。

### <a name="188"></a><a name="1.8.8"/>1.8.8

* 修復了 MongoBulk Executor 上通過添加填充和在某些情況下超出最大文檔大小限制意外增加文檔大小的問題。

### <a name="187"></a><a name="1.8.7"/>1.8.7

* 修復了 BulkDeleteAsync 在集合嵌套分區金鑰路徑時的問題。

### <a name="186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor 現在實現了 IIIIA，並且預計在使用後釋放它。

### <a name="185"></a><a name="1.8.5"/>1.8.5

* 在 SDK 版本上刪除了鎖。 包現在依賴于 SDK >= 2.5.1。

### <a name="184"></a><a name="1.8.4"/>1.8.4

* 使用具有數值的 POCO 物件清單調用 BulkImport 時修復識別碼的處理。

### <a name="183"></a><a name="1.8.3"/>1.8.3

* 在 BulkDelete 的回應中固定總時間，以正確測量總時間，包括任何重試。

### <a name="182"></a><a name="1.8.2"/>1.8.2

* 修復了某些方案中的高 CPU 限定。
* 現在，使用跟蹤源進行跟蹤。 使用者可以為`BulkExecutorTrace`源定義攔截器。
* 修復了在發送大小接近 2Mb 的文檔時可能導致鎖定的罕見情況。

### <a name="160"></a><a name="1.6.0"/>1.6.0

* 更新了批量執行器，現在使用最新版本的 Azure Cosmos DB .NET SDK （2.4.0）

### <a name="150"></a><a name="1.5.0"/>1.5.0

* 添加了對圖形批量執行器的支援，以在頂點和邊上接受 ttl

### <a name="141"></a><a name="1.4.1"/>1.4.1

* 修復了在閘道模式下運行時 Azure Cosmos DB 的彈性縮放期間導致異常的問題。

### <a name="140"></a><a name="1.4.0"/>1.4.0

* 添加了對 SQL API 帳戶接受分區金鑰、文件識別碼 副本以刪除的支援。

### <a name="130"></a><a name="1.3.0"/>1.3.0

* 修復了導致批量執行器使用的使用者代理中的格式問題。

### <a name="120"></a><a name="1.2.0"/>1.2.0

* 改進批量執行器導入和更新 API，以透明地適應 Cosmos 容器的彈性縮放，當存儲超過當前容量而不引發異常時。

### <a name="112"></a><a name="1.1.2"/>1.1.2

* 將 DocumentDB.NET SDK 相依性提高至版本 2.1.3。

### <a name="111"></a><a name="1.1.1"/>1.1.1

* 修復了一個問題，導致批量執行器在導入到固定集合時引發 JSRT 錯誤。

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

要瞭解批量執行器 JAVA 庫，請參閱以下文章：

[JAVA 大容量執行器庫 SDK 和發佈資訊](sql-api-sdk-bulk-executor-java.md)
