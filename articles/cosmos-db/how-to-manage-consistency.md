---
title: 在 Azure Cosmos DB 中管理一致性
description: 瞭解如何使用 Azure 入口網站、.NET SDK、JAVA SDK 和各種其他 Sdk，在 Azure Cosmos DB 中設定和管理一致性層級
author: anfeldma-ms
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/10/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 4be2b8cdd987b6357df283f0791593c51417dfc7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101488"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>管理 Azure Cosmos DB 中的一致性層級
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文說明如何管理 Azure Cosmos DB 中的一致性層級。 您會了解如何設定預設一致性、覆寫預設一致性、手動管理工作階段權杖，並了解概率限定過期 (PBS) 計量。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>設定預設一致性層級

[預設一致性層級](consistency-levels.md)是用戶端依預設使用的一致性層級。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要檢視或修改預設一致性層級，請登入 Azure 入口網站。 尋找您的 Azure Cosmos 帳戶，然後開啟 [預設一致性]  窗格。 選取要作為新預設值的一致性層級，然後選取 [儲存]  。 Azure 入口網站也會提供不同一致性層級 (含音符) 的視覺效果。 

:::image type="content" source="./media/how-to-manage-consistency/consistency-settings.png" alt-text="Azure 入口網站中的一致性功能表":::

# <a name="cli"></a>[CLI](#tab/cli)

建立具有會話一致性的 Cosmos 帳戶，然後更新預設一致性。

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

建立具有會話一致性的 Cosmos 帳戶，然後更新預設一致性。

```azurepowershell-interactive
# Create a new account with Session consistency
New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Location $locations -Name $accountName -DefaultConsistencyLevel "Session"

# Update an existing account's default consistency
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Name $accountName -DefaultConsistencyLevel "Strong"
```

---

## <a name="override-the-default-consistency-level"></a>覆寫預設一致性層級

用戶端可以覆寫服務所設定的預設一致性層級。 一致性層級可以根據每個要求來設定，而這會覆寫帳戶層級上設定的預設一致性層級。

> [!TIP]
> 一致性只能在要求層級 **放寬** 。 若要從較弱到更強的一致性進行移動，請更新 Cosmos 帳戶的預設一致性。

### <a name="net-sdk"></a><a id="override-default-consistency-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item,
        new PartitionKey(itemPartitionKey),
        requestOptions);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[非同步](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) 非同步 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencyAsync)]

# <a name="sync"></a>[同步](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) 同步 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySync)]

--- 

### <a name="java-v2-sdks"></a><a id="override-default-consistency-javav2"></a> JAVA V2 Sdk

# <a name="async"></a>[非同步](#tab/api-async)

Async JAVA V2 SDK (Maven .com. azure：： azure-cosmosdb) 

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

# <a name="sync"></a>[同步](#tab/api-sync)

同步 JAVA V2 SDK (Maven .com. azure：： azure-documentdb) 

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>使用工作階段權杖

Azure Cosmos DB 中的其中一個一致性層級是「工作階段」  一致性。 這是預設套用至 Cosmos 帳戶的預設層級。 使用「工作階段」  一致性時，用戶端會搭配每個讀取/查詢要求在內部使用工作階段權杖，以確保維持已設定的一致性層級。

若要手動管理工作階段權杖，請從回應中取得工作階段權杖，並就個別要求加以設定。 如果不需要手動管理工作階段權杖，則不需要使用下列範例。 SDK 會自動持續追蹤工作階段權杖。 如果未手動設定工作階段權杖，則 SDK 預設會使用最新的工作階段權杖。

### <a name="net-sdk"></a><a id="utilize-session-tokens-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[非同步](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) 非同步 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencySessionAsync)]

# <a name="sync"></a>[同步](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) 同步 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySessionSync)]

--- 

### <a name="java-v2-sdks"></a><a id="utilize-session-tokens-javav2"></a>JAVA V2 Sdk

# <a name="async"></a>[非同步](#tab/api-async)

Async JAVA V2 SDK (Maven .com. azure：： azure-cosmosdb) 

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

# <a name="sync"></a>[同步](#tab/api-sync)

同步 JAVA V2 SDK (Maven .com. azure：： azure-documentdb) 

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>監視機率限定過期 (PBS) 計量

最終一致性的界定標準為何？ 針對平均案例，我們可根據版本記錄和時間來提供過期界限。 [**機率限定過期 (PBS)**](https://pbs.cs.berkeley.edu/) 計量會嘗試量化過期的機率，並將其顯示為計量。 若要檢視 PBS 計量，請在 Azure 入口網站中移至您的 Azure Cosmos 帳戶。 開啟 [ **計量** ] 窗格，然後選取 [ **一致性** ] 索引標籤。查看以 **您的工作負載為基礎的強式一致讀取的圖表， (查看 PBS)** 。

:::image type="content" source="./media/how-to-manage-consistency/pbs-metric.png" alt-text="Azure 入口網站中的一致性功能表":::

## <a name="next-steps"></a>後續步驟

深入了解如何管理資料衝突，或繼續 Azure Cosmos DB 中的下一個重要概念。 請參閱下列文章：

* [Azure Cosmos DB 中的一致性層級](consistency-levels.md)
* [資料分割和散佈](./partitioning-overview.md)
* [管理區域之間的衝突](how-to-manage-conflicts.md)
* [資料分割和散佈](partitioning-overview.md)
* [新式分散式資料庫系統設計的一致性取捨](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [高可用性](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)