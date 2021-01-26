---
title: 將 Rust 應用程式連線至 Azure Cosmos DB 適用于 MongoDB 的 API
description: 本快速入門示範如何建立 Azure Cosmos DB 適用于 MongoDB 的 API 所支援的 Rust 應用程式。
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: 4b7e7258664aed3b171166bb392406cd5d826b3f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792545"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>快速入門：將 Rust 應用程式連線至 Azure Cosmos DB 適用于 MongoDB 的 API
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

Azure Cosmos DB 是多模型的資料庫服務，可讓您快速建立及查詢具有全域散發和水平調整功能的文件、資料表、索引鍵/值及圖形資料庫。 本文中顯示的範例是使用 [Rust driver For MongoDB](https://github.com/mongodb/mongo-rust-driver)的簡單命令列應用程式。 由於 Azure Cosmos DB 的 MongoDB API [與 mongodb 網路通訊協定相容](./mongodb-introduction.md#wire-protocol-compatibility)，因此任何 MongoDB 用戶端驅動程式都可以連接到它。

您將瞭解如何使用 MongoDB Rust 驅動程式，藉由探索範例程式碼中所執行的 CRUD (建立、讀取、更新、刪除) 作業，與 Azure Cosmos DB 的 MongoDB API 互動。 最後，您可以在本機執行應用程式，以查看其實際運作情形。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free)。 或[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) (不需 Azure 訂用帳戶)。 您也可以使用 [Azure Cosmos DB 模擬器](https://aka.ms/cosmosdb-emulator)搭配連接字串 `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`。
- [Rust](https://www.rust-lang.org/tools/install) (1.39 版或更新版本) 
- [Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>設定 Azure Cosmos DB

若要設定 Azure Cosmos DB 帳戶，請遵循 [此處的指示](create-mongodb-dotnet.md)。 應用程式將需要您可以使用 Azure 入口網站來提取的 MongoDB 連接字串。 如需詳細資訊，請參閱 [取得 MongoDB 連接字串以進行自訂](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize)。

## <a name="run-the-application"></a>執行應用程式

### <a name="clone-the-sample-application"></a>複製範例應用程式

執行下列命令來複製範例存放庫。

1. 開啟命令提示字元，建立名為 `git-samples` 的新資料夾，然後關閉命令提示字元。

    ```bash
    mkdir "C:\git-samples"
    ```

1. 開啟 git 終端機視窗 (例如 git bash)，並使用 `cd` 命令變更至要安裝範例應用程式的新資料夾。

    ```bash
    cd "C:\git-samples"
    ```

1. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>建置應用程式

若要建立二進位檔：

```bash
cargo build --release
```

### <a name="configure-the-application"></a>設定應用程式 

將連接字串、MongoDB 資料庫和集合名稱匯出為環境變數。 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> `ssl=true` 選項很重要，因為 Cosmos DB 需要設定此選項。 如需詳細資訊，請參閱[連接字串需求](connect-mongodb-account.md#connection-string-requirements)。
>

針對 `MONGODB_URL` 環境變數，請取代 `<COSMOSDB_ACCOUNT_NAME>` 和 `<COSMOSDB_PASSWORD>` 的預留位置

- `<COSMOSDB_ACCOUNT_NAME>`:您已建立的 Azure Cosmos DB 帳戶的名稱
- `<COSMOSDB_PASSWORD>`:在先前的步驟中擷取的資料庫金鑰

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

您可以為 `MONGODB_DATABASE` 和 `MONGODB_COLLECTION` 選擇您慣用的值，或將其保持原狀。

若要執行應用程式，請切換到正確的資料夾 (應用程式二進位檔存在) ：

```bash
cd target/release
```

建立 `todo`

```bash
./todo create "Create an Azure Cosmos DB database account"
```

如果成功，您應該會看到輸出中包含新建文件的 MongoDB `_id`：

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

建立另一個 `todo`

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

列出所有 `todo`

```bash
./todo list all
```

您應該會看到您剛剛新增的專案：

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

若要更新 (的狀態 `todo` ，請將它變更為 `completed` status) ，使用識別碼， `todo` 如下所示：

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

僅列出已完成的 `todo`

```bash
./todo list completed
```

您應該會看到您剛剛更新的項目

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

使用識別碼刪除 `todo`

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

列出 `todo` 加以確認

```bash
./todo list all
```

`todo`您剛剛刪除的不應該存在。

### <a name="view-data-in-data-explorer"></a>在資料總管中檢視資料

Azure Cosmos DB 中儲存的資料可在 Azure 入口網站中進行檢視和查詢。

若要檢視、查詢及處理在前一個步驟中建立的使用者資料，請在 Web 瀏覽器中登入 [Azure 入口網站](https://portal.azure.com)。

在頂端的 [搜尋] 方塊中，輸入 **Azure Cosmos DB**。 當您的 Cosmos 帳戶刀鋒視窗開啟時，選取您的 Cosmos 帳戶。 在左側導覽中，選取 [資料總管]。 在 [集合] 窗格中展開您的集合，然後您可以檢視集合中的文件、查詢資料，甚至是建立及執行預存程序、觸發程序和 UDF。

## <a name="review-the-code-optional"></a>檢查程式碼 (選用) 

如果您有興趣瞭解應用程式的運作方式，您可以複習本節中的程式碼片段。 系統會從檔案取得下列程式碼片段 `src/main.rs` 。

`main`函數是應用程式的進入點 `todo` 。 它預期會有環境變數提供 Azure Cosmos DB API for MongoDB 的連接 URL `MONGODB_URL` 。 會建立的新實例 `TodoManager` ，接著會根據使用者[ `match` ](https://doc.rust-lang.org/book/ch06-02-match.html) （ `TodoManager` `create` 、 `update` 、 `list` 或）所選擇的作業，委派給適當方法的運算式 `delete` 。

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager` 是 `struct` 封裝 [mongodb：： sync：： Collection](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html)的。 當您嘗試使用函式具現化時 `TodoManager` `new` ，它會起始與 Azure Cosmos DB 的 MongoDB API 的連接。

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

最重要的是， `TodoManager` 有可協助管理的方法 `todo` 。 讓我們逐一進行設定。

`add_todo`方法會採用 `todo` 使用者提供的描述，並建立結構的實例，如下所 `Todo` 示。 [Serde](https://github.com/serde-rs/serde)架構是用來對應 (將) BSON 資料序列化/還原序列化為結構的實例 `Todo` 。 請注意如何 `serde` 使用欄位屬性來自訂序列化/取消 serialzation 程式。 例如， `todo_id` Todo 中的欄位 `struct` 是 `ObjectId` ，而且會以的形式儲存在 MongoDB 中 `_id` 。

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

[Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one)接受代表[](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html) `todo` 要加入之詳細資料的檔。 請注意，從 `Todo` 到的轉換 `Document` 是兩個步驟的程式，使用 [to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) 和 [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document)的組合來達成。

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

[集合。 find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find) 可用來取得 *所有* 的， `todo` 或根據使用者提供的狀態 (或) 來篩選它們 `pending` `completed` 。 請注意，在 `while` 迴圈中，每個以 `Document` 搜尋結果取得的結果都會 `Todo` 使用 [bson：： from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html)轉換成結構。 這與在方法中所做的相反 `add_todo` 。

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

您 `todo` 可以使用) ，將狀態 (從更新 `pending` 為， `completed` 反之亦然。 `todo`會轉換成[bson：： oid：： ObjectId](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html) ，然後由[Collection.update_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one)方法用來找出需要更新的檔。

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

`todo`使用[Collection.delete_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one)方法來刪除是直接的。


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已瞭解如何使用 Azure Cloud Shell 建立 Azure Cosmos DB MongoDB API 帳戶，以及如何建立和執行 Rust 命令列應用程式來管理 `todo` 。 您現在可以將其他資料匯入 Azure Cosmos DB 帳戶中。

> [!div class="nextstepaction"]
> [將 MongoDB 資料匯入到 Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
