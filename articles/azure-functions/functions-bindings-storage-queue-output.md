---
title: Azure 佇列儲存體 Azure Functions 的輸出系結
description: 瞭解如何在 Azure Functions 中建立 Azure 佇列儲存體的訊息。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 1d86009d593ef7e594ec2981132bcfb856569c31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317220"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Azure 佇列儲存體 Azure Functions 的輸出系結

Azure Functions 可以藉由設定輸出系結來建立新的 Azure 佇列儲存體訊息。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](./functions-bindings-storage-queue.md)。

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下列範例所示範的 [C# 函式](functions-dotnet-class-library.md)會為每個收到的 HTTP 要求建立佇列訊息。

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

下列範例示範的是 *function.json* 檔案中的 HTTP 觸發程序繫結，以及使用該繫結的 [C# 指令碼 (.csx)](functions-reference-csharp.md) 程式碼。 此函式會針對每個收到的 HTTP 要求，使用 **CustomQueueMessage** 物件承載來建立佇列項目。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

[設定](#configuration)章節會說明這些屬性。

以下是建立單一佇列訊息的 C# 指令碼程式碼：

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

您可以使用 `ICollector` 或 `IAsyncCollector` 參數一次傳送多個訊息。 以下 C# 指令碼程式碼會傳送多個訊息，一個使用 HTTP 要求資料，一個使用硬式編碼值：

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例示範的是 *function.json* 檔案中的 HTTP 觸發程序繫結，以及使用該繫結的 [JavaScript 函式](functions-reference-node.md)。 此函式會針對每個收到的 HTTP 要求建立佇列項目。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

您可以定義 `myQueueItem` 輸出繫結的訊息陣列，藉此一次傳送多個訊息。 下列 JavaScript 程式碼會對每個接收到的 HTTP 要求，使用硬式編碼值傳送兩個佇列訊息。

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下列範例示範如何將單一和多個值輸出至儲存體佇列。 *function.json*所需的設定與任何一種方式相同。

儲存體佇列系結是在 [*類型*] 設定為的*function.js*中定義 `queue` 。

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
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

若要在佇列上設定個別訊息，您可以將單一值傳遞給 `set` 方法。

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

若要在佇列上建立多個訊息，請將參數宣告為適當的清單類型，並將符合清單) 類型 (的值陣列傳遞給 `set` 方法。

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

 下列範例顯示的 JAVA 函式會建立 HTTP 要求所觸發時的佇列訊息。

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對其值要寫入至佇列儲存體的參數使用 `@QueueOutput` 註釋。  參數類型應該是 `OutputBinding<T>` ，其中 `T` 是 POJO 的任何原生 JAVA 類型。

---

## <a name="attributes-and-annotations"></a>屬性和註釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用 [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs)。

該屬性會套用至 `out` 參數或函式的傳回值。 該屬性的建構函式會採用佇列名稱，如下列範例所示：

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

您可以設定 `Connection` 屬性來指定要使用的儲存體帳戶，如下列範例所示：

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

如需完整範例，請參閱 [輸出範例](#example)。

您可以使用 `StorageAccount` 屬性來指定類別、方法或參數層級的儲存體帳戶。 如需詳細資訊，請參閱「觸發程序 - 屬性」。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 指令碼不支援屬性。

# <a name="java"></a>[Java](#tab/java)

`QueueOutput`批註可讓您將訊息寫入為函式的輸出。 下列範例顯示會建立佇列訊息的 HTTP 觸發函式。

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| 屬性    | 描述 |
|-------------|-----------------------------|
|`name`       | 宣告函數簽章中的參數名稱。 觸發函式時，此參數的值會包含佇列訊息的內容。 |
|`queueName`  | 宣告儲存體帳戶中的佇列名稱。 |
|`connection` | 指向儲存體帳戶連接字串。 |

與批註相關聯的參數 `QueueOutput` 會輸入為[OutputBinding \<T\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java)實例。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `Queue` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `queue`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `out`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a | 代表函式程式碼中佇列的變數名稱。 設為 `$return` 以參考函式傳回值。|
|**queueName** |**QueueName** | 佇列的名稱。 |
|**connection** | **[連接]** |應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將設定 `connection` 為 "MyStorage"，則函式執行時間會尋找名稱為 "MyStorage" 的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

# <a name="c"></a>[C#](#tab/csharp)

使用方法參數（例如）來撰寫單一佇列訊息 `out T paramName` 。 您可以使用方法傳回類型，而不是 `out` 參數，而且 `T` 可以是下列類型之一：

* 可序列化為 JSON 的物件
* `string`
* `byte[]`
* [CloudQueueMessage] 

如果您嘗試繫結至 `CloudQueueMessage`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)。

在 C# 和 C# 指令碼中，藉由使用下列其中一個類型，寫入多個佇列訊息： 

* `ICollector<T>` 或 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

使用方法參數（例如）來撰寫單一佇列訊息 `out T paramName` 。 `paramName`是在function.js的屬性中指定的 `name` 值*function.json*。 您可以使用方法傳回類型，而不是 `out` 參數，而且 `T` 可以是下列類型之一：

* 可序列化為 JSON 的物件
* `string`
* `byte[]`
* [CloudQueueMessage] 

如果您嘗試繫結至 `CloudQueueMessage`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)。

在 C# 和 C# 指令碼中，藉由使用下列其中一個類型，寫入多個佇列訊息： 

* `ICollector<T>` 或 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

輸出佇列專案可透過 `context.bindings.<NAME>` `<NAME>` 符合 *function.js*中定義之名稱的位置使用。 對於佇列項目承載，可使用字串或 JSON 可序列化物件。

# <a name="python"></a>[Python](#tab/python)

有兩個選項可從函式輸出佇列訊息：

- 傳回**值**：將 `name` *function.js*中的屬性設定為 `$return` 。 使用此設定時，函式的傳回值會以佇列儲存體訊息的形式保存。

- **命令式**：傳遞值給宣告為[Out](/python/api/azure-functions/azure.functions.out?view=azure-python)類型之參數的[set](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none)方法。 傳遞給的值 `set` 會保存為佇列儲存訊息。

# <a name="java"></a>[Java](#tab/java)

有兩個選項可使用 [QueueOutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput) 注釋從函式輸出佇列訊息：

- 傳回**值**：藉由將批註套用至函式本身，函式的傳回值會保存為佇列訊息。

- **命令式**：若要明確設定訊息值，請將批註套用至類型的特定參數 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) ，其中 `T` 是 POJO 或任何原生 JAVA 類型。 使用此設定時，將值傳遞給 `setValue` 方法會將值保存為佇列訊息。

---

## <a name="exceptions-and-return-codes"></a>例外狀況和傳回碼

| 繫結 |  參考 |
|---|---|
| 佇列 | [佇列錯誤碼](/rest/api/storageservices/queue-service-error-codes) |
| Bob、資料表、佇列 | [儲存體錯誤碼](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Bob、資料表、佇列 |  [疑難排解](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

本節說明2.x 版和更高版本中此系結的可用通用設定。 下列檔案的範例 host.js只包含此系結的2.x 版 + 設定。 如需2.x 版和更高版本中全域設定設定的詳細資訊，請參閱 [ Azure Functions 的參考host.js](functions-host-json.md)。

> [!NOTE]
> 有關 Functions 1.x 中 host.json 的參考，請參閱[適用於 Azure Functions 1.x 的 host.json 參考](functions-host-json-v1.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------|
|maxPollingInterval|00:00:01|佇列輪詢之間的間隔上限。 最小值為00：00： 00.100 (100 ms) ，最高可遞增至 00:01:00 (1 分鐘) 。  在1.x 中，資料類型為毫秒，而在2.x 和更新版本中，則是 TimeSpan。|
|visibilityTimeout|00:00:00|處理訊息失敗時，重試之間的時間間隔。 |
|batchSize|16|Functions 執行階段會同時擷取，並以平行方式處理的佇列訊息數目。 當要處理的數目減少到 `newBatchThreshold` 時，執行階段就會取得另一個批次，並開始處理那些訊息。 因此，每個函式並行處理之訊息的上限為 `batchSize` 加上 `newBatchThreshold`。 這項限制個別套用至每個佇列觸發的函式。 <br><br>如果您需要避免平行執行在單一佇列上收到的訊息，可以將 `batchSize` 設定為 1。 不過，只要您的函式應用程式在單一虛擬機器 (VM) 上執行，這項設定就只會將並行排除。 如果函式應用程式相應放大為多個 VM，則每個 VM 可以執行每個佇列觸發之函式的一個執行個體。<br><br>最大值 `batchSize` 為 32。 |
|maxDequeueCount|5|將訊息移至有害佇列之前，嘗試處理訊息的次數。|
|newBatchThreshold|batchSize/2|每當要同時處理的訊息數目下降至這個數字時，執行階段就會擷取另一個批次。|

## <a name="next-steps"></a>後續步驟

- [以佇列儲存體資料變更的形式執行函數 (觸發程式) ](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
