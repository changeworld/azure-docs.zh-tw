---
title: Azure Cosmos DB Cassandra API 資源上布建輸送量
description: 瞭解如何在 Azure Cosmos DB Cassandra API 資源中布建容器、資料庫和自動調整輸送量。 您將使用 Azure 入口網站、CLI、PowerShell 和其他各種 Sdk。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 6b8b145c919a1f4e2ea9129a032da69bd30e6b71
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283810"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>在 Azure Cosmos DB Cassandra API 資源上布建資料庫、容器或自動調整輸送量

本文說明如何在 Azure Cosmos DB Cassandra API 中布建輸送量。 您可以在容器或資料庫上布建標準 (手動) 或自動調整輸送量，並在資料庫內的容器之間共用。 您可以使用 Azure 入口網站、Azure CLI 或 Azure Cosmos DB Sdk 來布建輸送量。

如果您使用不同的 API，請參閱 [SQL api](how-to-provision-container-throughput.md)、 [適用于 MongoDB 的 API](how-to-provision-throughput-mongodb.md)、 [Gremlin api](how-to-provision-throughput-gremlin.md) 文章來布建輸送量。

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-mongodb-dotnet.md#create-a-database-account)，或選取現有 Azure Cosmos 帳戶。

1. 開啟 **資料總管** 窗格，然後選取 [ **新增資料表**]。 接下來，提供下列詳細資料：

   * 指出您是要建立新的 keyspace，還是使用現有的。 如果您想要在 keyspace 層級布建輸送量，請選取 [布建 **資料庫輸送量** ] 選項。
   * 在 CQL 命令中輸入資料表識別碼。
   * 輸入主要金鑰值 (例如 `/userrID`) 。
   * 輸入您要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]。

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="使用資料庫層級輸送量建立新的集合時，資料總管的螢幕擷取畫面":::

> [!Note]
> 如果您要在使用 Cassandra API 設定的 Azure Cosmos 帳戶中佈建容器的輸送量，請使用分割區索引鍵路徑的 `/myPrimaryKey`。

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> .NET SDK

### <a name="provision-throughput-for-a-cassandra-table"></a>布建 Cassandra 資料表的輸送量

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
類似的命令可以透過任何符合 CQL 規範的驅動程式發出。

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>改變或變更 Cassandra 資料表的輸送量

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

類似的命令可以透過任何符合 CQL 規範的驅動程式執行。

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

您可以使用 Azure Resource Manager 範本，在資料庫或容器層級資源上布建所有 Azure Cosmos DB Api 的自動調整輸送量。 如需範例，請參閱 [Azure Cosmos DB Azure Resource Manager 範本](templates-samples-cassandra.md) 。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 可以用來針對所有 Azure Cosmos DB 的 Api，在資料庫或容器層級資源上布建自動調整輸送量。 如需範例，請參閱 [Azure Cosmos DB 的 Azure CLI 範例](cli-samples-cassandra.md)。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 可以用來針對所有 Azure Cosmos DB 的 Api，在資料庫或容器層級資源上布建自動調整輸送量。 如需範例，請參閱 [Azure Cosmos DB 的 Azure PowerShell 範例](powershell-samples-cassandra.md)。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Azure Cosmos DB 中的輸送量佈建：

* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)