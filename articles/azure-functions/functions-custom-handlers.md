---
title: Azure 函數自訂處理常式（預覽）
description: 瞭解如何將 Azure 函數與任何語言或執行階段版本一起使用。
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479250"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure 函數自訂處理常式（預覽）

每個函數應用都由特定于語言的處理常式執行。 雖然預設情況下 Azure 函數支援許多[語言處理常式](./supported-languages.md)，但在某些情況下，您可能需要對應用執行環境進行額外控制。 自訂處理常式為您提供此附加控制項。

自訂處理常式是從函數主機接收事件的羽量級 Web 服務器。 任何支援 HTTP 基元的語言都可以實現自訂處理常式。

自訂處理常式最適合您希望：

- 在官方支援的語言之外，使用語言實現函數應用
- 在語言版本或運行時中實現預設情況下不支援的功能應用
- 對應用執行環境進行精細控制

對於自訂處理常式，所有[觸發器以及輸入和輸出綁定](./functions-triggers-bindings.md)都通過[擴展包](./functions-bindings-register.md)支援。

## <a name="overview"></a>總覽

下圖顯示了函數主機和作為自訂處理常式實現的 Web 服務器之間的關係。

![Azure 函數自訂處理常式概述](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- 事件觸發發送到函數主機的請求。 該事件攜帶原始 HTTP 負載（對於沒有綁定的 HTTP 觸發函數）或保存函數的輸入綁定資料的有效負載。
- 然後，函數主機通過發出[請求負載](#request-payload)將請求代理到 Web 服務器。
- Web 服務器執行單個函數，並將[回應負載](#response-payload)返回給函數主機。
- 函數主機將回應代理為目標的輸出綁定負載。

作為自訂處理常式實現的 Azure 函數應用必須根據幾個約定配置*host.json*和*函數.json*檔。

## <a name="application-structure"></a>應用程式結構

要實現自訂處理常式，需要應用程式中的以下幾個方面：

- 應用根目錄的*host.json*檔
- 每個*函數的函數.json*檔（在與函數名稱匹配的資料夾中）
- 運行 Web 服務器的命令、腳本或可執行檔

下圖顯示了這些檔在檔案系統上查找名為"order"的函數的方式。

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>組態

應用程式通過*主機.json*檔進行配置。 此檔通過指向能夠處理 HTTP 事件的 Web 服務器告訴函數主機發送請求的位置。

自訂處理常式是通過配置*host.json*檔來定義的，該檔包含有關如何通過`httpWorker`該部分運行 Web 服務器的詳細資訊。

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

該`httpWorker`節指向 由 定義的目標`defaultExecutablePath`。 執行目標可以是命令、可執行檔或實現 Web 服務器的檔。

對於腳本化應用，`defaultExecutablePath`指向指令碼語言的運行時並`defaultWorkerPath`指向指令檔位置。 下面的示例顯示了 Node.js 中的 JavaScript 應用如何配置為自訂處理常式。

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

您還可以使用`arguments`陣列傳遞參數：

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

許多調試設置都有必要使用參數。 有關詳細資訊，請參閱[調試](#debugging)部分。

> [!NOTE]
> *host.json*檔必須與正在運行的 Web 服務器處於目錄結構中的同一級別。 預設情況下，某些語言和工具鏈可能不會將此檔放在應用程式根目錄。

#### <a name="bindings-support"></a>綁定支援

標準觸發器以及輸入和輸出綁定可通過引用*host.json*檔中[的擴展包](./functions-bindings-register.md)來獲取。

### <a name="function-metadata"></a>函數中繼資料

當與自訂處理常式一起使用時，*函數.json*內容與在任何其他上下文中定義函數的方式沒有什麼不同。 唯一的要求是*函數.json*檔必須位於命名的資料夾中，以匹配函數名稱。

### <a name="request-payload"></a>請求有效負載

純 HTTP 函數的請求負載是原始 HTTP 要求負載。 純 HTTP 函式定義為沒有輸入或輸出綁定的函數，這些函數返回 HTTP 回應。

任何包含輸入、輸出綁定或通過 HTTP 以外的事件源觸發的其他類型的函數都有自訂請求負載。

以下代碼表示示例請求負載。 有效負載包括一個 JSON 結構，`Data`其中包含`Metadata`兩個成員：和 。

成員`Data`包括匹配*函數.json*檔中綁定陣列中定義的輸入和觸發器名稱的鍵。

該`Metadata`成員包括[從事件源生成的中繼資料](./functions-bindings-expressions-patterns.md#trigger-metadata)。

給定以下*函數.json*檔中定義的綁定：

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

返回與此示例類似的請求負載：

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

### <a name="response-payload"></a>回應有效負載

按照慣例，函數回應格式化為鍵/值對。 支援的金鑰包括：

| <nobr>有效負載鍵</nobr>   | 資料類型 | 備註                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | 保存`bindings`*函數.json*檔陣列定義的回應值。<br /><br />例如，如果函數配置了名為"blob"的 Blob 存儲輸出綁定，則`Outputs`包含名為 的鍵`blob`，該鍵設置為 blob 的值。 |
| `Logs`        | array     | 消息將顯示在函式呼叫日誌中。<br /><br />在 Azure 中運行時，消息將顯示在應用程式見解中。 |
| `ReturnValue` | 字串    | 用於在將輸出配置為`$return`*函數.json*檔中時提供回應。 |

有關[示例有效負載，請參閱示例](#bindings-implementation)。

## <a name="examples"></a>範例

自訂處理常式可以使用支援 HTTP 事件的任何語言實現。 雖然 Azure 函數[完全支援 JavaScript 和 Node.js，](./functions-reference-node.md)但以下示例演示如何在 Node.js 中使用 JavaScript 實現自訂處理常式以進行教學。

> [!TIP]
> 作為學習如何以其他語言實現自訂處理常式的指南，如果希望在不支援的 Node.js 版本中運行函數應用，則此處顯示的基於 Node.js 的示例可能也很有用。

## <a name="http-only-function"></a>僅 HTTP 函數

下面的示例演示如何配置沒有附加綁定或輸出的 HTTP 觸發函數。 在此示例中實現的方案具有一個名為 的`http`函數，該函數`GET`接受`POST`或 。

以下程式碼片段表示如何組成對函數的請求。

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>實作

在名為*HTTP*的資料夾中，*函數.json*檔配置 HTTP 觸發的功能。

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

該函數配置為接受 和`GET``POST`請求，結果值通過名為 的`res`參數提供。

在應用的根目錄 *，host.json*檔配置為運行 Node.js 並指向`server.js`該檔。

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

檔*伺服器.js*檔實現 Web 服務器和 HTTP 功能。

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

在此示例中，Express 用於創建 Web 服務器來處理 HTTP 事件，並設置為通過 偵聽請求`FUNCTIONS_HTTPWORKER_PORT`。

函數在 的路徑上定義`/hello`。 `GET`請求通過返回一個簡單的 JSON 物件進行處理`POST`，並且請求可以通過`req.body`訪問請求正文。

此處的 order 函數的路由`/hello`是，`/api/hello`而不是因為函數主機將請求代理到自訂處理常式。

>[!NOTE]
>`FUNCTIONS_HTTPWORKER_PORT`不是用於調用函數的面向公共埠。 函數主機使用此埠調用自訂處理常式。

## <a name="function-with-bindings"></a>帶綁定的功能

在此示例中實現的方案具有一個名為 的`order`函數，該函數接受`POST`具有表示產品訂單的有效負載的 函數。 當訂單過帳到函數時，將創建佇列存儲消息並返回 HTTP 回應。

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

在名為*順序*的資料夾中，*函數.json*檔配置 HTTP 觸發的功能。

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

此功能定義為[HTTP 觸發函數](./functions-bindings-http-webhook-trigger.md)，該函數返回[HTTP 回應](./functions-bindings-http-webhook-output.md)並輸出[佇列存儲](./functions-bindings-storage-queue-output.md)消息。

在應用的根目錄 *，host.json*檔配置為運行 Node.js 並指向`server.js`該檔。

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

檔*伺服器.js*檔實現 Web 服務器和 HTTP 功能。

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

在此示例中，Express 用於創建 Web 服務器來處理 HTTP 事件，並設置為通過 偵聽請求`FUNCTIONS_HTTPWORKER_PORT`。

函數在 的路徑上定義`/order`。  此處的 order 函數的路由`/order`是，`/api/order`而不是因為函數主機將請求代理到自訂處理常式。

當`POST`要求傳送到此函數時，資料將通過幾個點公開：

- 請求正文可通過`req.body`
- 發佈到函數的資料可通過`req.body.Data.req.Body`

函數的回應格式化為鍵/值對，`Outputs`其中成員持有 JSON 值，其中鍵與*函數.json*檔中定義的輸出匹配。

通過設置`message`等於來自請求的消息和`res`預期的 HTTP 回應，此函數將消息輸出到佇列存儲並返回 HTTP 回應。

## <a name="debugging"></a>偵錯

要調試函數自訂處理常式應用，您需要添加適合語言和運行時的參數以啟用調試。

例如，要調試 Node.js 應用程式，標誌`--inspect`將作為*host.json*檔中的參數傳遞。

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
> 調試配置是*host.json*檔的一部分，這意味著您可能需要在部署到生產之前刪除某些參數。

使用此配置，可以使用以下命令啟動函數的主機進程：

```bash
func host start
```

啟動該過程後，可以附加調試器並命中中斷點。

### <a name="visual-studio-code"></a>Visual Studio Code

下面的示例配置演示如何設置*啟動.json*檔，將應用連接到 Visual Studio 代碼調試器。

此示例適用于 Node.js，因此您可能需要更改其他語言或運行時的此示例。

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

自訂處理常式可以部署到幾乎每個 Azure 函數託管選項（請參閱[限制](#restrictions)）。 如果處理常式需要自訂依賴項（如語言運行時），則可能需要使用[自訂容器](./functions-create-function-linux-custom-image.md)。

## <a name="restrictions"></a>限制

- Linux 消費計畫中不支援自訂處理常式。
- Web 服務器需要在 60 秒內啟動。

## <a name="samples"></a>範例

有關如何在各種不同語言中實現函數的示例，請參閱[自訂處理常式示例 GitHub 存儲庫](https://github.com/Azure-Samples/functions-custom-handlers)。
