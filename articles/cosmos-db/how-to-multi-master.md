---
title: 如何在 Azure Cosmos DB 中設定多重主機
description: 瞭解如何使用 Azure Cosmos DB 中的不同 Sdk 來為您的應用程式設定多宿主。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/10/2020
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 40d2e5f3d79fdc7725f04fbfd2c7f29f8db265a3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328525"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>在使用 Azure Cosmos DB 的應用程式中設定多重主機

所建立的帳戶在啟用多個寫入區域後，您就必須在應用程式中對 ConnectionPolicy 進行兩項變更，DocumentClient 才能在 Azure Cosmos DB 中啟用多重主機及多路連接功能。 在 ConnectionPolicy 內，將 UseMultipleWriteLocations 設定為 true，並將應用程式部署所在區域的名稱傳遞至 SetCurrentLocation。 這會根據所傳入位置的地理鄰近性填入 PreferredLocations 屬性。 如果之後在帳戶中新增新的區域，應用程式便不必更新或重新部署，而是會自動偵測較接近的區域，並在發生區域性事件時自動以該處作為主要位置。

> [!Note]
> 最初設定為單一寫入區域的 Cosmos 帳戶，可以設定為多個寫入區域 (也就是多重主機) 而不會產生停機時間。 若要深入了解，請參閱[設定多重寫入區域](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

若要在應用程式中啟用多重主機，請將 `UseMultipleWriteLocations` 設定為 `true`。 此外，將 `SetCurrentLocation` 設定為要在其中部署應用程式的區域，以及複寫 Azure Cosmos DB 的所在區域：

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

若要在應用程式中啟用多重主機，請將 `ApplicationRegion` 設定為要在其中部署應用程式和複寫 Cosmos DB 的區域：

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

或者，您可以使用 `CosmosClientBuilder` 和 `WithApplicationRegion` 來達成相同的結果：

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-master"></a> Java V4 SDK

若要在您的應用程式中啟用多宿主，請在用戶端產生器中呼叫 `.multipleWriteRegionsEnabled(true)` 和 `.preferredRegions(preferredRegions)` ，其中 `preferredRegions` 是 `List` 包含一個元素的專案-也就是要在其中部署應用程式的區域，以及複寫 Cosmos DB 的位置：

# <a name="async"></a>[非同步](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) 非同步 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[同步處理](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) 同步 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-milti-master"></a> Async JAVA V2 SDK

JAVA V2 SDK 使用 Maven [.com. azure：： azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)。 若要在應用程式中啟用多重主機，請設定 `policy.setUsingMultipleWriteLocations(true)`，並將 `policy.setPreferredLocations` 設定為要在其中部署應用程式和複寫 Cosmos DB 的區域：

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js、JavaScript 及 TypeScript SDK

若要在應用程式中啟用多重主機，請將 `connectionPolicy.UseMultipleWriteLocations` 設定為 `true`。 此外，將 `connectionPolicy.PreferredLocations` 設定為要在其中部署應用程式的區域，以及複寫 Cosmos DB 的所在區域：

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

若要在應用程式中啟用多重主機，請將 `connection_policy.UseMultipleWriteLocations` 設定為 `true`。 此外，將 `connection_policy.PreferredLocations` 設定為要在其中部署應用程式的區域，以及複寫 Cosmos DB 的所在區域。

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>後續步驟

請閱讀下列文章：

* [使用工作階段權杖在 Azure Cosmos DB 中管理一致性](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB 中的衝突類型和解決原則](conflict-resolution-policies.md)
* [Azure Cosmos DB 中的高可用性](high-availability.md)
* [Azure Cosmos DB 中的一致性層級](consistency-levels.md)
* [在 Azure Cosmos DB 中選擇正確的一致性層級](consistency-levels-choosing.md)
* [Azure Cosmos DB 中的一致性、可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
* [全域調整佈建的輸送量](scaling-throughput.md)
* [全域散發：本質上](global-dist-under-the-hood.md)
