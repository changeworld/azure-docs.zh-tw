---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 11ad3bdcaa40c479c9358fd623edf0e6fdafa0d6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002065"
---
使用事件中樞輸出繫結將事件寫入事件串流。 您必須具備事件中樞的傳送權限，才能將事件寫入其中。

請先確定必要的套件參考已就緒，再嘗試實作輸出繫結。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

下列範例顯示將訊息寫入事件中樞，並使用方法傳回值作為輸出的 [C# 函式](../articles/azure-functions/functions-dotnet-class-library.md)：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

下列程式碼示範如何使用 `IAsyncCollector` 介面來傳送一批訊息。 當您處理來自某個事件中樞的訊息，並將結果傳送至另一個事件中樞時，此案例很常見。

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

下列範例示範 function.json 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [C# 指令碼函式](../articles/azure-functions/functions-reference-csharp.md)。 此函式會將訊息寫入事件中樞。

下列範例顯示 *function.json* 檔案中的事件中樞繫結資料。 第一個範例是針對 Functions 2.x 和更高版本，而第二個範例則是針對 Functions 1.x。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

以下是可建立一則訊息的 C# 指令碼程式碼：

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

以下是可建立多則訊息的 C# 指令碼程式碼：

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例示範 function.json 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [JavaScript 函式](../articles/azure-functions/functions-reference-node.md)。 此函式會將訊息寫入事件中樞。

下列範例顯示 *function.json* 檔案中的事件中樞繫結資料。 第一個範例是針對 Functions 2.x 和更高版本，而第二個範例則是針對 Functions 1.x。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

以下是可傳送單一訊息的 JavaScript 程式碼：

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

以下是可傳送多則訊息的 JavaScript 程式碼：

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下列範例示範 function.json 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [Python 函式](../articles/azure-functions/functions-reference-python.md)。 此函式會將訊息寫入事件中樞。

下列範例顯示 *function.json* 檔案中的事件中樞繫結資料。

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

以下是可傳送單一訊息的 Python 程式碼：

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

下列範例會示範 Java 函式如何將包含目前時間的訊息寫入事件中樞。

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對其值要發佈至事件中樞的參數使用 `@EventHubOutput` 註釋。  參數的類型應為 `OutputBinding<T>`，其中 T 是 POJO 或任何原生 Java 類型。

---

## <a name="attributes-and-annotations"></a>屬性和註釋

# <a name="c"></a>[C#](#tab/csharp)

對於 [C# 類別庫](../articles/azure-functions/functions-dotnet-class-library.md)，請使用 [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) 屬性。

此屬性的建構函式接受事件中樞的名稱，以及包含連接字串的應用程式設定名稱。 如需這些設定的詳細資訊，請參閱[輸入 - 組態](#configuration)一節。 以下是 `EventHub` 屬性範例：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

如需完整範例，請參閱[輸出 - C# 範例](#example)。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 指令碼不支援屬性。

# <a name="java"></a>[Java](#tab/java)

在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對其值要發佈至事件中樞的參數使用 [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) 註釋。 參數的類型應為 `OutputBinding<T>`，其中 `T` 是 POJO 或任何原生 Java 類型。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `EventHub` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設定為 "eventHub"。 |
|**direction** | n/a | 必須設定為 "out"。 當您在 Azure 入口網站中建立繫結時，會自動設定此參數。 |
|**name** | n/a | 函式程式碼中所使用的變數名稱，代表事件。 |
|**path** |**EventHubName** | 僅限 Functions 1.x。 事件中樞的名稱。 當事件中樞名稱也呈現於連接字串時，該值會在執行階段覆寫這個屬性。 |
|**eventHubName** |**EventHubName** | Functions 2.x 和更新版本。 事件中樞的名稱。 當事件中樞名稱也呈現於連接字串時，該值會在執行階段覆寫這個屬性。 |
|**connection** |**[連接]** | 應用程式設定的名稱，其中包含事件中樞命名空間的連接字串。 按一下 *命名空間* 的 [連接資訊] 按鈕 (而不是事件中樞本身)，來複製此連接字串。 此連接字串必須具有傳送權限，才能將訊息傳送至事件資料流。|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

# <a name="c"></a>[C#](#tab/csharp)

使用方法參數 (例如 `out string paramName`) 來傳送訊息。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 若要寫入多則訊息，您可以使用 `ICollector<string>` 或 `IAsyncCollector<string>` 取代 `out string`。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

使用方法參數 (例如 `out string paramName`) 來傳送訊息。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 若要寫入多則訊息，您可以使用 `ICollector<string>` 或 `IAsyncCollector<string>` 取代 `out string`。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name>` 存取輸出事件，其中 `<name>` 是 *function.json* 的 `name` 屬性中指定的值。

# <a name="python"></a>[Python](#tab/python)

有兩個選項可從函式輸出事件中樞訊息：

- **傳回值**：將 function.json 中的 `name` 屬性設定為 `$return`。 使用此組態，函式的傳回值會保存為事件中樞訊息。

- **命令式**：將值傳遞至宣告為 [Out](/python/api/azure-functions/azure.functions.out?view=azure-python) 類型之參數的 [set](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) 方法。 傳遞至 `set` 的值會保存為事件中樞訊息。

# <a name="java"></a>[Java](#tab/java)

有兩個選項可使用 [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) 注釋，從函式輸出事件中樞訊息：

- **傳回值**：將注釋套用至函式本身，函式的傳回值就會保存為事件中樞訊息。

- **命令式**：若要明確設定訊息值，請將注釋套用至 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.OutputBinding) 類型的特定參數，其中 `T` 是 POJO 或任何原生 Java 類型。 使用此組態，將值傳遞至 `setValue` 方法，就會將此值保存為事件中樞訊息。

---

## <a name="exceptions-and-return-codes"></a>例外狀況和傳回碼

| 繫結 | 參考 |
|---|---|
| 事件中樞 | [操作指南](/rest/api/eventhub/publisher-policy-operations) \(英文\) |