---
title: 函數2.x 和更新版本的 Azure Cosmos DB 輸入系結
description: 瞭解如何在 Azure Functions 中使用 Azure Cosmos DB 輸入系結。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: dec41a5e05d22891aae9d16280ebb6b0c8da3f20
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185108"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x-and-higher"></a>Azure Functions 2.x 和更新版本 Azure Cosmos DB 輸入系結

Azure Cosmos DB 輸入繫結會使用 SQL API 來擷取一或多個 Azure Cosmos DB 文件，並傳遞給函式的輸入參數。 您能以叫用函式的觸發程序作為基礎，來判斷文件識別碼或查詢參數。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](./functions-bindings-cosmosdb-v2.md)。

> [!NOTE]
> 如果集合已 [分割](../cosmos-db/partitioning-overview.md#logical-partitions)，則查閱作業也必須指定資料分割索引鍵值。
>

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

本區段包含下列範例：

* [佇列觸發程序，從 JSON 中查閱識別碼](#queue-trigger-look-up-id-from-json-c)
* [HTTP 觸發程序，從查詢字串中查閱識別碼](#http-trigger-look-up-id-from-query-string-c)
* [HTTP 觸發程序，從路由資料中查閱識別碼](#http-trigger-look-up-id-from-route-data-c)
* [HTTP 觸發程序，使用 SqlQuery 從路由資料中查閱識別碼](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP 觸發程序，使用 SqlQuery 取得多個文件](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP 觸發程序，使用 DocumentClient 取得多個文件](#http-trigger-get-multiple-docs-using-documentclient-c)

範例會參考簡單的 `ToDoItem` 類型：

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        [JsonProperty("id")]
        public string Id { get; set; }
        
        [JsonProperty("partitionKey")]
        public string PartitionKey { get; set; }
        
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>佇列觸發程序，從 JSON 中查閱識別碼 

下列範例示範會顯示擷取單一文件的 [C# 函式](functions-dotnet-class-library.md)。 函式會由包含 JSON 物件的佇列訊息觸發。 佇列觸發程式會將 JSON 剖析為類型的物件 `ToDoItemLookup` ，其中包含要查閱的識別碼和分割區索引鍵值。 該識別碼和資料分割索引鍵值會用來 `ToDoItem` 從指定的資料庫和集合中取出檔。

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP 觸發程序，從查詢字串中查閱識別碼

下列範例示範會顯示擷取單一文件的 [C# 函式](functions-dotnet-class-library.md)。 此函式是由 HTTP 要求所觸發，該 HTTP 要求會使用查詢字串指定要查閱的識別碼和分割區索引鍵值。 該識別碼和資料分割索引鍵值會用來 `ToDoItem` 從指定的資料庫和集合中取出檔。

>[!NOTE]
>HTTP 查詢字串參數會區分大小寫。
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 觸發程序，從路由資料中查閱識別碼

下列範例示範會顯示擷取單一文件的 [C# 函式](functions-dotnet-class-library.md)。 此函式是由 HTTP 要求所觸發，該 HTTP 要求會使用路由資料來指定要查閱的識別碼和分割區索引鍵值。 該識別碼和資料分割索引鍵值會用來 `ToDoItem` 從指定的資料庫和集合中取出檔。

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP 觸發程序，使用 SqlQuery 從路由資料中查閱識別碼

下列範例示範會顯示擷取單一文件的 [C# 函式](functions-dotnet-class-library.md)。 函式會由 HTTP 要求觸發，該 HTTP 要求會使用路由資料指定要查閱的識別碼。 該識別碼會用來從指定的資料庫和集合中，擷取 `ToDoItem` 文件。

範例會顯示如何在 `SqlQuery` 參數中使用繫結運算式。 您可以如範例所示，將路由資料傳遞至 `SqlQuery` 參數，但目前[您無法傳遞查詢字串值](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583)。

> [!NOTE]
> 如果您只需要依識別碼進行查詢，建議使用查閱，就像 [先前的範例](#http-trigger-look-up-id-from-query-string-c)一樣，因為它會耗用較少的 [要求單位](../cosmos-db/request-units.md)。 點讀取作業 (取得) 比依識別碼查詢 [更有效率](../cosmos-db/optimize-cost-reads-writes.md) 。
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP 觸發程序，使用 SqlQuery 取得多個文件

下列範例示範會顯示擷取文件清單的 [C# 函式](functions-dotnet-class-library.md)。 函式是由 HTTP 要求所觸發。 查詢會在 `SqlQuery` 屬性內容中指定。

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP 觸發程序，使用 DocumentClient 取得多個文件

下列範例示範會顯示擷取文件清單的 [C# 函式](functions-dotnet-class-library.md)。 函式是由 HTTP 要求所觸發。 程式碼會使用由 Azure Cosmos DB 繫結提供的 `DocumentClient` 執行個體來讀取文件清單。 `DocumentClient` 執行個體也會用來進行寫入作業。

> [!NOTE]
> 您也可以使用 [IDocumentClient](/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) 介面讓測試變得更容易。

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

本區段包含下列範例：

* [佇列觸發程序，從字串中查閱識別碼](#queue-trigger-look-up-id-from-string-c-script)
* [佇列觸發程序，使用 SqlQuery 取得多個文件](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP 觸發程序，從查詢字串中查閱識別碼](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP 觸發程序，從路由資料中查閱識別碼](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP 觸發程序，使用 SqlQuery 取得多個文件](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP 觸發程序，使用 DocumentClient 取得多個文件](#http-trigger-get-multiple-docs-using-documentclient-c-script)

HTTP 觸發程序範例會參考簡單的 `ToDoItem` 類型：

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>佇列觸發程序，從字串中查閱識別碼

下列範例示範 function.json 檔案中的 Cosmos DB 輸入繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 函式會讀取單一文件，並更新文件的文字值。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
[設定](#configuration)章節會說明這些屬性。

以下是 C# 指令碼程式碼：

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>佇列觸發程序，使用 SqlQuery 取得多個文件

下列範例顯示 *function.json* 檔案中的 Azure Cosmos DB 輸入繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 函式會使用佇列觸發程序來自訂查詢參數，以擷取 SQL 查詢所指定的多份文件。

佇列觸發程序會提供參數 `departmentId`。 `{ "departmentId" : "Finance" }` 的佇列訊息會傳回財務部門的所有記錄。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 C# 指令碼程式碼：

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP 觸發程序，從查詢字串中查閱識別碼

下列範例會顯示擷取單一文件的 [C# 指令碼函式](functions-reference-csharp.md)。 此函式是由 HTTP 要求所觸發，該 HTTP 要求會使用查詢字串指定要查閱的識別碼和分割區索引鍵值。 該識別碼和資料分割索引鍵值會用來 `ToDoItem` 從指定的資料庫和集合中取出檔。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
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
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

以下是 C# 指令碼程式碼：

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 觸發程序，從路由資料中查閱識別碼

下列範例會顯示擷取單一文件的 [C# 指令碼函式](functions-reference-csharp.md)。 此函式是由 HTTP 要求所觸發，該 HTTP 要求會使用路由資料來指定要查閱的識別碼和分割區索引鍵值。 該識別碼和資料分割索引鍵值會用來 `ToDoItem` 從指定的資料庫和集合中取出檔。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

以下是 C# 指令碼程式碼：

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP 觸發程序，使用 SqlQuery 取得多個文件

下列範例會顯示擷取文件清單的 [C# 指令碼函式](functions-reference-csharp.md)。 函式是由 HTTP 要求所觸發。 查詢會在 `SqlQuery` 屬性內容中指定。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
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
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

以下是 C# 指令碼程式碼：

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP 觸發程序，使用 DocumentClient 取得多個文件

下列範例會顯示擷取文件清單的 [C# 指令碼函式](functions-reference-csharp.md)。 函式是由 HTTP 要求所觸發。 程式碼會使用由 Azure Cosmos DB 繫結提供的 `DocumentClient` 執行個體來讀取文件清單。 `DocumentClient` 執行個體也會用來進行寫入作業。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
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
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

以下是 C# 指令碼程式碼：

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

本章節包含下列範例，藉由從各種來源中指定識別碼值，讀取單一文件：

* [佇列觸發程序，從 JSON 中查閱識別碼](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP 觸發程序，從查詢字串中查閱識別碼](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP 觸發程序，從路由資料中查閱識別碼](#http-trigger-look-up-id-from-route-data-javascript)
* [佇列觸發程序，使用 SqlQuery 取得多個文件](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>佇列觸發程序，從 JSON 中查閱識別碼

下列範例示範 function.json 檔案中的 Cosmos DB 輸入繫結，以及使用此繫結的 [JavaScript 指令碼函式](functions-reference-node.md)。 函式會讀取單一文件，並更新文件的文字值。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP 觸發程序，從查詢字串中查閱識別碼

下列範例會顯示擷取單一文件的 [JavaScript 函式](functions-reference-node.md)。 此函式是由 HTTP 要求所觸發，該 HTTP 要求會使用查詢字串指定要查閱的識別碼和分割區索引鍵值。 該識別碼和資料分割索引鍵值會用來 `ToDoItem` 從指定的資料庫和集合中取出檔。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
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
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 觸發程序，從路由資料中查閱識別碼

下列範例會顯示擷取單一文件的 [JavaScript 函式](functions-reference-node.md)。 此函式是由 HTTP 要求所觸發，該 HTTP 要求會使用路由資料來指定要查閱的識別碼和分割區索引鍵值。 該識別碼和資料分割索引鍵值會用來 `ToDoItem` 從指定的資料庫和集合中取出檔。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>佇列觸發程序，使用 SqlQuery 取得多個文件

下列範例顯示 *function.json* 檔案中的 Azure Cosmos DB 輸入繫結，以及使用此繫結的 [JavaScript 函式](functions-reference-node.md)。 函式會使用佇列觸發程序來自訂查詢參數，以擷取 SQL 查詢所指定的多份文件。

佇列觸發程序會提供參數 `departmentId`。 `{ "departmentId" : "Finance" }` 的佇列訊息會傳回財務部門的所有記錄。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

本章節包含下列範例，藉由從各種來源中指定識別碼值，讀取單一文件：

* [佇列觸發程序，從 JSON 中查閱識別碼](#queue-trigger-look-up-id-from-json-python)
* [HTTP 觸發程序，從查詢字串中查閱識別碼](#http-trigger-look-up-id-from-query-string-python)
* [HTTP 觸發程序，從路由資料中查閱識別碼](#http-trigger-look-up-id-from-route-data-python)
* [佇列觸發程序，使用 SqlQuery 取得多個文件](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>佇列觸發程序，從 JSON 中查閱識別碼

下列範例示範 function.json 檔案中的 Cosmos DB 觸發程序繫結，以及使用此繫結的 [Python 函式](functions-reference-python.md)。 函式會讀取單一文件，並更新文件的文字值。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 Python 程式碼：

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP 觸發程序，從查詢字串中查閱識別碼

下列範例示範會擷取單一文件的 [Python 函式](functions-reference-python.md)。 此函式是由 HTTP 要求所觸發，該 HTTP 要求會使用查詢字串指定要查閱的識別碼和分割區索引鍵值。 該識別碼和資料分割索引鍵值會用來 `ToDoItem` 從指定的資料庫和集合中取出檔。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
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
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "scriptFile": "__init__.py"
}
```

以下是 Python 程式碼：

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 觸發程序，從路由資料中查閱識別碼

下列範例示範會擷取單一文件的 [Python 函式](functions-reference-python.md)。 此函式是由 HTTP 要求所觸發，該 HTTP 要求會使用路由資料來指定要查閱的識別碼和分割區索引鍵值。 該識別碼和資料分割索引鍵值會用來 `ToDoItem` 從指定的資料庫和集合中取出檔。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

以下是 Python 程式碼：

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>佇列觸發程序，使用 SqlQuery 取得多個文件

下列範例顯示 *function.json* 檔案中的 Azure Cosmos DB 輸入繫結，以及使用此繫結的 [Python 指令碼函式](functions-reference-python.md)。 函式會使用佇列觸發程序來自訂查詢參數，以擷取 SQL 查詢所指定的多份文件。

佇列觸發程序會提供參數 `departmentId`。 `{ "departmentId" : "Finance" }` 的佇列訊息會傳回財務部門的所有記錄。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 Python 程式碼：

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

本區段包含下列範例：

* [HTTP 觸發程序，從查詢字串中查閱識別碼 - 字串參數](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP 觸發程序，從查詢字串中查閱識別碼 - POJO 參數](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP 觸發程序，從路由資料中查閱識別碼](#http-trigger-look-up-id-from-route-data-java)
* [HTTP 觸發程序，使用 SqlQuery 從路由資料中查閱識別碼](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP 觸發程序，使用 SqlQuery 從路由資料中取得多份文件](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

範例會參考簡單的 `ToDoItem` 類型：

```java
public class ToDoItem {

  private String id;
  private String description;

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

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>HTTP 觸發程序，從查詢字串中查閱識別碼 - 字串參數

下列範例示範會擷取單一文件的 Java 函式。 此函式是由 HTTP 要求所觸發，該 HTTP 要求會使用查詢字串指定要查閱的識別碼和分割區索引鍵值。 該識別碼和資料分割索引鍵值會用來從指定的資料庫和集合中，以字串形式取得檔。

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對其值來自 Cosmos DB 的函式參數使用 `@CosmosDBInput` 註釋。  此註釋可以搭配原生 Java 類型、POJO 或使用 `Optional<T>` 的可為 Null 值使用。

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>HTTP 觸發程序，從查詢字串中查閱識別碼 - POJO 參數

下列範例示範會擷取單一文件的 Java 函式。 此函式是由 HTTP 要求所觸發，該 HTTP 要求會使用查詢字串指定要查閱的識別碼和分割區索引鍵值。 用來從指定的資料庫和集合取出檔的識別碼和分割區索引鍵值。 該文件接著會轉換為先前建立之 ```ToDoItem``` POJO 的執行個體，並當成引數傳遞到函式。

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 觸發程序，從路由資料中查閱識別碼

下列範例示範會擷取單一文件的 Java 函式。 此函式是由 HTTP 要求所觸發，該 HTTP 要求會使用路由參數來指定要查閱的識別碼和分割區索引鍵值。 該識別碼和資料分割索引鍵值會用來從指定的資料庫和集合中取出檔，並將其傳回做為 ```Optional<String>``` 。

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP 觸發程序，使用 SqlQuery 從路由資料中查閱識別碼

下列範例示範會擷取單一文件的 Java 函式。 此函式是由 HTTP 要求所觸發，該 HTTP 要求會使用路由參數來指定要查閱的識別碼。 該識別碼會用來從指定的資料庫和集合中擷取文件，並將結果集轉換為 ```ToDoItem[]```，因為根據查詢準則而定，可能會傳回許多文件。

> [!NOTE]
> 如果您只需要依識別碼進行查詢，建議使用查閱，就像 [先前的範例](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)一樣，因為它會耗用較少的 [要求單位](../cosmos-db/request-units.md)。 點讀取作業 (取得) 比依識別碼查詢 [更有效率](../cosmos-db/optimize-cost-reads-writes.md) 。
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>HTTP 觸發程序，使用 SqlQuery 從路由資料中取得多份文件

下列範例顯示可抓取多份檔的 JAVA 函式。 此函式是由 HTTP 要求所觸發，該 HTTP 要求會使用路由參數 ```desc``` 來指定要在欄位中搜尋的字串 ```description``` 。 搜尋字詞會用來從指定的資料庫和集合中擷取文件集合，並將結果集轉換為 ```ToDoItem[]```，然後將其當成引數傳遞到函式。

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

 ---

## <a name="attributes-and-annotations"></a>屬性和註釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用 [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) 屬性。

屬性的建構函式可接受資料庫名稱和集合名稱。 如需這些設定及其他您可以設定之屬性的相關資訊，請參閱[下列組態區段](#configuration)。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 指令碼不支援屬性。

# <a name="java"></a>[Java](#tab/java)

從 JAVA 函式執行時間連結 [庫](/java/api/overview/azure/functions/runtime)， `@CosmosDBOutput` 針對寫入 Cosmos DB 的參數使用批註。 批註參數類型應該是 `OutputBinding<T>` ，其中 `T` 是原生 JAVA 類型或 POJO。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `CosmosDB` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type**     | n/a | 必須設為 `cosmosDB`。        |
|**direction**     | n/a | 必須設為 `in`。         |
|**name**     | n/a | 代表函式中之文件的繫結參數名稱。  |
|**databaseName** |**DatabaseName** |包含文件的資料庫。        |
|**collectionName** |**CollectionName** | 包含文件的集合名稱。 |
|**id**    | **識別碼** | 要擷取之文件的識別碼。 此屬性支援[繫結運算式](./functions-bindings-expressions-patterns.md)。 請勿同時設定 `id` 和 **sqlQuery** 屬性。 如果您未設定其中一個，就會擷取整個集合。 |
|**sqlQuery**  |**SqlQuery**  | 用來擷取多份文件的 Azure Cosmos DB SQL 查詢。 屬性會支援執行階段繫結，如此範例所示：`SELECT * FROM c where c.departmentId = {departmentId}`。 請勿同時設定 `id` 和 `sqlQuery` 屬性。 如果您未設定其中一個，就會擷取整個集合。|
|**connectionStringSetting**     |**ConnectionStringSetting**|包含 Azure Cosmos DB 連接字串的應用程式設定名稱。 |
|**partitionKey**|**PartitionKey**|指定分割區索引鍵值進行查閱。 可能包含繫結參數。 這是在資料 [分割](../cosmos-db/partitioning-overview.md#logical-partitions) 集合中查閱的必要項。|
|**preferredLocations**| **PreferredLocations**|  (選擇性) 會針對 Azure Cosmos DB 服務中的異地複寫資料庫帳戶，定義 (區域) 的慣用位置。 值應以逗號分隔。 例如「美國東部、美國中南部、北歐」。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

# <a name="c"></a>[C#](#tab/csharp)

當函式成功結束時，會自動儲存透過命名輸入參數對輸入檔所做的任何變更。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

當函式成功結束時，會自動儲存透過命名輸入參數對輸入檔所做的任何變更。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

函數結束時，不會自動進行更新。 請改用 `context.bindings.<documentName>In` 和 `context.bindings.<documentName>Out` 來進行更新。 請參閱 JavaScript 範例。

# <a name="python"></a>[Python](#tab/python)

資料可透過參數提供給函數 `DocumentList` 。 對檔所做的變更不會自動儲存。

# <a name="java"></a>[Java](#tab/java)

從 JAVA 函式執行時間連結 [庫](/java/api/overview/azure/functions/runtime)，批註會將 [@CosmosDBInput](/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) Cosmos DB 資料公開給函數。 此註釋可以搭配原生 Java 類型、POJO 或使用 `Optional<T>` 的可為 Null 值使用。

---

## <a name="next-steps"></a>後續步驟

- [當 Azure Cosmos DB 檔建立或修改 (觸發程式時，執行函式) ](./functions-bindings-cosmosdb-v2-trigger.md)
- [將變更儲存至 Azure Cosmos DB 檔 (輸出系結) ](./functions-bindings-cosmosdb-v2-output.md)