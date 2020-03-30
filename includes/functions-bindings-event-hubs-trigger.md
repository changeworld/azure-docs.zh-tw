---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 438e3166e27511780dd871b5076a7b28ebade052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589710"
---
使用函數觸發器回應發送到事件中心事件流的事件。 您必須具有對基礎事件中心的讀取存取許可權才能設置觸發器。 觸發函數時，傳遞給函數的消息將作為字串鍵入。

## <a name="scaling"></a>調整大小

事件觸發函數的每個實例都由單個[事件處理器Host](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)實例備份。 觸發器（由事件中心供電）可確保只有一個[事件處理器Host](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)實例可以在給定的分區上獲取租約。

例如，請考慮以下事件中樞：

* 10 個分區
* 1，000 個事件在所有分區之間均勻分佈，每個分區中包含 100 條消息

當您的函式首次啟用時，只會有 1 個該函式的執行個體。 讓我們調用第一個函數實例`Function_0`。 該`Function_0`函數具有[事件處理器Host](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)的單個實例，該實例對所有 10 個分區都持有租約。 此執行個體會從分割區 0-9 讀取事件。 從這裡開始，會發生下列其中一件事：

* **不需要新的函數實例**：`Function_0`能夠在函數縮放邏輯生效之前處理所有 1，000 個事件。 在這種情況下，所有 1，000 條消息都由 處理`Function_0`。

* **添加了一個額外的函數實例**：如果函數縮放邏輯確定`Function_0`其消息數超過其處理時間，則將創建新的函數應用實例 （`Function_1`） 。 此新功能還具有[事件處理器Host](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)的關聯實例。 當基礎事件中心檢測到新的主機實例正在嘗試讀取消息時，它將跨主機實例的分區進行負載平衡。 例如，分割區 0-4 可能會指派給 `Function_0`，分割區 5-9 則指派給 `Function_1`。

* **添加了 N 個函數實例**：如果函數縮放邏輯確定兩者兼`Function_0``Function_1`包含的消息數超過其處理時間，則創建新`Functions_N`的函數應用實例。  應用創建到大於事件中心分區`N`數的點。 在本例中，事件中樞同樣會將分割區負載平衡，在此案例中，會跨執行個體 `Function_0`...`Functions_9` 來進行。

當進行縮放時`N`，實例的數量大於事件中心分區的數量。 此模式用於確保[EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)實例可用於獲取分區上的鎖，因為它們從其他實例變為可用。 僅對函數實例執行時使用的資源收費。 換句話說，您不會為此過度調配付費。

當所有函式執行完成時 (不論有無錯誤)，系統就會在相關聯的儲存體帳戶中新增檢查點。 當檢查點成功時，所有 1，000 條消息永遠不會再次檢索。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

