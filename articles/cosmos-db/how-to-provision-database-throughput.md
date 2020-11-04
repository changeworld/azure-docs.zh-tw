---
title: 在 Azure Cosmos DB SQL API 中布建資料庫輸送量
description: 瞭解如何使用 Azure 入口網站、CLI、PowerShell 和各種其他 Sdk，在 Azure Cosmos DB SQL API 的資料庫層級布建輸送量。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4ecbee2260da735cd6ba74d3b9ffb55b4a652e9e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341989"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db---sql-api"></a>Azure Cosmos DB SQL API 中的資料庫布建標準 (手動) 輸送量
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文說明如何在 Azure Cosmos DB SQL API 中的資料庫上布建標準 (手動) 的輸送量。 您可以佈建單一[容器](how-to-provision-container-throughput.md)的輸送量，或是為資料庫佈建，並在其容器之間共用輸送量。 若要了解何時應使用容器層級和資料庫層級輸送量，請參閱[在容器和資料庫上佈建輸送量的使用案例](set-throughput.md)一文。 您可以使用 Azure 入口網站或 Azure Cosmos DB SDK 來佈建資料庫層級的輸送量。

如果您使用不同的 API，請參閱 [適用于 MongoDB 的 API](how-to-provision-throughput-mongodb.md)， [Cassandra API](how-to-provision-throughput-cassandra.md) [Gremlin api](how-to-provision-throughput-gremlin.md) 文章來布建輸送量。

## <a name="provision-throughput-using-azure-portal"></a>使用 Azure 入口網站佈建輸送量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-sql-api-dotnet.md#create-account)，或選取現有 Azure Cosmos 帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增資料庫]。 提供下列詳細資料：

   * 輸入資料庫識別碼。
   * 選取 [布建 **資料庫輸送量** ] 選項。
   * 輸入輸送量 (例如 1000 RU)。
   * 選取 [確定]。

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-sql-api.png" alt-text="[新增資料庫] 對話方塊的螢幕擷取畫面":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>使用 Azure CLI 或 PowerShell 佈建輸送量

若要建立具有共用輸送量的資料庫，請參閱

* [使用 Azure CLI 建立資料庫](manage-with-cli.md#create-a-database-with-shared-throughput)
* [使用 PowerShell 建立資料庫](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>使用 .NET SDK 佈建輸送量

> [!Note]
> 您可以使用適用于 SQL API 的 Azure Cosmos Sdk 來為所有 Api 布建輸送量。 您也可以選擇性地對 Cassandra API 使用下列範例。

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="next-steps"></a>後續步驟

請參閱下列文章，了解如何 Azure Cosmos DB 中佈建的輸送量：

* [全域調整佈建的輸送量](./request-units.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [如何為容器佈建標準 (手動) 輸送量](how-to-provision-container-throughput.md)
* [如何為容器佈建自動調整輸送量](how-to-provision-autoscale-throughput.md)
* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)