---
title: 'Azure Functions 自訂處理常式 (預覽) '
description: 瞭解如何使用任何語言或執行階段版本的 Azure Functions。
author: anthonychu
ms.author: antchu
ms.date: 8/18/2020
ms.topic: article
ms.openlocfilehash: f3106553def982eb90ccc90822206e75a11ce354
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89294589"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure Functions 自訂處理常式 (預覽) 

每個函式應用程式都是由特定語言的處理常式執行。 雖然 Azure Functions 預設支援許多 [語言處理常式](./supported-languages.md) ，但在某些情況下，您可能會想要使用其他語言或執行時間。

自訂處理常式是從函數主機接收事件的輕量 web 伺服器。 任何支援 HTTP 基本專案的語言都可以執行自訂處理常式。

自訂處理常式最適用于您想要執行下列動作的情況：

- 使用目前不支援的語言（例如 Go 和 Rust）來執行函數應用程式。
- 在目前不支援的執行時間中執行函數應用程式，例如 Deno。

使用自訂處理常式時，您可以透過[延伸](./functions-bindings-register.md)模組套件組合使用[觸發程式和輸入和輸出](./functions-triggers-bindings.md)系結。

## <a name="overview"></a>概觀

下圖顯示函數主機與實作為自訂處理常式的 web 伺服器之間的關聯性。

