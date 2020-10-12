---
title: 從 Express.js 切換至 Azure Functions
description: 瞭解如何重構 Express.js 端點以 Azure Functions。
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 266df5371ff5f47526fa9d6567c62e31d51ebb05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87810219"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>從 Express.js 切換至 Azure Functions

Express.js 是 網頁程式開發人員最受歡迎的 Node.js 架構之一，而且仍是建立服務 API 端點之應用程式的絕佳選擇。

將程式碼遷移至無伺服器架構時，重構 Express.js 端點會影響下欄區域：

- **中介軟體**： Express.js 功能是一組強大的中介軟體。 許多中介軟體模組不再需要 Azure Functions 和 [AZURE API 管理](../api-management/api-management-key-concepts.md) 功能的輕量。 在遷移端點之前，請確定您可以複寫或取代由重要中介軟體所處理的任何邏輯。

- **不同的 api**：用來處理要求和回應的 api 在 Azure Functions 和 Express.js 之間有所不同。 下列範例詳細說明必要的變更。

- **預設路由**：根據預設，Azure Functions 端點會在路由底下公開 `api` 。 路由規則可透過[ `routePrefix` _host.json_ ](./functions-bindings-http-webhook-output.md#hostjson-settings)檔案進行設定。

- 設定**和慣例**：函式應用程式會使用檔案_上的function.js_來定義 HTTP 指令動詞、定義安全性原則，以及設定函數的[輸入和輸出](./functions-triggers-bindings.md)。 依預設，包含函式檔案的資料夾名稱會定義端點名稱，但您可以透過 `route` [function.json](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) 檔案中的屬性來變更名稱。

> [!TIP]
> 深入瞭解互動式教學課程 [如何重構 Node.js，並使用 Azure Functions 將 api 提供給無伺服器 api](/learn/modules/shift-nodejs-express-apis-serverless/)。

## <a name="example"></a>範例

### <a name="expressjs"></a>Express.js

下列範例顯示一般的 Express.js `GET` 端點。

```javascript
// server.js
app.get('/hello', (req, res) => {
  try {
    res.send("Success!");
  } catch(error) {
    const err = JSON.stringify(error);
    res.status(500).send(`Request error. ${err}`);
  }
});
```

將 `GET` 要求傳送至時 `/hello` ， `HTTP 200` 會傳回包含的回應 `Success` 。 如果端點發生錯誤，回應會是 `HTTP 500` 包含錯誤詳細資料的。

### <a name="azure-functions"></a>Azure Functions

Azure Functions 會將設定和程式碼檔案組織成每個函式的單一資料夾。 依預設，資料夾的名稱會指定函數名稱。

例如，名為的函式 `hello` 具有具有下列檔案的資料夾。

``` files
| - hello
|  - function.json
|  - index.js
```

下列範例會執行與上述 Express.js 端點相同的結果，但使用 Azure Functions。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// hello/index.js
module.exports = async function (context, req) {
  try {
    context.res = { body: "Success!" };
  } catch(error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`
    };
  }
};
```

# <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
// hello/index.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
  try {
    context.res = { body: "Success!" };
  } catch (error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`,
    };
  }
};

export default httpTrigger;
```

---

移至函式時，會進行下列變更：

- **課程模組：** 函式程式碼會實作為 JavaScript 模組。

- **內容和回應物件**：可 [`context`](./functions-reference-node.md#context-object) 讓您與函式的執行時間進行通訊。 您可以從內容讀取要求資料，並設定函數的回應。 同步程式碼需要您呼叫 `context.done()` 才能完成執行，而函 `asyc` 式會隱含地解析要求。

- **命名慣例**：用來包含 Azure Functions 檔案的資料夾名稱預設會用來做為端點名稱 (這可以在) 的 [function.js](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) 中覆寫。

- 設定 **：您**可以在檔案的[function.js](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)中定義 HTTP 動詞命令，例如 `POST` 或 `PUT` 。

下列 _function.js_ 檔案保存函數的設定資訊。

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

藉由 `get` 在陣列中定義 `methods` ，此函式可供 HTTP `GET` 要求使用。 如果您想要讓 API 接受支援 `POST` 要求，也可以新增 `post` 至陣列。

## <a name="next-steps"></a>後續步驟

- 深入瞭解互動式教學課程 [如何使用 Azure Functions 將 Node.js 和 Express Api 重構至無伺服器 api](/learn/modules/shift-nodejs-express-apis-serverless/)