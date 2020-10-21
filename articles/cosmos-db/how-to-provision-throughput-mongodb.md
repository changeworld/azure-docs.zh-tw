---
title: 在適用于 MongoDB 的 Azure Cosmos DB API 資源上布建輸送量
description: 瞭解如何在適用于 MongoDB 的 Azure Cosmos DB API 資源中布建容器、資料庫和自動調整輸送量。 您將使用 Azure 入口網站、CLI、PowerShell 和其他各種 Sdk。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 44a69581f0763972dd30a016bf0826b31657a3d4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283608"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>在適用于 MongoDB 資源的 Azure Cosmos DB API 上布建資料庫、容器或自動調整輸送量

本文說明如何在適用于 MongoDB 的 Azure Cosmos DB API 中布建輸送量。 您可以在容器或資料庫上布建標準 (手動) 或自動調整輸送量，並在資料庫內的容器之間共用。 您可以使用 Azure 入口網站、Azure CLI 或 Azure Cosmos DB Sdk 來布建輸送量。

如果您使用不同的 API，請參閱 [SQL api](how-to-provision-container-throughput.md) [Cassandra API](how-to-provision-throughput-cassandra.md) [Gremlin API](how-to-provision-throughput-gremlin.md) 文章來布建輸送量。

## <a name="azure-portal"></a><a id="portal-mongodb"></a> Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-mongodb-dotnet.md#create-a-database-account)，或選取現有 Azure Cosmos 帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增集合]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。 如果您想要在資料庫層級布建輸送量，請選取 [布建 **資料庫輸送量** ] 選項。
   * 輸入集合識別碼。
   * 輸入分割區索引鍵值，(例如 `/ItemID`)。
   * 輸入您要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]。

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="使用資料庫層級輸送量建立新的集合時，資料總管的螢幕擷取畫面":::

> [!Note]
> 如果您要在使用適用於 MongoDB 的 Azure Cosmos DB API 設定的 Azure Cosmos 帳戶中佈建容器的輸送量，請使用分割區索引鍵路徑的 `/myShardKey`。

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> .NET SDK

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

您可以使用 Azure Resource Manager 範本，在資料庫或容器層級資源上布建所有 Azure Cosmos DB Api 的自動調整輸送量。 如需範例，請參閱 [Azure Cosmos DB Azure Resource Manager 範本](templates-samples-mongodb.md) 。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 可以用來針對所有 Azure Cosmos DB 的 Api，在資料庫或容器層級資源上布建自動調整輸送量。 如需範例，請參閱 [Azure Cosmos DB 的 Azure CLI 範例](cli-samples-mongodb.md)。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 可以用來針對所有 Azure Cosmos DB 的 Api，在資料庫或容器層級資源上布建自動調整輸送量。 如需範例，請參閱 [Azure Cosmos DB 的 Azure PowerShell 範例](powershell-samples-mongodb.md)。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Azure Cosmos DB 中的輸送量佈建：

* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)