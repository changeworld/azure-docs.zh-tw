---
title: 適用於 Azure Cosmos DB 的 SQL API Python 範例
description: 在 GitHub 上找到適用於 Azure Cosmos DB 中一般工作 (包括 CRUD 作業) 的 Python 範例。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 07/23/2019
ms.author: sngun
ms.openlocfilehash: 1e7c3e06c8aa49f9a23d3cc600ee6f60218c2fbd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "72170146"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python 範例

> [!div class="op_single_selector"]
> * [.NET V2 SDK 範例](sql-api-dotnet-samples.md)
> * [.NET V3 SDK 範例](sql-api-dotnet-v3sdk-samples.md)
> * [Java 範例](sql-api-java-samples.md)
> * [非同步 Java 範例](sql-api-async-java-samples.md)
> * [Node.js 範例](sql-api-nodejs-samples.md)
> * [Python 範例](sql-api-python-samples.md)
> * [Azure 程式碼範例庫](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

[azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) GitHub 存放庫中包含可對 Azure Cosmos DB 資源執行 CRUD 作業和其他常見作業的範例解決方案。 本文提供：

* 每個 Python 範例專案檔中各項工作的連結。 
* 相關 API 參考內容的連結。

**必要條件**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- 您可以 [啟用 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)：您的 Visual Studio 訂用帳戶每個月都會提供額度，供您用在 Azure 付費服務。

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

您也需要 [Python SDK](sql-api-sdk-python.md)。 
   
   > [!NOTE]
   > 每個範例都各自獨立，會自己設定，並於完成後自行清理。 這些範例會對 [CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createcontainer-database-link--collection--options-none-) 發出多個呼叫。 每次執行時，您的訂用帳戶會計入一小時的使用費。 如需 Azure Cosmos DB 計費的詳細資訊，請參閱 [Azure Cosmos DB 價格](https://azure.microsoft.com/pricing/details/cosmos-db/)。
   > 
   > 

## <a name="database-examples"></a>資料庫範例
[DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) 專案的 [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) 檔案說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 資料庫，請參閱[使用資料庫、容器和項目](databases-containers-items.md)概念性文章。 

| Task | API 參考資料 |
| --- | --- |
| [建立資料庫](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L65-L76) |[CosmosClient.CreateDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createdatabase-database--options-none-) |
| [依識別碼讀取資料庫](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L79-L96) |[CosmosClient.ReadDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readdatabase-database-link--options-none-) |
| [列出帳戶的資料庫](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L99-L110) |[CosmosClient.ReadDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readdatabases-options-none-) |
| [刪除資料庫](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L113-L126) |[CosmosClient.DeleteDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#deletedatabase-database-link--options-none-) |

## <a name="collection-examples"></a>集合範例
[CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) 專案的 [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) 檔案說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 集合，請參閱[使用資料庫、容器和項目](databases-containers-items.md)概念性文章。 

| Task | API 參考資料 |
| --- | --- |
| [建立集合](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L84-L135) |[CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createcontainer-database-link--collection--options-none-) |
| [讀取資料庫中所有集合的清單](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L210-L222) |[CosmosClient.ReadContainers](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readcontainers-database-link--options-none-) |
| [依識別碼取得集合](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L190-L208) |[CosmosClient.ReadContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readcontainer-collection-link--options-none-) |
| [變更集合的輸送量](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L184-L188) | [CosmosClient.ReplaceOffer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replaceoffer-offer-link--offer-)|
| [刪除集合](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L224-L238) |[CosmosClient.DeleteContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#deletecontainer-collection-link--options-none-) |

## <a name="document-examples"></a>文件範例
[DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) 專案的 [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) 檔案說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 文件，請參閱[使用資料庫、容器和項目](databases-containers-items.md)概念性文章。 

| Task | API 參考資料 |
| --- | --- |
| [建立文件](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createitem-database-or-container-link--document--options-none-) |
| [建立文件的集合](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createitem-database-or-container-link--document--options-none-) |
| [依識別碼讀取文件](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[CosmosClient.ReadItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readitem-document-link--options-none-) |
| [讀取集合中的所有文件](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[CosmosClient.ReadItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readitems-collection-link--feed-options-none-) |
| [以條件式 ETag 檢查取代文件](https://github.com/Azure/azure-cosmos-python/blob/a21f6fb4bad3f59909ef43558b598f9fb476b7bc/test/crud_tests.py#L1216-L1218) | [CosmosClient.ReplaceItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replaceitem-document-link--new-document--options-none-) |

## <a name="indexing-examples"></a>索引範例
[IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) 專案的 [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) 檔案說明如何執行下列工作。  若要在執行下列範例之前先了解 Azure Cosmos DB 中的索引功能，請參閱[索引原則](index-policy.md)、[索引類型](index-types.md)及[索引路徑](index-paths.md)概念性文章。 

| Task | API 參考資料 |
| --- | --- |
| [使用手動 (而非自動) 索引](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) | 自動編製索引原則 |
| [從索引中排除指定的文件路徑](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) | 具有排除路徑的編製索引原則|
| [從索引中排除某個文件](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[IndexingDirective.Exclude](/python/api/azure-cosmos/azure.cosmos.documents.indexingdirective#exclude) |
| [設定編制索引模式](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[IndexingMode](/python/api/azure-cosmos/azure.cosmos.documents.indexingmode) |
| [對字串使用範圍索引](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) | 具有內含路徑的編製索引原則|
| [執行索引轉換](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[CosmosClient.ReplaceContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replacecontainer-collection-link--collection--options-none-) |

## <a name="query-examples"></a>查詢範例
範例專案也會示範如何執行下列查詢工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的 SQL 查詢參考，請參閱 [SQL 查詢範例](how-to-sql-query.md)概念性文章。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的 SQL 查詢參考，請參閱 [SQL 查詢範例](how-to-sql-query.md)概念性文章。 


| Task | API 參考資料 |
| --- | --- |
| [查詢帳戶中的資料庫](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L49-L62) |[CosmosClient.QueryDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#querydatabases-query--options-none-) |
| [查詢文件](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[CosmosClient.QueryItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#queryitems-database-or-container-link--query--options-none--partition-key-none-) |
| [對雜湊索引路徑強制執行範圍掃描作業](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[HttpHeaders.EnableScanInQuery](/python/api/azure-cosmos/azure.cosmos.http_constants.httpheaders#enablescaninquery) |

