---
title: 適用於 Azure Cosmos DB 的 SQL API 教學課程 (Node.js)
description: 示範如何使用 SQL API 連線及查詢 Azure Cosmos DB 的 Node.js 教學課程
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: 1115d7bb0d6857aa39f246743df54a6cf3fd3676
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098650"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>教學課程：使用 JavaScript SDK 建置 Node.js 主控台應用程式，以管理 Azure Cosmos DB SQL API 資料
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [非同步 Java](./create-sql-api-java.md)
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

## <a name="prerequisites"></a>必要條件 

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
3. 使用下列命令，建立空白的 JavaScript 檔案：

   * Windows：
     * `fsutil file createnew app.js 0`
     * `fsutil file createnew config.js 0`
     * `md data`
     * `fsutil file createnew data\databaseContext.js 0`

   * Linux/OS X：
     * `touch app.js`
     * `touch config.js`
     * `mkdir data`
     * `touch data/databaseContext.js`

4. 建立 `package.json` 檔案並將其初始化。 使用下列命令：
   * ```npm init -y```

5. 透過 npm 安裝 @azure/cosmos 模組。 使用下列命令：
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>設定應用程式的組態

現在，您的應用程式以存在，您必須確定它可與 Azure Cosmos DB 通訊。 藉由更新一些組態設定 (如下列步驟所示)，您即可設定應用程式而使其與 Azure Cosmos DB 通訊：

1. 在您慣用的文字編輯器中開啟 config.js 檔案。

1. 複製以下程式碼片段並貼到 config.js 檔案中，以及將屬性 `endpoint` 和 `key` 設定為您的 Azure Cosmos DB 端點 URI 和主要金鑰。 資料庫、容器名稱會設定為 **Tasks** 和 **Items** 。 您將用於此應用程式的分割區索引鍵是 **/category** 。

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   您可以在 [Azure 入口網站](https://portal.azure.com)的 [金鑰] 窗格中找到端點和金鑰詳細資料。

   :::image type="content" source="media/sql-api-nodejs-get-started/node-js-tutorial-keys.png" alt-text="從 Azure 入口網站取得金鑰的螢幕擷取畫面":::

JavaScript SDK 會使用通用詞彙「容器」和「項目」。 容器可以是集合、圖形或資料表。 項目可以是文件、邊緣/頂點或資料列，並且是容器內的內容。 在先前的程式碼片段中，`module.exports = config;` 程式碼會用來匯出 config 物件，因此您可以在 app.js 檔案中參考該物件。

## <a name="create-a-database-and-a-container"></a>建立資料庫和容器

1. 在您慣用的文字編輯器中開啟 databaseContext.js 檔案。

1. 複製下列程式碼並貼到 databaseContext.js 檔案中。 此程式碼會定義一個函式來建立「工作」、「項目」資料庫和容器 (如果您的 Azure Cosmos 帳戶中沒有的話)：

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   資料庫是分割於多個容器之項目的邏輯容器。 您可以使用 **Databases** 類別的 `createIfNotExists` 或 create 函式來建立資料庫。 容器是由項目所組成，在 SQL API 案例中指的是 JSON 文件。 您可以使用 **Containers** 類別的 `createIfNotExists` 或 create 函式來建立容器。 建立容器之後，您可以儲存及查詢資料。

   > [!WARNING]
   > 建立容器有一定的收費。 請瀏覽我們的[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)，以了解相關價格。

## <a name="import-the-configuration"></a>匯入設定

1. 在您慣用的文字編輯器中開啟 app.js 檔案。

1. 複製並貼上下列程式碼以匯入 `@azure/cosmos` 模組、設定，以及您在先前步驟中定義的 databaseCoNtext。 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>連線至 Azure Cosmos 帳戶

在 app.js 檔案中，複製並貼上下列程式碼，以使用先前儲存的端點和金鑰來建立新的 CosmosClient 物件。

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> 如果連線到 **Cosmos DB 模擬器** ，請停用節點程序中的 TLS 驗證：
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

您現在已有可將 Azure Cosmos DB 用戶端初始化的程式碼，接下來我們將討論如何使用 Azure Cosmos DB 資源。

## <a name="query-items"></a><a id="QueryItem"></a>查詢項目

Azure Cosmos DB 支援對儲存於每個容器中的 JSON 項目進行豐富查詢。 下列範例程式碼顯示您可以針對容器中項目執行的查詢。您可以使用 `Items` 類別的 query 函式來查詢項目。 將下列程式碼新增至 app.js 檔案，以從您的 Azure Cosmos 帳戶查詢項目：

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>建立項目

您可以使用 `Items` 類別的 create 函式來建立項目。 使用 SQL API 時，預期的項目會是文件，這是使用者定義的 (任意) JSON 內容。 在本教學課程中，您會在 tasks 資料庫中建立新的項目。

1. 在 app.js 檔案中定義項目的定義：

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. 新增下列程式碼，以建立先前定義的項目：

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>更新項目

Azure Cosmos DB 支援取代項目的內容。 複製以下程式碼並貼到 app.js 檔案中。 此程式碼會從容器中取得項目，並將 isComplete 欄位更新為 true。

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>刪除項目

Azure Cosmos DB 支援刪除 JSON 項目。 下列程式碼會示範如何依識別碼取得項目，並將其刪除。 複製以下程式碼並貼到 app.js 檔案中：

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DeleteItem":::

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
Tasks

Created container:
Items

Querying container: Items
1 - Pick up apples and strawberries.

Created new item: 3 - Complete Cosmos DB Node.js Quickstart ⚡

Updated item: 3 - Complete Cosmos DB Node.js Quickstart ⚡
Updated isComplete to true

Deleted item with id: 3
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

若不再需要這些資源，您可以刪除資源群組、Azure Cosmos DB 帳戶和所有相關資源。 請選取用於 Azure Cosmos DB 帳戶的資源群組，選取 [刪除]，然後確認要刪除的資源群組名稱，即可刪除資源。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [監視 Azure Cosmos DB 帳戶](./monitor-cosmos-db.md)

[create-account]: create-sql-api-dotnet.md#create-account