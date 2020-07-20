---
title: Azure Functions 自訂處理常式（預覽）
description: 瞭解如何使用任何語言或執行階段版本的 Azure Functions。
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: cdbb5bbde1e5efef9bef992a62a54f1525a16df7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85052586"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure Functions 自訂處理常式（預覽）

每個函式應用程式都是由特定語言的處理常式執行。 雖然 Azure Functions 預設支援許多[語言處理常式](./supported-languages.md)，但在某些情況下，您可能會想要對應用程式執行環境進行額外的控制。 自訂處理常式會提供您額外的控制。

自訂處理常式是從函式主機接收事件的輕量 web 伺服器。 任何支援 HTTP 基本物件的語言都可以執行自訂處理常式。

自訂處理常式最適用于您想要執行下列動作的情況：

- 以未正式支援的語言來執行函數應用程式。
- 在預設情況下，不支援的語言版本或執行時間中執行函數應用程式。
- 對函數應用程式執行環境提供更細微的控制。

透過自訂處理常式，所有[觸發程式和輸入和輸出](./functions-triggers-bindings.md)系結都可透過[延伸](./functions-bindings-register.md)模組組合來支援。

## <a name="overview"></a>總覽

下圖顯示函數主機和實作為自訂處理常式的 web 伺服器之間的關聯性。

