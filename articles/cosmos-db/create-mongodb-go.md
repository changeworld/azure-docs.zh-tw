---
title: 將 Go 應用程式連線至適用於 MongoDB 的 Azure Cosmos DB API
description: 本快速入門示範如何將現有的 Go 應用程式連線至適用於 MongoDB 的 Azure Cosmos DB API。
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: 3f6ff0f81c1979297ed0144eca336cb252c2e787
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574355"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>快速入門：將 Go 應用程式連線至適用於 MongoDB 的 Azure Cosmos DB API
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB 是多模型的資料庫服務，可讓您快速建立及查詢具有全域散發和水平調整功能的文件、資料表、索引鍵/值及圖形資料庫。 在本快速入門中，您可以使用 Azure Cloud Shell 來建立和管理 Azure Cosmos DB 帳戶、從 GitHub 複製現有的範例應用程式，並將其設定為與 Azure Cosmos DB 搭配使用。 

範例應用程式是以命令列為基礎的 `todo` 管理工具，以 Go 撰寫。 適用於 MongoDB 的 Azure Cosmos DB API [與 MongoDB 有線通訊協定](./mongodb-introduction.md#wire-protocol-compatibility)相容，因此任何 MongoDB 用戶端驅動程式都可以與其連線。 此應用程式使用 [MongoDB 的 Go 驅動程式](https://github.com/mongodb/mongo-go-driver)，且其運作方式對於資料儲存在 Azure Cosmos DB 資料庫中的應用程式而言是透明的。

## <a name="prerequisites"></a>必要條件
- 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free)。 或[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) (不需 Azure 訂用帳戶)。 您也可以使用 [Azure Cosmos DB 模擬器](https://aka.ms/cosmosdb-emulator)搭配連接字串 `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`。
- 在您的電腦上安裝 [Go](https://golang.org/)，且具備 Go 的運用知識。
- [Git](https://git-scm.com/downloads)。
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

執行下列命令來複製範例存放庫。

1. 開啟命令提示字元，建立名為 `git-samples` 的新資料夾，然後關閉命令提示字元。

    ```bash
    mkdir "C:\git-samples"
    ```

2. 開啟 git 終端機視窗 (例如 git bash)，並使用 `cd` 命令變更至要安裝範例應用程式的新資料夾。

    ```bash
    cd "C:\git-samples"
    ```

3. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 如果您想要了解應用程式的運作方式，可以檢閱下列程式碼片段。 或是，您可以直接跳到[執行應用程式](#run-the-application)。 應用程式的配置如下所示：

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

下列程式碼片段全部取自 `todo.go` 檔案。

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>將 Go 應用程式連線到 Azure Cosmos DB

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions) 會封裝 Azure Cosmos DB 的連接字串，而該字串會使用環境變數傳入 (後續章節將詳加說明)。 連線會使用 `clientOptions` 執行個體所傳到的 [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient) 進行初始化。 叫用 [`Ping` 函式](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping)，以確認連線成功 (這是一種具速錯機制的策略)

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> 請務必使用 [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) 設定，以免出現下列連線錯誤：`unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>建立 `todo` 項目

若要建立 `todo`，我們必須取得 [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) 的控制代碼，並叫用 [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne) 函式。 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

我們傳入 `Todo` 結構，其中包含描述和狀態 (最初設定為 `pending`)

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>列出 `todo` 項目

我們可以根據準則列出 TODO。 我們建立 [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) 用以封裝篩選準則

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find) 可用來根據篩選器搜尋文件，結果會轉換成 `Todo` 的配量

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

最後，資訊會以表格格式呈現

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>更新 `todo` 項目

`todo` 可以根據其 `_id` 進行更新。 我們可以根據 `_id` 建立 [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) 篩選器，並針對更新的資訊建立另一個篩選器；此案例中，這項資訊是新狀態 (`completed` 或 `pending`)。 最後，透過篩選器和更新的文件叫用 [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) 函式

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>刪除 `todo`

您可以根據 `_id` 刪除 `todo`，並以 [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) 執行個體的形式將其封裝。 叫用 [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne) 可刪除文件。

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>建置應用程式

切換至您複製應用程式的目錄，並建置應用程式 (使用 `go build`)。

```bash
cd monogdb-go-quickstart
go build -o todo
```

確認應用程式已正確建置。

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>設定 Azure Cosmos DB

### <a name="sign-in-to-azure"></a>登入 Azure

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI]。 

如果您要使用已安裝的 Azure CLI，請使用 [az login](/cli/azure/reference-index#az-login) 命令來登入 Azure 訂用帳戶，並遵循畫面上的指示進行。 如果您是使用 Azure Cloud Shell，可以跳過此步驟。

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>新增 Azure Cosmos DB 模組

如果您是使用已安裝的 Azure CLI，請檢查是否已安裝 `cosmosdb` 元件，方法是執行 `az` 命令。 如果 `cosmosdb` 位於基底命令清單中，請繼續進行下一個命令。 如果您是使用 Azure Cloud Shell，可以跳過此步驟。

如果 `cosmosdb` 不在基底命令的清單中，請重新安裝 [Azure CLI](/cli/azure/install-azure-cli)。

### <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](../azure-resource-manager/management/overview.md) 來建立[資源群組](/cli/azure/group#az-group-create)。 Azure 資源群組是在其中部署與管理 Azure 資源 (如 Web 應用程式、資料庫和儲存體帳戶) 的邏輯容器。 

下列範例會在西歐區域中建立一個資源群組。 選擇資源群組的唯一名稱。

如果您是使用 Azure Cloud Shell，請選取 [試用]、遵循畫面上的提示登入，然後將命令複製到命令提示字元。

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>建立 Azure Cosmos DB 帳戶

使用 [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) 命令來建立 Cosmos 帳戶。

在下列命令中，請在您看見 `<cosmosdb-name>` 預留位置的地方，替代成您自己的唯一 Cosmos 帳戶名稱。 這個唯一名稱會作為 Cosmos DB 端點 `https://<cosmosdb-name>.documents.azure.com/` 的一部分，因此，這個名稱在 Azure 中的所有 Cosmos 帳戶上必須是唯一的。 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

`--kind MongoDB` 參數會啟用 MongoDB 用戶端連接。

建立 Azure Cosmos DB 帳戶之後，Azure CLI 會顯示類似下列範例的資訊。 

> [!NOTE]
> 此範例會使用 JSON 作為 Azure CLI 輸出格式，這是預設值。 若要使用另一種輸出格式，請參閱 [Azure CLI 命令的輸出格式](/cli/azure/format-output-azure-cli)。

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>擷取資料庫索引鍵

若要連線至 Cosmos 資料庫，您需要資料庫金鑰。 使用 [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) 命令來擷取主要金鑰。

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Azure CLI 會輸出類似下列範例的資訊。 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>設定應用程式 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>將連接字串、MongoDB 資料庫和集合名稱匯出為環境變數。 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> `ssl=true` 選項很重要，因為 Cosmos DB 需要設定此選項。 如需詳細資訊，請參閱[連接字串需求](connect-mongodb-account.md#connection-string-requirements)。
>

針對 `MONGODB_CONNECTION_STRING` 環境變數，請取代 `<COSMOSDB_ACCOUNT_NAME>` 和 `<COSMOSDB_PASSWORD>` 的預留位置

1. `<COSMOSDB_ACCOUNT_NAME>`:您已建立的 Azure Cosmos DB 帳戶的名稱
2. `<COSMOSDB_PASSWORD>`:在先前的步驟中擷取的資料庫金鑰

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

您可以為 `MONGODB_DATABASE` 和 `MONGODB_COLLECTION` 選擇您慣用的值，或將其保持原狀。

## <a name="run-the-application"></a>執行應用程式

建立 `todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

如果成功，您應該會看到輸出中包含新建文件的 MongoDB `_id`：

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

建立另一個 `todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

列出所有 `todo`

```bash
./todo --list all
```

您應該會看到您剛剛以表格格式新增的項目，如下所示

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

若要更新 `todo` 的狀態 (例如，將其變更為 `completed` 狀態)，請使用 `todo` 識別碼

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

僅列出已完成的 `todo`

```bash
./todo --list completed
```

您應該會看到您剛剛更新的項目

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>在資料總管中檢視資料

Azure Cosmos DB 中儲存的資料可在 Azure 入口網站中進行檢視和查詢。

若要檢視、查詢及處理在前一個步驟中建立的使用者資料，請在 Web 瀏覽器中登入 [Azure 入口網站](https://portal.azure.com)。

在頂端的 [搜尋] 方塊中，輸入 **Azure Cosmos DB**。 當您的 Cosmos 帳戶刀鋒視窗開啟時，選取您的 Cosmos 帳戶。 在左側導覽中，選取 [資料總管]。 在 [集合] 窗格中展開您的集合，然後您可以檢視集合中的文件、查詢資料，甚至是建立及執行預存程序、觸發程序和 UDF。 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="顯示新建文件的資料總管":::


使用識別碼刪除 `todo`

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

列出 `todo` 加以確認

```bash
./todo --list all
```

您剛剛刪除的 `todo` 應不存在

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 Azure Cloud Shell 建立 Azure Cosmos DB MongoDB API 帳戶，以及如何建立和執行 Go 命令列應用程式以管理 `todo`。 您現在可以將其他資料匯入 Azure Cosmos DB 帳戶中。

> [!div class="nextstepaction"]
> [將 MongoDB 資料匯入到 Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)