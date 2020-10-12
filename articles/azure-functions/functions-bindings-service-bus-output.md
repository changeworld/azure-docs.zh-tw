---
title: Azure 服務匯流排 Azure Functions 的輸出系結
description: 瞭解如何從 Azure Functions 傳送 Azure 服務匯流排訊息。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 3fc8f205bff52fad6e55b7aa6692ec80ae5e954a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212161"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure 服務匯流排 Azure Functions 的輸出系結

使用 Azure 服務匯流排輸出繫結來傳送佇列或主題訊息。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](functions-bindings-service-bus-output.md)。

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

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

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

下列範例示範如何在 Python 中寫出服務匯流排佇列。

在*類型*設定為的*function.js*中，會定義服務匯流排系結定義 `serviceBus` 。

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

在* _ \_ _ \_ .py*中，您可以將值傳遞給方法，以將訊息寫出至佇列 `set` 。

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

下列範例顯示的 JAVA 函式，會 `myqueue` 在 HTTP 要求觸發時，將訊息傳送至服務匯流排佇列。

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

JAVA 函數也可以寫入至服務匯流排主題。 下列範例會使用 `@ServiceBusTopicOutput` 批註來描述輸出系結的設定。 

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

## <a name="attributes-and-annotations"></a>屬性和註釋

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

您可以設定 `Connection` 屬性，以指定包含要使用之服務匯流排連接字串的應用程式設定名稱，如下列範例所示：

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

