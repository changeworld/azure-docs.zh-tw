---
title: 使用適用於 MongoDB 的 Azure Cosmos DB API 建置 Python Flask Web 應用程式
description: 呈現 Python Flask 程式碼範例，您可使用 Azure Cosmos DB 適用於 MongoDB 的 API 進行連線及查詢。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: devx-track-python
ms.openlocfilehash: 1ed15182e700f125072c69ba9bcf60caf4da5c88
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87873036"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>快速入門：使用 Azure Cosmos DB 適用於 MongoDB 的 API 建置 Python 應用程式

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

在本快速入門中，您會使用適用於 Mongo DB API 的 Azure Cosmos DB 帳戶或 Azure Cosmos DB 模擬器來執行從 GitHub 複製的 Python Flask 待辦事項 Web 應用程式。 Azure Cosmos DB 是多模型的資料庫服務，可讓您快速建立及查詢具有全域散發和水平調整功能的文件、資料表、索引鍵/值及圖形資料庫。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 或[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) (不需 Azure 訂用帳戶)。 或者，您也可以使用 [Azure Cosmos DB 模擬器](local-emulator.md)。 
- [Python 3.6+](https://www.python.org/downloads/)
- 具有 [Python 擴充功能](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) 的 [Visual Studio Code](https://code.visualstudio.com/Download)。

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在讓我們從 GitHub 複製 Flask-MongoDB 應用程式、設定連接字串，然後執行它。 您會看到，以程式設計方式來處理資料有多麼的容易。

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
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. 執行下列命令來安裝 Python 模組。

    ```bash 
    pip install -r .\requirements.txt
    ```
4. 在 Visual Studio Code 中開啟資料夾。

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要瞭解如何在程式碼中建立資料庫資源，則可檢閱下列程式碼片段。 或是，您可以直接跳到[執行 Web 應用程式](#run-the-web-app)。 

下列程式碼片段皆取自 app.py  檔案，並使用本機 Azure Cosmos DB 模擬器的連接字串。 密碼必須分割 (如下所示)，以容納無法剖析的正斜線。

* 初始化 MongoDB 用戶端，擷取資料庫，然後驗證。

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* 擷取集合，或是在集合尚未存在的情況下建立它。

    ```python
    todos = db.todo #Select the collection
    ```

* 建立應用程式

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>執行 Web 應用程式

1. 確認 Azure Cosmos DB 模擬器正在執行。

2. 開啟終端機視窗，並 `cd` 至儲存應用程式的目錄。

3. 接著，使用 `set FLASK_APP=app.py` (PowerShell 編輯器請使用 `$env:FLASK_APP = app.py`) 來設定 Flask 應用程式的環境變數；如果使用 Mac，則請使用 `export FLASK_APP=app.py`。 

4. 使用 `flask run` 執行應用程式，並瀏覽至 http:\//127.0.0.1:5000/  。

5. 新增及移除工作，您將能在集合中看到那些工作的新增和變更。

## <a name="create-a-database-account"></a>建立資料庫帳戶

如果您想要針對即時 Azure Cosmos DB 帳戶測試程式碼，請移至 Azure 入口網站以建立帳戶。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>更新您的連接字串

若要針對即時 Azure Cosmos DB 帳戶測試程式碼，請取得您的連接字串資訊。 接著將它複製到應用程式。

1. 在 Azure 入口網站中，於您的 Azure Cosmos DB 帳戶的左側瀏覽區中選取 [連接字串]  ，然後選取 [讀寫金鑰]  。 您將使用畫面右側的複製按鈕來複製使用者名稱、連接字串和密碼。 

2. 開啟根目錄中的 *app.py* 檔案。

3. 從入口網站複製您的 [使用者名稱]  值 (使用 [複製] 按鈕)，並使它成為 *app.py* 檔案中的 **name** 值。

4. 接著，從入口網站複製您的 [連接字串]  值，並使其成為 app.py  檔案中的 MongoClient  值。

5. 最後，從入口網站複製您的 [密碼]  值，並使它成為 *app.py* 檔案中的 **password** 值。

您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 您可以使用與之前相同的方式執行它。

## <a name="deploy-to-azure"></a>部署至 Azure

若要部署此應用程式，您可以在 Azure 中建立新的 Web 應用程式，並搭配此 GitHub 存放庫的分支啟用持續部署。 請遵循此[教學課程](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment)，在 Azure 中搭配 GitHub 設定持續部署。

部署至 Azure 時，您應該移除應用程式金鑰，並確定以下區段未註解化：

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

接著，您需要將 MONGOURL、MONGO_PASSWORD 和 MONGO_USERNAME 新增到應用程式設定。 您可以遵循此[教學課程](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)，以深入了解 Azure Web Apps 中的應用程式設定。

如果您不想建立此存放庫的分支，也可以選取下方的 [部署至 Azure]  按鈕。 接著，您應該移至 Azure 並使用 Azure Cosmos DB 帳戶資訊設定應用程式設定。

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> 如果您打算將程式碼儲存在 GitHub 或其他原始檔控制選項之中，請務必從程式碼中移除您的連接字串。 您可以改成搭配 Web 應用程式的應用程式來設定它們。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立適用於 Mongo DB API 的 Azure Cosmos DB 帳戶，並使用 Azure Cosmos DB 模擬器來執行從 GitHub 複製的 Python Flask 待辦事項 Web 應用程式。 您現在可以將其他資料匯入 Azure Cosmos DB 帳戶中。 

> [!div class="nextstepaction"]
> [將 MongoDB 資料匯入到 Azure Cosmos DB](mongodb-migrate.md)