![Azure Functions 自訂處理常式總覽](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. 每個事件都會觸發傳送至函數主機的要求。 事件是 Azure Functions 所支援的任何觸發程式。
1. 然後，函式主機會向 web 伺服器發出 [要求](#request-payload) 承載。 承載會保存觸發程式和輸入系結資料，以及函式的其他中繼資料。
1. Web 服務器會執行個別的函式，並將 [回應](#response-payload) 內容傳回給函式主機。
1. 函數主機會將回應中的資料傳遞給函式的輸出系結，以進行處理。

實作為自訂處理常式的 Azure Functions 應用程式必須根據幾個慣例，在檔案上設定 *host.json*、 *local.settings.json*和 *function.js* 。

## <a name="application-structure"></a>應用程式結構

若要執行自訂處理常式，您需要應用程式的下列層面：

- 位於應用程式根目錄的檔案*host.js*
- 位於應用程式根目錄的檔案*local.settings.js*
- 在符合函式名稱的資料夾內，每個函式 (的 *function.js* 檔案) 
- 執行網頁伺服器的命令、腳本或可執行檔

下圖顯示這些檔案如何在檔案系統中尋找名為 "MyQueueFunction" 的函式，以及名為 *handler.exe*的自訂處理常式可執行檔。

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>設定

應用程式是透過的 *host.js* 設定，並 * 在檔案上local.settings.js* 。

#### <a name="hostjson"></a>host.json

*host.json* 會指示函式主機傳送要求的位置，其方式是指向能夠處理 HTTP 事件的 web 伺服器。

自訂處理常式的定義方式是透過在檔案上設定 *host.js* ，其中包含如何透過區段執行 web 伺服器的詳細資料 `customHandler` 。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

`customHandler`區段會指向所定義的目標 `defaultExecutablePath` 。 執行目標可以是執行 web 伺服器的命令、可執行檔或檔案。

使用 `arguments` 陣列，將任何引數傳遞至可執行檔。 引數支援擴充環境變數 (應用程式設定) 使用 `%%` 標記法。

您也可以使用來變更可執行檔所使用的工作目錄 `workingDirectory` 。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>系結支援

標準觸發程式，以及輸入和輸出系結，可透過參考檔案*host.js*中的[延伸](./functions-bindings-register.md)模組組合來取得。

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.json* 定義在本機執行函數應用程式時所使用的應用程式設定。 因為它可能包含秘密，所以應從原始檔控制中排除 *local.settings.js的* 。 在 Azure 中，請改為使用應用程式設定。

若為自訂處理常式，請在 `FUNCTIONS_WORKER_RUNTIME` `Custom` *local.settings.js的上*將設定為。

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

> [!NOTE]
> `Custom` 在 Linux Premium 或 App Service 方案中，可能無法被辨識為有效的執行時間。 如果這是您的部署目標，請將設定 `FUNCTIONS_WORKER_RUNTIME` 為空字串。

### <a name="function-metadata"></a>函數中繼資料

與自訂處理常式搭配使用時，內容 * 上的function.js* 與在任何其他內容中定義函數的方式並無不同。 唯一的需求是檔案 * 上的function.js* 必須位於名為的資料夾中，才能符合函式名稱。

下列 *function.js* 設定具有佇列觸發程式和佇列輸出系結的函式。 因為它位於名為 *MyQueueFunction*的資料夾中，所以它會定義名為 *MyQueueFunction*的函式。

**MyQueueFunction/function.js開啟**

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="request-payload"></a>要求承載

當接收到佇列訊息時，函式主機會將 HTTP post 要求傳送至主體中有承載的自訂處理常式。

下列程式碼代表範例要求承載。 承載包含具有兩個成員的 JSON 結構： `Data` 和 `Metadata` 。

`Data`成員包含的索引鍵符合輸入和觸發程式名稱，如*function.json*檔案的系結陣列中所定義。

`Metadata`成員包含[從事件來源產生的中繼資料](./functions-bindings-expressions-patterns.md#trigger-metadata)。

```json
{
  "Data": {
    "myQueueItem": "{ message: \"Message sent\" }"
  },
  "Metadata": {
    "DequeueCount": 1,
    "ExpirationTime": "2019-10-16T17:58:31+00:00",
    "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
    "InsertionTime": "2019-10-09T17:58:31+00:00",
    "NextVisibleTime": "2019-10-09T18:08:32+00:00",
    "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
    "sys": {
      "MethodName": "QueueTrigger",
      "UtcNow": "2019-10-09T17:58:32.2205399Z",
      "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
    }
  }
}
```

### <a name="response-payload"></a>回應裝載

依照慣例，函數回應會格式化為索引鍵/值組。 支援的金鑰包括：

| <nobr>承載金鑰</nobr>   | 資料類型 | 備註                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | 物件 (object)    | 保留 `bindings` *function.js*中陣列所定義的回應值。<br /><br />比方說，如果函式是使用名為 "myQueueOutput" 的佇列輸出系結來設定，則會 `Outputs` 包含名為的索引鍵，該索引鍵 `myQueueOutput` 是由自訂處理常式設定為傳送至佇列的訊息。 |
| `Logs`        | array     | 訊息會出現在函式呼叫記錄中。<br /><br />在 Azure 中執行時，訊息會出現在 Application Insights 中。 |
| `ReturnValue` | 字串    | 當輸出設定為 `$return` 在檔案的 *function.js* 時，用來提供回應。 |

這是回應裝載的範例。

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>範例

自訂處理常式可以用任何支援接收 HTTP 事件的語言來執行。 下列範例示範如何使用 Go 程式設計語言來執行自訂處理常式。

### <a name="function-with-bindings"></a>具有系結功能的函式

此範例中所執行的案例具有一個名為的函式 `order` ，該函式會接受 `POST` 具有表示產品訂單的裝載。 當訂單張貼至函式時，會建立佇列儲存體訊息並傳回 HTTP 回應。

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>實作

在名為 *order*的資料夾中， *function.json* file 會設定 HTTP 觸發的函式。

**訂單/function.js開啟**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

此函式會定義為 [HTTP 觸發](./functions-bindings-http-webhook-trigger.md) 函式，此函式會傳回 [HTTP 回應](./functions-bindings-http-webhook-output.md) 並輸出 [佇列儲存體](./functions-bindings-storage-queue-output.md) 訊息。

在應用程式的根目錄中，檔案 * 上的host.js* 設定為 `handler.exe` `handler` 在 Linux 或 macOS) 中執行名為 (的可執行檔。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

這是傳送給函數執行時間的 HTTP 要求。

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

函數執行時間接著會將下列 HTTP 要求傳送至自訂處理常式：

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> 為了簡潔起見，已移除一些承載的部分。

*handler.exe* 是已編譯的 Go 自訂處理常式，它會執行 web 伺服器，並回應函數主機的函式調用要求。

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

在此範例中，自訂處理常式會執行 web 伺服器來處理 HTTP 事件，並設定為透過來接聽要求 `FUNCTIONS_CUSTOMHANDLER_PORT` 。

雖然函式主控制項收到原始 HTTP 要求 `/api/order` ，但是它會使用函式名稱 (其資料夾名稱) 來叫用自訂處理常式。 在此範例中，函數是在的路徑上定義 `/order` 。 主機會在的路徑上傳送 HTTP 要求給自訂處理常式 `/order` 。

當 `POST` 要求傳送至此函式時，可以透過 HTTP 要求本文取得觸發程式資料和函式中繼資料。 原始 HTTP 要求主體可在承載的中存取 `Data.req.Body` 。

函式的回應會格式化為索引鍵/值組，其中 `Outputs` 成員會保存 JSON 值，其中的索引鍵會符合 *function.json* 檔案中所定義的輸出。

這是此處理程式傳回給函數主機的範例承載。

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

藉由將 `message` 輸出的結果設定等於要求中的訂單資料，此函式會將訂單資料輸出到設定的佇列。 函數主機也會將中設定的 HTTP 回應 `res` 傳回給呼叫者。

### <a name="http-only-function"></a>僅限 HTTP 函式

針對沒有額外系結或輸出的 HTTP 觸發函式，您可能會想要讓處理常式直接使用 HTTP 要求和回應，而不是自訂處理常式 [要求](#request-payload) 和 [回應](#response-payload) 裝載。 您可以使用設定，在 *host.js* 中設定此行為 `enableForwardingHttpRequest` 。

> [!IMPORTANT]
> 自訂處理常式功能的主要目的是要在 Azure Functions 上啟用目前沒有第一級支援的語言和執行時間。 雖然您可以使用自訂處理常式來執行 web 應用程式，但 Azure Functions 不是標準反向 proxy。 無法使用某些功能，例如回應串流、HTTP/2 和 Websocket。 HTTP 要求的某些元件（例如特定標頭和路由）可能會受到限制。 您的應用程式可能也會遇到過多 [冷啟動](functions-scale.md#cold-start)。
>
> 若要解決這些情況，請考慮在 [Azure App Service](../app-service/overview.md)上執行您的 web 應用程式。

下列範例示範如何設定沒有額外系結或輸出的 HTTP 觸發函式。 此範例中所執行的案例具有名為 `hello` 的函式，該函式會接受 `GET` 或 `POST` 。

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>實作

在名為 *hello*的資料夾中， *function.json* file 會設定 HTTP 觸發的函式。

**hello/function.js開啟**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

函數設定為接受 `GET` 和 `POST` 要求，且結果值是透過名為的引數提供 `res` 。

在應用程式的根目錄中，檔案 * 上的host.js* 設定為執行， `handler.exe` 並 `enableForwardingHttpRequest` 設定為 `true` 。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

當 `enableForwardingHttpRequest` 為時 `true` ，僅限 HTTP 函式的行為與預設自訂處理常式的行為不同，如下所示：

* HTTP 要求不包含自訂處理常式 [要求](#request-payload) 承載。 相反地，函式主機會使用原始 HTTP 要求的複本來叫用處理程式。
* 函數主機會使用與原始要求相同的路徑來叫用處理程式，包括任何查詢字串參數。
* 函數主機會傳回處理常式的 HTTP 回應複本，做為原始要求的回應。

以下是對函數主機的 POST 要求。 然後，函式主機會將要求的複本傳送至位於相同路徑的自訂處理常式。

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

檔案 *處理常式. go* 檔案會執行 web 伺服器和 HTTP 函數。

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

在此範例中，自訂處理常式會建立 web 伺服器來處理 HTTP 事件，並設定為透過來接聽要求 `FUNCTIONS_CUSTOMHANDLER_PORT` 。

`GET` 要求的處理方式是傳回字串，而 `POST` 要求可以存取要求主體。

這裡的 order 函式路由與 `/api/hello` 原始要求相同。

>[!NOTE]
>不 `FUNCTIONS_CUSTOMHANDLER_PORT` 是用來呼叫函式的公開端口。 函數主機會使用這個埠來呼叫自訂處理常式。

## <a name="deploying"></a>正在部署

自訂處理常式可以部署到每個 Azure Functions 裝載選項。 如果您的處理常式需要作業系統或平臺相依性 (例如語言執行時間) ，您可能需要使用 [自訂容器](./functions-create-function-linux-custom-image.md)。

在 Azure 中為自訂處理常式建立函式應用程式時，建議您選取 [.NET Core] 作為堆疊。 未來將會新增自訂處理常式的「自訂」堆疊。

若要使用 Azure Functions Core Tools 部署自訂處理常式應用程式，請執行下列命令。

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> 確定執行自訂處理常式所需的所有檔案都在資料夾中，並包含在部署中。 如果您的自訂處理常式是二進位可執行檔，或有平臺特定的相依性，請確定這些檔案符合目標部署平臺。

## <a name="restrictions"></a>限制

- 自訂處理常式 web 伺服器必須在60秒內啟動。

## <a name="samples"></a>範例

如需如何以各種不同的語言來執行函式的範例，請參閱 [自訂處理常式範例 GitHub](https://github.com/Azure-Samples/functions-custom-handlers) 存放庫。

## <a name="troubleshooting-and-support"></a>疑難排解和支援

### <a name="trace-logging"></a>追蹤記錄

如果您的自訂處理常式進程無法啟動，或是與函式主機進行通訊時發生問題，您可以將函式應用程式的記錄層級增加至， `Trace` 以查看主機的更多診斷訊息。

若要變更函數應用程式的預設記錄層級，請在 `logLevel`host.js的區段中設定設定 `logging` * *。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

函數主機會輸出額外的記錄訊息，包括自訂處理常式處理常式的相關資訊。 使用記錄來調查開始自訂處理常式進程或在自訂處理常式中叫用函式的問題。

在本機上，會將記錄檔列印到主控台。

在 Azure 中， [查詢 Application Insights 追蹤](functions-monitoring.md#query-telemetry-data) 以查看記錄訊息。 如果您的應用程式會產生大量的記錄，則只會將一部分的記錄檔訊息傳送至 Application Insights。 [停用取樣](functions-monitoring.md#configure-sampling) ，以確保記錄所有訊息。

### <a name="test-custom-handler-in-isolation"></a>獨立測試自訂處理常式

自訂處理常式應用程式是一種 web 伺服器進程，因此使用[捲曲](https://curl.haxx.se/)或[Postman](https://www.postman.com/)之類的工具來傳送 mock [HTTP 要求](#request-payload)，可能會很有説明。

您也可以在 CI/CD 管線中使用此策略，在您的自訂處理常式上執行自動化測試。

### <a name="execution-environment"></a>執行環境

自訂處理常式會在與一般 Azure Functions 應用程式相同的環境中執行。 測試您的處理常式，以確保環境包含其執行所需的所有相依性。 對於需要額外相依性的應用程式，您可能需要使用裝載于 Azure Functions [Premium 方案](functions-premium-plan.md)的[自訂容器映射](functions-create-function-linux-custom-image.md)來執行這些應用程式。

### <a name="get-support"></a>取得支援

如果您在具有自訂處理常式的函式應用程式上需要協助，您可以透過一般支援管道提交要求。 不過，由於用來建立自訂處理常式應用程式的各種可能語言，支援並不是無限的。

如果函數主機在啟動或與自訂處理常式進行通訊時發生問題，就可以使用支援。 針對您的自訂處理常式流程內部運作的特定問題，例如所選語言或架構的問題，我們的支援小組無法在此內容中提供協助。
