---
title: 適用於 Azure Cosmos DB 的 SQL API 教學課程 (Node.js)
description: 示範如何使用 SQL API 連線及查詢 Azure Cosmos DB 的 Node.js 教學課程
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: a67c00dab33272120097fde75fcf56f24f9f532b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411338"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>教學課程：使用 JavaScript SDK 建置 Node.js 主控台應用程式，以管理 Azure Cosmos DB SQL API 資料

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [非同步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

身為開發人員，您可能有使用 NoSQL 文件資料的應用程式。 您可以在 Azure Cosmos DB 中使用 SQL API 帳戶來儲存及存取此文件資料。 本教學課程說明如何建置 Node.js 主控台應用程式，以建立和查詢 Azure Cosmos DB 資源。

在本教學課程中，您將：

> [!div class="checklist"]
> * 建立並連線至 Azure Cosmos DB 帳戶。
> * 設定您的應用程式。
> * 建立資料庫。
> * 建立容器。
> * 將項目新增至容器。
> * 對項目、容器和資料庫執行基本作業。

## <a name="prerequisites"></a>Prerequisites 

請確定您具有下列資源：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費 Azure 試用](https://azure.microsoft.com/pricing/free-trial/)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 或更新版本。

## <a name="create-azure-cosmos-db-account"></a>建立 Azure Cosmos DB 帳戶

讓我們來建立 Azure Cosmos DB 帳戶。 如果您已經擁有想要使用的帳戶，就可以直接跳到[設定您的 Node.js 應用程式](#SetupNode)。 如果您是使用「Azure Cosmos DB 模擬器」，請依照 [Azure Cosmos DB 模擬器](local-emulator.md)的步驟來設定模擬器，然後直接跳到[設定您的 Node.js 應用程式](#SetupNode)。 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>設定您的 Node.js 應用程式

您開始撰寫建置應用程式的程式碼之前，您可以先建置應用程式的架構。 請執行下列步驟，設定具有架構程式碼的 Node.js 應用程式：

1. 開啟您偏好的終端機。
2. 找出您想儲存 Node.js 應用程式的資料夾或目錄位置。
3. 使用下列命令，建立兩個空白的 JavaScript 檔案：

   * Windows：
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X：
     * ```touch app.js```
     * ```touch config.js```

4. 建立 `package.json` 檔案並將其初始化。 使用下列命令：
   * ```npm init -y```

5. 透過 npm 安裝 @azure/cosmos 模組。 使用下列命令：
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>設定應用程式的組態

現在，您的應用程式以存在，您必須確定它可與 Azure Cosmos DB 通訊。 藉由更新一些組態設定 (如下列步驟所示)，您即可設定應用程式而使其與 Azure Cosmos DB 通訊：

1. 在您慣用的文字編輯器中開啟 ```config.js```。

1. 複製並貼上以下的程式碼片段，以及將屬性 ```config.endpoint``` 和 ```config.key``` 設定為您的 Azure Cosmos DB 端點 URI 和主要金鑰。 您可以在 [Azure 入口網站](https://portal.azure.com)找到這些設定。

   ![從 Azure 入口網站取得金鑰的螢幕擷取畫面][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.key = "~your primary key here~";
   ``` 

1. 在您設定 ```config.endpoint``` 和 ```config.key``` 屬性的位置下面，複製 ```database```、```container``` 和 ```items``` 資料並貼到您的 ```config``` 物件。 如果您已有想要儲存於資料庫中的資料，您可以使用 Azure Cosmos DB 中的資料移轉工具，而無須在此處定義資料。 您的 config.js 檔案應該有下列程式碼：

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   JavaScript SDK 會使用通用詞彙*容器*和*項目*。 容器可以是集合、圖形或資料表。 項目可以是文件、邊緣/頂點或資料列，並且是容器內的內容。 
   
   發出的 `module.exports = config;` 程式碼用於匯出您的 ```config``` 物件，如此就可以在 ```app.js``` 檔案中參考它。

## <a name="connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>連線至 Azure Cosmos DB 帳戶

1. 在文字編輯器中開啟您的空白 ```app.js``` 檔案。 複製並貼上以下的程式碼，以匯入 ```@azure/cosmos``` 模組和新建的 ```config``` 模組。

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. 複製並貼上以下的程式碼，以使用先前儲存的 ```config.endpoint``` 和 ```config.key``` 來建立新的 CosmosClient。

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });
   ```
   
> [!Note]
> 如果連線到 **Cosmos DB 模擬器**，請停用節點程序中的 TLS 驗證：
>   ```
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

您現在已有可將 Azure Cosmos DB 用戶端初始化的程式碼，接下來我們將討論如何使用 Azure Cosmos DB 資源。

## <a name="create-a-database"></a>建立資料庫

1. 複製並貼上下列程式碼，以設定資料庫識別碼和容器識別碼。 這些識別碼可讓 Azure Cosmos DB 用戶端尋找正確的資料庫和容器。

   ```javascript
   const client = new CosmosClient({ endpoint, key });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };
   ```

   資料庫可使用 **Databases** 類別的 `createIfNotExists` 或 create 函式來建立。 資料庫是分割於多個容器之項目的邏輯容器。 

2. 複製 **createDatabase** 和 **readDatabase** 方法，並將其貼到 app.js 檔案中的 ```databaseId``` 和 ```containerId``` 定義下方。 **createDatabase** 函式會以識別碼 ```FamilyDatabase``` 建立新的資料庫；如果該識別碼尚不存在，則會從 ```config``` 物件指定。 **readDatabase** 函式會讀取資料庫的定義，以確定該資料庫確實存在。

   ```javascript
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { resource: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. 複製下方的程式碼，並將其貼到您設定 **createDatabase** 和 **readDatabase** 函式的位置，以新增會列印結束訊息的協助程式函式 **exit**。 

   ```javascript
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. 複製下方的程式碼，並將其貼到您設定 **exit** 函式的位置，以呼叫 **createDatabase** 和 **readDatabase** 函式。

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   此時，您在 ```app.js``` 中的程式碼現在看起來應如下列程式碼：

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { resource: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. 在終端機中，找出您的 ```app.js``` 檔案並執行命令： 

   ```bash 
   node app.js
   ```

## <a name="create-a-container"></a><a id="CreateContainer"></a>建立容器

接著，請在 Azure Cosmos DB 帳戶內建立容器，以便儲存和查詢資料。 

> [!WARNING]
> 建立容器有一定的收費。 請瀏覽我們的[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)，以了解相關價格。

容器可使用 **Containers** 類別的 `createIfNotExists` 或 create 函式來建立。  容器中包含項目 (在 SQL API 的案例中為 JSON 文件) 和相關聯的 JavaScript 應用程式邏輯。

1. 將 **createContainer** 和 **readContainer** 函式複製並貼到 app.js 檔案中的 **readDatabase** 函式下方。 **createContainer** 函式會以 ```containerId``` 建立新的容器；如果該識別碼尚不存在，則會從 ```config``` 物件指定。 **readContainer** 函式會讀取容器定義以確認容器是否存在。

   ```javascript
   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { resource: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. 複製下列程式碼，並將其貼到 **readDatabase** 的呼叫下方，以執行 **createContainer** 和 **readContainer** 函式。

   ```javascript
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   此時，您在 ```app.js``` 中的程式碼應會顯示如下：

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { resource: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 在終端機中，找出您的 ```app.js``` 檔案並執行命令： 

   ```bash 
   node app.js
   ```

## <a name="create-an-item"></a><a id="CreateItem"></a>建立項目

項目可以使用 **Items** 類別的 create 函式來建立。 使用 SQL API 時，預期的項目會是文件，這是使用者定義的 (任意) JSON 內容。 您現在可以將項目插入 Azure Cosmos DB 中。

1. 將 **createFamilyItem** 函式複製並貼到 **readContainer** 函式下方。 **createFamilyItem** 函式會建立項目，其中包含儲存在 ```config``` 物件中的 JSON 資料。 在建立項目之前，我們會先確認沒有使用相同識別碼的項目存在。

   ```javascript
   /**
   * Create family item
   */
   async function createFamilyItem(itemBody) {
      const { item } = await client.database(databaseId).container(containerId).items.upsert(itemBody);
      console.log(`Created family item with id:\n${itemBody.id}\n`);
   };
   ```

1. 將下列程式碼複製並貼到 **readContainer** 的呼叫下方，以執行 **createFamilyItem** 函式。

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 在終端機中，找出您的 ```app.js``` 檔案並執行命令： 

   ```bash 
   node app.js
   ```


## <a name="query-azure-cosmos-db-resources"></a><a id="Query"></a>查詢 Azure Cosmos DB 資源

Azure Cosmos DB 支援對儲存於每個容器中的 JSON 文件進行豐富查詢。 下列範例程式碼說明您可以對容器中的文件執行的查詢。

1. 將 **queryContainer** 函式複製並貼到 app.js 檔案中的 **createFamilyItem** 函式下方。 Azure Cosmos DB 支援類 SQL 查詢，如下所示。

   ```javascript
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { resources } = await client.database(databaseId).container(containerId).items.query(querySpec).fetchAll();
    for (var queryResult of resources) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. 將下列程式碼複製並貼到 **createFamilyItem** 的呼叫下方，以執行 **queryContainer** 函式。

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 在終端機中，找出您的 ```app.js``` 檔案並執行命令：

   ```bash 
   node app.js
   ```


## <a name="replace-an-item"></a><a id="ReplaceItem"></a>取代項目
Azure Cosmos DB 支援取代項目的內容。

1. 將 **replaceFamilyItem** 函式複製並貼到 app.js 檔案中的 **queryContainer** 函式下方。 請注意，我們已將子系的屬性「等級」從先前的值 5 變更為 6。

   ```javascript
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
     const { item } = await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).replace(itemBody);
   };
   ```

1. 將下列程式碼複製並貼到 **queryContainer** 的呼叫下方，以執行 **replaceFamilyItem** 函式。 此外，再次將此程式碼加入至 **queryContainer** 呼叫，以確認已成功變更項目。

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 在終端機中，找出您的 ```app.js``` 檔案並執行命令：

   ```bash 
   node app.js
   ```


## <a name="delete-an-item"></a><a id="DeleteItem"></a>刪除項目

Azure Cosmos DB 支援刪除 JSON 項目。

1. 將 **deleteFamilyItem** 函式複製並貼到 **replaceFamilyItem** 函式下方。

   ```javascript
   /**
   * Delete the item by ID.
   */
   async function deleteFamilyItem(itemBody) {
     await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. 將下列程式碼複製並貼到對第二個 **queryContainer** 的呼叫下方，以執行 **deleteFamilyItem** 函式。

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 在終端機中，找出您的 ```app.js``` 檔案並執行命令： 

   ```bash 
   node app.js
   ```


## <a name="delete-the-database"></a><a id="DeleteDatabase"></a>刪除資料庫

刪除已建立的資料庫將會移除資料庫和所有子系資源 (容器、項目等)。

1. 將 **cleanup** 函式複製並貼到 **deleteFamilyItem** 函式下方，以移除資料庫及其所有的子系資源。

   ```javascript
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. 將下列程式碼複製並貼到 **deleteFamilyItem** 的呼叫下方，以執行 **cleanup** 函式。

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a name="run-your-nodejs-application"></a><a id="Run"></a>執行 Node.js 應用程式

整體來看，您的程式碼應會顯示如下：

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

在終端機中，找出您的 ```app.js``` 檔案並執行命令： 

```bash 
node app.js
```

您應該可以看到入門應用程式的輸出。 輸出應該會與下列範例文字相符。

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

## <a name="get-the-complete-nodejs-tutorial-solution"></a><a id="GetSolution"></a>取得完整的 Node.js 教學課程方案 

如果您沒有時間完成本教學課程中的步驟，或只想要下載程式碼，您可以從 [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ) 取得程式碼。 

若要執行包含本文中所有程式碼的入門解決方案，您將需要： 

* [Azure Cosmos DB 帳戶][create-account]。 
* 您可以在 GitHub 上找到[入門](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started)解決方案。 

透過 npm 安裝專案的相依性。 使用下列命令： 

* ```npm install``` 

接下來，更新 ```config.js``` 檔案中的 config.endpoint 和 config.key 值，如[步驟 3：設定您的應用程式組態](#Config)所說明。  

然後，在終端機中找出您的 ```app.js``` 檔案，並執行命令：  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>清除資源

若不再需要這些資源，您可以刪除資源群組、Azure Cosmos DB 帳戶和所有相關資源。 請選取用於 Azure Cosmos DB 帳戶的資源群組，選取 [刪除]  ，然後確認要刪除的資源群組名稱，即可刪除資源。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [監視 Azure Cosmos DB 帳戶](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
