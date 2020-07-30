---
title: 搭配適用于 MongoDB 的 Azure Cosmos DB API 使用讀取喜好設定
description: 了解如何搭配 Azure Cosmos DB 適用於 MongoDB 的 API 使用 MongoDB 讀取喜好設定
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 02/26/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: 22530e79a2bcf34a88ea857bda56b6f424894461
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421989"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>如何使用 Azure Cosmos DB 適用於 MongoDB 的 API 來設定全域散發讀取

本文說明如何搭配 Azure Cosmos DB 適用於 MongoDB 的 API 使用 [MongoDB 讀取喜好設定](https://docs.mongodb.com/manual/core/read-preference/)來全域散發讀取作業。

## <a name="prerequisites"></a>必要條件 
如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

如需使用 Azure 入口網站透過全域散發來設定 Cosmos 帳戶，然後與其連線的指示，請參閱此[快速入門](tutorial-global-distribution-mongodb.md)一文。

## <a name="clone-the-sample-application"></a>複製範例應用程式

開啟 Git 終端機視窗 (例如 Git Bash)，然後使用 `cd` 來切換到工作目錄。  

執行下列命令來複製範例存放庫。 根據您感興趣的平台，使用下列其中一個範例存放庫：

1. [.NET 範例應用程式](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS 範例應用程式]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose 範例應用程式](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java 範例應用程式](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot 範例應用程式](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>執行應用程式

根據使用的平台，安裝必要的套件，然後啟動應用程式。 若要安裝相依性，請依照範例應用程式存放庫中包含的讀我檔案進行。 例如，在 NodeJS 範例應用程式中，使用下列命令安裝必要套件，然後啟動應用程式。

```bash
cd mean
npm install
node index.js
```
應用程式會嘗試連線到 MongoDB 來源，並因為連接字串無效而失敗。 請依照讀我檔案中的步驟執行以更新連接字串 `url`。 此外，請將 `readFromRegion` 更新為 Cosmos 帳戶中的讀取區域。 下列指示出自 NodeJS 範例：

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

執行這些步驟之後，範例應用程式會執行並會產生下列輸出：

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>使用讀取喜好設定模式來讀取

MongoDB 通訊協定會提供下列讀取喜好設定模式供用戶端使用：

1. PRIMARY
2. PRIMARY_PREFERRED
3. SECONDARY
4. SECONDARY_PREFERRED
5. NEAREST

如需這些讀取喜好設定模式各項行為的詳細資訊，請參閱詳盡的 [MongoDB 讀取喜好設定行為](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) \(英文\) 文件。 在 Cosmos DB 中，primary 會對應至 WRITE 區域，而 secondary 會對應至 READ 區域。

根據常見案例，建議您使用下列設定：

1. 如果需要**低延遲讀取區域**，請使用 **NEAREST** 讀取喜好設定模式。 此設定會將讀取作業導向至最接近的可用區域。 請注意，如果最接近的區域是寫入區域，則會將這些作業導向至該區域。
2. 如果需要**高可用性和異地散發的讀取**（延遲不是條件約束），則請使用**主要慣用**或**次要慣用**的讀取喜好設定模式。 此設定會將讀取作業分別導向至可用的寫入或讀取區域。 如果區域無法使用，則會根據讀取喜好設定行為，將要求導向至下一個可用的區域。

下列來自範例應用程式的程式碼片段示範如何在 NodeJS 中設定 NEAREST 讀取喜好設定：

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

同樣地，下面的程式碼片段會示範如何在 NodeJS 中設定 SECONDARY_PREFERRED 讀取喜好設定：

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

將 `readPreference` 當作參數在連接字串 URI 選項中傳遞，也可以設定 [讀取喜好設定]：

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

請參閱其他平台對應的範例應用程式存放庫，例如 [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) 和 [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)。

## <a name="read-using-tags"></a>使用標記讀取

除了讀取喜好設定模式之外，MongoDB 通訊協定還允許使用標記來導向讀取作業。 在 Cosmos DB 適用於 MongoDB 的 API 中，預設會包含 `region` 標記當作 `isMaster` 回應的一部分：

```json
"tags": {
         "region": "West US"
      }
```

因此，MongoClient 可以將 `region` 標記和區域名稱一起使用，將讀取作業導向至特定區域。 針對 Cosmos 帳戶，您可以在 Azure 入口網站左邊的 [設定] -> [全域複寫資料]**** 下找到區域名稱。 此設定對於達成**讀取隔離**很有幫助，但只限用戶端應用程式想要將讀取作業導向至特定區域的情況。 此設定適用於在背景中執行且不是實際執行的重要服務之類的非生產/分析類型案例。

下列來自範例應用程式的程式碼片段示範如何在 NodeJS 中搭配標記來設定讀取喜好設定：

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

請參閱其他平台對應的範例應用程式存放庫，例如 [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) 和 [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)。

在本文中，您已了解如何搭配 Azure Cosmos DB 適用於 MongoDB 的 API 使用讀取喜好設定來全域散發讀取作業。

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除此文章所建立的所有資源：

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]****，然後按一下您所建立資源的名稱。 
2. 在資源群組頁面上，按一下 [刪除]****，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]****。

## <a name="next-steps"></a>後續步驟

* [將 MongoDB 資料匯入到 Azure Cosmos DB](mongodb-migrate.md)
* [使用 Azure Cosmos DB 適用於 MongoDB 的 API 來設定全域散發的資料庫](tutorial-global-distribution-mongodb.md)
* [使用 Azure Cosmos DB 模擬器在本機進行開發](local-emulator.md)
