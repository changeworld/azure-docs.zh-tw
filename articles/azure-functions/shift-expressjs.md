---
title: 從 Express.js 轉移至 Azure Functions
description: 瞭解如何將 Express.js 端點重構至 Azure Functions。
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 3e38fae84f63f4867befbc3695242dc62dc8dbe8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502642"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>從 Express.js 轉移至 Azure Functions

Express.js 是適用于 網頁程式開發人員的其中一個最受歡迎的 Node.js 架構，而且仍然是建立服務 API 端點之應用程式的絕佳選擇。

將程式碼遷移至無伺服器架構時，重構 Express.js 端點會影響下欄區域：

- **中介軟體**： Express.js 具備健全的中介軟體集合。 在 Azure Functions 和[AZURE API 管理](../api-management/api-management-key-concepts.md)功能中，不再需要許多中介軟體模組。 在遷移端點之前，請確定您可以複寫或取代由必要中介軟體處理的任何邏輯。

- **不同的 api**：用來處理要求和回應的 api 在 Azure Functions 和 Express.js 之間有所不同。 下列範例會詳細說明所需的變更。

- **預設路由**：根據預設，Azure Functions 端點會在路由下公開 `api` 。 路由規則可透過[ `routePrefix` _host.json_ ](./functions-bindings-http-webhook-output.md#hostjson-settings)檔案來設定。

- 設定**和慣例**：函式應用程式會使用檔案_上的function.js_來定義 HTTP 動詞命令、定義安全性原則，以及設定函數的[輸入和輸出](./functions-triggers-bindings.md)。 根據預設，包含函式檔案的資料夾名稱會定義端點名稱，但是您可以透過檔案中function.js的屬性來變更名稱 `route` 。 [function.json](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)

> [!TIP]
> 若要深入瞭解，請透過互動式教學課程將[Node.js 和 Express Api 重構至具有 Azure Functions 的無伺服器 api](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/)。

## <a name="example"></a>範例

### <a name="expressjs"></a>Express.js

下列範例顯示一般 Express.js `GET` 端點。

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

將 `GET` 要求傳送至時 `/hello` ， `HTTP 200` 會傳回包含的回應 `Success` 。 如果端點遇到錯誤，回應會是 `HTTP 500` 具有錯誤詳細資料的。

### <a name="azure-functions"></a>Azure Functions

Azure Functions 會將設定和程式碼檔案組織成每個功能的單一資料夾。 根據預設，資料夾的名稱會指定函數名稱。

例如，名為的函式具有具有下列檔案的 `hello` 資料夾。

``` files
| - hello
|  - function.json
|  - index.js
```

下列範例會執行與上述 Express.js 端點相同的結果，但具有 Azure Functions。

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

- **模組：** 函式程式碼會實作為 JavaScript 模組。

- **CoNtext 和 response 物件**：可 [`context`](./functions-reference-node.md#context-object) 讓您與函數的執行時間通訊。 從內容中，您可以讀取要求資料並設定函數的回應。 同步程式碼會要求您呼叫 `context.done()` 以完成執行，而函式會以 `asyc` 隱含方式解析要求。

- **命名慣例**：根據預設，用來包含 Azure Functions 檔案的資料夾名稱會作為端點名稱（這可以在的[function.js](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)中覆寫）。

- 設定 **：您**可以在檔案（例如或）[上定義function.js](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)中的 HTTP 指令動詞 `POST` `PUT` 。

下列_function.js_檔案包含函式的設定資訊。

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

藉由 `get` 在陣列中定義，函式 `methods` 可用於 HTTP `GET` 要求。 如果您想要讓 API 接受支援 `POST` 要求，您也可以將加入 `post` 至陣列。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解，請前往互動式教學課程使用[Azure Functions 將 Node.js 和 Express Api 重構至無伺服器 api](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/)
