---
title: 快速入門 - 使用 Node.js 從 Azure Cosmos DB SQL API 帳戶進行查詢
description: 如何使用 Node.js 建立連線至 Azure Cosmos DB SQL API 帳戶並查詢資料的應用程式。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-javascript
ms.openlocfilehash: 8ec5562af254aa1bcdb030057f61db496a17826b
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423383"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>快速入門：使用 Node.js 連線至 Azure Cosmos DB SQL API 帳戶並從中查詢資料

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java SDK v4](create-sql-api-java.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

在本快速入門中，您會從 Azure 入口網站以及藉由使用從 GitHub 複製的 Node.js 應用程式，來建立和管理 Azure Cosmos DB SQL API 帳戶。 Azure Cosmos DB 是多模型的資料庫服務，可讓您快速建立及查詢具有全域散發和水平調整功能的文件、資料表、索引鍵/值及圖形資料庫。

## <a name="walkthrough-video"></a>逐步解說影片

如需本文內容的完整逐步解說，請觀看這段影片。

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 或[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) (不需 Azure 訂用帳戶)。 您也可以搭配使用 [Azure Cosmos DB 模擬器](https://aka.ms/cosmosdb-emulator)與 `https://localhost:8081` 的 URI 和金鑰 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`。
- [Node.js 6.0.0+](https://nodejs.org/)。
- [Git](https://www.git-scm.com/downloads)。

## <a name="create-an-azure-cosmos-account"></a>建立 Azure Cosmos 帳戶

在此快速入門中，您可以使用[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 選項來建立 Azure Cosmos 帳戶。

1. 瀏覽至[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 頁面。

1. 選擇 [SQL] API 帳戶，然後選取 [建立]。 使用您的 Microsoft 帳戶登入 。

1. 登入成功之後，您的 Azure Cosmos 帳戶應該已就緒。 選取 [在 Azure 入口網站中開啟]，以開啟新建立的帳戶。

「免費試用 Azure Cosmos DB」選項不需要 Azure 訂用帳戶，您可以使用 Azure Cosmos 帳戶 30 天。 如果想使用 Azure Cosmos 帳戶較長的時間，您應該在 Azure 訂用帳戶內[建立帳戶](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account)。

## <a name="add-a-container"></a>新增容器

您現在可以在 Azure 入口網站中使用 [資料總管] 工具，建立資料庫和容器。

1. 選取 [資料總管] > [新增容器]。

   [新增容器] 區域會顯示在最右邊，您可能需要向右捲動才能看到它。

   :::image type="content" source="./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png" alt-text="Azure 入口網站資料總管，[新增容器] 窗格":::

2. 在 [新增容器] 頁面上，輸入新容器的設定。

   | 設定           | 建議的值 | 描述                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **資料庫識別碼**   | 工作           | 輸入 _Tasks_ 作為新資料庫的名稱。 資料庫名稱必須包含從 1 到 255 個字元，且不能包含 `/, \\, #, ?` 或尾端空格。 核取 [佈建資料庫輸送量] 選項，它可讓您在資料庫中的所有容器內共用佈建到資料庫的輸送量。 此選項也有助於節省成本。 |
   | **輸送量**    | 400             | 讓輸送量保持在每秒 400 個要求單位 (RU/秒)。 如果您想要降低延遲，稍後可以擴大輸送量。                                                                                                                                                                                                                                                    |
   | **容器識別碼**  | 項目           | 輸入 _Items_ 作為新容器的名稱。 容器識別碼與資料庫名稱具有相同的字元需求。                                                                                                                                                                                                                                                               |
   | **分割區索引鍵** | /類別       | 本文中所述的範例使用 _/category_ 作為分割區索引鍵。                                                                                                                                                                                                                                                                                                           |

   除了上述的設定，您可以選擇性地為容器新增 [唯一索引鍵]。 在此範例中，讓我們將欄位保留空白。 唯一索引鍵可讓開發人員在資料庫中新增一層資料完整性。 您可在建立容器時建立唯一索引鍵原則，以確保每個資料分割索引鍵一或多個值的唯一性。 若要深入了解，請參閱 [Azure Cosmos DB 中的唯一索引鍵](unique-keys.md)一文。

   選取 [確定]。 [資料總管] 會顯示新的資料庫和容器。

## <a name="add-sample-data"></a>新增範例資料

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>查詢資料

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 GitHub 複製 Node.js 應用程式、設定連接字串，然後加以執行。

1. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要了解如何在程式碼中建立 Azure Cosmos 資料庫資源，您可以檢閱下列程式碼片段。 或者也可以直接跳至[更新您的連接字串](#update-your-connection-string)。

如果您熟悉舊版 SQL JavaScript SDK，則可能已習慣看到「集合」和「文件」等字詞。 因為 Azure Cosmos DB 支援[多個 API 模型](introduction.md)，[2.0+ 版的 JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) 會使用「容器」這個泛用字詞，此字詞可能是用來說明容器內容的集合、圖表或資料表和「項目」。

Cosmos DB JavaScript SDK 稱為「@azure/cosmos」，可以從 npm 安裝...

```bash
npm install @azure/cosmos
```

下列程式碼片段全部取自 _app.js_ 檔案。

- `CosmosClient` 是從 `@azure/cosmos` npm 套件匯入。

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- 已初始化新的 `CosmosClient` 物件。

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- 選取「工作」資料庫。

  ```javascript
  const database = client.database(databaseId);
  ```

- 選取「項目」容器/集合。

  ```javascript
  const container = database.container(containerId);
  ```

- 選取「項目」容器中的所有項目。

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- 建立新的項目

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- 更新項目

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- 刪除項目

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> 在「更新」和「刪除」兩種方法中，都必須藉由呼叫 `container.item()` 從資料庫中選取項目。 傳入的兩個參數是項目的識別碼和項目的分割區索引鍵。 在此案例中，分割區索引鍵是「類別」欄位的值。

## <a name="update-your-connection-string"></a>更新您的連接字串

現在請回到 Azure 入口網站，以取得 Azure Cosmos 帳戶的連接字串詳細資料。 將連接字串複製到應用程式中，使其可連線至您的資料庫。

1. 在您於 [Azure 入口網站](https://portal.azure.com/)的 Azure Cosmos DB 帳戶中，從左側瀏覽區選取 [金鑰]，然後選取 [讀寫金鑰]。 在下一個步驟中，使用畫面右側的複製按鈕，將 URI 和主要金鑰複製到 app.js 檔案。

   :::image type="content" source="./media/create-sql-api-dotnet/keys.png" alt-text="在 Azure 入口網站的 [金鑰] 刀鋒視窗中檢視並複製存取金鑰":::

2. 開啟 config.js 檔案。

3. 從入口網站複製您的 URI 值 (使用 [複製] 按鈕)，並使其成為 config.js 中的端點金鑰值。

   `endpoint: "<Your Azure Cosmos account URI>"`

4. 然後，從入口網站複製您的主要金鑰值，並使其成為 config.js 中的 `config.key` 值。 您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>執行應用程式

1. 在終端機中執行 `npm install` 以安裝「@azure/cosmos」npm 套件

2. 在終端機中執行 `node app.js` 來啟動您的節點應用程式。

3. 您稍早在本快速入門中建立的兩個項目都會列出。已建立新項目。 該項目上的「isComplete」旗標會更新為「true」，最後系統會刪除該項目。

您可以繼續在此範例應用程式中實驗，或返回資料總管、修改和處理您的資料。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 建立容器，以及如何執行 Node.js 應用程式。 您現在可以將其他資料匯入 Azure Cosmos DB 帳戶中。

> [!div class="nextstepaction"]
> [將資料匯入到 Azure Cosmos DB](import-data.md)