如需完整範例，請參閱 [輸出範例](#example)。

您可以使用 `ServiceBusAccount` 屬性來指定要在類別、方法或參數層級使用的服務匯流排帳戶。  如需詳細資訊，請參閱[觸發程序 - 屬性](functions-bindings-service-bus-trigger.md#attributes-and-annotations)。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 指令碼不支援屬性。

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueOutput`和 `ServiceBusTopicOutput` 批註可用於以函式輸出的形式寫入訊息。 使用這些批註裝飾的參數必須宣告為 `OutputBinding<T>` ，其中 `T` 是對應至訊息型別的型別。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `ServiceBus` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 "serviceBus"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設定為 "out"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a | 代表函式程式碼中佇列或主題訊息的變數名稱。 設為 "$return" 以參考函式傳回值。 |
|**queueName**|**QueueName**|佇列的名稱。  只有在傳送佇列訊息時設定 (不適用於主題)。
|**topicName**|**TopicName**|主題的名稱。 只有在傳送主題訊息時設定 (不適用於佇列)。|
|**connection**|**[連接]**|應用程式設定的名稱包含要用於此繫結的服務匯流排連接字串。 如果應用程式設定名稱是以 "AzureWebJobs" 開頭，您只能指定名稱的其餘部分。 例如，如果您將設定 `connection` 為 "MyServiceBus"，則函式執行時間會尋找名為 "AzureWebJobsMyServiceBus" 的應用程式設定。 如果您將 `connection` 保留空白，則 Functions 執行階段會使用應用程式設定中名稱為 "AzureWebJobsServiceBus" 的預設服務匯流排連接字串。<br><br>若要取得連接字串，請遵循[取得管理認證](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)所示的步驟。 連接字串必須是用於服務匯流排命名空間，而不限於特定佇列或主題。|
|僅限**accessRights** (v1) |**存取**|連接字串的存取權限。 可用值為 `manage` 和 `listen`。 預設值是 `manage`，這表示 `connection` 已具備**管理**權限。 如果您使用沒有**管理**權限的連接字串，請將 `accessRights` 設定為 "listen"。 否則，Functions 執行階段在嘗試執行需要管理權限的作業時可能會失敗。 在 Azure Functions 2.x 版和更新版本中，因為最新版的服務匯流排 SDK 不支援管理作業，所以無法使用此屬性。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

在 Azure Functions 1.x 中，執行階段會建立佇列 (如果佇列不存在)，且您已將 `accessRights` 設為 `manage`。 在2.x 版和更高版本的函式中，佇列或主題必須已經存在;如果您指定的佇列或主題不存在，此函式將會失敗。 

# <a name="c"></a>[C#](#tab/csharp)

針對輸出系結使用下列參數類型：

* `out T paramName` - `T` 可以是任何可序列化 JSON 的類型。 當函式結束時，如果參數值為 Null，則 Functions 會使用 Null 物件建立訊息。
* `out string` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `out byte[]` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `out BrokeredMessage` -如果函式結束時，參數值為 null，則函式不會建立函數1.x 的訊息 () 
* `out Message` -如果函式結束時，參數值為 null，則函式不會為函式2.x 和更新版本建立訊息 () 
* `ICollector<T>` 或 `IAsyncCollector<T>` (非同步方法) -用於建立多個訊息。 當您呼叫 `Add` 方法時，就會建立一則訊息。

使用 c # 函數時：

* 非同步函式需要傳回值或 `IAsyncCollector` 不是 `out` 參數。

* 若要存取會話識別碼，請系結至 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) 型別並使用 `sessionId` 屬性。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

針對輸出系結使用下列參數類型：

* `out T paramName` - `T` 可以是任何可序列化 JSON 的類型。 當函式結束時，如果參數值為 Null，則 Functions 會使用 Null 物件建立訊息。
* `out string` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `out byte[]` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `out BrokeredMessage` -如果函式結束時，參數值為 null，則函式不會建立函數1.x 的訊息 () 
* `out Message` -如果函式結束時，參數值為 null，則函式不會為函式2.x 和更新版本建立訊息 () 
* `ICollector<T>` 或 `IAsyncCollector<T>` - 適用於建立多個訊息。 當您呼叫 `Add` 方法時，就會建立一則訊息。

使用 c # 函數時：

* 非同步函式需要傳回值或 `IAsyncCollector` 不是 `out` 參數。

* 若要存取會話識別碼，請系結至 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) 型別並使用 `sessionId` 屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用存取佇列或主題 `context.bindings.<name from function.json>` 。 您可以將字串、位元組陣列或 JavaScript 物件 (還原序列化為的 JSON) `context.binding.<name>` 。

# <a name="python"></a>[Python](#tab/python)

使用 [AZURE 服務匯流排 SDK](../service-bus-messaging/index.yml) ，而不是內建的輸出系結。

# <a name="java"></a>[Java](#tab/java)

使用 [AZURE 服務匯流排 SDK](../service-bus-messaging/index.yml) ，而不是內建的輸出系結。

---

## <a name="exceptions-and-return-codes"></a>例外狀況和傳回碼

| 繫結 | 參考 |
|---|---|
| 服務匯流排 | [服務匯流排錯誤碼](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| 服務匯流排 | [服務匯流排限制](../service-bus-messaging/service-bus-quotas.md) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

本節說明2.x 版和更高版本中此系結的可用通用設定。 下列檔案的範例 host.js只包含此系結的設定。 如需有關全域設定的詳細資訊，請參閱 [ Azure Functions 版本的參考host.js](functions-host-json.md)。

> [!NOTE]
> 有關 Functions 1.x 中 host.json 的參考，請參閱[適用於 Azure Functions 1.x 的 host.json 參考](functions-host-json-v1.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
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

如果您已 `isSessionsEnabled` 將設定為 `true` ，則 `sessionHandlerOptions` 會接受。  如果您已 `isSessionsEnabled` 將設定為 `false` ，則 `messageHandlerOptions` 會接受。

|屬性  |預設 | 描述 |
|---------|---------|---------|
|prefetchCount|0|取得或設定訊息接收者可以同時要求的訊息數目。|
|maxAutoRenewDuration|00:05:00|將自動更新訊息鎖定的最大持續時間。|
|autoComplete|true|觸發程式是否應該在處理之後自動呼叫完成，或是函式程式碼會手動呼叫 complete。<br><br>`false`只有在 c # 中才支援將設定為。<br><br>如果設定為，則觸發程式會在函式 `true` 執行成功完成時自動完成訊息，否則會放棄訊息。<br><br>當設定為時 `false` ，您必須負責呼叫 [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver?view=azure-dotnet) 方法來完成、放棄或 deadletter 訊息。 如果擲回例外狀況 (但未呼叫任何 `MessageReceiver` 方法) ，則會保留鎖定。 鎖定過期後，訊息會以遞增的方式重新排入佇列， `DeliveryCount` 而且會自動更新鎖定。<br><br>在非 C # 函式中，函式中的例外狀況會導致執行時間在背景中呼叫 `abandonAsync` 。 如果未發生任何例外狀況，則 `completeAsync` 會在背景中呼叫。 |
|maxConcurrentCalls|16|對回呼的最大並行呼叫數目，訊息抽取應針對每個縮放的實例起始。 Functions 執行階段預設會並行處理多個訊息。|
|maxConcurrentSessions|2000|每個縮放的實例可以同時處理的最大會話數目。|

## <a name="next-steps"></a>後續步驟

- [ (觸發程式建立服務匯流排佇列或主題訊息時，執行函式) ](./functions-bindings-service-bus-trigger.md)
