---
title: Azure Functions 的 Azure 服務匯流排繫結
description: 在創建 Azure 服務匯流排消息時，瞭解如何運行 Azure 函數。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273556"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure 函數的 Azure 服務匯流排觸發器

使用服務匯流排觸發程序來回應來自服務匯流排佇列或主題的訊息。

有關設置和配置詳細資訊的資訊，請參閱[概述](functions-bindings-service-bus-output.md)。

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下列範例示範的 [C# 函式](functions-dotnet-class-library.md)可讀取[訊息中繼資料](#message-metadata)和記錄服務匯流排佇列訊息：

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

下列範例示範 function.json** 檔案中的服務匯流排觸發程序繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 此函式可讀取[訊息中繼資料](#message-metadata)和記錄服務匯流排佇列訊息。

以下是 *function.json* 檔案中的繫結資料：

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

以下是 C# 指令碼程式碼：

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

下列範例示範 function.json** 檔案中的服務匯流排觸發程序繫結，以及使用此繫結的 [JavaScript 函式](functions-reference-node.md)。 此函式可讀取[訊息中繼資料](#message-metadata)和記錄服務匯流排佇列訊息。 

以下是 *function.json* 檔案中的繫結資料：

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

以下是 JavaScript 指令碼程式碼：

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下面的示例演示如何通過觸發器讀取服務匯流排佇列消息。

服務匯流排綁定在*函數.json*中定義，其中*類型*設置為`serviceBusTrigger`。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

* _ \_init_\_.py*中的代碼將參數聲明為`func.ServiceBusMessage`，它允許您讀取函數中的佇列消息。

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

# <a name="java"></a>[JAVA](#tab/java)

以下 JAVA 函數使用`@ServiceBusQueueTrigger`JAVA[函數運行時庫中](/java/api/overview/azure/functions/runtime)的注釋來描述服務匯流排佇列觸發器的配置。 函數會獲取放置在佇列中的消息並將其添加到日誌中。

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

當消息添加到服務匯流排主題時，也可以觸發 JAVA 函數。 下面的示例使用`@ServiceBusTopicTrigger`注釋來描述觸發器配置。

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

---

## <a name="attributes-and-annotations"></a>屬性和注釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用下列屬性以設定服務匯流排觸發程序：

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  該屬性的建構函式會採用佇列名稱或標題和訂用帳戶。 在 Azure Functions 第 1.x 版中，您也可以指定連線的存取權限。 如果您未指定存取權限，則預設值為 `Manage`。 如需詳細資訊，請參閱[觸發程序 - 組態](#configuration)一節。

  以下範例顯示搭配字串參數使用的屬性：

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  可以將`Connection`屬性設置為指定包含要使用的服務匯流排連接字串的應用設置的名稱，如以下示例所示：

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  有關完整示例，請參閱[觸發器示例](#example)。

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  提供另一種方式來指定要使用的服務匯流排帳戶。 建構函式會採用內含服務匯流排連接字串的應用程式設定名稱。 屬性可以套用在參數、方法或類別層級。 下列範例所示範的是類別層級與方法層級：

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

要使用的服務匯流排帳戶按以下順序決定：

* `ServiceBusTrigger` 屬性的 `Connection` 內容。
* `ServiceBusAccount` 屬性套用至與 `ServiceBusTrigger` 屬性相同的參數。
* `ServiceBusAccount` 屬性套用至該函式。
* `ServiceBusAccount` 屬性套用至該類別。
* "AzureWebJobsServiceBus" 應用程式設定。

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

C# 腳本不支援屬性。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="java"></a>[JAVA](#tab/java)

注釋`ServiceBusQueueTrigger`允許您創建在創建服務匯流排佇列消息時運行的函數。 可用的配置選項包括佇列名稱和連接字串名稱。

注釋`ServiceBusTopicTrigger`允許您指定主題和訂閱，以定位觸發函數的資料。

有關詳細資訊，請參閱觸發器[示例](#example)。

---

## <a name="configuration"></a>組態

下表介紹了您在*函數.json*檔和`ServiceBusTrigger`屬性中設置的綁定配置屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**型別** | n/a | 必須設定為 "serviceBusTrigger"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設定為 "in"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**名稱** | n/a | 代表函式程式碼中佇列或主題訊息的變數名稱。 |
|**佇列名稱**|**佇列名稱**|要監視的佇列名稱。  只有在監視佇列時設定 (不適用於主題)。
|**topicName**|**主題名稱**|要監視的主題名稱。 只有在監視主題時設定 (不適用於佇列)。|
|**訂閱名稱**|**訂閱名稱**|要監視的訂用帳戶名稱。 只有在監視主題時設定 (不適用於佇列)。|
|**連接**|**連接**|應用程式設定的名稱包含要用於此繫結的服務匯流排連接字串。 如果應用程式設定名稱是以 "AzureWebJobs" 開頭，您只能指定名稱的其餘部分。 例如，如果設置為`connection`"MyServiceBus"，則函數運行時將查找名為"AzureWebJobsMyServiceBus"的應用設置。 如果您將 `connection` 保留空白，則 Functions 執行階段會使用應用程式設定中名稱為 "AzureWebJobsServiceBus" 的預設服務匯流排連接字串。<br><br>若要取得連接字串，請遵循[取得管理認證](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)所示的步驟。 連接字串必須是用於服務匯流排命名空間，而不限於特定佇列或主題。 |
|**存取權限**|**存取**|連接字串的存取權限。 可用值為 `manage` 和 `listen`。 預設值是 `manage`，這表示 `connection` 已具備**管理**權限。 如果您使用沒有**管理**權限的連接字串，請將 `accessRights` 設定為 "listen"。 否則，Functions 執行階段在嘗試執行需要管理權限的作業時可能會失敗。 在 Azure 函數版本 2.x 及更高版本中，此屬性不可用，因為最新版本的服務匯流排 SDK 不支援管理操作。|
|**正在啟用會話**|**已啟用會話**|`true`如果連接到[會話感知](../service-bus-messaging/message-sessions.md)佇列或訂閱。 `false`否則，這是預設值。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

# <a name="c"></a>[C#](#tab/csharp)

佇列或主題消息可用於以下參數類型：

* `string` - 如果訊息是文字。
* `byte[]` - 適用於二進位資料。
* 自訂類型 - 如果訊息包含 JSON，Azure Functions 會嘗試將 JSON 資料還原序列化。
* `BrokeredMessage`- 使用 BrokeredMessage 為您提供非序列化消息[。 GetBody\<T>（）](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)方法。

這些參數類型適用于 Azure 函數版本 1.x;對於 2.x 和更高[`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message)版本，`BrokeredMessage`請使用 而不是 。

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

佇列或主題消息可用於以下參數類型：

* `string` - 如果訊息是文字。
* `byte[]` - 適用於二進位資料。
* 自訂類型 - 如果訊息包含 JSON，Azure Functions 會嘗試將 JSON 資料還原序列化。
* `BrokeredMessage`- 使用 BrokeredMessage 為您提供非序列化消息[。 GetBody\<T>（）](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)方法。

這些參數適用于 Azure 函數版本 1.x;對於 2.x 和更高[`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message)版本，`BrokeredMessage`請使用 而不是 。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

使用`context.bindings.<name from function.json>`訪問佇列或主題消息。 服務匯流排訊息會以字串或 JSON 物件的形式傳遞至函式。

# <a name="python"></a>[Python](#tab/python)

佇列消息可通過鍵入為`func.ServiceBusMessage`的參數對函數可用。 服務匯流排訊息會以字串或 JSON 物件的形式傳遞至函式。

# <a name="java"></a>[JAVA](#tab/java)

傳入的服務匯流排消息可通過 或`ServiceBusQueueMessage``ServiceBusTopicMessage`參數獲取。

[有關詳細資訊，請參閱示例](#example)。

---

## <a name="poison-messages"></a>毒資訊

無法在 Azure Functions 中控制或設定有害訊息處理。 服務匯流排會自行處理有害訊息。

## <a name="peeklock-behavior"></a> PeekLock 行為

Functions 執行階段會在 [PeekLock 模式](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)中收到訊息。 它會在函數成功完成時，於訊息中呼叫 `Complete`，或是在函數失敗時呼叫 `Abandon`。 如果函式執行時間較 `PeekLock` 逾時還長，只要函式正在執行中，就會自動更新鎖定。 

`maxAutoRenewDuration` 在 *host.json* 中是可設定的，其對應至 [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet)。 根據服務匯流排文件，此設定允許的上限為 5 分鐘，而您可以將 Functions 時間限制從預設的 5 分鐘增加至 10 分鐘。 您不會想對服務匯流排函式這麼做，因為您會超過服務匯流排更新限制。

## <a name="message-metadata"></a>消息中繼資料

服務匯流排觸發程序提供數個[中繼資料屬性](./functions-bindings-expressions-patterns.md#trigger-metadata)。 這些屬性可作為其他繫結中繫結運算式的一部分或程式碼中的參數使用。 這些屬性是[BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)類的成員。

|屬性|類型|描述|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|傳遞數目。|
|`DeadLetterSource`|`string`|無效信件來源。|
|`ExpiresAtUtc`|`DateTime`|到期時間 (UTC)。|
|`EnqueuedTimeUtc`|`DateTime`|加入佇列的時間 (UTC)。|
|`MessageId`|`string`|服務匯流排可用來識別重複訊息的使用者定義值 (如果已啟用)。|
|`ContentType`|`string`|發送方和接收方用於應用程式特定邏輯的內容類型識別碼。|
|`ReplyTo`|`string`|回覆佇列位址。|
|`SequenceNumber`|`Int64`|由服務匯流排指派給訊息的唯一編號。|
|`To`|`string`|傳送位址。|
|`Label`|`string`|特定于應用程式的標籤。|
|`CorrelationId`|`string`|相互關連識別碼。|

請參閱稍早在本文中使用這些屬性的[程式碼範例](#example)。

## <a name="next-steps"></a>後續步驟

- [從 Azure 函數發送 Azure 服務匯流排消息（輸出綁定）](./functions-bindings-service-bus-output.md)
