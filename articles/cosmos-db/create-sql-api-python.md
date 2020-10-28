---
title: 快速入門：使用 Azure Cosmos DB SQL API 帳戶建置 Python 應用程式
description: 提供 Python 程式碼範例，您可用來連線及查詢 Azure Cosmos DB SQL API
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
- devx-track-python
ms.openlocfilehash: aa23f6d5d7b81e93145fd7db3fb5b45e46918d21
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487580"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>快速入門：使用 Azure Cosmos DB SQL API 帳戶建置 Python 應用程式

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

在本快速入門中，您會從 Azure 入口網站以及從 Visual Studio Code 搭配從 GitHub 複製的 Python 應用程式，來建立和管理 Azure Cosmos DB SQL API 帳戶。 Azure Cosmos DB 是多模型的資料庫服務，可讓您快速建立及查詢具有全域散發和水平調整功能的文件、資料表、索引鍵/值及圖形資料庫。

## <a name="prerequisites"></a>必要條件

- Cosmos DB 帳戶。 您可用的選項包括：
    * 在 Azure 作用中訂用帳戶內：
        * [建立 Azure 免費帳戶](https://azure.microsoft.com/free) 或使用現有的訂用帳戶 
        * [Visual Studio 每月點數](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB 免費層](./optimize-dev-test.md#azure-cosmos-db-free-tier)
    * 若沒有作用中的 Azure 訂用帳戶：
        * [免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，此測試環境可使用 30 天。
        * [Azure Cosmos DB 模擬器](https://aka.ms/cosmosdb-emulator) 
- [Python 2.7 或 3.5.3+](https://www.python.org/downloads/)，以及 `PATH` 中的 `python` 可執行檔。
- [Visual Studio Code](https://code.visualstudio.com/) \(英文\)。
- 適用於 [Visual Studio Code 的 Python 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)。
- [Git](https://www.git-scm.com/downloads)。 
- [適用於 Python 的 Azure Cosmos DB SQL API SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>新增容器

您現在可以在 Azure 入口網站中使用 [資料總管] 工具，建立資料庫和容器。 

1. 選取 [資料總管] > [新增容器]。 
    
    [新增容器] 區域會顯示在最右邊，您可能需要向右捲動才能看到它。

    :::image type="content" source="./media/create-sql-api-python/azure-cosmosdb-data-explorer.png" alt-text="Azure 入口網站資料總管，[新增容器] 窗格":::

2. 在 [新增容器] 頁面上，輸入新容器的設定。

    |設定|建議的值|描述
    |---|---|---|
    |**資料庫識別碼**|工作|輸入 *Tasks* 作為新資料庫的名稱。 資料庫名稱必須包含從 1 到 255 個字元，且不能包含 `/, \\, #, ?` 或尾端空格。 核取 [佈建資料庫輸送量] 選項，它可讓您在資料庫中的所有容器內共用佈建到資料庫的輸送量。 此選項也有助於節省成本。 |
    |**輸送量**|400|讓輸送量保持在每秒 400 個要求單位 (RU/秒)。 如果您想要降低延遲，稍後可以擴大輸送量。| 
    |**容器識別碼**|項目|輸入 *Items* 作為新容器的名稱。 容器識別碼與資料庫名稱具有相同的字元需求。|
    |**分割區索引鍵**| /類別| 本文中所述的範例使用 */category* 作為分割區索引鍵。|
    
    除了上述的設定，您可以選擇性地為容器新增 [唯一索引鍵]。 在此範例中，讓我們將欄位保留空白。 唯一索引鍵可讓開發人員在資料庫中新增一層資料完整性。 您可在建立容器時建立唯一索引鍵原則，以確保每個資料分割索引鍵一或多個值的唯一性。 若要深入了解，請參閱 [Azure Cosmos DB 中的唯一索引鍵](unique-keys.md)一文。
    
    選取 [確定]。 [資料總管] 會顯示新的資料庫和容器。

## <a name="add-sample-data"></a>新增範例資料

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>查詢資料

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 GitHub 複製 SQL API 應用程式、設定連接字串，然後加以執行。 本快速入門使用第 4 版的 [Python SDK](https://pypi.org/project/azure-cosmos/#history)。

1. 開啟命令提示字元，建立名為 git-samples 的新資料夾，然後關閉命令提示字元。

    ```cmd
    md "git-samples"
    ```
   如果您使用 bash 提示字元，應改為使用下列命令︰

   ```bash
   mkdir "git-samples"
   ```

2. 開啟 git 終端機視窗 (例如 git bash)，並使用 `cd` 命令變更至要安裝範例應用程式的新資料夾。

    ```bash
    cd "git-samples"
    ```

3. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 Azure Cosmos DB 帳戶中，選取左側導覽列中的 [金鑰]。 在下一個步驟中，使用畫面右側的複製按鈕，將 **URI** 和 **主要金鑰** 複製到 cosmos_get_started.py 檔案中。

    :::image type="content" source="./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png" alt-text="Azure 入口網站資料總管，[新增容器] 窗格":::

2. 在 Visual Studio Code 中，開啟 \git-samples\azure-cosmos-db-python-getting-started 中的 cosmos_get_started.py 檔案。

3. 從入口網站複製您的 **URI** 值 (使用 [複製] 按鈕)，並使其成為 cosmos_get_started.py 中的 **端點** 變數值。 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. 然後，從入口網站複製您的 [主要金鑰] 值，並使其成為 cosmos_get_started.py 中的 [金鑰] 值。 您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 

    `key = 'FILLME'`

5. 儲存 cosmos_get_started.py 檔案。

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 了解以程式碼建立的資料庫資源，或直接跳到[更新您的連接字串](#update-your-connection-string)。

下列程式碼片段全部取自 cosmos_get_started.py 檔案。

* 已初始化 CosmosClient。 請務必如[更新連接字串](#update-your-connection-string)一節所述，更新「端點」和「索引鍵」值。 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* 已建立新資料庫。

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* 隨即建立新的容器，其中包含 400 RU/秒的[已佈建輸送量](request-units.md)。 我們選擇 `lastName` 作為[分割索引鍵](partitioning-overview.md#choose-partitionkey)，讓我們能進行可對此屬性進行篩選的有效查詢。 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* 有些項目會新增至容器。 容器是可擁有不同架構的項目 (JSON 文件) 集合。 協助程式方法 ```get_[name]_family_item``` 會以 JSON 文件形式，傳回 Azure Cosmos DB 中儲存的系列表示法。

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* 已使用 `read_item` 方法執行端點讀取 (索引鍵值查閱)。 我們會列出每個作業的 [RU 費用](request-units.md)。

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* 已使用 SQL 查詢語法執行查詢。 因為我們在 WHERE 子句中使用 ```lastName``` 的分割索引鍵值，所以 Azure Cosmos DB 會有效地將此查詢路由傳送至相關的分割區，以改善效能。

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>執行應用程式

1. 在 Visual Studio Code 中，選取 [檢視] > [命令選擇區]。 

2. 在提示字元中，輸入 **Python:Select Interpreter** ，然後選取要使用的 Python 版本。

    Visual Studio Code 中的頁尾會更新以指出選取的解譯器。 

3. 選取 [檢視] > [整合式終端機] 以開啟 Visual Studio Code 整合式終端機。

4. 在整合式終端機視窗中，確定您在 azure-cosmos-db-python-getting-started 資料夾中。 如果不是，請執行下列命令來切換至範例資料夾。 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. 執行以下命令來安裝 azure-cosmos 套件。 

    ```python
    pip install --pre azure-cosmos
    ```

    如果您收到有關在嘗試安裝 azure-cosmos 時拒絕存取的錯誤，您必須[以系統管理員身分執行 VS Code](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights)。

6. 執行下列命令以執行範例，並且在 Azure Cosmos DB 中建立和儲存新文件。

    ```python
    python cosmos_get_started.py
    ```

7. 若要確認已建立並儲存新項目，請在 Azure 入口網站中，選取 [資料總管] > [AzureSampleFamilyDatabase] > [項目]。 檢視已建立的項目。 例如，以下是適用於 Andersen 家族的範例 JSON 文件：
   
   ```json
   {
       "id": "Andersen-1569479288379",
       "lastName": "Andersen",
       "district": "WA5",
       "parents": [
           {
               "familyName": null,
               "firstName": "Thomas"
           },
           {
               "familyName": null,
               "firstName": "Mary Kay"
           }
       ],
       "children": null,
       "address": {
           "state": "WA",
           "county": "King",
           "city": "Seattle"
       },
       "registered": true,
       "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
       "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
       "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
       "_attachments": "attachments/",
       "_ts": 1569479288
   }
   ```

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 建立容器，以及如何在 Visual Studio Code 中執行 Python 應用程式。 您現在可以將其他資料匯入 Azure Cosmos DB 帳戶中。 

> [!div class="nextstepaction"]
> [將資料匯入 SQL API 的 Azure Cosmos DB](import-data.md)