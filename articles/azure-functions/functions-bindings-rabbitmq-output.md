---
title: Azure Functions 的 RabbitMQ 輸出系結
description: 瞭解如何從 Azure Functions 傳送 RabbitMQ 訊息。
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 8715fd3d71a5f65695b045f8a32a1b88bcfdd308
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672537"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>Azure Functions 總覽的 RabbitMQ 輸出系結

> [!NOTE]
> RabbitMQ 系結只在 **Windows Premium 和專用** 方案上受到完整支援。 目前不支援耗用量和 Linux。

使用 RabbitMQ 輸出系結，將訊息傳送至 RabbitMQ 佇列。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](functions-bindings-rabbitmq-output.md)。

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下列範例顯示 [c #](functions-dotnet-class-library.md) 函式，此函式會在每隔5分鐘由 TimerTrigger 觸發時傳送 RabbitMQ 訊息，並使用方法傳回值作為輸出：

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ("outputQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

下列範例顯示如何使用 >iasynccollector 介面傳送訊息。

```cs
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("destinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    // processing:
    var myProcessedEvent = DoSomething(rabbitMQEvent);
    
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
}
```

下列範例示範如何以 Poco 的形式傳送訊息。

```cs
public class TestClass
{
    public string x { get; set; }
}

[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "TriggerConnectionString")] TestClass rabbitMQEvent,
[RabbitMQ("destinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<TestClass> outputPocObj,
ILogger log)
{
    // send the message
    await outputPocObj.Add(rabbitMQEvent);
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

下列範例顯示在檔案 *function.js* 中的 RabbitMQ 輸出系結，以及使用此系結的 [c # 腳本](functions-reference-csharp.md) 函式。 函數會從 HTTP 觸發程式讀取訊息，並將其輸出至 RabbitMQ 佇列。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

以下是 C# 指令碼程式碼：

```csx
using System;
using Microsoft.Extensions.Logging;

public static void Run(string input, out string outputMessage, ILogger log)
{
    log.LogInformation(input);
    outputMessage = input;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例顯示在檔案 *function.js* 中的 RabbitMQ 輸出系結，以及使用此系結的 [JavaScript 函數](functions-reference-node.md) 。 函數會從 HTTP 觸發程式讀取訊息，並將其輸出至 RabbitMQ 佇列。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下列範例顯示在檔案 *function.js* 中的 RabbitMQ 輸出系結，以及使用此系結的 Python 函數。 函數會從 HTTP 觸發程式讀取訊息，並將其輸出至 RabbitMQ 佇列。

以下是 *function.json* 檔案中的繫結資料：

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
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    msg.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

下列範例顯示的 JAVA 函式，會在 TimerTrigger 每隔5分鐘觸發一次，以將訊息傳送至 RabbitMQ 佇列。

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "hello") OutputBinding<String> output,
final ExecutionContext context) {
    output.setValue("Some string");
}
```

---

## <a name="attributes-and-annotations"></a>屬性和註釋

# <a name="c"></a>[C#](#tab/csharp)

在 [c # 類別庫](functions-dotnet-class-library.md)中，使用 [RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs)。

以下是方法簽章 `RabbitMQAttribute` 中的屬性：

```csharp
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("SourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("DestinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    ...
}
```

如需完整範例，請參閱 c # [範例](#example)。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 指令碼不支援屬性。

# <a name="java"></a>[Java](#tab/java)

`RabbitMQOutput`批註可讓您建立在傳送 RabbitMQ 訊息時執行的函式。 可用的設定選項包括佇列名稱和連接字串名稱。 如需其他參數詳細資料，請流覽 [RabbitMQOutput JAVA 批註](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQOutput.java)。

如需詳細資訊，請參閱輸出系結 [範例](#example) 。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `RabbitMQ` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設定為 "RabbitMQ"。|
|**direction** | n/a | 必須設定為 "out"。 |
|**name** | n/a | 代表函式程式碼中佇列的變數名稱。 |
|**queueName**|**QueueName**| 要傳送訊息的佇列名稱。 |
|**主機 名**|**HostName**|如果使用 ConnectStringSetting) ，則會忽略 ( <br>佇列的主機名稱 (例如： 10.26.45.210) |
|**userName**|**使用者名稱**|如果使用 ConnectionStringSetting) ，則會忽略 ( <br>應用程式設定的名稱，其中包含要存取佇列的使用者名稱。 例如 UserNameSetting： "< UserNameFromSettings >"|
|**password**|**密碼**|如果使用 ConnectionStringSetting) ，則會忽略 ( <br>應用程式設定的名稱，其中包含用來存取佇列的密碼。 例如 UserNameSetting： "< UserNameFromSettings >"|
|**connectionStringSetting**|**ConnectionStringSetting**|包含 RabbitMQ 訊息佇列連接字串之應用程式設定的名稱。 請注意，如果您直接指定連接字串，而不是透過 local.settings.js中的應用程式設定，則觸發程式將無法運作。  (例如： In *function.js*： connectionStringSetting： "rabbitMQConnection" <br> 在 *local.settings.json*： "rabbitMQConnection"： "< ActualConnectionstring >" ) |
|**port**|**通訊埠**|如果使用 ConnectionStringSetting) 取得或設定使用的埠， (會忽略。 預設為 0。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

# <a name="c"></a>[C#](#tab/csharp)

針對輸出系結使用下列參數類型：

* `byte[]` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `string` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `POCO` -如果參數值未格式化為 c # 物件，則會收到錯誤。 如需完整範例，請參閱 c # [範例](#example)。

使用 c # 函數時：

* 非同步函式需要傳回值或 `IAsyncCollector` 不是 `out` 參數。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

針對輸出系結使用下列參數類型：

* `byte[]` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `string` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `POCO` -如果參數值未格式化為 c # 物件，則會收到錯誤。 如需完整範例，請參閱 c # 腳本 [範例](#example)。

使用 c # 腳本函數時：

* 非同步函式需要傳回值或 `IAsyncCollector` 不是 `out` 參數。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

佇列訊息可透過內容系結取得。<NAME> 其中 <NAME> 符合 function.js中定義的名稱。 如果承載是 JSON，此值會還原序列化為物件。

# <a name="python"></a>[Python](#tab/python)

請參閱 Python [範例](#example)。

# <a name="java"></a>[Java](#tab/java)

針對輸出系結使用下列參數類型：

* `byte[]` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `string` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `POJO` -如果參數值未格式化為 JAVA 物件，則會收到錯誤。

---

## <a name="next-steps"></a>後續步驟

- [ (觸發程式建立 RabbitMQ 訊息時執行函數) ](./functions-bindings-rabbitmq-trigger.md)
