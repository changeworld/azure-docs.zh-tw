---
title: 使用 gocql 用戶端建置具有 Azure Cosmos DB Cassandra API 的 Go 應用程式
description: 本快速入門說明如何使用 Go 用戶端與 Azure Cosmos DB Cassandra API 互動
ms.service: cosmos-db
author: abhirockzz
ms.author: abhishgu
ms.subservice: cosmosdb-cassandra
ms.devlang: go
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: 87f3ea2d1ce8d3d9f2f584db379618fc6c4a4a67
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491235"
---
# <a name="quickstart-build-a-go-app-with-the-gocql-client-to-manage-azure-cosmos-db-cassandra-api-data"></a>快速入門：建置具有 `gocql` 用戶端的 Go 應用程式來管理 Azure Cosmos DB Cassandra API 資料

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> * [Golang](create-cassandra-go.md)
>

Azure Cosmos DB 是多模型的資料庫服務，可讓您快速建立及查詢具有全域散發和水平調整功能的文件、資料表、索引鍵/值及圖形資料庫。 在本快速入門中，您將從建立 Azure Cosmos DB Cassandra API 帳戶開始。 接著，您將執行 Go 應用程式來建立 Cassandra keyspace、資料表，並執行一些作業。 此 Go 應用程式會使用 [gocql](https://github.com/gocql/gocql)，這是 Go 語言的 Cassandra 用戶端。 

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=cassandrago-docs-abhishgu)。 或[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/?WT.mc_id=cassandrago-docs-abhishgu) (不需 Azure 訂用帳戶)。
- 在您的電腦上安裝 [Go](https://golang.org/)，且具備 Go 的運用知識。
- [Git](https://git-scm.com/downloads)。

## <a name="create-a-database-account"></a>建立資料庫帳戶

您必須先使用 Azure Cosmos DB 建立 Cassandra 帳戶，才可以建立資料庫。

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

從 GitHub 複製應用程式開始。

1. 開啟命令提示字元，並建立名為 `git-samples` 的新資料夾。

    ```bash
    md "C:\git-samples"
    ```

2. 開啟 Git 終端機視窗，例如 Git Bash。 使用 `cd` 命令變更至新資料夾，以安裝範例應用程式。

    ```bash
    cd "C:\git-samples"
    ```

3. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-go-getting-started.git
    ```

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 如果您有興趣了解程式碼如何建立資料庫資源，可以檢閱下列程式碼片段。 或是，您可以直接跳到[執行應用程式](#run-the-application)

`GetSession` 函式 (`utils\utils.go` 的一部分) 會傳回用來執行叢集作業 (例如插入、尋找等) 的 [`*gocql.Session`](https://godoc.org/github.com/gocql/gocql#Session)。

```go
func GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword string) *gocql.Session {
    clusterConfig := gocql.NewCluster(cosmosCassandraContactPoint)
    port, err := strconv.Atoi(cosmosCassandraPort)
    
    clusterConfig.Authenticator = gocql.PasswordAuthenticator{Username: cosmosCassandraUser, Password: cosmosCassandraPassword}
    clusterConfig.Port = port
    clusterConfig.SslOpts = &gocql.SslOptions{Config: &tls.Config{MinVersion: tls.VersionTLS12}}
    clusterConfig.ProtoVersion = 4
    
    session, err := clusterConfig.CreateSession()
    ...
    return session
}
```

系統會將 Azure Cosmos DB Cassandra 主機傳遞至 [`gocql.NewCluster`](https://godoc.org/github.com/gocql/gocql#NewCluster) 函式，以取得 [`*gocql.ClusterConfig`](https://godoc.org/github.com/gocql/gocql#ClusterConfig)結構，然後將其設定為使用使用者名稱、密碼、連接埠和適當的 TLS 版本 ([HTTPS/SSL/TLS 加密安全性需求](./database-security.md?WT.mc_id=cassandrago-docs-abhishgu#how-does-azure-cosmos-db-secure-my-database))

然後會從 `main` 函式 (`main.go`) 呼叫 `GetSession` 函式。

```go
func main() {
    session := utils.GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword)
    defer session.Close()
    ...
}
```

系統會以環境變數的形式接受連線能力資訊和認證 (已在 `init` 方法中解析)

```go
func init() {
    cosmosCassandraContactPoint = os.Getenv("COSMOSDB_CASSANDRA_CONTACT_POINT")
    cosmosCassandraPort = os.Getenv("COSMOSDB_CASSANDRA_PORT")
    cosmosCassandraUser = os.Getenv("COSMOSDB_CASSANDRA_USER")
    cosmosCassandraPassword = os.Getenv("COSMOSDB_CASSANDRA_PASSWORD")

    if cosmosCassandraContactPoint == "" || cosmosCassandraUser == "" || cosmosCassandraPassword == "" {
        log.Fatal("missing mandatory environment variables")
    }
}
```

然後會用來在 Azure Cosmos DB 執行各種作業 (`operations\setup.go`的一部分)，從 `keyspace` 和 `table` 建立開始。

如其名所示，`DropKeySpaceIfExists` 函式只有在存在時才會置放 `keyspace`。

```go
const dropKeyspace = "DROP KEYSPACE IF EXISTS %s"

func DropKeySpaceIfExists(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(dropKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to drop keyspace", err)
    }
    log.Println("Keyspace dropped")
}
```

`CreateKeySpace` 函式是用來建立 `keyspace` (`user_profile`)

```go
const createKeyspace = "CREATE KEYSPACE %s WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"

