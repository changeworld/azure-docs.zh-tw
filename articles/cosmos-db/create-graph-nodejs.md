---
title: 使用 Gremlin API 建置 Azure Cosmos DB Node.js 應用程式
description: 提供可用來連線及查詢 Azure Cosmos DB 的 Node.js 程式碼範例。
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: 7ab259886409610fc89c35f4c96c9babb977b44d
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061773"
---
# <a name="quickstart-build-a-nodejs-application-by-using-azure-cosmos-db-gremlin-api-account"></a>快速入門：使用 Azure Cosmos DB Gremlin API 帳戶建置 Node.js 應用程式

> [!div class="op_single_selector"]
> * [Gremlin 主控台](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

在本快速入門中，您會從 Azure 入口網站建立和管理 Azure Cosmos DB Gremlin (圖形) API 帳戶，並使用從 GitHub 複製的 Node.js 應用程式來新增資料。 Azure Cosmos DB 是多模型的資料庫服務，可讓您快速建立及查詢具有全域散發和水平調整功能的文件、資料表、索引鍵/值及圖形資料庫。

## <a name="prerequisites"></a>Prerequisites
- 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 
- [Node.js 0.10.29+](https://nodejs.org/)。
- [Git](https://git-scm.com/downloads)。

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>新增圖形

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 GitHub 複製 Gremlin API 應用程式、設定連接字串，然後加以執行。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟命令提示字元，建立名為 git-samples 的新資料夾，然後關閉命令提示字元。

    ```bash
    md "C:\git-samples"
    ```

2. 開啟 git 終端機視窗 (例如 git bash)，並使用 `cd` 命令變更至要安裝範例應用程式的新資料夾。

    ```bash
    cd "C:\git-samples"
    ```

3. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. 在 Visual Studio 中開啟解決方案檔案。 

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要瞭解如何在程式碼中建立資料庫資源，則可檢閱下列程式碼片段。 或者也可以直接跳至[更新您的連接字串](#update-your-connection-string)。 

下列程式碼片段全部取自 *app.js* 檔案。

此主控台應用程式會使用開放原始碼的 [Gremlin Node.js](https://www.npmjs.com/package/gremlin) 驅動程式。

* 已建立 Gremlin 用戶端。

    ```javascript
    const authenticator = new Gremlin.driver.auth.PlainTextSaslAuthenticator(
        `/dbs/${config.database}/colls/${config.collection}`, 
        config.primaryKey
    )


    const client = new Gremlin.driver.Client(
        config.endpoint, 
        { 
            authenticator,
            traversalsource : "g",
            rejectUnauthorized : true,
            mimeType : "application/vnd.gremlin-v2.0+json"
        }
    );

    ```

  這些設定都位於我們在[下一節](#update-your-connection-string)要編輯的 config.js  中。

* 定義了一系列的函式來執行不同的 Gremlin 作業。 這是其中一個：

    ```javascript
    function addVertex1()
    {
        console.log('Running Add Vertex1'); 
        return client.submit("g.addV(label).property('id', id).property('firstName', firstName).property('age', age).property('userid', userid).property('pk', 'pk')", {
                label:"person",
                id:"thomas",
                firstName:"Thomas",
                age:44, userid: 1
            }).then(function (result) {
                    console.log("Result: %s\n", JSON.stringify(result));
            });
    }
    ```

* 每個函式會搭配一個 Gremlin 查詢字串參數執行 `client.execute` 方法。 以下是執行 `g.V().count()` 的範例：

    ```javascript
    function countVertices()
    {
        console.log('Running Count');
        return client.submit("g.V().count()", { }).then(function (result) {
            console.log("Result: %s\n", JSON.stringify(result));
        });
    }
    ```

* 在檔案結尾，接著會叫用所有方法。 這會一個接著一個執行所有方法：

    ```javascript
    client.open()
    .then(dropGraph)
    .then(addVertex1)
    .then(addVertex2)
    .then(addEdge)
    .then(countVertices)
    .catch((err) => {
        console.error("Error running query...");
        console.error(err)
    }).then((res) => {
        client.close();
        finish();
    }).catch((err) => 
        console.error("Fatal error:", err)
    );
    ```


## <a name="update-your-connection-string"></a>更新您的連接字串

1. 開啟 config.xml  檔案。 

2. 在 config.js  中，使用 Azure 入口網站中您 Cosmos DB 帳戶的 [概觀]  頁面中的 [Gremlin 端點]  值填入 `config.endpoint` 金鑰。 

    `config.endpoint = "https://<your_Gremlin_account_name>.gremlin.cosmosdb.azure.com:443/";`

    ![在 Azure 入口網站的 [概觀] 頁面中檢視並複製存取金鑰](./media/create-graph-nodejs/gremlin-uri.png)

3. 在 config.js  中，使用 Azure 入口網站中您 Cosmos DB 帳戶的 [金鑰]  頁面中的 [主要金鑰]  值填入 config.primaryKey 值。 

    `config.primaryKey = "PRIMARYKEY";`

   ![Azure 入口網站 [索引鍵] 刀鋒視窗](./media/create-graph-nodejs/keys.png)

4. 針對 config.database 和 config.collection 的值，輸入資料庫名稱和圖形 (容器) 名稱。 

您完成的 config.js  檔案範例應如下所示︰

```javascript
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"; 
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>執行主控台應用程式

1. 開啟終端機視窗並變更 (透過 `cd` 命令) 至專案內含 package.json  檔案的安裝目錄。

2. 執行 `npm install` 以安裝必要的 npm 模組，包括 `gremlin`。

3. 在終端機中執行 `node app.js` 來啟動您的節點應用程式。

## <a name="browse-with-data-explorer"></a>使用資料總管進行瀏覽

您現在可以回到 Azure 入口網站中的 [資料總管]，檢視、查詢、修改並使用新的圖形資料。

在 [資料總管] 中，新的資料庫會出現在 [圖形]  窗格中。 展開資料庫，後面接著該容器，然後選取 [圖形]  。

當您選取 [套用篩選條件]  時，範例應用程式所產生的資料會顯示在 [圖形]  索引標籤內的下一個窗格中。

試著使用 `.has('firstName', 'Thomas')` 完成 `g.V()` 以測試篩選條件。 請注意此值會區分大小寫。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何建立 Azure Cosmos DB 帳戶、使用 [資料總管] 建立圖形，以及執行 Node.js 應用程式來將資料新增至圖形。 您現在可以使用 Gremlin 來建置更複雜的查詢，以及實作強大的圖形周遊邏輯。 

> [!div class="nextstepaction"]
> [使用 Gremlin 進行查詢](tutorial-query-graph.md)
