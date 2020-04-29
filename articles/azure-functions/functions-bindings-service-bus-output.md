---
title: Azure Functions 的 Azure 服務匯流排繫結
description: 瞭解如何從 Azure Functions 傳送 Azure 服務匯流排訊息。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 02d9ce87d45c5f1c9a123aae18f7d710b268f03e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80582245"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Functions 的 Azure 服務匯流排輸出系結

使用 Azure 服務匯流排輸出繫結來傳送佇列或主題訊息。

如需安裝和設定詳細資料的相關資訊，請參閱[總覽](functions-bindings-service-bus-output.md)。

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下列範例示範的 [C# 函式](functions-dotnet-class-library.md)可記錄服務匯流排佇列訊息：

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C # 腳本](#tab/csharp-script)

下列範例示範 function.json** 檔案中的服務匯流排輸出繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 此函式會使用計時器觸發程序，每隔 15 秒傳送一則佇列訊息。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

以下是可建立單一訊息的 C# 指令碼程式碼：

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

以下是可建立多則訊息的 C# 指令碼程式碼：

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例示範 function.json** 檔案中的服務匯流排觸發程序繫結，以及使用此繫結的 [JavaScript 函式](functions-reference-node.md)。 此函式會使用計時器觸發程序，每隔 15 秒傳送一則佇列訊息。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

以下是可建立單一訊息的 JavaScript 指令碼程式碼：

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

以下是可建立多則訊息的 JavaScript 指令碼程式碼：

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下列範例示範如何在 Python 中寫出服務匯流排的佇列。

服務匯流排系結定義定義于函式 *. json*中，其中*類型*設定`serviceBus`為。

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
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

在 .py 中`set` * _ \_ _，您可以藉由將值傳遞給方法，將訊息寫出至\_* 佇列。

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

下列範例顯示的 JAVA 函式會在 HTTP 要求觸發時，將`myqueue`訊息傳送至服務匯流排的佇列。

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對其值要寫入至服務匯流排佇列的函式參數使用 `@QueueOutput` 註釋。  參數類型應為 `OutputBinding<T>`，其中 T 是任何原生 Java 類型的 POJO。

JAVA 函數也可以寫入服務匯流排主題。 下列範例會使用`@ServiceBusTopicOutput`批註來描述輸出系結的設定。 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

---

## <a name="attributes-and-annotations"></a>屬性和注釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別程式庫](functions-dotnet-class-library.md)中，使用 [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs)。

該屬性的建構函式會採用佇列名稱或標題和訂用帳戶。 您也可以指定連線的存取權限。 [輸出 - 組態](#configuration)一節說明如何選擇存取權限設定。 以下範例顯示套用至函式傳回值的屬性：

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

您可以設定`Connection`屬性來指定應用程式設定的名稱，其中包含要使用的服務匯流排連接字串，如下列範例所示：

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

如需完整範例，請參閱[輸出-範例](#example)。

您可以使用 `ServiceBusAccount` 屬性來指定要在類別、方法或參數層級使用的服務匯流排帳戶。  如需詳細資訊，請參閱[觸發程序 - 屬性](functions-bindings-service-bus-trigger.md#attributes-and-annotations)。

# <a name="c-script"></a>[C # 腳本](#tab/csharp-script)

C # 腳本不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueOutput`和`ServiceBusTopicOutput`批註可用來將訊息寫入為函式輸出。 以這些注釋裝飾的參數必須宣告為`OutputBinding<T>` ，其中`T`是對應至訊息類型的類型。

---

## <a name="configuration"></a>設定

下表說明您在*函數 json*檔案和`ServiceBus`屬性中設定的系結設定屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 "serviceBus"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**方向** | n/a | 必須設定為 "out"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a | 代表函式程式碼中佇列或主題訊息的變數名稱。 設為 "$return" 以參考函式傳回值。 |
|**queueName**|**QueueName**|佇列的名稱。  只有在傳送佇列訊息時設定 (不適用於主題)。
|**topicName**|**TopicName**|主題的名稱。 只有在傳送主題訊息時設定 (不適用於佇列)。|
|**connection**|**建立**|應用程式設定的名稱包含要用於此繫結的服務匯流排連接字串。 如果應用程式設定名稱是以 "AzureWebJobs" 開頭，您只能指定名稱的其餘部分。 例如，如果您將設定`connection`為 "MyServiceBus"，函數執行時間會尋找名為 "AzureWebJobsMyServiceBus" 的應用程式設定。 如果您將 `connection` 保留空白，則 Functions 執行階段會使用應用程式設定中名稱為 "AzureWebJobsServiceBus" 的預設服務匯流排連接字串。<br><br>若要取得連接字串，請遵循[取得管理認證](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)所示的步驟。 連接字串必須是用於服務匯流排命名空間，而不限於特定佇列或主題。|
|**accessRights**|**存取**|連接字串的存取權限。 可用值為 `manage` 和 `listen`。 預設值是 `manage`，這表示 `connection` 已具備**管理**權限。 如果您使用沒有**管理**權限的連接字串，請將 `accessRights` 設定為 "listen"。 否則，Functions 執行階段在嘗試執行需要管理權限的作業時可能會失敗。 在 Azure Functions 2.x 版和更新版本中，因為最新版的服務匯流排 SDK 不支援管理作業，所以無法使用這個屬性。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用方式

在 Azure Functions 1.x 中，執行階段會建立佇列 (如果佇列不存在)，且您已將 `accessRights` 設為 `manage`。 在函數2.x 版和更新版本中，佇列或主題必須已經存在;如果您指定的佇列或主題不存在，此函式將會失敗。 

# <a name="c"></a>[C#](#tab/csharp)

針對輸出系結使用下列參數類型：

* `out T paramName` - `T` 可以是任何可序列化 JSON 的類型。 當函式結束時，如果參數值為 Null，則 Functions 會使用 Null 物件建立訊息。
* `out string` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `out byte[]` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `out BrokeredMessage`-如果函式結束時，參數值為 null，函數不會建立訊息（針對函式1.x）
* `out Message`-如果參數值在函式結束時為 null，函式不會建立訊息（for 函數2.x 和更新版本）
* `ICollector<T>` 或 `IAsyncCollector<T>` - 適用於建立多個訊息。 當您呼叫 `Add` 方法時，就會建立一則訊息。

使用 c # 函數時：

* 非同步函數需要傳回值或`IAsyncCollector` ，而不是`out`參數。

* 若要存取會話識別碼，請系結[`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message)至類型，並`sessionId`使用屬性。

# <a name="c-script"></a>[C # 腳本](#tab/csharp-script)

針對輸出系結使用下列參數類型：

* `out T paramName` - `T` 可以是任何可序列化 JSON 的類型。 當函式結束時，如果參數值為 Null，則 Functions 會使用 Null 物件建立訊息。
* `out string` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `out byte[]` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `out BrokeredMessage`-如果函式結束時，參數值為 null，函數不會建立訊息（針對函式1.x）
* `out Message`-如果參數值在函式結束時為 null，函式不會建立訊息（for 函數2.x 和更新版本）
* `ICollector<T>` 或 `IAsyncCollector<T>` - 適用於建立多個訊息。 當您呼叫 `Add` 方法時，就會建立一則訊息。

使用 c # 函數時：

* 非同步函數需要傳回值或`IAsyncCollector` ，而不是`out`參數。

* 若要存取會話識別碼，請系結[`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message)至類型，並`sessionId`使用屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用`context.bindings.<name from function.json>`來存取佇列或主題。 您可以將字串、位元組陣列或 JavaScript 物件（還原序列化為 JSON）指派給`context.binding.<name>`。

# <a name="python"></a>[Python](#tab/python)

請使用[AZURE 服務匯流排 SDK](https://docs.microsoft.com/azure/service-bus-messaging) ，而不是內建的輸出系結。

# <a name="java"></a>[Java](#tab/java)

請使用[AZURE 服務匯流排 SDK](https://docs.microsoft.com/azure/service-bus-messaging) ，而不是內建的輸出系結。

---

## <a name="exceptions-and-return-codes"></a>例外狀況和傳回碼

| 繫結 | 參考資料 |
|---|---|
| 服務匯流排 | [服務匯流排錯誤碼](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| 服務匯流排 | [服務匯流排限制](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

本節說明2.x 版和更高版本中可供此系結使用的全域設定。 下面的範例 host. json 檔案僅包含此系結的設定。 如需有關全域設定的詳細資訊，請參閱[Azure Functions 版本的 host. json 參考](functions-host-json.md)。

> [!NOTE]
> 有關 Functions 1.x 中 host.json 的參考，請參閱[適用於 Azure Functions 1.x 的 host.json 參考](functions-host-json-v1.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------|
|prefetchCount|0|取得或設定訊息接收者可以同時要求的訊息數目。|
|maxAutoRenewDuration|00:05:00|將自動更新訊息鎖定的最大持續時間。|
|autoComplete|true|觸發程式是否應該立即將訊息標示為完成（自動完成），或等待函式順利結束以呼叫 complete。|
|maxConcurrentCalls|16|訊息幫浦應該起始之回呼的並行呼叫數上限。 Functions 執行階段預設會並行處理多個訊息。 若要指示執行階段一次只處理一個佇列或主題訊息，請將 `maxConcurrentCalls` 設定為 1。 |

## <a name="next-steps"></a>後續步驟

- [建立服務匯流排佇列或主題訊息時執行函數（觸發程式）](./functions-bindings-service-bus-trigger.md)
