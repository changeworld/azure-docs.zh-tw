---
title: 使用 Mongoose 將 Angular 應用程式連線到適用於 MongoDB 的 Azure Cosmos DB API
description: 本教學課程說明如何使用 Angular 和 Express 建置 Node.js 應用程式，以管理 Cosmos DB 中儲存的資料。 在這個部分中，您會使用 Mongoose 連線至 Azure Cosmos DB。
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: fb0e08c9d85f4472a2fa13001e71edd12149d430
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858725"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>使用 Azure Cosmos DB 適用於 MongoDB 的 API 建立 Angular 應用程式

這個多部分的教學課程示範如何使用 Express 和 Angular 建立 Node.js 應用程式，然後將它連線至[使用 Cosmos DB 適用於 MongoDB 的 API 設定的 Cosmos 帳戶](mongodb-introduction.md)。 本文說明教學課程的第 5 部分並以 [第 4 部分](tutorial-develop-mongodb-nodejs-part4.md)為基礎。

在教學課程的這個部分中，您會：

> [!div class="checklist"]
> * 使用 Mongoose 連線至 Cosmos DB。
> * 取得 Cosmos DB 連接字串。
> * 建立 Hero 模型。
> * 建立 Hero 服務以取得 Hero 資料。
> * 在本機執行應用程式。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

* 在開始本教學課程之前，請完成[第 4 部分](tutorial-develop-mongodb-nodejs-part4.md)的步驟。

* 此教學課程需要您在本機執行 Azure CLI。 您必須安裝 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

