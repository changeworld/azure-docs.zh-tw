---
title: 搭配 Gremlin SDK 使用 Azure Cosmos DB 資源權杖
description: 了解如何建立資源權杖，並使用這些權杖來存取圖形資料庫。
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 42f3c7f3351bddab429489dccf28587549d76e18
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897837"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>搭配 Gremlin SDK 使用 Azure Cosmos DB 資源權杖

本文說明如何使用 [Azure Cosmos DB 的資源權杖](secure-access-to-data.md)，透過 Gremlin SDK 存取圖形資料庫。

## <a name="create-a-resource-token"></a>建立資源權杖

Apache TinkerPop Gremlin SDK 沒有 API 可用來建立資源權杖。 「資源權杖」  一詞是 Azure Cosmos DB 的概念。 若要建立資源權杖，請下載 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)。 如果您的應用程式需要建立資源權杖，並使用它們來存取圖形資料庫，則需要 2 個不同的 SDK。

下方將概要說明資源權杖上的物件模型階層：

- **Azure Cosmos DB 帳戶** - 具有相關聯 DNS 的最上層實體 (例如 `contoso.gremlin.cosmos.azure.com`)。
  - **Azure Cosmos DB 資料庫**
    - **使用者**
      - **權限**
        - **權杖** - 權限物件屬性，代表允許或拒絕的動作。

資源權杖使用下列格式：`"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`。 此字串對用戶端而言是不透明的，而且應該在不修改或解譯的情況下使用。

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>使用資源權杖
當您建立 GremlinServer 類別時，可以直接使用資源權杖作為「密碼」屬性。

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a master key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

相同的方法適用於所有的 TinkerPop Gremlin SDK。

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>限制

透過單一 Gremlin 帳戶，您可以發出不限數目的權杖。 不過，1 小時內只能同時使用最多 100 個權杖。 如果應用程式超過每小時的權杖限制，則會拒絕驗證要求，而且您會收到下列錯誤訊息：「已超過允許同時使用的 100 個資源權杖限制。」 關閉使用特定權杖的作用中連線不會為新權杖釋出位置。 Azure Cosmos DB Gremlin 資料庫引擎會在緊接驗證要求之前的一小時內，持續追蹤唯一標記。

## <a name="permission"></a>權限

應用程式在使用資源權杖時常遇到的錯誤是：「未在對應要求的授權標頭中提供足夠的權限。 請使用另一個授權標頭並重試。」 當 Gremlin 周遊嘗試寫入邊線或頂點，但資源權杖僅授與「讀取」  權限時，就會傳回此錯誤。 檢查您的周遊是否包含下列任一步驟：.addV()  、.addE()  、.drop()  或 .property()  。

## <a name="next-steps"></a>後續步驟
* Azure Cosmos DB 中的[角色型存取控制](role-based-access-control.md)
* [了解如何保護對 Azure Cosmos DB 中資料的存取](secure-access-to-data.md)
