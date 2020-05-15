---
title: 教學課程：使用 Azure Cosmos DB JavaScript SDK 建置 Node.js Web 應用程式來管理 SQL API 資料
description: 這個 Node.js 教學課程會探索如何使用 Microsoft Azure Cosmos DB，從 Node.js Express Web 應用程式 (裝載在 Microsoft Azure App Service 的 Web Apps 功能中) 儲存和存取資料。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: 9257a87c69e98db1107528551fe9ce0553c50f75
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858161"
---
# <a name="tutorial-build-a-nodejs-web-app-using-the-javascript-sdk-to-manage-a-sql-api-account-in-azure-cosmos-db"></a>教學課程：使用 JavaScript SDK 建置 Node.js Web 應用程式來管理 Azure Cosmos DB 中的 SQL API 帳戶 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

身為開發人員，您可能有使用 NoSQL 文件資料的應用程式。 您可以在 Azure Cosmos DB 中使用 SQL API 帳戶來儲存及存取此文件資料。 此 Node.js 教學課程說明如何使用 Node.js Express 應用程式 (裝載在 Microsoft Azure App Service 的 Web Apps 功能中)，從 Azure Cosmos DB 中的 SQL API 帳戶儲存和存取資料。 在本教學課程中，您會建置一個網頁型應用程式 (待辦事項清單應用程式)，以便能夠建立、擷取和完成工作。 在 Azure Cosmos DB 中，這些工作會儲存為 JSON 文件。 

本教學課程示範如何使用 Azure 入口網站在 Azure Cosmos DB 中建立 SQL API 帳戶。 然後，您會建置和執行以 Node.js SDK 為基礎的 Web 應用程式，以建立資料庫和容器，並在容器中新增項目。 本教學課程使用 JavaScript SDK 3.0 版。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 建立 Azure Cosmos DB 帳戶
> * 建立新的 Node.js 應用程式
> * 將應用程式連線至 Azure Cosmos DB
> * 執行應用程式並將其部署至 Azure

## <a name="prerequisites"></a><a name="_Toc395783176"></a>必要條件

