---
title: 尋找適用于 MongoDB 的 Azure Cosmos DB API 作業的要求單位費用
description: 瞭解如何針對針對 Azure Cosmos 容器執行的 MongoDB 查詢，尋找要求單位 (RU) 費用。 您可以使用 Azure 入口網站、MongoDB .NET、JAVA Node.js 驅動程式。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: b7d880183ac5f920bbed1a85d7660db6a8f21462
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078470"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>針對 Azure Cosmos DB API for MongoDB 中執行的作業尋找要求單位費用
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB 支援許多 API，例如 SQL、MongoDB、Cassandra、Gremlin 和資料表。 每個 API 都有它自己的一組資料庫作業。 這些作業的範圍可從簡單的點讀取和寫入到複雜查詢。 每個資料庫作業都會根據作業的複雜度來取用系統資源。

所有資料庫作業的成本會由 Azure Cosmos DB 進行正規化，並以要求單位 (簡稱 RU) 表示。 您可以將 ru 視為效能貨幣，以抽象化執行 Azure Cosmos DB 所支援之資料庫作業所需的系統資源（例如 CPU、IOPS 和記憶體）。 無論您使用哪種 API 與 Azure Cosmos 容器互動，成本始終由 RU 測量。 無論資料庫作業是寫入、點讀取或查詢，成本一律會以 ru 來測量。 若要深入瞭解，請參閱 [要求單位和它的考慮](request-units.md) 文章。

本文提供不同的方式，可讓您尋找 [要求單位](request-units.md) (RU) 耗用量，以針對 Azure Cosmos DB API for MongoDB 中的容器執行的任何作業。 如果您使用不同的 API，請參閱 [SQL api](find-request-unit-charge.md)、 [CASSANDRA API](find-request-unit-charge-cassandra.md)、 [Gremlin API](find-request-unit-charge-gremlin.md)和 [資料表 API](find-request-unit-charge-table.md) 文章，以找出 RU/秒費用。

RU 費用可用名為 `getLastRequestStatistics` 的自訂[資料庫命令](https://docs.mongodb.com/manual/reference/command/)公開。 此命令會傳回一份文件，其中包含最後執行的作業名稱，及其要求費用和持續時間。 如果您使用的是 Azure Cosmos DB API for MongoDB，會有多個選項可供擷取 RU 費用。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-mongodb-dotnet.md#create-a-database-account)並為其提供資料，或選取已包含資料的現有帳戶。

1. 前往 [資料總管]  窗格，然後選取您要處理的容器。

1. 選取 [新增查詢]  。

1. 輸入有效的查詢，然後選取 [執行查詢]  。

1. 選取下 [查詢統計資料]  ，以顯示您執行的要求實際的要求費用。

:::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Azure 入口網站之中 MongoDB 查詢要求費用的螢幕擷取畫面":::

## <a name="use-the-mongodb-net-driver"></a>使用 MongoDB .NET 驅動程式

使用[官方的 MongoDB .NET 驅動程式](https://docs.mongodb.com/ecosystem/drivers/csharp/)時，可以對 `IMongoDatabase` 物件呼叫 `RunCommand` 方法來執行命令。 使用此方法時，必須實作 `Command<>` 抽象類別：

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

如需詳細資訊，請參閱 [快速入門：使用適用于 MongoDB 的 AZURE COSMOS DB API 建立 .net web 應用程式](create-mongodb-dotnet.md)。

## <a name="use-the-mongodb-java-driver"></a>使用 MongoDB Java 驅動程式


使用[官方的 MongoDB Java 驅動程式](https://mongodb.github.io/mongo-java-driver/)時，可以對 `MongoDatabase` 物件呼叫 `runCommand` 方法來執行命令：

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

如需詳細資訊，請參閱 [快速入門：使用適用于 MongoDB 的 AZURE COSMOS DB API 和 JAVA SDK 來建立 web 應用程式](create-mongodb-java.md)。

## <a name="use-the-mongodb-nodejs-driver"></a>使用 MongoDB Node.js 驅動程式

使用[官方的 MongoDB Node.js 驅動程式](https://mongodb.github.io/node-mongodb-native/)時，可以於 `db` 物件呼叫 `command` 方法來執行命令：

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

如需詳細資訊，請參閱 [快速入門：將現有的 MongoDB Node.js web 應用程式遷移至 Azure Cosmos DB](create-mongodb-nodejs.md)。

## <a name="next-steps"></a>下一步

若要了解如何最佳化您的 RU 耗用量，請參閱下列文章：

* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)
* [在 Azure Cosmos DB 中最佳化已佈建的輸送量成本](optimize-cost-throughput.md)
* [在 Azure Cosmos DB 中最佳化查詢成本](./optimize-cost-reads-writes.md)