下列範例顯示的 [C# 函式](../articles/azure-functions/functions-dotnet-class-library.md)，可記錄事件中樞觸發程序的訊息本文。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

若要取得函式程式碼中[事件中繼資料](#event-metadata)的存取權，請繫結至 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 物件 (`Microsoft.Azure.EventHubs` 需要 using 陳述式)。 您也可以在方法簽章中使用繫結運算式，以存取相同的屬性。  下列範例示範取得相同資料的兩種方式：

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

若要以批次方式接收事件，請讓 `string` 或 `EventData` 成為陣列。  

> [!NOTE]
> 以批次方式接收時，您無法像上述範例中一樣使用 `DateTime enqueuedTimeUtc` 來繫結至方法參數，而必須從每個 `EventData` 物件接收這些事件  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

下列範例示範 function.json** 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [C# 指令碼函式](../articles/azure-functions/functions-reference-csharp.md)。 此函式會記錄事件中樞觸發程序的訊息本文。

下列範例顯示 *function.json* 檔案中的事件中樞繫結資料。

### <a name="version-2x-and-higher"></a>版本 2.x 及更高版本

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>1.x 版

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

以下是 C# 指令碼程式碼：

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

若要取得函式程式碼中[事件中繼資料](#event-metadata)的存取權，請繫結至 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 物件 (`Microsoft.Azure.EventHubs` 需要 using 陳述式)。 您也可以在方法簽章中使用繫結運算式，以存取相同的屬性。  下列範例示範取得相同資料的兩種方式：

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

若要以批次方式接收事件，請讓 `string` 或 `EventData` 成為陣列：

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

下列範例示範 function.json** 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [JavaScript 函式](../articles/azure-functions/functions-reference-node.md)。 此函式會讀取[事件中繼資料](#event-metadata)和記錄訊息。

下列範例顯示 *function.json* 檔案中的事件中樞繫結資料。

### <a name="version-2x-and-higher"></a>版本 2.x 及更高版本

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>1.x 版

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

若要批次接收事件，請在 *function.json* 檔案中將 `cardinality` 設定為 `many`，如下列範例所示。

### <a name="version-2x-and-higher"></a>版本 2.x 及更高版本

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>1.x 版

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下列範例示範 function.json** 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [Python 函式](../articles/azure-functions/functions-reference-python.md)。 此函式會讀取[事件中繼資料](#event-metadata)和記錄訊息。

下列範例顯示 *function.json* 檔案中的事件中樞繫結資料。

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

以下是 Python 程式碼：

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

# <a name="java"></a>[JAVA](#tab/java)

下面的示例顯示了一個事件中心觸發器綁定，該綁定記錄事件中心觸發器的郵件內文。

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對其值來自事件中樞的參數使用 `EventHubTrigger` 註釋。 具有這些附註的參數會使得函式在事件抵達時執行。  此註釋可以搭配原生 Java 類型、POJO 或使用 `Optional<T>` 的可為 Null 值使用。

 ---

## <a name="attributes-and-annotations"></a>屬性和注釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別庫](../articles/azure-functions/functions-dotnet-class-library.md)中，使用 [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) 屬性。

此屬性的建構函式接受事件中樞的名稱、取用者群組的名稱，以及包含連接字串的應用程式設定名稱。 如需這些設定的詳細資訊，請參閱[觸發程序組態](#configuration)一節。 以下是 `EventHubTriggerAttribute` 屬性範例：

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

如需完整範例，請參閱[觸發程序 - C# 範例](#example)。

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

C# 腳本不支援屬性。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="java"></a>[JAVA](#tab/java)

從 JAVA[函數運行時庫](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)，對值來自事件中心的參數使用[EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger)注釋。 具有這些附註的參數會使得函式在事件抵達時執行。 此註釋可以搭配原生 Java 類型、POJO 或使用 `Optional<T>` 的可為 Null 值使用。

---

## <a name="configuration"></a>組態

下表介紹了您在*函數.json*檔和`EventHubTrigger`屬性中設置的綁定配置屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**型別** | n/a | 必須設為 `eventHubTrigger`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**名稱** | n/a | 代表函式程式碼中事件項目的變數名稱。 |
|**路徑** |**事件中心名稱** | 僅限 Functions 1.x。 事件中樞的名稱。 當事件中樞名稱也呈現於連接字串時，該值會在執行階段覆寫這個屬性。 |
|**eventHubName** |**事件中心名稱** | 功能 2.x 及以上。 事件中樞的名稱。 當事件中樞名稱也呈現於連接字串時，該值會在執行階段覆寫這個屬性。 可通過應用設置引用 %事件HubName% |
|**消費者群體** |**消費者群體** | 選擇性屬性，可設定用來訂閱中樞內事件的[取用者群組](../articles/event-hubs/event-hubs-features.md#event-consumers)。 如果省略，則會使用 `$Default` 取用者群組。 |
|**基數** | n/a | 適用於 JavaScript。 設定為 `many` 才能啟用批次處理。  如果省略或設置為`one`，將傳遞單個消息到函數。 |
|**連接** |**連接** | 應用程式設定的名稱，其中包含事件中樞命名空間的連接字串。 通過按一下[命名空間](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)（而不是事件中心本身）**的連接資訊**按鈕複製此連接字串。 此連接字串至少必須具備讀取權限，才能啟動觸發程序。|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="event-metadata"></a>事件中繼資料

事件中樞觸發程序提供數個[中繼資料屬性](../articles/azure-functions/./functions-bindings-expressions-patterns.md)。 中繼資料屬性可用作其他綁定中的綁定運算式的一部分或代碼中的參數。 屬性來自[事件資料](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata)類。

|屬性|類型|描述|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` 執行個體。|
|`EnqueuedTimeUtc`|`DateTime`|加入佇列的時間 (UTC)。|
|`Offset`|`string`|相對於事件中樞資料分割串流的資料位移。 此位移是事件中樞串流中事件的標記或識別碼。 此識別碼在事件中樞串流的資料分割內是唯一的。|
|`PartitionKey`|`string`|事件資料應該傳送至的資料分割。|
|`Properties`|`IDictionary<String,Object>`|事件資料的使用者屬性。|
|`SequenceNumber`|`Int64`|事件的邏輯序號。|
|`SystemProperties`|`IDictionary<String,Object>`|系統屬性，包括事件資料。|

請參閱稍早在本文中使用這些屬性的[程式碼範例](#example)。

## <a name="hostjson-properties"></a>host.json 屬性

[host.json](../articles/azure-functions/functions-host-json.md#eventhub) 檔案包含可控制事件中樞觸發程序行為的設定。

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]