依照本文的指示進行之前，請確定您具備下列資源：

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] 6.10 版或更高版本。
* [Express 產生器](https://www.expressjs.com/starter/generator.html) (您可以透過 `npm install express-generator -g` 安裝 Express)
* 在您的本機工作站上安裝 [Git][Git]。

## <a name="create-an-azure-cosmos-db-account"></a><a name="_Toc395637761"></a>建立 Azure Cosmos DB 帳戶
我們將從建立 Azure Cosmos DB 帳戶開始著手。 如果您已經擁有帳戶，或如果您正在使用 Azure Cosmos DB 模擬器來進行本教學課程，可以跳到[步驟 2：建立新的 Node.js 應用程式](#_Toc395783178)。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="create-a-new-nodejs-application"></a><a name="_Toc395783178"></a>建立新的 Node.js 應用程式
現在，我們來了解如何使用 Express 架構來建立基本的 Hello World Node.js 專案。

1. 開啟您喜好的終端機，例如 Node.js 命令提示字元。

1. 瀏覽至您想要在其中儲存新應用程式的目錄。

1. 使用 Express 產生器來產生名為 **todo**的新應用程式。

   ```bash
   express todo
   ```

1. 開啟 **todo** 目錄並安裝相依性。

   ```bash
   cd todo
   npm install
   ```

1. 執行新的應用程式。

   ```bash
   npm start
   ```

1. 您可以檢視新的應用程式，請導覽瀏覽器至 `http://localhost:3000`。
   
   ![了解 Node.js - Hello World 應用程式在瀏覽器視窗中的螢幕擷取畫面](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

   在終端機視窗中使用 CTRL+C 停止應用程式，然後選取 **y** 以終止批次作業。

## <a name="install-the-required-modules"></a><a name="_Toc395783179"></a>安裝必要的模組

**package.json** 檔案是建立在專案根目錄中的其中一個檔案。 這個檔案包含 Node.js 應用程式所需的其他模組清單。 當您將此應用程式部署至 Azure 時，此檔案可用來決定 Azure 上應安裝哪些模組才能支援您的應用程式。 再安裝兩個本教學課程所需的套件。

1. 透過 npm 安裝 **\@azure/cosmos** 模組。 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="connect-the-nodejs-application-to-azure-cosmos-db"></a><a name="_Toc395783180"></a>將 Node.js 應用程式連線至 Azure Cosmos DB
您已完成初始安裝和設定，接下來您會撰寫所需的程式碼，以供待辦事項應用程式與 Azure Cosmos DB 通訊。

### <a name="create-the-model"></a>建立模型
1. 在專案目錄的根目錄中，建立一個名為 **models** 的新目錄。  

2. 在 **models** 目錄中，建立名為 **taskDao.js** 的新檔案。 此檔案包含建立資料庫和容器所需的程式碼。 其中也會定義用來在 Azure Cosmos DB 中讀取、更新、建立和尋找工作的方法。 

3. 將下列程式碼複製到 **taskDao.js** 檔案：

   ```javascript
    // @ts-check
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const debug = require('debug')('todo:taskDao')

    // For simplicity we'll set a constant partition key
    const partitionKey = undefined
    class TaskDao {
      /**
       * Manages reading, adding, and updating Tasks in Cosmos DB
       * @param {CosmosClient} cosmosClient
       * @param {string} databaseId
       * @param {string} containerId
       */
      constructor(cosmosClient, databaseId, containerId) {
        this.client = cosmosClient
        this.databaseId = databaseId
        this.collectionId = containerId

        this.database = null
        this.container = null
      }

      async init() {
        debug('Setting up the database...')
        const dbResponse = await this.client.databases.createIfNotExists({
          id: this.databaseId
        })
        this.database = dbResponse.database
        debug('Setting up the database...done!')
        debug('Setting up the container...')
        const coResponse = await this.database.containers.createIfNotExists({
          id: this.collectionId
        })
        this.container = coResponse.container
        debug('Setting up the container...done!')
      }

      async find(querySpec) {
        debug('Querying for items from the database')
        if (!this.container) {
          throw new Error('Collection is not initialized.')
        }
        const { resources } = await this.container.items.query(querySpec).fetchAll()
        return resources
      }

      async addItem(item) {
        debug('Adding an item to the database')
        item.date = Date.now()
        item.completed = false
        const { resource: doc } = await this.container.items.create(item)
        return doc
      }

      async updateItem(itemId) {
        debug('Update an item in the database')
        const doc = await this.getItem(itemId)
        doc.completed = true

        const { resource: replaced } = await this.container
          .item(itemId, partitionKey)
          .replace(doc)
        return replaced
      }

      async getItem(itemId) {
        debug('Getting an item from the database')
        const { resource } = await this.container.item(itemId, partitionKey).read()
        return resource
      }
    }

    module.exports = TaskDao
   ```
4. 儲存並關閉 **taskDao.js** 檔案。  

### <a name="create-the-controller"></a>建立控制器

1. 在專案的 **routes** 目錄中，建立名為 **tasklist.js** 的新檔案。  

2. 在 **tasklist.js**中加入以下程式碼。 此程式碼會載入供 **tasklist.js** 使用的 CosmosClient 和 async 模組。 此程式碼也會定義 **TaskList** 類別，此類別會傳遞作為我們稍早定義的 **TaskDao** 物件執行個體：
   
   ```javascript
    const TaskDao = require("../models/TaskDao");
    
    class TaskList {
      /**
       * Handles the various APIs for displaying and managing tasks
       * @param {TaskDao} taskDao
       */
      constructor(taskDao) {
        this.taskDao = taskDao;
      }
      async showTasks(req, res) {
        const querySpec = {
          query: "SELECT * FROM root r WHERE r.completed=@completed",
          parameters: [
            {
              name: "@completed",
              value: false
            }
          ]
        };

        const items = await this.taskDao.find(querySpec);
        res.render("index", {
          title: "My ToDo List ",
          tasks: items
        });
      }

      async addTask(req, res) {
        const item = req.body;

        await this.taskDao.addItem(item);
        res.redirect("/");
      }

      async completeTask(req, res) {
        const completedTasks = Object.keys(req.body);
        const tasks = [];

        completedTasks.forEach(task => {
          tasks.push(this.taskDao.updateItem(task));
        });

        await Promise.all(tasks);

        res.redirect("/");
      }
    }

    module.exports = TaskList;
   ```

3. 儲存並關閉 **tasklist.js** 檔案。

### <a name="add-configjs"></a>新增 config.js

1. 在專案目錄的根目錄中，建立名為 **config.js** 的新檔案。 

2. 將下列程式碼新增至 **config.js** 檔案。 此程式碼會定義組態設定和應用程式所需的值。
   
   ```javascript
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. 在 **config.js** 檔案中，使用 [Azure 入口網站](https://portal.azure.com)上 Azure Cosmos DB 帳戶的 [金鑰] 頁面中找到的值，更新 HOST 和 AUTH_KEY 的值。 

4. 儲存並關閉 **config.js** 檔案。

### <a name="modify-appjs"></a>修改 app.js

1. 在專案目錄中，開啟 **app.js** 檔案。 這是稍早建立 Express Web 應用程式時所建立的檔案。  

2. 將下列程式碼新增至 **app.js** 檔案。 此程式碼會定義要使用的組態檔，並將值載入您在後續章節將用到的某些變數中。 
   
   ```javascript
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const config = require('./config')
    const TaskList = require('./routes/tasklist')
    const TaskDao = require('./models/taskDao')

    const express = require('express')
    const path = require('path')
    const logger = require('morgan')
    const cookieParser = require('cookie-parser')
    const bodyParser = require('body-parser')

    const app = express()

    // view engine setup
    app.set('views', path.join(__dirname, 'views'))
    app.set('view engine', 'jade')

    // uncomment after placing your favicon in /public
    //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
    app.use(logger('dev'))
    app.use(bodyParser.json())
    app.use(bodyParser.urlencoded({ extended: false }))
    app.use(cookieParser())
    app.use(express.static(path.join(__dirname, 'public')))

    //Todo App:
    const cosmosClient = new CosmosClient({
      endpoint: config.host,
      key: config.authKey
    })
    const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId)
    const taskList = new TaskList(taskDao)
    taskDao
      .init(err => {
        console.error(err)
      })
      .catch(err => {
        console.error(err)
        console.error(
          'Shutting down because there was an error settinig up the database.'
        )
        process.exit(1)
      })

    app.get('/', (req, res, next) => taskList.showTasks(req, res).catch(next))
    app.post('/addtask', (req, res, next) => taskList.addTask(req, res).catch(next))
    app.post('/completetask', (req, res, next) =>
      taskList.completeTask(req, res).catch(next)
    )
    app.set('view engine', 'jade')

    // catch 404 and forward to error handler
    app.use(function(req, res, next) {
      const err = new Error('Not Found')
      err.status = 404
      next(err)
    })

    // error handler
    app.use(function(err, req, res, next) {
      // set locals, only providing error in development
      res.locals.message = err.message
      res.locals.error = req.app.get('env') === 'development' ? err : {}

      // render the error page
      res.status(err.status || 500)
      res.render('error')
    })

    module.exports = app
   ```

3. 最後，儲存並關閉 **app.js** 檔案。

## <a name="build-a-user-interface"></a><a name="_Toc395783181"></a>建置使用者介面

現在我們將建置使用者介面，讓使用者可以與應用程式互動。 我們在前幾節中建立的 Express 應用程式使用 **Jade** 作為檢視引擎。

1. **views** 目錄中的 **layout.jade** 檔是用來作為其他 **.jade** 檔案的全域範本。 在此步驟中，您將會將其修改為使用 Twitter Bootstrap，這是用來設計網站的工具組。  

2. 開啟在 **views** 資料夾中找到的 **layout.jade** 檔案，並將其中的內容取代為下列程式碼：

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    此程式碼會指示 **Jade** 引擎轉譯出我們應用程式的部分 HTML，並建立稱為 **content** 的**區塊**，讓我們能在其中提供內容頁面的配置。 儲存並關閉 **layout.jade** 檔案。

3. 現在，開啟 **index.jade** 檔案 (應用程式即將使用的檢視)，並將檔案中的內容取代為下列程式碼：

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

此程式碼會擴充配置，並為我們在前面的 **layout.jade** 檔案中看到的 **content** 預留位置提供內容。 在此配置中，我們建立了兩個 HTML 表單。

第一個表單包含資料的表格，以及可讓您透過張貼到控制器的 **/completeTask** 方法來更新項目的按鈕。
    
第二個表單包含兩個輸入欄位，以及可讓您透過張貼到控制器的 **/addtask** 方法來建立項目的按鈕。 這就是應用程式運作所需的一切。

## <a name="run-your-application-locally"></a><a name="_Toc395783181"></a>在本機執行您的應用程式

現在，應用程式已建立，您可以使用下列步驟在本機執行此應用程式：  

1. 若要在本機電腦上測試應用程式，請在終端機中執行 `npm start` 以啟動應用程式，然後重新整理 `http://localhost:3000` 瀏覽器頁面。 頁面此時應如下列螢幕擷取畫面所示：
   
    ![[我的待辦事項清單] 應用程式在瀏覽器視窗中的螢幕擷取畫面](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > 如果您收到有關 layout.jade 檔案或 index.jade 檔案縮排的錯誤，請確定這兩個檔案的前兩行靠左對齊 (沒有空格)。 如果前兩行之前有空格，請將空格移除，儲存這兩個檔案，然後重新整理瀏覽器視窗。 

2. 使用 [項目]、[項目名稱] 和 [類別] 欄位來輸入新工作，然後選取 [新增項目]  。 它會使用這些屬性在 Azure Cosmos DB 中建立文件。 

3. 系統應該會更新此頁面，以在 [待辦事項] 清單中顯示新建立的項目。
   
    ![[待辦事項] 清單中包含一個新項目的應用程式螢幕擷取畫面](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)

4. 若要完成工作，請選取 [已完成] 資料行中的核取方塊，然後選取 [更新工作]  。 它會更新您已建立的文件，並從檢視中加以移除。

5. 若要停止應用程式，請在終端機視窗中按 CTRL+C，然後選取 **Y** 以終止批次作業。

## <a name="deploy-your-application-to-web-apps"></a><a name="_Toc395783182"></a>將應用程式部署至 Web Apps

應用程式在本機成功執行之後，您可以使用下列步驟將其部署至 Azure 部署：

1. 請為您的 Web Apps 應用程式提供 Git 存放庫 (如果您還沒有這麼做)。

2. 將您的 Web Apps 應用程式新增為 Git 遠端。
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. 將應用程式推送到遠端加以部署。
   
   ```bash
   git push azure master
   ```

4. 在幾秒鐘之後，您的 Web 應用程式即會發布，並在瀏覽器中啟動。

## <a name="clean-up-resources"></a>清除資源

若不再需要這些資源，您可以刪除資源群組、Azure Cosmos DB 帳戶和所有相關資源。 請選取用於 Azure Cosmos DB 帳戶的資源群組，選取 [刪除]  ，然後確認要刪除的資源群組名稱，即可刪除資源。

## <a name="next-steps"></a><a name="_Toc395637775"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Xamarin 和 Azure Cosmos DB 建置行動應用程式](mobile-apps-with-xamarin.md)


[Node.js]: https://nodejs.org/
[Git]: https://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