func CreateKeySpace(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(createKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to create keyspace", err)
    }
    log.Println("Keyspace created")
}
```

接下來就是建立資料表 (`user`)，以處理 `CreateUserTable` 函式

```go
const createTable = "CREATE TABLE %s.%s (user_id int PRIMARY KEY, user_name text, user_bcity text)"

func CreateUserTable(keyspace, table string, session *gocql.Session) {
    err := session.Query(fmt.Sprintf(createTable, keyspace, table)).Exec()
    if err != nil {
        log.Fatal("failed to create table ", err)
    }
    log.Println("Table created")
}
```

建立 keyspace 和資料表後，我們會叫用 CRUD 作業 (`operations\crud.go` 的一部分)。 

`InsertUser` 用來建立 `User`， 並會將使用者資訊 (識別碼、名稱和城市) 設定為使用 [`Bind`](https://godoc.org/github.com/gocql/gocql#Query.Bind) 的查詢引數

```go
const createQuery = "INSERT INTO %s.%s (user_id, user_name , user_bcity) VALUES (?,?,?)"

func InsertUser(keyspace, table string, session *gocql.Session, user model.User) {
    err := session.Query(fmt.Sprintf(createQuery, keyspace, table)).Bind(user.ID, user.Name, user.City).Exec()
    if err != nil {
        log.Fatal("Failed to create user", err)
    }
    log.Println("User created")
}
```

`FindUser` 用來使用特定的使用者識別碼來搜尋使用者 (`model\user.go`)，同時 [`Scan`](https://godoc.org/github.com/gocql/gocql#Iter.Scan) 會將使用者屬性 (由 Cassandra 傳回) 繫結至個別變數 (`userid`、`name`、`city`) - 這只是其中一種方式，讓您將取得的結果作為搜尋查詢結果

```go
const selectQuery = "SELECT * FROM %s.%s where user_id = ?"

func FindUser(keyspace, table string, id int, session *gocql.Session) model.User {
    var userid int
    var name, city string
    err := session.Query(fmt.Sprintf(selectQuery, keyspace, table)).Bind(id).Scan(&userid, &name, &city)

    if err != nil {
        if err == gocql.ErrNotFound {
            log.Printf("User with id %v does not exist\n", id)
        } else {
            log.Printf("Failed to find user with id %v - %v\n", id, err)
        }
    }
    return model.User{ID: userid, Name: name, City: city}
}
```

`FindAllUsers` 用來提取所有使用者。 [`SliceMap`](https://godoc.org/github.com/gocql/gocql#Iter.SliceMap) 用來以 `map` 的配量形式取得所有使用者資訊的速記。 將每個 `map` 視為機碼值組，其中的資料行名稱 (例如 `user_id`) 是索引鍵及其各自的值。

```go
const findAllUsersQuery = "SELECT * FROM %s.%s"

func FindAllUsers(keyspace, table string, session *gocql.Session) []model.User {
    var users []model.User
    results, _ := session.Query(fmt.Sprintf(findAllUsersQuery, keyspace, table)).Iter().SliceMap()

    for _, u := range results {
        users = append(users, mapToUser(u))
    }
    return users
}
```

使用者資訊的每個 `map` 都會使用 `mapToUser` 函式轉換成 `User`，而此函式只會從其個別的資料行中擷取值，並用該值建立 `User` 結構的執行個體

```go
func mapToUser(m map[string]interface{}) model.User {
    id, _ := m["user_id"].(int)
    name, _ := m["user_name"].(string)
    city, _ := m["user_bcity"].(string)

    return model.User{ID: id, Name: name, City: city}
}
```

## <a name="run-the-application"></a>執行應用程式

如先前所述，應用程式會以環境變數的形式接受連線能力和認證。 

1. 在 [Azure 入口網站](https://portal.azure.com/)中的 Azure Cosmos DB 帳戶中，選取 [連接字串]。 

    :::image type="content" source="./media/create-cassandra-go/copy-username-connection-string-azure-portal.png" alt-text="在 Azure 入口網站中的連接字串頁面檢視及複製詳細資料":::

複製下列屬性 (`CONTACT POINT`、`PORT`、`USERNAME` 和 `PRIMARY PASSWORD`) 的值，並個別設定其環境變數

```shell
set COSMOSDB_CASSANDRA_CONTACT_POINT=<value for "CONTACT POINT">
set COSMOSDB_CASSANDRA_PORT=<value for "PORT">
set COSMOSDB_CASSANDRA_USER=<value for "USERNAME">
set COSMOSDB_CASSANDRA_PASSWORD=<value for "PRIMARY PASSWORD">
```

在終端機視窗中，變更為正確的資料夾。 例如：

```shell
cd "C:\git-samples\azure-cosmosdb-cassandra-go-getting-started"
```

2. 在終端機視窗中，執行下列命令以啟動應用程式。

```shell
go run main.go
```

3. 終端機視窗會顯示各種作業的通知，包括 keyspace 和資料表設定、使用者建立等。

4. 在 Azure 入口網站中，開啟 [資料總管] 以查詢、修改及使用這個新資料。 

    :::image type="content" source="./media/create-cassandra-go/view-data-explorer-go-app.png" alt-text="在 Azure 入口網站中的連接字串頁面檢視及複製詳細資料":::

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 Cassandra API 建立 Azure Cosmos DB 帳戶，並已執行可建立 Cassandra 資料庫和容器的 Go 應用程式。 您現在可以將其他資料匯入 Azure Cosmos DB 帳戶中。 

> [!div class="nextstepaction"]
> [將 Cassandra 資料匯入到 Azure Cosmos DB](cassandra-import-data.md)