* 本教學課程會為您解說逐步建置應用程式的步驟。 如果您需要下載已完成的專案，您可以從 GitHub 上的 [angular-cosmosdb 存放庫](https://github.com/Azure-Samples/angular-cosmosdb)取得已完成的應用程式。

## <a name="use-mongoose-to-connect"></a>使用 Mongoose 進行連線

Mongoose 是適用於 MongoDB 及 Node.js 的物件資料模型 (ODM) 程式庫。 您可以使用 Mongoose 連線至 Azure Cosmos DB 帳戶。 使用下列步驟，安裝 Mongoose 並連線至 Azure Cosmos DB：

1. 安裝 mongoose npm 模組，這是用來與 MongoDB 連繫的 API。

    ```bash
    npm i mongoose --save
    ```

1. 在 **server** 資料夾中，建立名為 **mongo.js** 的檔案。 您會將 Azure Cosmos DB 帳戶的連線詳細資料新增至此檔案。

1. 將以下程式碼複製到 **mongo.js** 檔案。 此程式碼提供下列功能：

   * 需要 Mongoose。
   * 覆寫 Mongo 承諾，以使用 ES6/ES2015 內建和更新版本的基本承諾。
   * 呼叫 env 檔案，該檔案可讓您根據您是在預備、生產或開發環境來設定某些項目。 您會在下一節建立該檔案。
   * 包含 MongoDB 連接字串，該字串會設定於 env 檔案中。
   * 建立可呼叫 Mongoose 的連線函式。

     ```javascript
     const mongoose = require('mongoose');
     /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
     */
     mongoose.Promise = global.Promise;

     const env = require('./env/environment');

     // eslint-disable-next-line max-len
     const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

     function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
     }

     module.exports = {
     connect,
     mongoose
     };
     ```
    
1. 在 [總管] 窗格中，於 **server** 之下建立名為 **environment** 的資料夾。 在 **environment** 資料夾中，建立名為 **environment.js** 的檔案。

1. 從 mongo.js 檔案，我們需要包含 `dbName`、`key` 和 `cosmosPort` 參數的值。 將以下程式碼複製到 **environment.js** 檔案：

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>取得連接字串

若要將應用程式與 Azure Cosmos DB 連線，您需要更新應用程式的組態設定。 使用下列步驟來更新設定： 

1. 在 Azure 入口網站中，取得您 Azure Cosmos DB 帳戶的連接埠號碼、Azure Cosmos DB 帳戶名稱和主索引鍵值。

1. 在 **environment.js** 中，將 `port` 的值變更為 10255。 

    ```javascript
    const port = 10255;
    ```

1. 在 **environment.js** 中，將 `accountName` 的值變更為您在教學課程的[第 4 部分](tutorial-develop-mongodb-nodejs-part4.md)中建立的 Azure Cosmos DB 帳戶名稱。 

1. 在終端機視窗中使用下列的 CLI 命令，擷取 Azure Cosmos DB 帳戶的主索引鍵： 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name> 是您在教學課程的[第 4 部分](tutorial-develop-mongodb-nodejs-part4.md)中建立的 Azure Cosmos DB 帳戶名稱。

1. 將主索引鍵複製到 **environment.js** 檔案中作為 `key` 值。

您的應用程式現在具有連線到 Azure Cosmos DB 所需的所有資訊。 

## <a name="create-a-hero-model"></a>建立 Hero 模型

接下來，您需要定義模型檔案，以定義要儲存在 Azure Cosmos DB 中的資料結構描述。 使用下列步驟來建立「Hero 模型」  ，該模型可定義資料的結構描述：

1. 在 [總管] 窗格中，於 **server** 資料夾之下建立名為 **hero.model.js** 的檔案。

1. 將以下程式碼複製到 **hero.model.js** 檔案。 此程式碼提供下列功能：

   * 需要 Mongoose。
   * 建立具有識別碼、名稱和招呼語的新結構描述。
   * 使用結構描述建立模型。
   * 匯出磁碟。 
   * 將集合命名為 **Heroes** (而不是 **Heros**，這是以 Mongoose 複數命名規則為基礎的集合預設名稱)。

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>建立 Hero 服務

建立 Hero 模型之後，您必須定義可讀取資料的服務，並執行 list、create、delete 和 update 作業。 使用下列步驟來建立「Hero 服務」  ，該服務可查詢 Azure Cosmos DB 的資料：

1. 在 [總管] 窗格中，於 **server** 資料夾之下建立名為 **hero.service.js** 的檔案。

1. 將以下程式碼複製到 **hero.service.js** 檔案。 此程式碼提供下列功能：

   * 取得您建立的模型。
   * 連線到資料庫。
   * 建立 `docquery` 變數，其使用 `hero.find` 方法來定義可傳回所有 Hero 的查詢。
   * 使用具有承諾的 `docquery.exec` 函式執行查詢，以取得所有 Hero 的清單，其中的回應狀態為 200。 
   * 如果狀態為 500，則會送回錯誤訊息。
   * 因為我們正在使用模組，所以會取得 Hero。 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="configure-routes"></a>設定路由

接下來，您需要設定路由，以處理 get、create、read 和 delete 要求的 URL。 路由方法可指定回呼函式 (也稱為「處理常式函式」  )。 當應用程式收到對所指定端點和 HTTP 方法的要求時，就會呼叫這些函式。 使用下列步驟來新增 Hero 服務及定義路由：

1. 在 Visual Studio Code 中，於 **routes.js** 檔案中註解排除可傳送範例 Hero 資料的 `res.send` 函式。 改為新增一行來呼叫 `heroService.getHeroes` 函式。

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. 在 **routes.js** 檔案中，`require` Hero 服務：

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. 在 **hero.service.js** 檔案中，更新 `getHeroes` 函式以接受 `req` and `res` 參數，如下所示：

    ```javascript
    function getHeroes(req, res) {
    ```

讓我們花點時間檢閱和逐步解說程式碼。 首先，我們會進入 index.js 檔案，該檔案可設定節點伺服器。 請注意它會設定及定義您的路由。 接下來，routes.js 檔案會與 Hero 服務交談並告知取得您的函式 (例如 **getHeroes**)，然後傳遞要求和回應。 hero.service.js 檔案會取得模型並連線至 Mongo。 然後會在我們呼叫它時執行 **getHeroes**，並傳回回應 200。 

## <a name="run-the-app"></a>執行應用程式

接著，使用下列步驟來執行應用程式：

1. 在 Visual Studio Code 中，儲存所有變更。 選取左側的 [偵錯]  按鈕 ![Visual Studio Code 中的偵錯圖示](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png)，然後選取 [開始偵錯]  按鈕 ![Visual Studio Code 中的偵錯圖示](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png)。

1. 現在切換至瀏覽器。 開啟 [開發人員工具]  和 [網路]  索引標籤。移至 `http://localhost:3000`，您會看到我們的應用程式。

    ![Azure 入口網站中的新 Azure Cosmos DB 帳戶](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

應用程式中尚未儲存任何 Hero。 在本教學課程的下一個部分，我們會新增 put、push 和 delete 功能。 然後我們可以使用 Azure Cosmos 資料庫的 Mongoose 連線，在 UI 中新增、更新和刪除 Hero。 

## <a name="clean-up-resources"></a>清除資源

若不再需要這些資源，您可以刪除資源群組、Azure Cosmos DB 帳戶和所有相關資源。 使用下列步驟來刪除資源群組：

 1. 移至您在其中建立 Azure Cosmos DB 帳戶的資源群組。
 1. 選取 [刪除資源群組]  。
 1. 確認要刪除的資源群組名稱，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

繼續進行本教學課程的第 6 部分，將 Post、Put 和 Delete 函式新增至應用程式：

> [!div class="nextstepaction"]
> [第 6 部分：將 Post、Put 和 Delete 函式新增至應用程式](tutorial-develop-mongodb-nodejs-part6.md)
