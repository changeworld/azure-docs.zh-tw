---
title: 適用於 Azure Cosmos DB 的 SQL API Python 範例
description: 在 GitHub 上找到適用於 Azure Cosmos DB 中一般工作 (包括 CRUD 作業) 的 Python 範例。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 05/20/2020
ms.author: sngun
ms.openlocfilehash: 3b92ae206aa623e948a612f31b48213421bf9da3
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798501"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python 範例

> [!div class="op_single_selector"]
> * [.NET V2 SDK 範例](sql-api-dotnet-samples.md)
> * [.NET V3 SDK 範例](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK 範例](sql-api-java-sdk-samples.md)
> * [Node.js 範例](sql-api-nodejs-samples.md)
> * [Python 範例](sql-api-python-samples.md)
> * [Azure 程式碼範例庫](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

[azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) GitHub 存放庫中包含可對 Azure Cosmos DB 資源執行 CRUD 作業和其他常見作業的範例解決方案。 本文提供：

* 每個 Python 範例專案檔中各項工作的連結。
* 相關 API 參考內容的連結。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- 您可以[啟用 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)：您的 Visual Studio 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

您也需要 [Python SDK](sql-api-sdk-python.md)。

   > [!NOTE]
   > 每個範例都各自獨立，會自己設定，並於完成後自行清理。 這些範例會對 `CosmosClient.CreateContainer`發出多次呼叫。 每次執行時，您的訂用帳戶會計入一小時的使用費。 如需 Azure Cosmos DB 計費的詳細資訊，請參閱 [Azure Cosmos DB 價格](https://azure.microsoft.com/pricing/details/cosmos-db/)。

## <a name="database-examples"></a>資料庫範例

[database_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) Python 範例示範如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 資料庫，請參閱[使用資料庫、容器和項目](databases-containers-items.md)概念性文章。

| Task | API 參考資料 |
| --- | --- |
| [建立資料庫](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [依識別碼讀取資料庫](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [查詢資料庫](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [列出帳戶的資料庫](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [刪除資料庫](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>容器範例

[container_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) Python 範例示範如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 集合，請參閱[使用資料庫、容器和項目](databases-containers-items.md)概念性文章。

| Task | API 參考資料 |
| --- | --- |
| [查詢容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |database.query_containers |
| [建立容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |database.create_container |
| [列出資料庫中的所有容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |database.list_containers |
| [依識別碼取得容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |database.get_container_client |
| [管理容器的佈建輸送量](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer、container.replace_throughput|
| [刪除容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |database.delete_container |

## <a name="item-examples"></a>項目範例

[item_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) Python 範例示範如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 文件，請參閱[使用資料庫、容器和項目](databases-containers-items.md)概念性文章。

| Task | API 參考資料 |
| --- | --- |
| [在容器中建立項目](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [依識別碼讀取項目](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [讀取容器中的所有項目](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [依識別碼查詢項目](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [取代項目](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [Upsert 專案](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [刪除項目](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [取得容器中項目的變更摘要](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>索引範例

[index_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) Python 範例示範如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的索引功能，請參閱[索引原則](index-policy.md)、[索引類型](index-types.md)及[索引路徑](index-paths.md)概念性文章。

| Task | API 參考資料 |
| --- | --- |
| [從索引中排除特定項目](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [將手動編製索引使用於已編製索引的特定項目](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | documents.IndexingDirective.Include |
| [從索引中排除路徑](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |定義要在 `IndexingPolicy` 屬性中排除的路徑 |
| [對字串使用範圍索引](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | 使用字串資料類型上的範圍索引來定義編製索引原則。 `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [執行索引轉換](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |database.replace_container (使用已更新的編製索引原則)|
| [只有路徑上有雜湊索引時才使用掃描](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | 在查詢項目時設定 `enable_scan_in_query=True` 和 `enable_cross_partition_query=True` |