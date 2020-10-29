---
title: 在 Azure Cosmos 資料庫中管理資料的 Node.js 範例
description: 在 GitHub 上找到適用於 Azure Cosmos DB 中一般工作 (包括 CRUD 作業) 的 Node.js 範例。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: 97563b8b442429a77bfb4d19f14e26a8158e2b3b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92477890"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中管理資料的 Node.js 範例

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

[azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) GitHub 存放庫中包含可對 Azure Cosmos DB 資源執行 CRUD 作業和其他常見作業的範例解決方案。 本文提供：

* 每個 Node.js 範例專案檔中各項工作的連結。
* 相關 API 參考內容的連結。

**先決條件**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- 您可以[啟用 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)：您的 Visual Studio 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

您還需要 [JavaScript SDK](sql-api-sdk-node.md)。
   
   > [!NOTE]
   > 每個範例都各自獨立，自己設定，並自行清理。 據此，這些範例對 [Containers.create](/javascript/api/%40azure/cosmos/containers?preserve-view=true&view=azure-node-latest) 發出多個呼叫。 每當執行此動作時，即會根據所建立容器的效能層，對訂用帳戶計入一小時的使用量費用。
   > 
   > 

## <a name="database-examples"></a>資料庫範例

[DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) 檔案會顯示如何在資料庫上執行 CRUD 作業。 若要在執行下列範例之前先了解 Azure Cosmos 資料庫，請參閱[使用資料庫、容器和項目](account-databases-containers-items.md)概念性文章。 

| Task | API 參考資料 |
| --- | --- |
| [建立資料庫 (如果未存在)](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases?view=azure-node-latest&preserve-view=true#createifnotexists-databaserequest--requestoptions-) |
| [列出帳戶的資料庫](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases?view=azure-node-latest&preserve-view=true#readall-feedoptions-) |
| [依識別碼讀取資料庫](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database?view=azure-node-latest&preserve-view=true#read-requestoptions-) |
| [刪除資料庫](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database?view=azure-node-latest&preserve-view=true#delete-requestoptions-) |

## <a name="container-examples"></a>容器範例

[ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) 檔案會顯示如何在容器上執行 CRUD 作業。 若要在執行下列範例之前先了解 Azure Cosmos 集合，請參閱[使用資料庫、容器和項目](account-databases-containers-items.md)概念性文章。 

| Task | API 參考資料 |
| --- | --- |
| [建立容器 (如果未存在)](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers?view=azure-node-latest&preserve-view=true#createifnotexists-containerrequest--requestoptions-) |
| [列出帳戶的容器](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers?view=azure-node-latest&preserve-view=true#readall-feedoptions-) |
| [讀取容器定義](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container?view=azure-node-latest&preserve-view=true#read-requestoptions-) |
| [刪除容器](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container?view=azure-node-latest&preserve-view=true#delete-requestoptions-) |

## <a name="item-examples"></a>項目範例

[ItemManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) 檔案會顯示如何在項目上執行 CRUD 作業。 若要在執行下列範例之前先了解 Azure Cosmos 文件，請參閱[使用資料庫、容器和項目](account-databases-containers-items.md)概念性文章。 

| Task | API 參考資料 |
| --- | --- |
| [建立項目](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items?view=azure-node-latest&preserve-view=true#create-t--requestoptions-) |
| [讀取容器中的所有項目](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items?view=azure-node-latest&preserve-view=true#readall-feedoptions-) |
| [依識別碼讀取項目](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item?view=azure-node-latest&preserve-view=true#read-requestoptions-) |
| [在項目已變更時才讀取項目](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#accesscondition) |
| [查詢文件](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](/javascript/api/%40azure/cosmos/items?preserve-view=true&view=azure-node-latest) |
| [取代項目](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest) |
| [以條件式 ETag 檢查取代項目](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#accesscondition) |
| [刪除項目](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest) |

## <a name="indexing-examples"></a>索引範例

[IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) 檔案會顯示如何管理索引編製。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的索引功能，請參閱[索引原則](index-policy.md)、[索引類型](index-overview.md#index-kinds)及[索引路徑](index-policy.md#include-exclude-paths)概念性文章。 

| Task | API 參考資料 |
| --- | --- |
| [手動編製特定項目的索引](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#indexingdirective) |
| [手動從索引中排除特定項目](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#indexingdirective) |
| [從索引中排除某個路徑](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](/javascript/api/%40azure/cosmos/indexingpolicy?preserve-view=true&view=azure-node-latest#excludedpaths) |
| [建立字串路徑的範圍索引](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](/javascript/api/%40azure/cosmos/indexkind?preserve-view=true&view=azure-node-latest)、[IndexingPolicy](/javascript/api/%40azure/cosmos/indexingpolicy?preserve-view=true&view=azure-node-latest)、[Items.query](/javascript/api/%40azure/cosmos/items?preserve-view=true&view=azure-node-latest) |
| [使用預設 indexPolicy 建立容器，然後在線上加以更新](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](/javascript/api/%40azure/cosmos/containers?preserve-view=true&view=azure-node-latest)

## <a name="server-side-programming-examples"></a>伺服器端程式設計範例

[ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) 專案的 [index.ts](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) 檔案說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的伺服器端程式設計，請參閱[預存程序、觸發程序和使用者定義函式](stored-procedures-triggers-udfs.md)概念性文章。 

| Task | API 參考資料 |
| --- | --- |
| [建立預存程序](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](/javascript/api/%40azure/cosmos/storedprocedures?preserve-view=true&view=azure-node-latest) |
| [執行預存程序](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](/javascript/api/%40azure/cosmos/storedprocedure?preserve-view=true&view=azure-node-latest) |

如需伺服器端程式設計的詳細資訊，請參閱 [Azure Cosmos DB 伺服器端程式設計：預存程序、資料庫觸發程序和 UDF](stored-procedures-triggers-udfs.md)。