![Azure Functions 自訂處理常式總覽](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- 事件會觸發傳送至函數主機的要求。 事件會攜帶未經處理的 HTTP 承載（適用于沒有系結的 HTTP 觸發函式），或包含函式之輸入系結資料的裝載。
- 然後，函式主機會藉由發出[要求](#request-payload)承載，以 proxy 將要求送至 web 伺服器。
- Web 服務器會執行個別的函式，並將[回應](#response-payload)承載傳回給函式主機。
- 函式會將回應做為輸出系結裝載的目標 proxy。

實作為自訂處理常式的 Azure Functions 應用程式必須根據幾個慣例，在檔案上設定*host.js*和*function.js* 。

## <a name="application-structure"></a>應用程式結構

若要執行自訂處理常式，您的應用程式需要下列層面：

- 位於應用程式根目錄的檔案*host.js*
- 每個函式的檔案*function.js* （在符合函式名稱的資料夾內）
- 執行網頁伺服器的命令、腳本或可執行檔

下圖顯示這些檔案如何在檔案系統上查看名為 "order" 的函式。

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>組態

應用程式是透過*host.json*檔案進行設定。 這個檔案會指示函式裝載在何處傳送要求，方法是指向能夠處理 HTTP 事件的 web 伺服器。

自訂處理常式的定義方式是在檔案*上設定host.js* ，並提供如何透過一節執行 web 伺服器的詳細資料 `httpWorker` 。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

`httpWorker`區段會指向所定義的目標 `defaultExecutablePath` 。 執行目標可能是用來執行 web 伺服器的命令、可執行檔或檔案。

針對已編寫腳本的應用程式，會 `defaultExecutablePath` 指向指令碼語言的執行時間，並 `defaultWorkerPath` 指向腳本檔案位置。 下列範例顯示如何將 Node.js 中的 JavaScript 應用程式設定為自訂處理常式。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

您也可以使用陣列傳遞引數 `arguments` ：

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

引數是許多偵錯工具的必要參數。 如需詳細資訊，請參閱[調試](#debugging)程式一節。

> [!NOTE]
> 檔案*上的host.js*必須與執行中的網頁伺服器位於相同層級的目錄結構中。 某些語言和工具鏈預設可能不會將這個檔案放在應用程式根目錄。

#### <a name="bindings-support"></a>系結支援

標準觸發程式以及輸入和輸出系結可透過參考檔案中*host.js*的[延伸](./functions-bindings-register.md)模組配套來取得。

### <a name="function-metadata"></a>函數中繼資料

與自訂處理常式搭配使用時， *function.js*內容與您在任何其他內容下定義函式的方式不同。 唯一的要求是檔案*上的function.js*必須位於名為的資料夾中，以符合函式名稱。

### <a name="request-payload"></a>要求承載

純 HTTP 函式的要求承載是原始 HTTP 要求承載。 純 HTTP 函式定義為沒有輸入或輸出系結的函式，會傳回 HTTP 回應。

任何其他類型的函式，包括輸入、輸出系結或透過 HTTP 以外的事件來源觸發，都有自訂要求承載。

下列程式碼代表範例要求承載。 裝載包含具有兩個成員的 JSON 結構： `Data` 和 `Metadata` 。

`Data`成員包含符合輸入和觸發程式名稱的索引鍵，如*function.json*檔案中的系結陣列中所定義。

`Metadata`成員包含[從事件來源產生的中繼資料](./functions-bindings-expressions-patterns.md#trigger-metadata)。

指定在下列*function.js*檔案中定義的系結：

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

會傳回類似此範例的要求承載：

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

### <a name="response-payload"></a>回應承載

依照慣例，函數回應會格式化為索引鍵/值組。 支援的金鑰包括：

| <nobr>承載金鑰</nobr>   | 資料類型 | 備註                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | 保留 `bindings` *function.json*檔案之陣列所定義的回應值。<br /><br />例如，如果已使用名為 "blob" 的 blob 儲存體輸出系結來設定函式，則 `Outputs` 會包含名為的索引鍵 `blob` ，它會設定為 blob 的值。 |
| `Logs`        | array     | 訊息會出現在函式呼叫記錄中。<br /><br />在 Azure 中執行時，訊息會顯示在 Application Insights 中。 |
| `ReturnValue` | 字串    | 當輸出設定為檔案 `$return` 中的*function.js*時，用來提供回應。 |

如需[範例](#bindings-implementation)承載，請參閱範例。

## <a name="examples"></a>範例

自訂處理常式可以用任何支援 HTTP 事件的語言來執行。 雖然 Azure Functions[完全支援 JavaScript 和 Node.js](./functions-reference-node.md)，但下列範例會示範如何在 Node.js 中，使用 javascript 來執行自訂處理常式，以用於指示目的。

> [!TIP]
> 當您瞭解如何以其他語言來執行自訂處理常式時，如果您想要在不支援的 Node.js 版本中執行函式應用程式，此處所示的 Node.js 型範例也可能很有用。

## <a name="http-only-function"></a>僅限 HTTP 函式

下列範例示範如何設定不含其他系結或輸出的 HTTP 觸發函式。 在此範例中執行的案例具有名為 `http` 的函式，可接受 `GET` 或 `POST` 。

下列程式碼片段代表如何撰寫對函式的要求。

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>實作

在名為*HTTP*的資料夾中， *function.json* file 會設定 HTTP 觸發的函式。

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
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

函式已設定為接受 `GET` 和 `POST` 要求，而且結果值是透過名為的引數提供 `res` 。

在應用程式的根目錄，檔案*上的host.js*設定為執行 Node.js 並指向檔案 `server.js` 。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

檔案*server.js*檔案會實行 web 伺服器和 HTTP 函數。

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

在此範例中，會使用 Express 來建立 web 伺服器來處理 HTTP 事件，並將設定為透過接聽要求 `FUNCTIONS_HTTPWORKER_PORT` 。

函式定義于的路徑 `/hello` 。 `GET`要求是藉由傳回簡單的 JSON 物件來處理，而 `POST` 要求則可以透過存取要求 `req.body` 主體。

這裡的 order 函式路由是，而不是因為函式 `/hello` `/api/hello` 主機會將要求代理至自訂處理常式。

>[!NOTE]
>不 `FUNCTIONS_HTTPWORKER_PORT` 是用來呼叫函數的公用埠。 函式主機會使用此埠來呼叫自訂處理常式。

## <a name="function-with-bindings"></a>具有系結的函式

在此範例中執行的案例具有名為 `order` 的函式，它會接受 `POST` 具有代表產品訂單之裝載的。 當訂單張貼至函式時，會建立佇列儲存體訊息並傳回 HTTP 回應。

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>實作

在名為*order*的資料夾中， *function.json* file 會設定 HTTP 觸發的函式。

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
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

此函式定義為[HTTP 觸發](./functions-bindings-http-webhook-trigger.md)的函式，會傳回[HTTP 回應](./functions-bindings-http-webhook-output.md)並輸出[佇列儲存體](./functions-bindings-storage-queue-output.md)訊息。

在應用程式的根目錄，檔案*上的host.js*設定為執行 Node.js 並指向檔案 `server.js` 。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

檔案*server.js*檔案會實行 web 伺服器和 HTTP 函數。

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

在此範例中，會使用 Express 來建立 web 伺服器來處理 HTTP 事件，並將設定為透過接聽要求 `FUNCTIONS_HTTPWORKER_PORT` 。

函式定義于的路徑 `/order` 。  這裡的 order 函式路由是，而不是因為函式 `/order` `/api/order` 主機會將要求代理至自訂處理常式。

當 `POST` 要求傳送至這個函式時，資料會透過幾個重點公開：

- 您可以透過取得要求本文`req.body`
- 張貼至函數的資料可透過`req.body.Data.req.Body`

函式的回應會格式化成索引鍵/值組，其中的 `Outputs` 成員會保存一個 JSON 值，其中的索引鍵會符合檔案中*function.js*所定義的輸出。

藉由設定 `message` 等於來自要求的訊息和 `res` 預期的 HTTP 回應，此函式會將訊息輸出到佇列儲存體，並傳回 HTTP 回應。

## <a name="debugging"></a>偵錯

若要對函式自訂處理常式應用程式進行「偵測」，您必須加入適用于語言和執行時間的引數，以啟用偵測。

比方說，若要進行 Node.js 應用程式的 debug，旗標會當做檔案 `--inspect` 中*host.js*的引數傳遞。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> 「偵錯工具設定」是您*host.json*檔案的一部分，這表示您可能需要先移除一些引數，才能部署到生產環境。

使用此設定時，您可以使用下列命令來啟動函式的主機進程：

```bash
func host start
```

啟動程式之後，您可以附加偵錯工具，並叫用中斷點。

### <a name="visual-studio-code"></a>Visual Studio Code

下列範例是示範如何在檔案*上設定launch.js*以將應用程式連線至 Visual Studio Code 偵錯工具的範例設定。

這個範例適用于 Node.js，因此您可能必須針對其他語言或執行時間改變此範例。

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>正在部署

自訂處理常式幾乎可以部署到每個 Azure Functions 裝載選項（請參閱[限制](#restrictions)）。 如果您的處理常式需要自訂相依性（例如語言執行時間），您可能需要使用[自訂容器](./functions-create-function-linux-custom-image.md)。

若要使用 Azure Functions Core Tools 部署自訂處理常式應用程式，請執行下列命令。

```bash
func azure functionapp publish $functionAppName --no-build --force
```

## <a name="restrictions"></a>限制

- 網頁伺服器必須在60秒內啟動。

## <a name="samples"></a>範例

如需如何以各種不同的語言來執行函式的範例，請參閱[自訂處理常式範例 GitHub](https://github.com/Azure-Samples/functions-custom-handlers)存放庫。
