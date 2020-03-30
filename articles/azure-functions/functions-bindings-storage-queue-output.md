---
title: Azure 函數的 Azure 佇列存儲輸出綁定
description: 瞭解如何在 Azure 函數中創建 Azure 佇列存儲消息。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 76af5f398edd736874fa79095f2e80c02298eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277332"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Azure 函數的 Azure 佇列存儲輸出綁定

Azure 函數可以通過設置輸出綁定創建新的 Azure 佇列存儲消息。

有關設置和配置詳細資訊的資訊，請參閱[概述](./functions-bindings-storage-queue.md)。

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

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

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

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

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
      "name": "$return",
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
    context.done(null, input.body);
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

下面的示例演示如何將單個值和多個值輸出到存儲佇列。 *函數.json*所需的配置無論哪種方式都是相同的。

存儲佇列綁定在*函數.json*中定義，其中*類型*設置為`queue`。

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

要在佇列中設置單個消息，可以將單個值傳遞給`set`方法。

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

要在佇列中創建多條消息，請將參數聲明為適當的清單類型，並將值陣列（與清單類型匹配）傳遞給`set`方法。

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[JAVA](#tab/java)

 下面的示例顯示了一個 JAVA 函數，該函數在由 HTTP 要求觸發時為其創建佇列消息。

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

在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對其值要寫入至佇列儲存體的參數使用 `@QueueOutput` 註釋。  參數類型應為`OutputBinding<T>`，其中`T`是 POJO 的任何本機 JAVA 類型。

---

## <a name="attributes-and-annotations"></a>屬性和注釋

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

有關完整示例，請參閱[輸出示例](#example)。

您可以使用 `StorageAccount` 屬性來指定類別、方法或參數層級的儲存體帳戶。 如需詳細資訊，請參閱「觸發程序 - 屬性」。

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

C# 腳本不支援屬性。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="java"></a>[JAVA](#tab/java)

注釋`QueueOutput`允許您將消息寫入函數的輸出。 下面的示例顯示了創建佇列消息的 HTTP 觸發函數。

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
|`name`       | 在函數簽名中聲明參數名稱。 觸發函數時，此參數的值具有佇列消息的內容。 |
|`queueName`  | 聲明存儲帳戶中的佇列名稱。 |
|`connection` | 指向存儲帳戶連接字串。 |

與`QueueOutput`注釋關聯的參數類型為[輸出綁定\<T\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java)實例。

---

## <a name="configuration"></a>組態

下表介紹了您在*函數.json*檔和`Queue`屬性中設置的綁定配置屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**型別** | n/a | 必須設為 `queue`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `out`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**名稱** | n/a | 代表函式程式碼中佇列的變數名稱。 設為 `$return` 以參考函式傳回值。|
|**佇列名稱** |**佇列名稱** | 佇列的名稱。 |
|**連接** | **連接** |應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果設置為`connection`"我的存儲"，函數運行時將查找名為"我的存儲"的應用設置。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

# <a name="c"></a>[C#](#tab/csharp)

使用 方法參數（如`out T paramName`）編寫單個佇列消息。 您可以使用方法傳回類型，而不是 `out` 參數，而且 `T` 可以是下列類型之一：

* 可序列化為 JSON 的物件
* `string`
* `byte[]`
* [CloudQueueMessage] 

如果您嘗試繫結至 `CloudQueueMessage`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)。

在 C# 和 C# 指令碼中，藉由使用下列其中一個類型，寫入多個佇列訊息： 

* `ICollector<T>` 或 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

使用 方法參數（如`out T paramName`）編寫單個佇列消息。 是`paramName`函數 的屬性中`name`指定的值 *。* 您可以使用方法傳回類型，而不是 `out` 參數，而且 `T` 可以是下列類型之一：

* 可序列化為 JSON 的物件
* `string`
* `byte[]`
* [CloudQueueMessage] 

如果您嘗試繫結至 `CloudQueueMessage`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)。

在 C# 和 C# 指令碼中，藉由使用下列其中一個類型，寫入多個佇列訊息： 

* `ICollector<T>` 或 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

輸出佇列項可通過`context.bindings.<NAME>`與`<NAME>`*函數.json*中定義的名稱匹配的位置可用。 對於佇列項目承載，可使用字串或 JSON 可序列化物件。

# <a name="python"></a>[Python](#tab/python)

從函數中提供事件中心消息有兩個選項：

- **傳回值**：將`name`*函數.json*中的`$return`屬性設置為 。 使用此配置，函數的傳回值將保留為佇列存儲消息。

- **命令 ：** 將值傳遞給聲明為[Out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python)類型的參數的[集](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none)方法。 傳遞給`set`的值將保留為佇列存儲消息。

# <a name="java"></a>[JAVA](#tab/java)

使用[佇列輸出](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput)注釋從函數中輸出事件中心消息有兩個選項：

- **傳回值**：通過將注釋應用於函數本身，函數的傳回值將保留為事件中心消息。

- **命令：** 要顯式設置消息值，請將注釋應用於類型[`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)的特定參數，其中`T`POJO 或任何本機 JAVA 類型。 使用此配置，將值傳遞給`setValue`該方法會將該值保留為事件中心消息。

---

## <a name="exceptions-and-return-codes"></a>例外狀況和傳回碼

| 繫結 |  參考資料 |
|---|---|
| 佇列 | [佇列錯誤碼](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Bob、資料表、佇列 | [儲存體錯誤碼](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Bob、資料表、佇列 |  [疑難排解](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

本節介紹 2.x 和更高版本中可用於此綁定的全域配置設置。 下面的示例 host.json 檔僅包含此綁定的版本 2.x+ 設置。 有關版本 2.x 及以後的全域配置設置的詳細資訊，請參閱[Azure 函數的 host.json 引用](functions-host-json.md)。

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
|maxPollingInterval|00:00:01|佇列輪詢之間的間隔上限。 最小為 00：00：00.100（100 毫秒），增量為 00：01：00（1 分鐘）。  在 1.x 中，資料類型為毫秒，在 2.x 和更高版本中，資料類型為 TimeSpan。|
|visibilityTimeout|00:00:00|處理訊息失敗時，重試之間的時間間隔。 |
|batchSize|16|Functions 執行階段會同時擷取，並以平行方式處理的佇列訊息數目。 當要處理的數目減少到 `newBatchThreshold` 時，執行階段就會取得另一個批次，並開始處理那些訊息。 因此，每個函式並行處理之訊息的上限為 `batchSize` 加上 `newBatchThreshold`。 這項限制個別套用至每個佇列觸發的函式。 <br><br>如果您需要避免平行執行在單一佇列上收到的訊息，可以將 `batchSize` 設定為 1。 不過，只要您的函式應用程式在單一虛擬機器 (VM) 上執行，這項設定就只會將並行排除。 如果函式應用程式相應放大為多個 VM，則每個 VM 可以執行每個佇列觸發之函式的一個執行個體。<br><br>最大值 `batchSize` 為 32。 |
|maxDequeueCount|5|將訊息移至有害佇列之前，嘗試處理訊息的次數。|
|newBatchThreshold|batchSize/2|每當要同時處理的訊息數目下降至這個數字時，執行階段就會擷取另一個批次。|

## <a name="next-steps"></a>後續步驟

- [在佇列存儲資料更改時運行函數（觸發器）](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
