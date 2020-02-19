---
title: Azure Functions HTTP 觸發程式
description: 瞭解如何透過 HTTP 呼叫 Azure 函式。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 8dbb4ff0c9f8df6609d8447e84dcfe878a954fff
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443953"
---
# <a name="azure-functions-http-trigger"></a>Azure Functions HTTP 觸發程式

HTTP 觸發程序可讓您透過 HTTP 要求叫用函式。 您可以使用 HTTP 觸發程序來建置無伺服器 API 並回應 Webhook。

HTTP 觸發函式的預設傳回值為：

- 函式2.x 和更高版本中具有空白主體的 `HTTP 204 No Content`
- 函式1.x 中具有空白主體的 `HTTP 200 OK`

若要修改 HTTP 回應，請設定[輸出](./functions-bindings-http-webhook-output.md)系結。

如需有關 HTTP 系結的詳細資訊，請參閱[總覽](./functions-bindings-http-webhook.md)和輸出系結[參考](./functions-bindings-http-webhook-output.md)。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下列範例會顯示在查詢字串或 HTTP 要求主體中尋找 [ 參數的 ](functions-dotnet-class-library.md)C# 函式`name`。 請注意，傳回值用於輸出繫結，但傳回值屬性並非必要。

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[C#文字](#tab/csharp-script)

下列範例示範 function.json 檔案中的觸發程序繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 函式會尋找 `name` 參數，其位於查詢字串或 HTTP 要求的主體。

以下是 *function.json* 檔案：

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

[設定](#configuration)章節會說明這些屬性。

以下是繫結至 `HttpRequest` 的 C# 指令碼：

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

您可以繫結至自訂的物件，而不是 `HttpRequest`。 會從要求主體建立這個物件，並剖析成 JSON。 同樣地，類型可以傳遞至 HTTP 回應輸出系結，並傳回做為回應主體，以及 `200` 狀態碼。

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例示範的是使用繫結之 function.json 檔案，以及 [JavaScript 函式](functions-reference-node.md)中的觸發程序繫結。 函式會尋找 `name` 參數，其位於查詢字串或 HTTP 要求的主體。

以下是 *function.json* 檔案：

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下列範例示範 *function.json* 檔案中的觸發程序繫結，以及使用此繫結的 [Python 函式](functions-reference-python.md)。 函式會尋找 `name` 參數，其位於查詢字串或 HTTP 要求的主體。

以下是 *function.json* 檔案：

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 Python 程式碼：

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

# <a name="java"></a>[Java](#tab/java)

* [讀取查詢字串的參數](#read-parameter-from-the-query-string)
* [從 POST 要求讀取主體](#read-body-from-a-post-request)
* [從路由讀取參數](#read-parameter-from-a-route)
* [從 POST 要求讀取 POJO 主體](#read-pojo-body-from-a-post-request)

下列範例顯示 HTTP 觸發程式系結。

#### <a name="read-parameter-from-the-query-string"></a>讀取查詢字串中的參數

此範例會從查詢字串中讀取名為 `id` 的參數，並使用它來建立傳回至用戶端且內容類型為 `application/json` 的 JSON 文件。

```java
@FunctionName("TriggerStringGet")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("GET parameters are: " + request.getQueryParameters());

    // Get named parameter
    String id = request.getQueryParameters().getOrDefault("id", "");

    // Convert and display
    if (id.isEmpty()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String name = "fake_name";
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-body-from-a-post-request"></a>從 POST 要求讀取主體

此範例會讀取 POST 要求的主體，作為 `String`，並使用它來建立傳回至用戶端且內容類型為 `application/json` 的 JSON 文件。

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route"></a>從路由讀取參數

此範例會從路由路徑讀取名為 `id` 的必要參數，和選用參數 `name`，然後使用這些參數來建立傳回至用戶端且內容類型為 `application/json` 的 JSON 文件。 T

```java
@FunctionName("TriggerStringRoute")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS,
            route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
        HttpRequestMessage<Optional<String>> request,
        @BindingName("id") String id,
        @BindingName("name") String name,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Route parameters are: " + id);

    // Convert and display
    if (id == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-pojo-body-from-a-post-request"></a>從 POST 要求讀取 POJO 主體

以下是此範例中所參照 `ToDoItem` 類別的程式碼：

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

此範例會讀取 POST 要求的主體。 要求主體會自動還原序列化至 `ToDoItem` 物件，並傳回至用戶端，且內容類型為 `application/json`。 `ToDoItem` 參數會在指派給 `body` 類別的 `HttpMessageResponse.Builder` 屬性時，由 Functions 執行階段序列化。

```java
@FunctionName("TriggerPojoPost")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.POST}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<ToDoItem>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Request body is: " + request.getBody().orElse(null));

    // Check request body
    if (!request.getBody().isPresent()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final ToDoItem body = request.getBody().get();
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(body)
                        .build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>屬性和注釋

在[ C#類別庫](functions-dotnet-class-library.md)和 JAVA 中，可以使用 `HttpTrigger` 屬性來設定函式。

您可以在屬性函式參數、webhook 類型和路由範本中，設定授權層級和允許的 HTTP 方法。 如需這些設定的詳細資訊，請參閱[configuration](#configuration)。

# <a name="c"></a>[C#](#tab/csharp)

這個範例示範如何使用[HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs)屬性。

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

如需完整範例，請參閱[觸發程式範例](#example)。

# <a name="c-script"></a>[C#文字](#tab/csharp-script)

C#腳本不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="java"></a>[Java](#tab/java)

這個範例示範如何使用[HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java)屬性。

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

如需完整範例，請參閱[觸發程式範例](#example)。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `HttpTrigger` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
| **type** | n/a| 必要項目 - 必須設定為 `httpTrigger`。 |
| **direction** | n/a| 必要項目 - 必須設定為 `in`。 |
| **name** | n/a| 必要項目 - 函式程式碼中用於要求或要求主體的變數名稱。 |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |會判斷要求中必須存在哪些金鑰 (若有的話) 才能叫用函式。 授權層級可為下列其中一個值： <ul><li><code>anonymous</code>&mdash;不需要 API 金鑰。</li><li><code>function</code>&mdash;需要函式專屬的 API 金鑰。 如果沒有提供任何值，此為預設值。</li><li><code>admin</code>&mdash;需要主要金鑰。</li></ul> 如需詳細資訊，請參閱有關[授權金鑰](#authorization-keys)章節。 |
| **methods** |**方法** | 函式將回應的 HTTP 方法陣列。 如果未指定，函式將會回應所有的 HTTP 方法。 請參閱[自訂 HTTP 端點](#customize-the-http-endpoint)。 |
| **route** | **路由** | 會定義路由範本，從而控制函式所要回應的要求 URL。 如果沒有提供任何值，預設值為 `<functionname>`。 如需詳細資訊，請參閱[自訂 HTTP 端點](#customize-the-http-endpoint)。 |
| **webHookType** | **WebHookType** | _只有針對 1.x 版執行階段才有支援。_<br/><br/>會設定 HTTP 觸發程序作為指定提供者的 [webhook](https://en.wikipedia.org/wiki/Webhook) 接收器。 如果設定這個屬性，請勿設定 `methods` 屬性。 Webhook 類型可以是下列值其中之一：<ul><li><code>genericJson</code>&mdash;一般用途的 Webhook 端點，不需要特定提供者的邏輯。 此設定會將要求限制為只有那些使用 HTTP POST 和包含 `application/json` 內容類型的要求。</li><li><code>github</code>&mdash;函式會回應 [GitHub Webhook](https://developer.github.com/webhooks/)。 請勿使用 _authLevel_ 屬性搭配 GitHub Webhook。 如需詳細資訊，請參閱本文稍後的 GitHub Webhook 一節。</li><li><code>slack</code>&mdash;函式會回應 [Slack Webhook](https://api.slack.com/outgoing-webhooks)。 請勿使用 _authLevel_ 屬性搭配 Slack Webhook。 如需詳細資訊，請參閱本文稍後的 Slack Webhook 一節。</li></ul>|

## <a name="payload"></a>Payload

觸發程式輸入類型會宣告為 `HttpRequest` 或自訂類型。 如果您選擇 `HttpRequest`，就會取得要求物件的完整存取權。 針對自訂的類型，執行階段會嘗試剖析 JSON 要求本文來設定物件屬性。

## <a name="customize-the-http-endpoint"></a>自訂 HTTP 端點

根據預設，當您為 HTTP 觸發程序建立函式時，將可透過下列形式的路由來定址該函式：

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

您可以在 HTTP 觸發程序的輸入繫結上使用選擇性的 `route` 屬性來自訂此路由。 舉例來說，下列 *function.json* 檔案定義了 HTTP 觸發程序的 `route` 屬性：

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

在使用此設定的情況下，現在便可使用下列路由來定址該函式，而不需使用原始路由。

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

此設定可讓函式程式碼支援位址、_類別_和_識別碼_中的兩個參數。

# <a name="c"></a>[C#](#tab/csharp)

您可以將任何 [Web API 路由條件約束](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)與您的參數搭配使用。 下列 C# 函式程式碼會同時利用這兩個參數。

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[C#文字](#tab/csharp-script)

您可以將任何 [Web API 路由條件約束](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)與您的參數搭配使用。 下列 C# 函式程式碼會同時利用這兩個參數。

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

在 Node 中，函式執行時間會提供來自 `context` 物件的要求主體。 如需詳細資訊，請參閱 [JavaScript 觸發程序範例](#example)。

下列範例顯示如何從 `context.bindingData`讀取路由參數。

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="python"></a>[Python](#tab/python)

函數執行內容是透過宣告為 `func.HttpRequest`的參數公開。 這個實例可讓函數存取資料路由參數、查詢字串值和方法，讓您能夠傳回 HTTP 回應。

一旦定義之後，即可透過呼叫 `route_params` 方法，將路由參數提供給函式。

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="java"></a>[Java](#tab/java)

函數執行內容是 `HttpTrigger` 屬性中所宣告的屬性。 屬性可讓您定義路由參數、授權層級、HTTP 指令動詞和連入要求實例。

路由參數是透過 `HttpTrigger` 屬性來定義。

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

所有函式路由預設前面都會加上 *api*。 您也可以在 `http.routePrefix`host.json[ 檔案中使用 ](functions-host-json.md) 屬性來自訂或移除前置詞。 下列範例會在 *host.json* 檔案中使用空字串作為前置詞來移除 *api* 路由前置詞。

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

## <a name="using-route-parameters"></a>使用路由參數

定義函式 `route` 模式的路由參數可用於每個系結。 例如，如果您有定義為 `"route": "products/{id}"` 的路由，則資料表儲存體系結可以使用系結設定中 `{id}` 參數的值。

下列設定顯示如何將 `{id}` 參數傳遞至系結的 `rowKey`。

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

## <a name="working-with-client-identities"></a>使用用戶端身分識別

如果您的函式應用程式使用 [App Service 驗證/授權](../app-service/overview-authentication-authorization.md)，您可以透過程式碼來檢視已驗證的用戶端相關資訊。 這項資訊是以[由平台插入的要求標頭](../app-service/app-service-authentication-how-to.md#access-user-claims)形式提供。 

您也可以從繫結資料來讀取這項資訊。 這項功能僅適用于2.x 和更新版本中的函式執行時間。 它目前也僅適用於 .NET 語言。

# <a name="c"></a>[C#](#tab/csharp)

已驗證用戶端的相關資訊可在[ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal)中取得。 ClaimsPrincipal 可作為要求內容的一部分提供，如下列範例所示：

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

ClaimsPrincipal 也可以直接納入為函式簽章中的額外參數：

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="c-script"></a>[C#文字](#tab/csharp-script)

已驗證用戶端的相關資訊可在[ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal)中取得。 ClaimsPrincipal 可作為要求內容的一部分提供，如下列範例所示：

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

ClaimsPrincipal 也可以直接納入為函式簽章中的額外參數：

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

已驗證的使用者可透過[HTTP 標頭](../app-service/app-service-authentication-how-to.md#access-user-claims)取得。

# <a name="python"></a>[Python](#tab/python)

已驗證的使用者可透過[HTTP 標頭](../app-service/app-service-authentication-how-to.md#access-user-claims)取得。

# <a name="java"></a>[Java](#tab/java)

已驗證的使用者可透過[HTTP 標頭](../app-service/app-service-authentication-how-to.md#access-user-claims)取得。

---

## <a name="authorization-keys"></a>授權金鑰

Functions 可讓您使用金鑰來提高開發期間存取 HTTP 函式端點的困難度。  標準 HTTP 觸發程序可要求在要求中有這樣的 API 金鑰存在。 

> [!IMPORTANT]
> 雖然金鑰可能有助於在開發期間遮蔽您的 HTTP 端點，但這並不適合用來作為在生產環境中保護 HTTP 觸發程序的方式。 若要深入了解，請參閱[在生產環境中保護 HTTP 端點](#secure-an-http-endpoint-in-production)。

> [!NOTE]
> 在 Functions 1.x 執行階段中，Webhook 提供者可以使用金鑰以多種方式授權要求，端視提供者支援的方式而定。 [Webhook 和金鑰](#webhooks-and-keys)中提供了這方面的相關說明。 2\.x 版和更新版本中的函數執行時間不包含 webhook 提供者的內建支援。

金鑰類型有兩種：

* **主機金鑰**：這些金鑰由函數應用程式中所有的函式共用。 當做為 API 金鑰使用時，這些金鑰會允許存取函數應用程式中的任何函式。
* **函式金鑰**：這些金鑰僅適用於據以定義它們的特定函式。 當做為 API 金鑰使用時，這些金鑰僅允許存取該函式。

每個金鑰均為具名以供參考，並且在函式和主機層級有一預設金鑰 (名稱為 "default")。 函式金鑰的優先順序高於主機金鑰。 當您使用相同的名稱來定義兩個金鑰時，一律會使用函式金鑰。

每個函數應用程式都有特殊的**主要金鑰**。 此金鑰是一個名為 `_master` 的主機金鑰，它會提供對執行階段 API 的系統管理存取權。 無法撤銷此金鑰。 當您將授權等級設定為 `admin` 時，要求就必須使用主要金鑰；任何其他金鑰則會導致授權失敗。

> [!CAUTION]  
> 由於主要金鑰會在您的函數應用程式中授與提高的權限，因此您不應該與第三方共用此金鑰，或是在原生用戶端應用程式中散發它。 當您選擇管理授權層級時，請務必謹慎。

## <a name="obtaining-keys"></a>取得金鑰

金鑰會當作您函數應用程式的一部分儲存於 Azure 中，並在加密後靜置。 若要檢視您的金鑰，請建立新的金鑰或將金鑰輪替為新的值，瀏覽至您在 [Azure 入口網站](https://portal.azure.com)中的其中一個 HTTP 觸發函式，然後選取 [管理]。

![在入口網站中管理函式金鑰。](./media/functions-bindings-http-webhook/manage-function-keys.png)

您可以使用[金鑰管理 api](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)以程式設計方式取得功能鍵。

## <a name="api-key-authorization"></a>API 金鑰授權

大多數 HTTP 觸發程序範本都會要求在要求中有 API 金鑰。 因此，您的 HTTP 要求通常看起來會像以下 URL：

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

金鑰可包含在名為 `code` 的查詢字串變數中，如以上所示。 它也可以包含在 `x-functions-key` HTTP 標頭中。 金鑰的值可以是針對函式定義的任何函式金鑰，或是任何主機金鑰。

您可以允許匿名要求，這不需要金鑰。 您也可以要求使用主要金鑰。 您可以使用繫結 JSON 中的 `authLevel` 屬性來變更預設授權層級。 如需詳細資訊，請參閱[觸發程序 - 組態](#configuration)。

> [!NOTE]
> 在本機執行函式時，不論指定的授權層級設定為何，都會停用授權。 發佈至 Azure 之後，就會強制執行您觸發程序中的 `authLevel` 設定。 [在容器中的本機執行](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally)時，仍然需要金鑰。


## <a name="secure-an-http-endpoint-in-production"></a>在生產環境中保護 HTTP 端點

若要在生產環境中完全保護您的函式端點，您應該考慮實作下列其中一個函數應用程式等級安全性選項：

* 為您的函數應用程式開啟「App Service 驗證/授權」。 App Service 平臺可讓您使用 Azure Active Directory （AAD）和數個協力廠商身分識別提供者來驗證用戶端。 您可以使用此策略來為您的函式執行自訂授權規則，而且您可以從函式程式碼處理使用者資訊。 若要深入了解，請參閱 [Azure App Service 中的驗證與授權](../app-service/overview-authentication-authorization.md)和[使用用戶端身分識別](#working-with-client-identities)。

* 使用「Azure API 管理」(APIM) 來驗證要求。 APIM 為傳入要求提供多種 API 安全性選項。 若要深入了解，請參閱 [API 管理驗證原則](../api-management/api-management-authentication-policies.md)。 備妥 APIM 之後，您可以設定讓函數應用程式只接受來自您 APIM 執行個體 IP 位址的要求。 若要深入了解，請參閱 [IP 位址限制](ip-addresses.md#ip-address-restrictions)。

* 將您的函數應用程式部署至 Azure App Service Environment (ASE)。 ASE 提供一個可供執行您函式的專用主控環境。 ASE 可讓您設定一個單一前端閘道，可用來驗證所有傳入要求。 如需詳細資訊，請參閱[設定 App Service Environment 的 Web 應用程式防火牆 (WAF)](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)。

使用其中一個函數應用層級的安全性方法時，您應該將 HTTP 觸發的函式授權層級設定為 `anonymous`。

## <a name="webhooks"></a>Webhook

> [!NOTE]
> Webhook 模式僅適用於 1.x 版 Functions 執行階段。 已進行這項變更，以改善2.x 版和更高版本中的 HTTP 觸發程式效能。

在版本 1.x 中，Webhook 範本會提供 Webhook 承載的額外驗證。 在2.x 版和更新版本中，基底 HTTP 觸發程式仍然有效，而且是建議的 webhook 方法。 

### <a name="github-webhooks"></a>GitHub Webhook

若要回應 GitHub Webhook，請先建立含有 HTTP 觸發程序的函式，然後將 **webHookType** 屬性設定為 `github`。 接著將其 URL 和 API 金鑰複製到您 GitHub 存放庫的 [新增 Webhook] 頁面。 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Slack Webhook

Slack webhook 會為您產生權杖，而不是由您指定，因此您必須使用 Slack 的權杖來設定函式專屬的金鑰。 請參閱[授權金鑰](#authorization-keys)。

## <a name="webhooks-and-keys"></a>Webhook 和金鑰

Webhook 授權是由 Webhook 接收器元件 (HTTP 觸發程序的一部分) 處理，處理機制則會以 Webhook 類型作為基礎而有所不同。 每個機制都依賴金鑰。 根據預設，將會使用名稱為 "default" 的函式金鑰。 如需使用不同的金鑰，請設定 Webhook 提供者以下列其中一種方式將金鑰名稱隨著要求一起傳送：

* **查詢字串**：提供者會在 `clientid` 查詢字串參數中傳遞金鑰名稱，例如 `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`。
* **要求標頭**︰提供者在 `x-functions-clientid` 標頭中傳遞金鑰名稱。

## <a name="limits"></a>限制

HTTP 要求長度的限制為 100 MB (104,857,600 個位元組)，而 URL 長度的限制為 4 KB (4,096 個位元組)。 這些限制由執行階段 `httpRuntime`Web.config 檔案[的 ](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config) 元素所指定。

如果使用 HTTP 觸發程式的函式未在230秒內完成， [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)將會超時，並傳回 HTTP 502 錯誤。 函式會繼續執行，但無法傳回 HTTP 回應。 對於長時間執行的函式，建議您遵循非同步模式，並傳回可以偵測要求狀態的位置。 如需函式可以執行多久的相關資訊，請參閱[級別和裝載 - 使用情況方案](functions-scale.md#timeout)。


## <a name="next-steps"></a>後續步驟

- [從函式傳回 HTTP 回應](./functions-bindings-http-webhook-output.md)
