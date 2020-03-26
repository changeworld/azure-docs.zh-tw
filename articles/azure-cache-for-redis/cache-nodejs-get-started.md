---
title: 快速入門：使用 Azure Cache for Redis 搭配 Node.js
description: 在本快速入門中，您將了解如何搭配使用 Azure Cache for Redis 與 Node.js 和 node_redis。
author: yegu-ms
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2018
ms.author: yegu
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 07e2d6f174e5af4af9bdcac73dc74f5cf061ed41
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78300480"
---
# <a name="quickstart-use-azure-cache-for-redis-with-nodejs"></a>快速入門：使用 Azure Cache for Redis 搭配 Node.js

在本快速入門中，您會將 Azure Cache for Redis 納入 Node.js 應用程式中，以便存取可從 Azure 內任何應用程式存取的安全、專用快取。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
- [node_redis](https://github.com/mranney/node_redis)，您可以使用命令 `npm install redis` 進行安裝。 

如需使用其他 Node.js 用戶端的範例，請參閱列在 [Node.js Redis 用戶端](https://redis.io/clients#nodejs)之 Node.js 用戶端的個別文件。

## <a name="create-a-cache"></a>建立快取
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]


為**主機名稱**和**主要**存取金鑰新增環境變數。 您會從程式碼使用這些變數，而不是直接在程式碼中包含敏感性資訊。

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="connect-to-the-cache"></a>連接到快取

[node_redis](https://github.com/mranney/node_redis) \(英文\) 的最新組建提供使用 SSL 連線到 Azure Cache for Redis 的支援。 下列範例示範如何使用 6380 的 SSL 端點來連線到 Azure Cache for Redis。 

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

請勿對程式碼中的每個作業建立新連線。 相反地，請盡可能重複使用連線。 

## <a name="create-a-new-nodejs-app"></a>建立新的 Node.js 應用程式

建立名為 redistest.js  的新指令檔。 使用命令 `npm install redis bluebird` 安裝必要的套件。

在檔案中新增下列 JavaScript 範例。 此程式碼示範如何使用快取主機名稱和金鑰環境變數來連線至 Azure Cache for Redis 執行個體。 此程式碼也會將字串值儲存到快取中，以及擷取其中的字串值。 `PING` 和 `CLIENT LIST` 命令也會執行。 如需更多搭配使用 Redis 與 [node_redis](https://github.com/mranney/node_redis) 用戶端的範例，請參閱 [https://redis.js.org/](https://redis.js.org/)。

```js
var redis = require("redis");
var bluebird = require("bluebird");

// Convert Redis client API to use promises, to make it usable with async/await syntax
bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);

async function testCache() {

    // Connect to the Azure Cache for Redis over the SSL port using the key.
    var cacheConnection = redis.createClient(6380, process.env.REDISCACHEHOSTNAME, 
        {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
        
    // Perform cache operations using the cache connection object...

    // Simple PING command
    console.log("\nCache command: PING");
    console.log("Cache response : " + await cacheConnection.pingAsync());

    // Simple get and put of integral data types into the cache
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    console.log("\nCache command: SET Message");
    console.log("Cache response : " + await cacheConnection.setAsync("Message",
        "Hello! The cache is working from Node.js!"));    

    // Demonstrate "SET Message" executed as expected...
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    // Get the client list, useful to see if connection list is growing...
    console.log("\nCache command: CLIENT LIST");
    console.log("Cache response : " + await cacheConnection.clientAsync("LIST"));    
}

testCache();
```

使用 Node.js 執行指令碼。

```
node redistest.js
```

在下列範例中，您會看到 `Message` 金鑰先前有快取值，此值是在 Azure 入口網站中使用 Redis 主控台所設定的。 應用程式更新了該快取值。 應用程式也已執行 `PING` 和 `CLIENT LIST` 命令。

![Redis Cache 應用程式已完成](./media/cache-nodejs-get-started/redis-cache-app-complete.png)

## <a name="clean-up-resources"></a>清除資源

如果您準備繼續進行下一個教學課程，則可以保留在本快速入門中所建立的資源，並重複使用它們。

否則，如果您已完成快速入門範例應用程式，便可以將在此快速入門中所建立的 Azure 資源刪除，以避免衍生費用。 

> [!IMPORTANT]
> 刪除資源群組是無法回復的動作，資源群組和其內的所有資源將會永久刪除。 請確定您不會不小心刪除錯誤的資源群組或資源。 如果您是在包含有需要保留之資源的現有資源群組內，建立用來裝載此範例的資源，則可以從每個資源各自的刀鋒視窗中個別刪除每個資源，而不必刪除正個資源群組。
>

登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [資源群組]  。

在 [依名稱篩選]  文字方塊中，輸入您資源群組的名稱。 本文的指示是使用名為 TestResources  的資源群組。 在結果清單中的目標資源群組上方，選取 **...** ，然後按一下 [刪除資源群組]  。

![刪除 Azure 資源群組](./media/cache-nodejs-get-started/redis-cache-delete-resource-group.png)

系統將會要求您確認是否刪除資源。 輸入您資源群組的名稱以進行確認，然後選取 [刪除]  。

片刻過後，系統便會刪除該資源群組及其所有內含的資源。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何從 Node.js 應用程式使用 Azure Cache for Redis。 請繼續閱讀下一個快速入門，以搭配使用 Azure Cache for Redis 與 ASP.NET Web 應用程式。

> [!div class="nextstepaction"]
> [建立可使用 Azure Cache for Redis 的 ASP.NET Web 應用程式。](./cache-web-app-howto.md)
