---
title: Azure Functions 的 Azure 資料表儲存體繫結
description: 了解如何在 Azure Functions 中使用 Azure 資料表繫結。
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 1be6420598e7983ef9014f617da1f87f5550fa6a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705355"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Functions 的 Azure 資料表儲存體繫結

本文說明如何在 Azure Functions 中使用 Azure 資料表儲存體繫結。 Azure Functions 支援 Azure 資料表儲存體的輸入和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>套件 - Functions 1.x

[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 套件 2.x 版中提供資料表儲存體繫結。 套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub 存放庫中。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>封裝-函數2.x 和更新版本

[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet 套件 3.x 版中提供資料表儲存體繫結。 套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub 存放庫中。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>輸入

使用 Azure 資料表儲存體輸入繫結以讀取 Azure 儲存體帳戶中的資料表。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>一個實體

下列範例示範可讀取單一資料表資料列的 [C# 函式](functions-dotnet-class-library.md)。 

列索引鍵值 "{queueTrigger}" 表示資料列索引鍵來自佇列訊息字串。

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

下列範例[ C#顯示的函式會讀取](functions-dotnet-class-library.md)多個資料表資料列，其中 `MyPoco` 類別衍生自 `TableEntity`。

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>CloudTable

[Functions V2 執行階段](functions-versions.md)不支援 `IQueryable`。 替代方式是使用 Azure 儲存體 SDK，藉此利用 `CloudTable` 方法參數來讀取資料表。 以下是查詢 Azure Functions 記錄資料表的函數範例：

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

如需如何使用 CloudTable 的詳細資訊，請參閱[開始使用 Azure 資料表儲存體](../cosmos-db/table-storage-how-to-use-dotnet.md)。

如果您嘗試繫結至 `CloudTable`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

### <a name="one-entity"></a>一個實體

下列範例所示範的是使用繫結之 *function.json* 檔案，以及 [C# 指令碼](functions-reference-csharp.md)程式碼中的資料表輸入繫結。 此函式會使用佇列觸發程序來讀取單一資料表列。 

*function.json* 檔案會指定 `partitionKey` 和 `rowKey`。 `rowKey` 值 "{queueTrigger}" 表示資料列索引鍵來自佇列訊息字串。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[設定](#input---configuration)章節會說明這些屬性。

以下是 C# 指令碼程式碼：

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

下列範例所示範的是使用繫結之 *function.json* 檔案，以及 [C# 指令碼](functions-reference-csharp.md)程式碼中的資料表輸入繫結。 此函式會讀取佇列訊息中指定之分割區索引鍵的實體。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[設定](#input---configuration)章節會說明這些屬性。

C# 指令碼程式碼會新增對「Azure 儲存體 SDK」的參考，讓實體類型可以衍生自 `TableEntity`：

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

在 2.x[版和更高版本](functions-versions.md)的函式執行時間中不支援 `IQueryable`。 替代方式是使用 Azure 儲存體 SDK，藉此利用 `CloudTable` 方法參數來讀取資料表。 以下是查詢 Azure Functions 記錄資料表的函數範例：

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

如需如何使用 CloudTable 的詳細資訊，請參閱[開始使用 Azure 資料表儲存體](../cosmos-db/table-storage-how-to-use-dotnet.md)。

如果您嘗試繫結至 `CloudTable`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。


# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

下列範例所示範的是使用繫結的 *function.json* 檔案和 [JavaScript 指令碼](functions-reference-node.md)程式碼中的資料表輸入繫結。 此函式會使用佇列觸發程序來讀取單一資料表列。 

*function.json* 檔案會指定 `partitionKey` 和 `rowKey`。 `rowKey` 值 "{queueTrigger}" 表示資料列索引鍵來自佇列訊息字串。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[設定](#input---configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

單一資料表資料列 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="javatabjava"></a>[Java](#tab/java)

下列範例顯示由 HTTP 觸發的函式，其會傳回資料表儲存體中指定資料分割內的項目總計數。

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {HttpMethod.GET},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```

---

## <a name="input---attributes-and-annotations"></a>輸入-屬性和注釋

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

 在 [C# 類別庫](functions-dotnet-class-library.md)中，使用下列屬性來設定資料表輸入繫結：

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  屬性的建構函式採用資料表名稱、分割區索引鍵以及資料列索引鍵。 屬性可用於 `out` 參數或函數的傳回值，如下列範例所示：

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  您可以設定 `Connection` 屬性來指定要使用的儲存體帳戶，如下列範例所示：

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  如需完整範例，請參閱＜輸入 - C# 範例＞。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  提供另一種方式來指定要使用的儲存體帳戶。 建構函式採用的是內含儲存體連接字串的應用程式設定名稱。 屬性可以套用在參數、方法或類別層級。 下列範例所示範的是類別層級與方法層級：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

要使用的儲存體帳戶按以下順序決定：

* `Table` 屬性的 `Connection` 內容。
* `StorageAccount` 屬性套用至與 `Table` 屬性相同的參數。
* `StorageAccount` 屬性套用至該函式。
* `StorageAccount` 屬性套用至該類別。
* 函數應用程式 (「AzureWebJobsStorage」應用程式設定) 的預設儲存體帳戶。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

C#腳本不支援屬性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="javatabjava"></a>[Java](#tab/java)

在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對其值來自資料表儲存體的參數使用 `@TableInput` 註釋。  此註釋可以搭配原生 Java 類型、POJO 或使用 `Optional<T>` 的可為 Null 值使用。

---

## <a name="input---configuration"></a>輸入 - 組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `Table` 屬性。

|function.json 屬性 | 屬性內容 |說明|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `table`。 當您在 Azure 入口網站中建立繫結時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `in`。 當您在 Azure 入口網站中建立繫結時，會自動設定此屬性。 |
|**name** | n/a | 代表函式程式碼中的資料表或實體的變數名稱。 | 
|**tableName** | **TableName** | 資料表的名稱。| 
|**partitionKey** | **PartitionKey** |選擇性。 要讀取之資料表實體的分割區索引鍵。 若要了解如何使用該屬性，請參閱[使用方式](#input---usage)一節。| 
|**rowKey** |**RowKey** | 選擇性。 要讀取之資料表實體的資料列索引鍵。 若要了解如何使用該屬性，請參閱[使用方式](#input---usage)一節。| 
|**take** |**Take** | 選擇性。 要在 JavaScript 中讀取的實體數目上限。 若要了解如何使用該屬性，請參閱[使用方式](#input---usage)一節。| 
|**filter** |**Filter** | 選擇性。 用於在 JavaScript 中輸入資料表的 OData 篩選運算式。 若要了解如何使用該屬性，請參閱[使用方式](#input---usage)一節。| 
|**connection** |**[連接]** | 應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為 "MyStorage"，函數執行時間會尋找名為 "MyStorage" 的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>輸入 - 使用方式

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

* **讀取中的一個資料列**

  請設定 `partitionKey` 和 `rowKey`。 使用方法參數 `T <paramName>` 存取資料表資料。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 `T` 通常是實作 `ITableEntity` 的型別或衍生自 `TableEntity`。 此案例中不使用 `filter` 和 `take` 屬性。

* **讀取一或多個資料列**

  使用方法參數 `IQueryable<T> <paramName>` 存取資料表資料。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 `T` 通常是實作 `ITableEntity` 的型別或衍生自 `TableEntity`。 您可以使用 `IQueryable` 方法，以執行任何所需的篩選條件。 此案例中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 屬性。  

  > [!NOTE]
  > [Functions V2 執行階段](functions-versions.md)不支援 `IQueryable`。 替代方式是使用 Azure 儲存體 SDK，藉以[使用 CloudTable paramName 方法參數](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) \(英文\) 來讀取資料表。 如果您嘗試繫結至 `CloudTable`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

* **讀取中的一個資料列**

  請設定 `partitionKey` 和 `rowKey`。 使用方法參數 `T <paramName>` 存取資料表資料。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 `T` 通常是實作 `ITableEntity` 的型別或衍生自 `TableEntity`。 此案例中不使用 `filter` 和 `take` 屬性。

* **讀取一或多個資料列**

  使用方法參數 `IQueryable<T> <paramName>` 存取資料表資料。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 `T` 通常是實作 `ITableEntity` 的型別或衍生自 `TableEntity`。 您可以使用 `IQueryable` 方法，以執行任何所需的篩選條件。 此案例中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 屬性。  

  > [!NOTE]
  > [Functions V2 執行階段](functions-versions.md)不支援 `IQueryable`。 替代方式是使用 Azure 儲存體 SDK，藉以[使用 CloudTable paramName 方法參數](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) \(英文\) 來讀取資料表。 如果您嘗試繫結至 `CloudTable`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

請設定 `filter` 和 `take` 屬性。 請勿設定 `partitionKey` 或 `rowKey`。 使用 `context.bindings.<BINDING_NAME>` 來存取輸入資料表實體 (或多個實體)。 還原序列化的物件具有 `RowKey` 和 `PartitionKey` 屬性。

# <a name="pythontabpython"></a>[Python](#tab/python)

資料表資料會當做 JSON 字串傳遞至函數。 藉由呼叫 `json.loads` （如輸入[範例](#input)所示）來還原序列化訊息。

# <a name="javatabjava"></a>[Java](#tab/java)

[TableInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableinput)屬性可讓您存取觸發函數的資料表資料列。

---

## <a name="output"></a>輸出

使用 Azure 資料表儲存體輸出繫結以寫入 Azure 儲存體帳戶中的資料表。

> [!NOTE]
> 此輸出繫結不支援更新現有的實體。 [從 Azure 儲存體 SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) 使用 `TableOperation.Replace` 作業來更新現有的實體。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

下列範例示範 [C# 函式](functions-dotnet-class-library.md)，該函式會使用 HTTP 觸發程序來寫入單一資料表資料列。 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

下列範例所示範的是使用繫結之 *function.json* 檔案，以及 [C# 指令碼](functions-reference-csharp.md)程式碼中的資料表輸出繫結。 函式會寫入多個資料表實體。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[設定](#output---configuration)章節會說明這些屬性。

以下是 C# 指令碼程式碼：

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

下列範例所示範的是使用繫結之 *function.json* 檔案，以及 [JavaScript 函式](functions-reference-node.md)中的資料表輸出繫結。 函式會寫入多個資料表實體。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[設定](#output---configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

下列範例示範如何使用資料表儲存體輸出系結。 `table` 系結會藉由將值指派給 `name`、`tableName`、`partitionKey`和 `connection`，在*json*中進行設定：

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
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
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

下列函式會為 `rowKey` 值產生唯一的 UUI，並將訊息保存到資料表儲存體中。

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="javatabjava"></a>[Java](#tab/java)

下列範例顯示的 JAVA 函式會使用 HTTP 觸發程式來寫入單一資料表資料列。

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}
    public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

下列範例顯示的 JAVA 函式會使用 HTTP 觸發程式來寫入多個資料表資料列。

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>輸出-屬性和注釋

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用 [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)。

屬性的建構函式採用資料表名稱。 屬性可用於 `out` 參數或函數的傳回值，如下列範例所示：

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

您可以設定 `Connection` 屬性來指定要使用的儲存體帳戶，如下列範例所示：

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

如需完整範例，請參閱[輸出 - C# 範例](#output)。

您可以使用 `StorageAccount` 屬性來指定類別、方法或參數層級的儲存體帳戶。 如需詳細資訊，請參閱[輸入 - 屬性](#input---attributes-and-annotations)。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

C#腳本不支援屬性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="javatabjava"></a>[Java](#tab/java)

在 JAVA 函式執行時間連結[庫](/java/api/overview/azure/functions/runtime)中，在參數上使用[TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/)注釋，將值寫入資料表儲存體。

如需[詳細資訊](#output)，請參閱範例。

---

## <a name="output---configuration"></a>輸出 - 設定

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `Table` 屬性。

|function.json 屬性 | 屬性內容 |說明|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `table`。 當您在 Azure 入口網站中建立繫結時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `out`。 當您在 Azure 入口網站中建立繫結時，會自動設定此屬性。 |
|**name** | n/a | 函式程式碼中所使用的變數名稱，代表資料表或實體。 設為 `$return` 以參考函式傳回值。| 
|**tableName** |**TableName** | 資料表的名稱。| 
|**partitionKey** |**PartitionKey** | 要寫入之資料表實體的分割區索引鍵。 若要了解如何使用該屬性，請參閱[使用方式](#output---usage)一節。| 
|**rowKey** |**RowKey** | 要寫入之資料表實體的資料列索引鍵。 若要了解如何使用該屬性，請參閱[使用方式](#output---usage)一節。| 
|**connection** |**[連接]** | 應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為 "MyStorage"，函數執行時間會尋找名為 "MyStorage" 的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>輸出 - 使用方式

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

使用方法參數 `ICollector<T> paramName` 或 `IAsyncCollector<T> paramName` （其中 `T` 包含 `PartitionKey` 和 `RowKey` 屬性）來存取輸出資料表實體。 這些屬性通常會伴隨著執行 `ITableEntity` 或繼承 `TableEntity`。

或者，您可以使用 `CloudTable` 方法參數，使用 Azure 儲存體 SDK 來寫入資料表。 如果您嘗試繫結至 `CloudTable`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

使用方法參數 `ICollector<T> paramName` 或 `IAsyncCollector<T> paramName` （其中 `T` 包含 `PartitionKey` 和 `RowKey` 屬性）來存取輸出資料表實體。 這些屬性通常會伴隨著執行 `ITableEntity` 或繼承 `TableEntity`。 `paramName` 值是在*函數. json*的 `name` 屬性中指定。

或者，您可以使用 `CloudTable` 方法參數，使用 Azure 儲存體 SDK 來寫入資料表。 如果您嘗試繫結至 `CloudTable`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name>` 存取輸出事件，其中 `<name>` 是在*function*的 `name` 屬性中指定的值。

# <a name="pythontabpython"></a>[Python](#tab/python)

有兩個選項可從函式輸出資料表儲存體資料列訊息：

- 傳回**值**：將*函數. json*中的 `name` 屬性設定為 `$return`。 使用此設定時，函數的傳回值會保存為數據表儲存體資料列。

- **命令式**：將值傳遞給宣告為[Out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python)類型之參數的[set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none)方法。 傳遞至 `set` 的值會保存為事件中樞訊息。

# <a name="javatabjava"></a>[Java](#tab/java)

有兩個選項可使用[TableStorageOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet)注釋，從函式輸出資料表儲存體資料列：

- 傳回**值**：藉由將注釋套用至函數本身，函數的傳回值會保存為數據表儲存體資料列。

- **命令式**：若要明確設定訊息值，請將注釋套用至類型[`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)的特定參數，其中 `T` 包含 `PartitionKey` 和 `RowKey` 屬性。 這些屬性通常會伴隨著執行 `ITableEntity` 或繼承 `TableEntity`。

---

## <a name="exceptions-and-return-codes"></a>例外狀況和傳回碼

| 繫結 | 參考 |
|---|---|
| 表格 | [資料表錯誤碼](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Bob、資料表、佇列 | [儲存體錯誤碼](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Bob、資料表、佇列 | [疑難排解](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
