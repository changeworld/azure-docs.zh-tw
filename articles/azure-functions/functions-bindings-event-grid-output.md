---
title: Azure 事件網格輸出綁定用於 Azure 函數
description: 瞭解如何在 Azure 函數中發送事件網格事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368944"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure 事件網格輸出綁定用於 Azure 函數

使用事件網格輸出綁定將事件寫入自訂主題。 您必須具有[自訂主題的有效訪問金鑰](../event-grid/security-authentication.md#custom-topic-publishing)。

有關設置和配置詳細資訊的資訊，請參閱[概述](./functions-bindings-event-grid.md)。

> [!NOTE]
> 事件網格輸出綁定不支援共用訪問簽名（SAS 權杖）。 您必須使用主題的訪問金鑰。

> [!IMPORTANT]
> 事件網格輸出綁定僅適用于函數 2.x 及更高版本。

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下面的示例顯示了一個[C# 函數](functions-dotnet-class-library.md)，該函數使用方法傳回值作為輸出將消息寫入事件網格自訂主題：

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

下面的示例演示如何使用`IAsyncCollector`介面發送一批消息。

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

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

下面的示例顯示了*函數.json*檔中的事件網格輸出綁定資料。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

下面是創建一個事件的 C# 腳本代碼：

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

下面是創建多個事件的 C# 腳本代碼：

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

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

下面的示例顯示了*函數.json*檔中的事件網格輸出綁定資料。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

下面是創建單個事件的 JavaScript 代碼：

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

下面是創建多個事件的 JavaScript 代碼：

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

事件網格輸出綁定不適用於 Python。

# <a name="java"></a>[JAVA](#tab/java)

事件網格輸出綁定不適用於 JAVA。

---

## <a name="attributes-and-annotations"></a>屬性和注釋

# <a name="c"></a>[C#](#tab/csharp)

對於[C# 類庫](functions-dotnet-class-library.md)，請使用[事件網格屬性屬性](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs)。

屬性的建構函式獲取包含自訂主題名稱的應用設置的名稱以及包含主題鍵的應用設置的名稱。 如需這些設定的詳細資訊，請參閱[輸入 - 組態](#configuration)一節。 以下是 `EventGrid` 屬性範例：

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

有關完整示例，請參閱[示例](#example)。

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

C# 腳本不支援屬性。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

事件網格輸出綁定不適用於 Python。

# <a name="java"></a>[JAVA](#tab/java)

事件網格輸出綁定不適用於 JAVA。

---

## <a name="configuration"></a>組態

下表介紹了您在*函數.json*檔和`EventGrid`屬性中設置的綁定配置屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**型別** | n/a | 必須設置為"事件網格"。 |
|**direction** | n/a | 必須設定為 "out"。 當您在 Azure 入口網站中建立繫結時，會自動設定此參數。 |
|**名稱** | n/a | 函式程式碼中所使用的變數名稱，代表事件。 |
|**主題 EndpointUri** |**主題端點Uri** | 包含自訂主題的 URI 的應用設置的名稱，例如`MyTopicEndpointUri`。 |
|**主題鍵設置** |**主題鍵設置** | 包含自訂主題的訪問金鑰的應用設置的名稱。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> 請確保將`TopicEndpointUri`配置屬性的值設置為包含自訂主題 URI 的應用設置的名稱。 不要直接在此屬性中指定自訂主題的 URI。

## <a name="usage"></a>使用量

# <a name="c"></a>[C#](#tab/csharp)

使用 方法參數（如`out EventGridEvent paramName`）發送消息。 若要寫入多則訊息，您可以使用 `ICollector<EventGridEvent>` 或 `IAsyncCollector<EventGridEvent>` 取代 `out EventGridEvent`。

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

使用 方法參數（如`out EventGridEvent paramName`）發送消息。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 若要寫入多則訊息，您可以使用 `ICollector<EventGridEvent>` 或 `IAsyncCollector<EventGridEvent>` 取代 `out EventGridEvent`。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

使用`context.bindings.<name>`*函數.json*`<name>``name`屬性中指定的值來訪問輸出事件。

# <a name="python"></a>[Python](#tab/python)

事件網格輸出綁定不適用於 Python。

# <a name="java"></a>[JAVA](#tab/java)

事件網格輸出綁定不適用於 JAVA。

---

## <a name="next-steps"></a>後續步驟

* [調度事件網格事件](./functions-bindings-event-grid-trigger.md)
