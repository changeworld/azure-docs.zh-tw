---
title: Azure Cosmos DB：適用於 SQL API 的 .NET (Microsoft.Azure.Cosmos) 範例
description: 在 GitHub 上尋找適合使用 Azure Cosmos DB SQL API 執行之一般工作的 C# .NET V3 SDK 範例。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 66cf408799b37de1160ec3a76ee11ff1ffbb770b
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91801387"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>適用於 SQL API 的 Azure Cosmos DB.NET V3 SDK (Microsoft.Azure.Cosmos) 範例

> [!div class="op_single_selector"]
> * [.NET V2 SDK 範例](sql-api-dotnet-samples.md)
> * [.NET V3 SDK 範例](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK 範例](sql-api-java-sdk-samples.md)
> * [Spring Data V3 SDK 範例](sql-api-spring-data-sdk-samples.md)
> * [Node.js 範例](sql-api-nodejs-samples.md)
> * [Python 範例](sql-api-python-samples.md)
> * [Azure 程式碼範例庫](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
>
>

[azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage) GitHub 存放庫包含可對 Azure Cosmos DB 資源執行 CRUD 和其他常見作業的最新 .NET 範例解決方案。 如果您熟悉舊版 .NET SDK，則可能已使用「集合」和「文件」等詞彙。 Azure Cosmos DB 支援多個 API 模型，因此 3.0 版的 .NET SDK 會使用「容器」和「項目」等泛用詞彙。 容器可以是集合、圖形或資料表。 項目可以是文件、邊緣/頂點或資料列，並且是容器內的內容。 本文提供：

* 每個範例 C# 專案檔中各項工作的連結。
* 相關 API 參考內容的連結。

## <a name="prerequisites"></a>Prerequisites

已安裝 Azure 開發工作流程的 Visual Studio 2019

- 您可以下載並使用**免費**的 [Visual Studio 2019 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

   [Microsoft.Azure.cosmos NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

Azure 訂用帳戶或免費的 Cosmos DB 試用帳戶

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- 您可以[啟用 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)：您的 Visual Studio 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> 範例都是各自獨立的，並且會在設定之後自行清理。 每個事件都會向您的訂用帳戶收取使用您容器效能層級一小時的費用。
> 

## <a name="database-examples"></a>資料庫範例

DatabaseManagement 專案範例的 [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) 方法說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 資料庫，請參閱[使用資料庫、容器和項目](databases-containers-items.md)。

| Task | API 參考資料 |
| --- | --- |
| [建立資料庫](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient.CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet&preserve-view=true) |
| [依識別碼讀取資料庫](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Database.ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync?view=azure-dotnet&preserve-view=true) |
| [讀取帳戶的所有資料庫](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator?view=azure-dotnet&preserve-view=true) |
| [刪除資料庫](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database.DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet&preserve-view=true) |

## <a name="container-examples"></a>容器範例

範例 *ContainerManagement* 專案的 [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) 方法會說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 容器，請參閱[使用資料庫、容器和項目](databases-containers-items.md)。

| Task | API 參考資料 |
| --- | --- |
| [建立容器](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true) |
| [建立含有自訂索引原則的容器](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L111-L127) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true) |
| [變更已設定的容器效能](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L149-L171) |[Container.ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet&preserve-view=true) |
| [依識別碼取得容器](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L176-L185) |[Container.ReadContainerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync?view=azure-dotnet&preserve-view=true) |
| [讀取資料庫中的所有容器](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L193-L205) |[Database.GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator?view=azure-dotnet&preserve-view=true) |
| [刪除容器](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L213-L2018) |[Container.DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync?view=azure-dotnet&preserve-view=true) |

## <a name="item-examples"></a>項目範例

範例 *ItemManagement* 專案的 [RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) 方法會說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 項目，請參閱[使用資料庫、容器和項目](databases-containers-items.md)。

| Task | API 參考資料 |
| --- | --- |
| [建立項目](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container.CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet&preserve-view=true) |
| [依識別碼讀取項目](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[container.ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet&preserve-view=true) |
| [查詢項目](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |
| [取代項目](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L323-L363) |[container.ReplaceItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync?view=azure-dotnet&preserve-view=true) |
| [Upsert 專案](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L366-L411) |[container.UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet&preserve-view=true) |
| [刪除項目](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L414-L424) |[container.DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync?view=azure-dotnet&preserve-view=true) |
| [以條件式 ETag 檢查取代項目](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L585-L674) |[RequestOptions.IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag?view=azure-dotnet&preserve-view=true) |

## <a name="indexing-examples"></a>索引範例

範例 IndexManagement 專案的 [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) 方法說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的索引功能，請參閱[索引原則](index-policy.md)、[索引類型](index-types.md)及[索引路徑](index-paths.md)。 

| Task | API 參考資料 |
| --- | --- |
| [從索引中排除項目](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective?view=azure-dotnet&preserve-view=true) |
| [使用延遲索引](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode?view=azure-dotnet&preserve-view=true) |
| [從索引中排除指定的項目路徑](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths?view=azure-dotnet&preserve-view=true) |

## <a name="query-examples"></a>查詢範例

Queries 專案範例的 [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) 方法會說明如何使用 SQL 查詢文法、使用 LINQ 提供者搭配查詢以及使用 Lambda 來執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的 SQL 查詢參考，請參閱[適用於 Azure Cosmos DB 的 SQL 查詢範例](how-to-sql-query.md)。

| Task | API 參考資料 |
| --- | --- |
| [查詢單一分割區中的項目](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |
| [查詢多個分割區中的項目](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |
| [使用 SQL 陳述式進行查詢](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |

## <a name="change-feed-examples"></a>變更摘要範例

範例 *ChangeFeed* 專案的 [RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) 方法會說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的變更摘要，請參閱[讀取 Azure Cosmos DB 變更摘要](read-change-feed.md)和[變更摘要處理器](change-feed-processor.md)。

| Task | API 參考資料 |
| --- | --- |
| [基本變更摘要功能](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet&preserve-view=true) |
| [從特定時間開始讀取變更摘要](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet&preserve-view=true) |
| [從頭開始讀取變更摘要](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder.WithStartTime(DateTime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime?view=azure-dotnet&preserve-view=true) |
| [從變更摘要處理器移轉至 V3 SDK 中的變更摘要](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet&preserve-view=true) |

## <a name="server-side-programming-examples"></a>伺服器端程式設計範例

ServerSideScripts 專案範例的 [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) 方法會說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的伺服器端程式設計，請參閱[預存程序、觸發程序和使用者定義函式](stored-procedures-triggers-udfs.md)。

| Task | API 參考資料 |
| --- | --- |
| [建立預存程序](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Scripts.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync?view=azure-dotnet&preserve-view=true) |
| [執行預存程序](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Scripts.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync?view=azure-dotnet&preserve-view=true) |
| [刪除預存程序](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Scripts.DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync?view=azure-dotnet&preserve-view=true) |
