---
title: Azure Functions 的 Azure 佇列儲存體觸發程式
description: 瞭解如何執行 Azure 函式做為 Azure 佇列儲存體資料變更。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 74ca984232bef979062221a451d0ee10a6965bc6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277371"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Azure Functions 的 Azure 佇列儲存體觸發程式

佇列儲存體觸發程式會在訊息新增至 Azure 佇列儲存體時執行函數。

## <a name="encoding"></a>編碼

Functions 預期有 base64** 編碼的字串。 任何對於編碼類型的調整 (以便準備資料作為 base64** 編碼的字串) 都必須在呼叫服務中實作。

## <a name="example"></a>範例

在佇列上收到新項目時，可使用佇列觸發程序以啟動函式。 佇列訊息會當成函式輸入提供。

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C # 腳本](#tab/csharp-script)

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

[設定](#configuration)章節會說明這些屬性。

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

[使用方式](#usage)章節會說明 `myQueueItem` (由 function.json 中的`name` 屬性命名)。  [訊息中繼資料區段](#message-metadata)會說明所有其他顯示的變數。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

[設定](#configuration)章節會說明這些屬性。

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

[使用方式](#usage)章節會說明 `myQueueItem` (由 function.json 中的`name` 屬性命名)。  [訊息中繼資料區段](#message-metadata)會說明所有其他顯示的變數。

# <a name="python"></a>[Python](#tab/python)

下列範例示範如何讀取透過觸發程式傳遞至函式的佇列訊息。

儲存體佇列觸發程式定義于函式 *. json*中，其中*類型*設定`queueTrigger`為。

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

程式碼* _ \_ _ \_.py*會將參數宣告為`func.ServiceBusMessage`，這可讓您讀取函數中的佇列訊息。

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

# <a name="java"></a>[Java](#tab/java)

下列 JAVA 範例顯示儲存體佇列觸發程式函式，此函式會記錄放入佇列`myqueuename`中的已觸發訊息。

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

## <a name="attributes-and-annotations"></a>屬性和注釋

# <a name="c"></a>[C#](#tab/csharp)

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

  您可以設定`Connection`屬性來指定應用程式設定，其中包含要使用的儲存體帳戶連接字串，如下列範例所示：

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  如需完整範例，請參閱[範例](#example)。

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

# <a name="c-script"></a>[C # 腳本](#tab/csharp-script)

C # 腳本不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="java"></a>[Java](#tab/java)

`QueueTrigger`批註可讓您存取觸發函數的佇列。 下列範例會透過`message`參數，將佇列訊息提供給函式。

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

## <a name="configuration"></a>設定

下表說明您在*函數 json*檔案和`QueueTrigger`屬性中設定的系結設定屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a| 必須設為 `queueTrigger`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**方向**| n/a | 僅限在 *function.json* 檔案中。 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a |在函式程式碼中包含佇列項目承載的變數名稱。  |
|**queueName** | **QueueName**| 要輪詢的佇列名稱。 |
|**connection** | **建立** |應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將設定`connection`為 "MyStorage"，函數執行時間會尋找名為 "MyStorage" 的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用方式

# <a name="c"></a>[C#](#tab/csharp)

使用方法參數（例如） `string paramName`來存取訊息資料。 您可以繫結至下列任何類型：

* 物件：Functions 執行階段會將 JSON 裝載還原序列化為程式碼中所定義之任意類別的執行個體。 
* `string`
* `byte[]`
* [CloudQueueMessage]

如果您嘗試繫結至 `CloudQueueMessage`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)。

# <a name="c-script"></a>[C # 腳本](#tab/csharp-script)

使用方法參數（例如） `string paramName`來存取訊息資料。 `paramName`是在`name` *函數*的屬性中指定的值。 您可以繫結至下列任何類型：

* 物件：Functions 執行階段會將 JSON 裝載還原序列化為程式碼中所定義之任意類別的執行個體。 
* `string`
* `byte[]`
* [CloudQueueMessage]

如果您嘗試繫結至 `CloudQueueMessage`，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

佇列專案裝載可透過取得`context.bindings.<NAME>` ，其中`<NAME>`符合在函式中定義的名稱。 *function.json* 如果裝載是 JSON，此值會還原序列化為物件。

# <a name="python"></a>[Python](#tab/python)

透過輸入為[QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python)的參數來存取佇列訊息。

# <a name="java"></a>[Java](#tab/java)

[QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable)注釋可讓您存取觸發函式的佇列訊息。

---

## <a name="message-metadata"></a>訊息中繼資料

佇列觸發程序提供數個[中繼資料屬性](./functions-bindings-expressions-patterns.md#trigger-metadata)。 這些屬性可作為其他繫結中繫結運算式的一部分或程式碼中的參數使用。 屬性是[CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)類別的成員。

|屬性|類型|描述|
|--------|----|-----------|
|`QueueTrigger`|`string`|佇列承載 (如果為有效字串)。 如果佇列訊息裝載是字串， `QueueTrigger`其值與`name` *函數. json*中的屬性所命名的變數相同。|
|`DequeueCount`|`int`|此訊息已從佇列清除的次數。|
|`ExpirationTime`|`DateTimeOffset`|訊息到期時間。|
|`Id`|`string`|佇列訊息識別碼。|
|`InsertionTime`|`DateTimeOffset`|訊息新增至佇列的時間。|
|`NextVisibleTime`|`DateTimeOffset`|下次顯示訊息的時間。|
|`PopReceipt`|`string`|訊息的離開通知。|

## <a name="poison-messages"></a>有害訊息

當佇列觸發程序函數失敗時，Azure Functions 會針對指定的佇列訊息重試該函數最多五次，包括第一次嘗試。 如果所有五個嘗試都失敗，則函式執行時間會將訊息新增至名為* &lt;originalqueuename>-有害*的佇列。 您可以撰寫函數，透過記錄或傳送通知表示需要手動處理，來處理有害佇列中的訊息。

若要手動處理有害訊息，請檢查佇列訊息的 [dequeueCount](#message-metadata)。

## <a name="polling-algorithm"></a>輪詢演算法

佇列觸發程序會實作隨機指數型倒退演算法，以降低閒置佇列輪詢對儲存體交易成本的影響。

此演算法會使用下列邏輯：

- 當找到訊息時，執行時間會等待兩秒，然後檢查是否有另一個訊息
- 當找不到任何訊息時，它會等候大約四秒，然後再試一次。
- 連續嘗試取得佇列訊息失敗後，等候時間會持續增加，直到它到達等待時間上限 (預設值為一分鐘)。
- 可透過 [host.json 檔案](functions-host-json.md#queues)中的 `maxPollingInterval` 屬性來設定最長等待時間。

針對本機開發，輪詢間隔的最大值預設為2秒。

就計費而言，執行時間輪詢所花費的時間是「免費」，而且不會計入您的帳戶。

## <a name="concurrency"></a>並行

有多個佇列訊息在等候時，佇列觸發程序會擷取批次訊息，並同時叫用函式執行個體來處理它們。 依預設，批次大小為 16。 當要處理的數目減少到 8 時，執行階段就會取得另一個批次，並開始處理那些訊息。 因此，每個函式在一個虛擬機器 (VM) 上並行處理的訊息上限為 24。 這項限制會個別套用至每個 VM 上每個佇列觸發的函式。 如果您的函式應用程式擴展至多個 VM，則每個 VM 會等候觸發程序，並嘗試執行函式。 例如，如果函式應用程式擴展到 3 個 VM，一個佇列觸發函式的並行執行個體數上限會預設為 72。

可在 [host.json 檔案](functions-host-json.md#queues)中設定批次大小和取得新批次的閾值。 如果您需要將一個函式應用程式中佇列觸發函式的平行執行最小化，可以將批次大小設定為 1。 只要您的函式應用程式在單一虛擬機器 (VM) 上執行，這項設定就只會將並行排除。 

佇列觸發程序會自動防止函式處理佇列訊息多次；不需將函式撰寫成等冪函式。

## <a name="hostjson-properties"></a>host. json 屬性

[host.json](functions-host-json.md#queues) 檔案包含控制佇列觸發程序行為的設定。 如需可用設定的詳細資訊，請參閱[host. json 設定](functions-bindings-storage-queue-output.md#hostjson-settings)一節。

## <a name="next-steps"></a>後續步驟

- [寫入佇列儲存體訊息（輸出系結）](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
