---
title: 適用於 Azure Functions 的 Azure 事件方格輸出繫結
description: 了解如何在 Azure Functions 中傳送事件方格事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit, tracking-python
ms.openlocfilehash: 2eaa2202ac6c2f0fac0f53c6eeb2f5d08c764f1e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85413343"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>適用於 Azure Functions 的 Azure 事件方格輸出繫結

使用事件方格輸出繫結，以將事件寫入至自訂事件。 您必須具有[適用於自訂主題的有效存取金鑰](../event-grid/security-authenticate-publishing-clients.md)。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](./functions-bindings-event-grid.md)。

> [!NOTE]
> 事件方格輸出繫結不支援共用存取簽章 (SAS 權杖)。 您必須使用主題的存取金鑰。

> [!IMPORTANT]
> 事件方格輸出繫結僅適用於 Functions 2.x 版和更新版本。

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下列範例顯示的 [C# 函式](functions-dotnet-class-library.md) 可將訊息寫入至事件方格自訂主題，並使用方法傳回值作為輸出：

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

下列程式碼示範如何使用 `IAsyncCollector` 介面來傳送一批訊息。

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

下列範例顯示 *function.json* 檔案中的事件方格輸出繫結資料。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

以下是可建立一個事件的 C# 指令碼程式碼：

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

以下是可建立數個事件的 C# 指令碼程式碼：

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例顯示 *function.json* 檔案中的事件方格輸出繫結資料。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

以下是可建立單一事件的 JavaScript 指令碼程式碼：

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

以下是可建立多個事件的 JavaScript 指令碼程式碼：

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下列範例示範 *function.json* 檔案中的觸發程序繫結，以及使用此繫結的 [Python 函式](functions-reference-python.md)。 然後，它會將事件傳送至自訂主題，如所指定 `topicEndpointUri` 。

以下是 *function.json* 檔案中的繫結資料：

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下是透過設定，將事件傳送至自訂主題的 Python 範例 `EventGridOutputEvent` ：

```python
import logging
import azure.functions as func
import datetime


def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

# <a name="java"></a>[Java](#tab/java)

Java 無法使用事件方格輸出繫結。

---

## <a name="attributes-and-annotations"></a>屬性和註釋

# <a name="c"></a>[C#](#tab/csharp)

對於 [C# 類別庫](functions-dotnet-class-library.md)，請使用 [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) 屬性。

此屬性的建構函式會接受包含自訂主題名稱的應用程式設定名稱，以及包含主題金鑰的應用程式設定名稱。 如需這些設定的詳細資訊，請參閱[輸入 - 組態](#configuration)一節。 以下是 `EventGrid` 屬性範例：

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

如需完整範例，請參閱[範例](#example)。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 無法使用事件方格輸出繫結。

# <a name="java"></a>[Java](#tab/java)

Java 無法使用事件方格輸出繫結。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `EventGrid` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設定為 "eventGrid"。 |
|**direction** | n/a | 必須設定為 "out"。 當您在 Azure 入口網站中建立繫結時，會自動設定此參數。 |
|**name** | n/a | 函式程式碼中所使用的變數名稱，代表事件。 |
|**topicEndpointUri** |**TopicEndpointUri** | 應用程式設定的名稱，其中包含自訂主題的 URI，例如 `MyTopicEndpointUri`。 |
|**topicKeySetting** |**TopicKeySetting** | 應用程式設定的名稱，其中包含自訂主題的存取金鑰。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> 請確定您會將 `TopicEndpointUri` 設定屬性的值設定為包含自訂主題 URI 的應用程式設定名稱。 請勿直接在此屬性中指定自訂主題的 URI。

## <a name="usage"></a>使用量

# <a name="c"></a>[C#](#tab/csharp)

使用方法參數 (例如 `out EventGridEvent paramName`) 來傳送訊息。 若要寫入多則訊息，您可以使用 `ICollector<EventGridEvent>` 或 `IAsyncCollector<EventGridEvent>` 取代 `out EventGridEvent`。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

使用方法參數 (例如 `out EventGridEvent paramName`) 來傳送訊息。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 若要寫入多則訊息，您可以使用 `ICollector<EventGridEvent>` 或 `IAsyncCollector<EventGridEvent>` 取代 `out EventGridEvent`。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name>` 存取輸出事件，其中 `<name>` 是 *function.json* 的 `name` 屬性中指定的值。

# <a name="python"></a>[Python](#tab/python)

Python 無法使用事件方格輸出繫結。

# <a name="java"></a>[Java](#tab/java)

Java 無法使用事件方格輸出繫結。

---

## <a name="next-steps"></a>後續步驟

* [分派事件方格事件](./functions-bindings-event-grid-trigger.md)
