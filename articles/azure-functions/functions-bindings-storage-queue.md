---
title: Azure Functions 的 Azure 佇列儲存體繫結
description: 了解如何在 Azure Functions 中使用 Azure 佇列儲存體觸發程序和輸出繫結。
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 33bdf718e74011dbd7adedd766ebc90923fffb83
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189848"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure Functions 的 Azure 佇列儲存體繫結

本文說明如何在 Azure Functions 中使用 Azure 佇列儲存體繫結。 Azure Functions 支援適用於佇列的觸發程序和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>套件 - Functions 1.x

[Microsoft. Azure webjob](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) Nuget 套件2.x 版中提供佇列儲存體系結。 套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) GitHub 存放庫中。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>封裝-函數2.x 和更新版本

在3.x 版中，會提供[佇列儲存體的系](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage)結，例如版本3.x。 套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) GitHub 存放庫中。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Encoding
Functions 預期有 base64 編碼的字串。 任何對於編碼類型的調整 (以便準備資料作為 base64 編碼的字串) 都必須在呼叫服務中實作。

## <a name="trigger"></a>觸發程序

在佇列上收到新項目時，可使用佇列觸發程序以啟動函式。 佇列訊息會當成函式輸入提供。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

下列範例所示範的 [C# 函式](functions-dotnet-class-library.md)會輪詢 `myqueue-items` 佇列，並在每次處理佇列項目時寫入記錄。

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

下列範例示範的是 *function.json* 檔案中的佇列觸發程序繫結，以及使用該繫結的 [C# 指令碼 (.csx)](functions-reference-csharp.md) 程式碼。 此函式會輪詢 `myqueue-items` 佇列，並在每次處理佇列項目時寫入記錄。

以下是 *function.json* 檔案：

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[設定](#trigger---configuration)章節會說明這些屬性。

以下是 C# 指令碼程式碼：

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

[使用方式](#trigger---usage)章節會說明 `myQueueItem` (由 function.json 中的`name` 屬性命名)。  [訊息中繼資料區段](#trigger---message-metadata)會說明所有其他顯示的變數。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

下列範例示範的是 *function.json* 檔案中的佇列觸發程序繫結，以及使用該繫結的 [JavaScript 函式](functions-reference-node.md)。 此函式會輪詢 `myqueue-items` 佇列，並在每次處理佇列項目時寫入記錄。

以下是 *function.json* 檔案：

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[設定](#trigger---configuration)章節會說明這些屬性。

> [!NOTE]
> 名稱參數會在 JavaScript 程式碼中反映為包含佇列項目承載的 `context.bindings.<name>`。 此承載也會當作第二個參數傳遞給函式。

以下是 JavaScript 程式碼：

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

[使用方式](#trigger---usage)章節會說明 `myQueueItem` (由 function.json 中的`name` 屬性命名)。  [訊息中繼資料區段](#trigger---message-metadata)會說明所有其他顯示的變數。

# <a name="pythontabpython"></a>[Python](#tab/python)

下列範例示範如何讀取透過觸發程式傳遞至函式的佇列訊息。

儲存體佇列觸發程式定義于*function. json*中，其中*type*設為 `queueTrigger`。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

程式碼 *_\_init_\_。 .py*會將參數宣告為 `func.ServiceBusMessage`，這可讓您讀取函式中的佇列訊息。

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

下列 JAVA 範例顯示儲存體佇列觸發程式函式，此函式會記錄放入佇列 `myqueuename`中已觸發的訊息。

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

 ---

## <a name="trigger---attributes-and-annotations"></a>觸發程式-屬性和注釋

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用下列屬性以設定佇列觸發程序：

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  屬性的建構函式會採用佇列名稱進行監視，如下列範例所示：

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  您可以設定 `Connection` 屬性來指定應用程式設定，其中包含要使用的儲存體帳戶連接字串，如下列範例所示：

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  如需完整範例，請參閱[觸發程序 - C# 範例](#trigger)。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  提供另一種方式來指定要使用的儲存體帳戶。 建構函式採用的是內含儲存體連接字串的應用程式設定名稱。 屬性可以套用在參數、方法或類別層級。 下列範例所示範的是類別層級與方法層級：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

要使用的儲存體帳戶按以下順序決定：

* `QueueTrigger` 屬性的 `Connection` 內容。
* `StorageAccount` 屬性套用至與 `QueueTrigger` 屬性相同的參數。
* `StorageAccount` 屬性套用至該函式。
* `StorageAccount` 屬性套用至該類別。
* 「AzureWebJobsStorage」應用程式設定。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

C#腳本不支援屬性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JAVA 腳本不支援屬性。

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="javatabjava"></a>[Java](#tab/java)

`QueueTrigger` 注釋可讓您存取觸發函數的佇列。 下列範例會透過 `message` 參數，將佇列訊息提供給函式。

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| 屬性    | 描述 |
|-------------|-----------------------------|
|`name`       | 宣告函式簽章中的參數名稱。 觸發函式時，這個參數的值會有佇列訊息的內容。 |
|`queueName`  | 宣告儲存體帳戶中的佇列名稱。 |
|`connection` | 指向儲存體帳戶連接字串。 |

---

## <a name="trigger---configuration"></a>觸發程式 - 設定

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `QueueTrigger` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a| 必須設為 `queueTrigger`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction**| n/a | 僅限在 *function.json* 檔案中。 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a |在函式程式碼中包含佇列項目承載的變數名稱。  |
|**queueName** | **QueueName**| 要輪詢的佇列名稱。 |
|**connection** | **[連接]** |應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為 "MyStorage"，函數執行時間會尋找名為 "MyStorage" 的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>觸發程序 - 使用方式

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

使用方法參數（例如 `string paramName`）存取訊息資料。 您可以繫結至下列任何類型：

* 物件：Functions 執行階段會將 JSON 裝載還原序列化為程式碼中所定義之任意類別的執行個體。 
* `string`
* `byte[]`
* [CloudQueueMessage]

如果您嘗試繫結至 `CloudQueueMessage`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

使用方法參數（例如 `string paramName`）存取訊息資料。 `paramName` 是在*函數. json*的 `name` 屬性中指定的值。 您可以繫結至下列任何類型：

* 物件：Functions 執行階段會將 JSON 裝載還原序列化為程式碼中所定義之任意類別的執行個體。 
* `string`
* `byte[]`
* [CloudQueueMessage]

如果您嘗試繫結至 `CloudQueueMessage`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

佇列專案裝載可透過 `context.bindings.<NAME>` 取得，其中 `<NAME>` 符合在函*式中定義的名稱。* 如果裝載是 JSON，此值會還原序列化為物件。

# <a name="pythontabpython"></a>[Python](#tab/python)

透過輸入為[QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python)的參數來存取佇列訊息。

# <a name="javatabjava"></a>[Java](#tab/java)

[QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable)注釋可讓您存取觸發函式的佇列訊息。

---

## <a name="trigger---message-metadata"></a>觸發程序 - 訊息中繼資料

佇列觸發程序提供數個[中繼資料屬性](./functions-bindings-expressions-patterns.md#trigger-metadata)。 這些屬性可作為其他繫結中繫結運算式的一部分或程式碼中的參數使用。 這些是 [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) 類別的屬性。

|屬性|類型|描述|
|--------|----|-----------|
|`QueueTrigger`|`string`|佇列承載 (如果為有效字串)。 如果佇列訊息承載是字串，`QueueTrigger` 的值與*函數. json*中 `name` 屬性所命名的變數相同。|
|`DequeueCount`|`int`|此訊息已從佇列清除的次數。|
|`ExpirationTime`|`DateTimeOffset`|訊息到期時間。|
|`Id`|`string`|佇列訊息識別碼。|
|`InsertionTime`|`DateTimeOffset`|訊息新增至佇列的時間。|
|`NextVisibleTime`|`DateTimeOffset`|下次顯示訊息的時間。|
|`PopReceipt`|`string`|訊息的離開通知。|

## <a name="trigger---poison-messages"></a>觸發程序 - 有害訊息

當佇列觸發程序函數失敗時，Azure Functions 會針對指定的佇列訊息重試該函數最多五次，包括第一次嘗試。 如果五次嘗試全都失敗，Functions 執行階段會將訊息新增至名為 *&lt;originalqueuename>-poison* 的佇列。 您可以撰寫函數，透過記錄或傳送通知表示需要手動處理，來處理有害佇列中的訊息。

若要手動處理有害訊息，請檢查佇列訊息的 [dequeueCount](#trigger---message-metadata)。

## <a name="trigger---polling-algorithm"></a>觸發程序 - 輪詢演算法

佇列觸發程序會實作隨機指數型倒退演算法，以降低閒置佇列輪詢對儲存體交易成本的影響。

此演算法會使用下列邏輯：

- 當找到訊息時，執行時間會等待兩秒，然後檢查是否有另一個訊息
- 當找不到任何訊息時，它會等候大約四秒，然後再試一次。
- 連續嘗試取得佇列訊息失敗後，等候時間會持續增加，直到它到達等待時間上限 (預設值為一分鐘)。
- 可透過 `maxPollingInterval`host.json 檔案[中的 ](functions-host-json.md#queues) 屬性來設定最長等待時間。

針對本機開發，輪詢間隔的最大值預設為2秒。

就計費而言，執行時間輪詢所花費的時間是「免費」，而且不會計入您的帳戶。

## <a name="trigger---concurrency"></a>觸發程序 - 並行

有多個佇列訊息在等候時，佇列觸發程序會擷取批次訊息，並同時叫用函式執行個體來處理它們。 依預設，批次大小為 16。 當要處理的數目減少到 8 時，執行階段就會取得另一個批次，並開始處理那些訊息。 因此，每個函式在一個虛擬機器 (VM) 上並行處理的訊息上限為 24。 這項限制會個別套用至每個 VM 上每個佇列觸發的函式。 如果您的函式應用程式擴展至多個 VM，則每個 VM 會等候觸發程序，並嘗試執行函式。 例如，如果函式應用程式擴展到 3 個 VM，一個佇列觸發函式的並行執行個體數上限會預設為 72。

可在 [host.json 檔案](functions-host-json.md#queues)中設定批次大小和取得新批次的閾值。 如果您需要將一個函式應用程式中佇列觸發函式的平行執行最小化，可以將批次大小設定為 1。 只要您的函式應用程式在單一虛擬機器 (VM) 上執行，這項設定就只會將並行排除。 

佇列觸發程序會自動防止函式處理佇列訊息多次；不需將函式撰寫成等冪函式。

## <a name="trigger---hostjson-properties"></a>觸發程序 - host.json 屬性

[host.json](functions-host-json.md#queues) 檔案包含控制佇列觸發程序行為的設定。 如需可用設定的詳細資訊，請參閱[host. json 設定](#hostjson-settings)一節。

## <a name="output"></a>輸出

使用 Azure 佇列儲存體輸出繫結，將訊息寫入佇列。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

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

[設定](#output---configuration)章節會說明這些屬性。

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

[設定](#output---configuration)章節會說明這些屬性。

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

# <a name="pythontabpython"></a>[Python](#tab/python)

下列範例示範如何將單一和多個值輸出到儲存體佇列。 *函數. json*所需的設定也是相同的方式。

儲存體佇列系結定義于*function json*中，其中*type*設為 `queue`。

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

若要在佇列上設定個別訊息，請將單一值傳遞給 `set` 方法。

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

若要在佇列上建立多個訊息，請將參數宣告為適當的清單類型，並將值陣列（符合清單類型）傳遞給 `set` 方法。

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="javatabjava"></a>[Java](#tab/java)

 下列範例顯示的 JAVA 函式會建立由 HTTP 要求觸發時的佇列訊息。

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

在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對其值要寫入至佇列儲存體的參數使用 `@QueueOutput` 註釋。  參數類型應該是 `OutputBinding<T>`，其中 `T` 是 POJO 的任何原生 JAVA 類型。

---

## <a name="output---attributes-and-annotations"></a>輸出-屬性和注釋

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

如需完整範例，請參閱[輸出 - C# 範例](#output)。

您可以使用 `StorageAccount` 屬性來指定類別、方法或參數層級的儲存體帳戶。 如需詳細資訊，請參閱「觸發程序 - 屬性」。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

C#腳本不支援屬性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JAVA 腳本不支援屬性。

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="javatabjava"></a>[Java](#tab/java)

`QueueOutput` 注釋可讓您存取，以寫入訊息的輸出函式。 下列範例顯示的是 HTTP 觸發的函式，它會建立佇列訊息。

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
|`name`       | 宣告函式簽章中的參數名稱。 觸發函式時，這個參數的值會有佇列訊息的內容。 |
|`queueName`  | 宣告儲存體帳戶中的佇列名稱。 |
|`connection` | 指向儲存體帳戶連接字串。 |

與 `QueueOutput` 注釋相關聯的參數會輸入為[OutputBinding\<t\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java)實例。

---

## <a name="output---configuration"></a>輸出 - 設定

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `Queue` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `queue`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `out`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a | 代表函式程式碼中佇列的變數名稱。 設為 `$return` 以參考函式傳回值。|
|**queueName** |**QueueName** | 佇列的名稱。 |
|**connection** | **[連接]** |應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為 "MyStorage"，函數執行時間會尋找名為 "MyStorage" 的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>輸出 - 使用方式

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

使用方法參數（例如 `out T paramName`）撰寫單一佇列訊息。 您可以使用方法傳回類型，而不是 `out` 參數，而且 `T` 可以是下列類型之一：

* 可序列化為 JSON 的物件
* `string`
* `byte[]`
* [CloudQueueMessage] 

如果您嘗試繫結至 `CloudQueueMessage`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

在 C# 和 C# 指令碼中，藉由使用下列其中一個類型，寫入多個佇列訊息： 

* `ICollector<T>` 或 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

使用方法參數（例如 `out T paramName`）撰寫單一佇列訊息。 `paramName` 是在*函數. json*的 `name` 屬性中指定的值。 您可以使用方法傳回類型，而不是 `out` 參數，而且 `T` 可以是下列類型之一：

* 可序列化為 JSON 的物件
* `string`
* `byte[]`
* [CloudQueueMessage] 

如果您嘗試繫結至 `CloudQueueMessage`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

在 C# 和 C# 指令碼中，藉由使用下列其中一個類型，寫入多個佇列訊息： 

* `ICollector<T>` 或 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

輸出佇列專案可透過 `context.bindings.<NAME>`，其中 `<NAME>` 符合在函*式中定義的名稱。* 對於佇列項目承載，可使用字串或 JSON 可序列化物件。

# <a name="pythontabpython"></a>[Python](#tab/python)

有兩個選項可從函式輸出事件中樞訊息：

- 傳回**值**：將*函數. json*中的 `name` 屬性設定為 `$return`。 使用此設定時，函數的傳回值會保存為佇列儲存體訊息。

- **命令式**：將值傳遞給宣告為[Out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python)類型之參數的[set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none)方法。 傳遞給 `set` 的值會保存為佇列儲存體訊息。

# <a name="javatabjava"></a>[Java](#tab/java)

有兩個選項可從函式使用[QueueOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput)批註輸出事件中樞訊息：

- 傳回**值**：藉由將注釋套用至函式本身，函式的傳回值會保存為事件中樞訊息。

- **命令式**：若要明確設定訊息值，請將注釋套用至類型[`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)的特定參數，其中 `T` 是 POJO 或任何原生 JAVA 類型。 使用此設定時，將值傳遞至 `setValue` 方法會將值保存為事件中樞訊息。

---

## <a name="exceptions-and-return-codes"></a>例外狀況和傳回碼

| 繫結 |  參考 |
|---|---|
| 佇列 | [佇列錯誤碼](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Bob、資料表、佇列 | [儲存體錯誤碼](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Bob、資料表、佇列 |  [疑難排解](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

本節說明2.x 版和更高版本中可供此系結使用的全域設定。 下面的範例 host. json 檔案僅包含此系結的2.x 版和設定。 如需有關2.x 版和更早版本中的全域設定的詳細資訊，請參閱[Azure Functions 的 host. json 參考](functions-host-json.md)。

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
|maxPollingInterval|00:00:01|佇列輪詢之間的間隔上限。 最小值為00：00：00.100 （100毫秒），而遞增至00:01:00 （1分鐘）。  在1.x 中，資料類型是毫秒，而在2.x 和更高的版本中，它是 TimeSpan。|
|visibilityTimeout|00:00:00|處理訊息失敗時，重試之間的時間間隔。 |
|batchSize|16|Functions 執行階段會同時擷取，並以平行方式處理的佇列訊息數目。 當要處理的數目減少到 `newBatchThreshold` 時，執行階段就會取得另一個批次，並開始處理那些訊息。 因此，每個函式並行處理之訊息的上限為 `batchSize` 加上 `newBatchThreshold`。 這項限制個別套用至每個佇列觸發的函式。 <br><br>如果您需要避免平行執行在單一佇列上收到的訊息，可以將 `batchSize` 設定為 1。 不過，只要您的函式應用程式在單一虛擬機器 (VM) 上執行，這項設定就只會將並行排除。 如果函式應用程式相應放大為多個 VM，則每個 VM 可以執行每個佇列觸發之函式的一個執行個體。<br><br>最大值 `batchSize` 為 32。 |
|maxDequeueCount|5|將訊息移至有害佇列之前，嘗試處理訊息的次數。|
|newBatchThreshold|batchSize/2|每當要同時處理的訊息數目下降至這個數字時，執行階段就會擷取另一個批次。|

## <a name="next-steps"></a>後續步驟

* [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [移至使用佇列儲存體輸出繫結的教學課程](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
