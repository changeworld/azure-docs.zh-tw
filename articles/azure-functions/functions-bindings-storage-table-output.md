---
title: 適用于 Azure Functions 的 Azure 資料表儲存體輸出系結
description: 瞭解如何在 Azure Functions 中使用 Azure 資料表儲存體輸出系結。
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: ec857db64529a27db7412c61f8f09c66f8a76363
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92098215"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>適用于 Azure Functions 的 Azure 資料表儲存體輸出系結

使用 Azure 資料表儲存體輸出繫結以寫入 Azure 儲存體帳戶中的資料表。

> [!NOTE]
> 此輸出繫結不支援更新現有的實體。 [從 Azure 儲存體 SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) 使用 `TableOperation.Replace` 作業來更新現有的實體。

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

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

[設定](#configuration)章節會說明這些屬性。

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

[設定](#configuration)章節會說明這些屬性。

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

# <a name="python"></a>[Python](#tab/python)

下列範例示範如何使用資料表儲存體輸出系結。 系結 `table` 是在 *function.js* 中設定，方法是將值指派給 `name` 、 `tableName` 、 `partitionKey` 和 `connection` ：

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

下列函式會產生值的唯一 UUI `rowKey` ，並將訊息保存到資料表儲存體中。

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

# <a name="java"></a>[Java](#tab/java)

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

## <a name="attributes-and-annotations"></a>屬性和註釋

# <a name="c"></a>[C#](#tab/csharp)

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

如需完整範例，請參閱 [c # 範例](#example)。

您可以使用 `StorageAccount` 屬性來指定類別、方法或參數層級的儲存體帳戶。 如需詳細資訊，請參閱[輸入 - 屬性](./functions-bindings-storage-table-input.md#attributes-and-annotations)。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 指令碼不支援屬性。

# <a name="java"></a>[Java](#tab/java)

在 JAVA 函式執行時間連結 [庫](/java/api/overview/azure/functions/runtime)中，使用參數上的 [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) 注釋，將值寫入資料表儲存體中。

如需 [詳細資訊](#example)，請參閱範例。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `Table` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `table`。 當您在 Azure 入口網站中建立繫結時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `out`。 當您在 Azure 入口網站中建立繫結時，會自動設定此屬性。 |
|**name** | n/a | 函式程式碼中所使用的變數名稱，代表資料表或實體。 設為 `$return` 以參考函式傳回值。| 
|**名** |**TableName** | 資料表的名稱。| 
|**partitionKey** |**PartitionKey** | 要寫入之資料表實體的分割區索引鍵。 如需如何使用此屬性的指引，請參閱 [使用方式一節](#usage) 。| 
|**rowKey** |**RowKey** | 要寫入之資料表實體的資料列索引鍵。 如需如何使用此屬性的指引，請參閱 [使用方式一節](#usage) 。| 
|**connection** |**[連接]** | 應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將設定 `connection` 為 "MyStorage"，則函式執行時間會尋找名為 "MyStorage" 的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

# <a name="c"></a>[C#](#tab/csharp)

使用方法參數 `ICollector<T> paramName` 或 `IAsyncCollector<T> paramName` `T` 包含 `PartitionKey` 和 `RowKey` 屬性的，存取輸出資料表實體。 這些屬性通常會伴隨執行 `ITableEntity` 或繼承 `TableEntity` 。

或者，您也可以使用 `CloudTable` 方法參數，利用 AZURE 儲存體 SDK 來寫入資料表。 如果您嘗試繫結至 `CloudTable`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

使用方法參數 `ICollector<T> paramName` 或 `IAsyncCollector<T> paramName` `T` 包含 `PartitionKey` 和 `RowKey` 屬性的，存取輸出資料表實體。 這些屬性通常會伴隨執行 `ITableEntity` 或繼承 `TableEntity` 。 `paramName`值是在 `name` *function.js*的屬性中指定。

或者，您也可以使用 `CloudTable` 方法參數，利用 AZURE 儲存體 SDK 來寫入資料表。 如果您嘗試繫結至 `CloudTable`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name>` 存取輸出事件，其中 `<name>` 是 *function.json* 的 `name` 屬性中指定的值。

# <a name="python"></a>[Python](#tab/python)

有兩個選項可從函式輸出資料表儲存體資料列訊息：

- **傳回值**：將 function.json 中的 `name` 屬性設定為 `$return`。 使用此設定時，函數的傳回值會保存為數據表儲存體資料列。

- **命令式**：將值傳遞至宣告為 [Out](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true) 類型之參數的 [set](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true#set-val--t-----none) 方法。 傳遞至 `set` 的值會保存為事件中樞訊息。

# <a name="java"></a>[Java](#tab/java)

有兩個選項可使用 [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet&preserve-view=true) 注釋，從函式輸出資料表儲存體資料列：

- 傳回**值**：藉由將批註套用至函式本身，函式的傳回值會保存為數據表儲存體資料列。

- **命令式**：若要明確設定訊息值，請將批註套用至類型的特定參數 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) ，其中 `T` 包含 `PartitionKey` 和 `RowKey` 屬性。 這些屬性通常會伴隨執行 `ITableEntity` 或繼承 `TableEntity` 。

---

## <a name="exceptions-and-return-codes"></a>例外狀況和傳回碼

| 繫結 | 參考 |
|---|---|
| Table | [資料表錯誤碼](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Bob、資料表、佇列 | [儲存體錯誤碼](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Bob、資料表、佇列 | [疑難排解](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
