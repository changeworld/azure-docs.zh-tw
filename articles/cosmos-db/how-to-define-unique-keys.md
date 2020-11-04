---
title: 定義 Azure Cosmos 容器的唯一索引鍵
description: 瞭解如何使用 Azure 入口網站、PowerShell、.NET、JAVA 和各種其他 Sdk 來定義 Azure Cosmos 容器的唯一索引鍵。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/02/2019
ms.author: thweiss
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 55fc5222c1c245c56ba0a26caa816c5c845147c1
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336617"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>定義 Azure Cosmos 容器的唯一索引鍵
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文提供不同方式來定義建立 Azure Cosmos 容器時的[唯一索引鍵](unique-keys.md)。 目前可執行這項作業的方式是使用 Azure 入口網站或透過其中一個 SDK。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-sql-api-dotnet.md#create-account)，或選取現有帳戶。

1. 開啟 [資料總管] 窗格，然後選取您要處理的容器。

1. 按一下 [新容器]。

1. 在 [新增容器] 對話方塊中，按一下 [+ 新增唯一索引鍵] 來新增唯一索引鍵項目。

1. 輸入唯一索引鍵條件限制的路徑

1. 如有需要，可按一下 [+ 新增唯一索引鍵] 來新增更多唯一索引鍵項目

    :::image type="content" source="./media/how-to-define-unique-keys/unique-keys-portal.png" alt-text="Azure 入口網站中唯一索引鍵條件限制項目的螢幕擷取畫面":::

## <a name="use-powershell"></a>使用 PowerShell

若要建立具有唯一索引鍵的容器，請參閱 [建立具有唯一索引鍵和 TTL 的 Azure Cosmos 容器](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk"></a>使用 .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

使用 [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 建立新的容器時，`UniqueKeyPolicy` 物件可以用來定義唯一索引鍵的條件限制。

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

使用 [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)建立新的容器時，請使用 SDK 的流暢 API，以簡潔易懂的方式宣告唯一索引鍵。

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>使用 Java SDK

使用 [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) 建立新的容器時，`UniqueKeyPolicy` 物件可以用來定義唯一索引鍵的條件限制。

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>使用 Node.js SDK

使用 [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) 建立新的容器時，`UniqueKeyPolicy` 物件可以用來定義唯一索引鍵的條件限制。

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>使用 Python SDK

使用 [Python SDK](https://pypi.org/project/azure-cosmos/) 建立新的容器時，唯一的索引鍵條件限制可以指定為字典一部分，以作為參數傳遞。

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [分割](partitioning-overview.md)
- 探索[如何為工作編製索引](index-overview.md)
