---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: d8c6b79dca97de3dd46eb9c677f2c94191f276b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89303995"
---
使用函式觸發程序將回應傳送至事件中樞事件串流。 您必須具有基礎事件中樞的讀取存取權，才能設定觸發程序。 觸發函式時，傳遞至函式的訊息會以字串的形式輸入。

## <a name="scaling"></a>調整大小

由事件所觸發的函式，其每個執行個體只會由單一 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 執行個體來提供支援。 觸發程序 (由事件中樞提供) 會確保只有 1 個 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 執行個體可以在指定的分割區上取得租用。

例如，請考慮以下事件中樞：

* 10 個資料分割
* 1000 個平均散佈於所有分割區上的事件，且每個分割區中有 100 個訊息

當您的函式首次啟用時，只會有 1 個該函式的執行個體。 讓我們將第一個函式執行個體稱為 `Function_0`。 `Function_0` 函式具有單一 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 的執行個體，這個執行個體在全部 10 個分割區全都擁有租用。 此執行個體會從分割區 0-9 讀取事件。 從這裡開始，會發生下列其中一件事：

* **不需要新的函式執行個體**：`Function_0` 能夠在 Functions 的規模調整邏輯開始生效之前完全處理這 1000 個事件。 在此情況下，`Function_0` 會完全處理這 1000 個訊息。

* **再新增 1 個函式執行個體**：如果 Functions 規模調整邏輯判斷 `Function_0` 所擁有的數目比能夠處理的數目還多，就會建立一個新的函數應用程式執行個體 (`Function_1`)。 這個新的函式也會具有相關聯的 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 執行個體。 當基礎事件中樞偵測到新的主控件執行個體正在嘗試讀取訊息時，會在主控件執行個體之間進行分割區的負載平衡。 例如，分割區 0-4 可能會指派給 `Function_0`，分割區 5-9 則指派給 `Function_1`。

* **再新增 N 個函式執行個體**：如果 Functions 規模調整邏輯判斷 `Function_0` 和 `Function_1` 所擁有的訊息都比能夠處理的還多，則會建立新的 `Functions_N` 函數應用程式執行個體。  系統會將應用程式建立到 `N` 大於事件中樞分割區數目的點。 在本例中，事件中樞同樣會將分割區負載平衡，在此案例中，會跨執行個體 `Function_0`...`Functions_9` 來進行。

發生規模調整時，`N` 個執行個體是大於事件中樞分割區數目的數字。 使用這個模式以確保 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 執行個體可用來在分割區變成可從其他執行個體使用時，取得這些分割區的鎖定。 您只需針對函式執行個體執行時所使用的資源付費。 換句話說，您不需要針對這個過度佈建付費。

當所有函式執行完成時 (不論有無錯誤)，系統就會在相關聯的儲存體帳戶中新增檢查點。 當檢查點檢查成功時，就永遠不會再次擷取所有的 1000 個訊息。

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

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

下列範例示範 function.json** 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [C# 指令碼函式](../articles/azure-functions/functions-reference-csharp.md)。 此函式會記錄事件中樞觸發程序的訊息本文。

下列範例顯示 *function.json* 檔案中的事件中樞繫結資料。

### <a name="version-2x-and-higher"></a>2\.x 版和更新版本

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>1\.x 版

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例示範 function.json** 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [JavaScript 函式](../articles/azure-functions/functions-reference-node.md)。 此函式會讀取[事件中繼資料](#event-metadata)和記錄訊息。

下列範例顯示 *function.json* 檔案中的事件中樞繫結資料。

### <a name="version-2x-and-higher"></a>2\.x 版和更新版本

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>1\.x 版

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

### <a name="version-2x-and-higher"></a>2\.x 版和更新版本

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

### <a name="version-1x"></a>1\.x 版

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

    # Metadata
    for key in event.metadata:
        logging.info(f'Metadata: {key} = ', event.metadata[key])
```

# <a name="java"></a>[Java](#tab/java)

下列範例顯示事件中樞觸發程序繫結，可記錄事件中樞觸發程序的訊息本文。

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

## <a name="attributes-and-annotations"></a>屬性和註釋

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

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 指令碼不支援屬性。

# <a name="java"></a>[Java](#tab/java)

從 Java [函式執行階段程式庫](https://docs.microsoft.com/java/api/overview/azure/functions/runtime) 中，對其值來自事件中樞的參數使用 [EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) 註釋。 具有這些附註的參數會使得函式在事件抵達時執行。 此註釋可以搭配原生 Java 類型、POJO 或使用 `Optional<T>` 的可為 Null 值使用。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `EventHubTrigger` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `eventHubTrigger`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a | 代表函式程式碼中事件項目的變數名稱。 |
|**path** |**EventHubName** | 僅限 Functions 1.x。 事件中樞的名稱。 當事件中樞名稱也呈現於連接字串時，該值會在執行階段覆寫這個屬性。 |
|**eventHubName** |**EventHubName** | Functions 2.x 和更新版本。 事件中樞的名稱。 當事件中樞名稱也呈現於連接字串時，該值會在執行階段覆寫這個屬性。 可透過[應用程式設定](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings) `%eventHubName%` 參照 |
|**consumerGroup** |**ConsumerGroup** | 選擇性屬性，可設定用來訂閱中樞內事件的[取用者群組](../articles/event-hubs/event-hubs-features.md#event-consumers)。 如果省略，則會使用 `$Default` 取用者群組。 |
|**基數** | n/a | 用於所有非 C# 語言。 設定為 `many` 才能啟用批次處理。  如果省略或設定為 `one`，會傳遞單一訊息至函式。<br><br>在 C# 中，每當觸發程序具有類型的陣列時，就會自動指派此屬性。|
|**connection** |**[連接]** | 應用程式設定的名稱，其中包含事件中樞命名空間的連接字串。 按一下[命名空間](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)的 [連接資訊] 按鈕 (而不是事件中樞本身)，來複製此連接字串。 此連接字串至少必須具備讀取權限，才能啟動觸發程序。|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="event-metadata"></a>事件中繼資料

事件中樞觸發程序提供數個[中繼資料屬性](../articles/azure-functions/./functions-bindings-expressions-patterns.md)。 中繼資料屬性可作為其他繫結中繫結運算式的一部分或程式碼中的參數使用。 屬性來自 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) 類別。

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
<a name="host-json"></a>

[host.json](../articles/azure-functions/functions-host-json.md#eventhub) 檔案包含可控制事件中樞觸發程序行為的設定。 視 Azure Functions 版本而定，設定會有所不同。

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]