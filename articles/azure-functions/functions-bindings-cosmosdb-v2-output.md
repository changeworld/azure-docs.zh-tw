---
title: 函數2.x 和更新版本的 Azure Cosmos DB 輸出系結
description: 瞭解如何在 Azure Functions 中使用 Azure Cosmos DB 的輸出系結。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 6a75b0c5b30f60afe51eebc395d21b7c05e8af7f
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212751"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x-and-higher"></a>Azure Functions 2.x 和更新版本的 Azure Cosmos DB 輸出系結

Azure Cosmos DB 輸出繫結可讓您使用 SQL API，將新的文件寫入 Azure Cosmos DB 資料庫。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](./functions-bindings-cosmosdb-v2.md)。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

本區段包含下列範例：

* [佇列觸發程序，寫入一份文件](#queue-trigger-write-one-doc-c)
* [佇列觸發程序，使用 IAsyncCollector 寫入文件](#queue-trigger-write-docs-using-iasynccollector-c)

範例會參考簡單的 `ToDoItem` 類型：

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

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>佇列觸發程序，寫入一份文件

下列範例顯示 [C# 函式](functions-dotnet-class-library.md)，它使用佇列儲存體之訊息中提供的資料，將文件新增至資料庫。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>佇列觸發程序，使用 IAsyncCollector 寫入文件

下列範例顯示 [C# 函式](functions-dotnet-class-library.md)，它會使用佇列訊息 JSON 中提供的資料，將文件集合新增至資料庫中。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

本區段包含下列範例：

* [佇列觸發程序，寫入一份文件](#queue-trigger-write-one-doc-c-script)
* [佇列觸發程序，使用 IAsyncCollector 寫入文件](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>佇列觸發程序，寫入一份文件

下列範例顯示 *function.json* 檔案中的 Azure Cosmos DB 輸出繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 此函式會使用可接收 JSON 佇列的佇列輸入繫結，其格式如下︰

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

此函式會針對每一筆記錄建立下列格式的 Azure Cosmos DB 文件：

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 C# 指令碼程式碼：

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>佇列觸發程序，使用 IAsyncCollector 寫入文件

如果要建立多個文件，您可以繫結至 `ICollector<T>` 或 `IAsyncCollector<T>`，`T` 表示其中一種支援的類型。

此範例會參考簡單的 `ToDoItem` 類型：

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

以下是 function.json 檔案：

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下是 C# 指令碼程式碼：

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例顯示 function.json** 檔案中的 Azure Cosmos DB 輸出繫結，以及使用此繫結的 [JavaScript 函式](functions-reference-node.md)。 此函式會使用可接收 JSON 佇列的佇列輸入繫結，其格式如下︰

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

此函式會針對每一筆記錄建立下列格式的 Azure Cosmos DB 文件：

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

下列範例示範如何將檔寫入 Azure CosmosDB 資料庫，做為函數的輸出。

系結定義定義于的 *function.js上* ，其 *類型* 設定為 `cosmosDB` 。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

若要寫入資料庫，請將檔物件傳遞至 `set` database 參數的方法。

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [佇列觸發程序，透過傳回值將訊息儲存至資料庫](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP 觸發程序，透過傳回值將一份文件儲存至資料庫](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP 觸發程序，透過 OutputBinding 將一份文件儲存至資料庫](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP 觸發程序，透過 OutputBinding 將多份文件儲存至資料庫](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>佇列觸發程序，透過傳回值將訊息儲存至資料庫

下列範例示範的 Java 函式使用佇列儲存體中的訊息資料，將文件新增至資料庫。

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>HTTP 觸發程序，透過傳回值將一份文件儲存至資料庫

下列範例所示範的 Java 函式，其簽章會加上 ```@CosmosDBOutput``` 的標註並具有 ```String``` 類型的傳回值。 函式所傳回的 JSON 文件將會自動寫入至對應的 CosmosDB 集合。

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>HTTP 觸發程序，透過 OutputBinding 將一份文件儲存至資料庫

下列範例所示範的 Java 函式，會透過 ```OutputBinding<T>``` 輸出參數將一份文件寫入至 CosmosDB。 在此範例中， ```outputItem``` 參數需要以標注 ```@CosmosDBOutput``` ，而不是函數簽章。 使用 ```OutputBinding<T>``` 可讓您的函式利用繫結來將文件寫入至 CosmosDB，同時又能將不同的值傳回給函式呼叫端，例如 JSON 或 XML 文件。

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>HTTP 觸發程序，透過 OutputBinding 將多份文件儲存至資料庫

下列範例所示範的 Java 函式，會透過 ```OutputBinding<T>``` 輸出參數將多份文件寫入至 CosmosDB。 在此範例中， ```outputItem``` 參數是以標注 ```@CosmosDBOutput``` ，而非函式簽章。 輸出參數 ```outputItem``` 有一份 ```ToDoItem``` 物件清單作為其範本參數類型。 使用 ```OutputBinding<T>``` 可讓您的函式利用繫結來將多份文件寫入至 CosmosDB，同時又能將不同的值傳回給函式呼叫端，例如 JSON 或 XML 文件。

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對即將寫入至 Cosmos DB 的參數使用 `@CosmosDBOutput` 註釋。  註釋參數類型應該是 ```OutputBinding<T>```，其中 T 是原生 Java 類型或 POJO。

---

## <a name="attributes-and-annotations"></a>屬性和註釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用 [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/dev/test/WebJobs.Extensions.CosmosDB.Tests) 屬性。

屬性的建構函式可接受資料庫名稱和集合名稱。 如需這些設定及其他您可以設定之屬性的相關資訊，請參閱[輸出 - 組態](#configuration)。 以下是方法簽章中的 `CosmosDB` 屬性範例：

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 指令碼不支援屬性。

# <a name="java"></a>[Java](#tab/java)

`CosmosDBOutput`批註可用來將資料寫入 Cosmos DB。 您可以將批註套用至函式或個別的函數參數。 在函式方法上使用時，函式的傳回值會寫入 Cosmos DB。 如果您使用具有參數的注釋，則參數的類型必須宣告為 `OutputBinding<T>` `T` 原生 JAVA 類型或 POJO 的。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `CosmosDB` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type**     | n/a | 必須設為 `cosmosDB`。        |
|**direction**     | n/a | 必須設為 `out`。         |
|**name**     | n/a | 代表函式中之文件的繫結參數名稱。  |
|**名稱** | **DatabaseName**|包含其中將建立文件之集合的資料庫。     |
|**collectionName** |**CollectionName**  | 包含其中將建立文件之集合的名稱。 |
|**createIfNotExists**  |**CreateIfNotExists**    | 一個布林值，用來指出當集合不存在時，是否要建立集合。 預設是 false**，因為會使用保留的輸送量來建立新集合，可能會涉及成本。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)。  |
|**partitionKey**|**PartitionKey** |當 `CreateIfNotExists` 為 true 時，它會定義所建立集合的分割區索引鍵路徑。|
|**collectionThroughput**|**CollectionThroughput**| 當 `CreateIfNotExists` 為 true 時，它會定義所建立集合的 [輸送量](../cosmos-db/set-throughput.md) 。|
|**connectionStringSetting**    |**ConnectionStringSetting** |包含 Azure Cosmos DB 連接字串的應用程式設定名稱。        |
|**preferredLocations**| **PreferredLocations**|  (選擇性) 會針對 Azure Cosmos DB 服務中異地複寫的資料庫帳戶定義慣用位置 (區域) 。 值應該以逗號分隔。 例如，「美國東部、美國中南部、北歐」。 |
|**useMultipleWriteLocations**| **UseMultipleWriteLocations**|  (選擇性) 當設定為時 `true` `PreferredLocations` ，它可以利用 Azure Cosmos DB 服務中的 [多區域寫入](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) 。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用方式

根據預設，當您在函式中寫入輸出參數時，會在資料庫中建立文件。 這份文件已自動產生 GUID 作為文件識別碼。 您可以藉由在傳遞至輸出參數的 JSON 物件中指定 `id` 屬性，來指定輸出文件的文件識別碼。

> [!Note]
> 當您指定現有文件的識別碼時，新的輸出文件會覆寫現有文件。

## <a name="exceptions-and-return-codes"></a>例外狀況和傳回碼

| 繫結 | 參考 |
|---|---|
| CosmosDB | [CosmosDB 錯誤碼](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.json 設定

蔖節說明 2.x 版中適用於此繫結的全域組態設定。 如需有關 2.x 版中全域組態設定的詳細資訊，請參閱[適用於 Azure Functions 2.x 版的 host.json 參考](functions-host-json.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------|
|GatewayMode|閘道|連線到 Azure Cosmos DB 服務時函式所使用的連線模式。 選項為 `Direct` 和 `Gateway`|
|通訊協定|Https|連線到 Azure Cosmos DB 服務時函式所使用的連線通訊協定。  請參閱[此處以了解這兩種模式](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n/a|要在應用程式的所有函式上使用的租用前置詞。|

## <a name="next-steps"></a>後續步驟

- [ (觸發程式建立或修改 Azure Cosmos DB 檔時執行函式) ](./functions-bindings-cosmosdb-v2-trigger.md)
- [閱讀 Azure Cosmos DB 檔 (輸入系結) ](./functions-bindings-cosmosdb-v2-input.md)
