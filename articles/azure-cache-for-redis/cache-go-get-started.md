---
title: 使用 Azure Cache for Redis 搭配 Go
description: 在此快速入門中，您將了解如何建立可使用 Azure Cache for Redis 的 Go 應用程式。
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: de7bdc22b4bfdf9ef5865b1b601b32672eb868d1
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165122"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>快速入門：使用 Azure Cache for Redis 搭配 Go

在本文中，您將了解如何在 Go 中建置 REST API，以在 [Azure Cache for Redis](./cache-overview.md) 中儲存和擷取 [雜湊](https://redis.io/topics/data-types-intro#redis-hashes)資料結構所支援的使用者資訊。 

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
- [Go](https://golang.org/doc/install) (最好是 1.13 版或更高版本)
- [Git](https://git-scm.com/downloads)
- HTTP 用戶端，例如 [curl](https://curl.se/)

## <a name="create-an-azure-cache-for-redis-instance"></a>建立 Azure Cache for Redis 執行個體
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>檢閱程式碼 (選用)

如果您想要了解程式碼的運作方式，可以檢閱下列程式碼片段。 或是，隨意直接跳到[執行應用程式](#run-the-application)。

開放原始碼 [go-redis](https://github.com/go-redis/redis) 程式庫可用來與 Azure Cache for Redis 互動。

`main` 函式會從讀取 Azure Cache for Redis 執行個體的主機名稱和密碼 (存取金鑰) 開始。

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

然後，我們會建立與 Azure Cache for Redis 的連線。 請注意，正在使用 [tls.Config](https://golang.org/pkg/crypto/tls/#Config) - Azure Cache for Redis 只接受以 [TLS 1.2 作為最低必要版本](cache-remove-tls-10-11.md)的安全連線。

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

如果連線成功，[HTTP 處理常式](https://golang.org/pkg/net/http/#HandleFunc)會設定為處理 `POST` 和 `GET` 作業，且 HTTP 伺服器已啟動。 

> [!NOTE] 
> [gorilla mux 程式庫](https://github.com/gorilla/mux)用於路由傳送 (雖然不是絕對必要，但我們可能已使用此範例應用程式的標準程式庫來避免)。
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

`userHandler` 結構會封裝 [redis.Client](https://pkg.go.dev/github.com/go-redis/redis/v8#Client)，其由 `createUser`、`getUser` 方法所使用 - 為了簡潔起見，尚未包含這些方法的程式碼。 

- `createUser`：接受 JSON 承載 (包含使用者資訊)，並將其儲存為 Azure Cache for Redis 中的 `HASH`。
- `getUser`：從 `HASH` 擷取使用者資訊，如果找不到，則會傳回 HTTP `404` 回應。

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
```

## <a name="clone-the-sample-application"></a>複製範例應用程式

從 GitHub 複製應用程式開始。

1. 開啟命令提示字元，並建立名為 `git-samples` 的新資料夾。

    ```bash
    md "C:\git-samples"
    ```

1. 開啟 Git 終端機視窗，例如 Git Bash。 使用 `cd` 命令變更至新資料夾，您會在其中複製範例應用程式。

    ```bash
    cd "C:\git-samples"
    ```

1. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>執行應用程式

應用程式會以環境變數的形式接受連線能力和認證。 

1. 在 [Azure 入口網站](https://portal.azure.com/)中針對 Azure Cache for Redis 執行個體擷取 **主機名稱** 和 **存取金鑰** (可透過存取金鑰取得)

1. 將其設定為個別的環境變數：

    ```shell
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. 在終端機視窗中，變更為正確的資料夾。 例如：

    ```shell
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. 在終端機視窗中，執行下列命令以啟動應用程式。

    ```shell
    go run main.go
    ```

HTTP 伺服器將會在連接埠 `8080` 上啟動。

## <a name="test-the-application"></a>測試應用程式

1. 建立幾個使用者項目。 下列範例會使用 curl：

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. 使用 `id` 擷取現有的使用者：

    ```bash
    curl -i localhost:8080/users/1
    ```

    您應會收到 JSON 回應，例如：
    
    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. 如果您嘗試擷取不存在的使用者，將會收到 HTTP `404`。 例如：

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
    ```

## <a name="clean-up-resources"></a>清除資源

如果您已完成此快速入門中所建立的 Azure 資源群組和資源，則您可刪除它們以避免衍生費用。

> [!IMPORTANT]
> 刪除資源群組是無法回復的動作，而資源群組及其中的所有資源都會永久刪除。 如果您在想要保留的現有資源群組中建立了 Azure Cache for Redis 執行個體，您可從快取的 [概觀] 頁面中選取 [刪除]，只刪除快取。 

刪除資源群組及其 Azure 執行個體的 Redis 快取：

1. 從 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [資源群組]。
1. 在 [依名稱篩選] 文字方塊中，輸入包含您快取執行個體的資源群組名稱，然後從搜尋結果中選取它。 
1. 在資源群組頁面上，選取 [刪除資源群組]。
1. 輸入資源群組名稱，然後選取 [刪除]。
   
   ![刪除 Azure Cache for Redis 的資源群組](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何開始使用 Go 搭配 Azure Cache for Redis。 您已設定並執行簡單的 REST API 應用程式，以建立並取得 Redis `HASH` 資料結構所支援的使用者資訊。

> [!div class="nextstepaction"]
> [建立可使用 Azure Cache for Redis 的簡單 ASP.NET Web 應用程式。](./cache-web-app-howto.md)
