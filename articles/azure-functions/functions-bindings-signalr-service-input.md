---
title: Azure Functions SignalR Service 輸入系結
description: 瞭解如何在 Azure Functions 中傳回 SignalR 服務端點 URL 和存取權杖。
author: craigshoemaker
ms.topic: reference
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 53d336aff3177a76c5e02266ffb8484bd9945119
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77530259"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Azure Functions 的 SignalR Service 輸入系結

在用戶端可以連線到 Azure SignalR Service 之前，它必須擷取服務端點 URL 和有效的存取權杖。 SignalRConnectionInfo** 輸入繫結會產生 SignalR Service 端點 URL 和有效的存取權杖，可用來連線到服務。 因為權杖是限時的，且可用來驗證連線的特定使用者，所以您不應該快取權杖或者在用戶端之間共用權杖。 使用此繫結的 HTTP 觸發程序可以供用戶端用來擷取連線資訊。

如需如何使用此系結來建立可供 SignalR 用戶端 SDK 使用的「negotiate」函式的詳細資訊，請參閱 SignalR Service 概念檔中的[Azure Functions 開發和](../azure-signalr/signalr-concept-serverless-development-config.md)設定一文。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](functions-bindings-signalr-service.md)。

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下列範例示範 [C# 函式](functions-dotnet-class-library.md)，該函式會使用輸入繫結來取得 SignalR 連線資訊，並且透過 HTTP 傳回。

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

下列範例顯示*function.json*檔案的連接資訊輸入系結，以及使用該系結來傳回連接資訊的[c # 腳本](functions-reference-csharp.md)函式。

以下是 *function.json* 檔案中的繫結資料：

function.json 範例：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

以下是 c # 腳本程式碼：

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例示範 function.json** 檔案中的 SignalR 連線資訊輸入繫結，以及使用繫結來傳回連線資訊的 [JavaScript 函式](functions-reference-node.md)。

以下是 *function.json* 檔案中的繫結資料：

function.json 範例：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

下列範例顯示在檔案的*function.js*中的 SignalR 連線資訊輸入系結，以及使用該系結來傳回連接資訊的[Python](functions-reference-python.md)函式。

以下是 *function.json* 檔案中的繫結資料：

function.json 範例：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

以下是 Python 程式碼：

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

下列範例示範的[JAVA](functions-reference-java.md)函式會使用輸入系結取得 SignalR 連接資訊，並透過 HTTP 傳回它。

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="authenticated-tokens"></a>已驗證權杖

如果函式是由已驗證的用戶端觸發，您可以將使用者識別碼宣告新增至產生的權杖。 您可以使用[App Service authentication](../app-service/overview-authentication-authorization.md)，輕鬆地將驗證新增至函數應用程式。

App Service 驗證會設定名為 `x-ms-client-principal-id` 和 `x-ms-client-principal-name` 的 HTTP 標頭，這些標頭分別包含已驗證使用者的用戶端主體識別碼和名稱。

# <a name="c"></a>[C#](#tab/csharp)

您可以使用系結運算式，將系結的 `UserId` 屬性設定為任一標[binding expression](./functions-bindings-expressions-patterns.md)頭的值： `{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}` 。

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

您可以使用系結運算式，將系結的 `userId` 屬性設定為任一標[binding expression](./functions-bindings-expressions-patterns.md)頭的值： `{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}` 。

function.json 範例：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

以下是 c # 腳本程式碼：

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

您可以使用系結運算式，將系結的 `userId` 屬性設定為任一標[binding expression](./functions-bindings-expressions-patterns.md)頭的值： `{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}` 。

function.json 範例：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

您可以使用系結運算式，將系結的 `userId` 屬性設定為任一標[binding expression](./functions-bindings-expressions-patterns.md)頭的值： `{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}` 。

function.json 範例：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

以下是 Python 程式碼：

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

您可以使用系結運算式，將系結的 `userId` 屬性設定為任一標[binding expression](./functions-bindings-expressions-patterns.md)頭的值： `{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}` 。

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="next-steps"></a>後續步驟

- [傳送 SignalR Service 訊息（輸出系結）](./functions-bindings-signalr-service-output.md) 
