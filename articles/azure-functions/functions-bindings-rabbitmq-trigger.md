---
title: Azure Functions 的 RabbitMQ 觸發程式
description: 瞭解如何在建立 RabbitMQ 訊息時執行 Azure 函數。
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 4ba19fdf700790d89fe04867985fb803c3b0a2fc
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760396"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>Azure Functions 總覽的 RabbitMQ 觸發程式

> [!NOTE]
> RabbitMQ 系結僅在 **Premium 和專用** 方案上受到完整支援。 不支援耗用量。

使用 RabbitMQ 觸發程式來回應 RabbitMQ 佇列中的訊息。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](functions-bindings-rabbitmq.md)。

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下列範例顯示的 [c #](functions-dotnet-class-library.md) 函式會將 RabbitMQ 訊息讀取並記錄為 [RabbitMQ 事件](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html)：

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

下列範例顯示如何將訊息讀取為 POCO。

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }

    public class RabbitMQTriggerCSharp{
        [FunctionName("RabbitMQTriggerCSharp")]
        public static void RabbitMQTrigger_BasicDeliverEventArgs(
            [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass pocObj,
            ILogger logger
            )
        {
            logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {pocObj}");
        }
    }
}
```

和 Json 物件一樣，如果訊息未正確格式化為 c # 物件，就會發生錯誤。 如果是，則會系結至變數 pocObj，可用於所需的任何內容。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

下列範例顯示在檔案 *function.js* 中的 RabbitMQ 觸發程式系結，以及使用此系結的 [c # 腳本](functions-reference-csharp.md) 函式。 函式會讀取並記錄 RabbitMQ 訊息。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

以下是 C# 指令碼程式碼：

```C#
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例顯示在檔案 *function.js* 中的 RabbitMQ 觸發程式系結，以及使用此系結的 [JavaScript 函數](functions-reference-node.md) 。 函式會讀取並記錄 RabbitMQ 訊息。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

以下是 JavaScript 指令碼程式碼：

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

下列範例示範如何透過觸發程式讀取 RabbitMQ 佇列訊息。

RabbitMQ 系結是在 [*類型*] 設定為的 *function.js* 中定義 `RabbitMQTrigger` 。

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

下列 JAVA 函數會使用 `@RabbitMQTrigger` [java RabbitMQ](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) 型別中的注釋來描述 RabbitMQ 佇列觸發程式的設定。 函式會抓取放置在佇列中的訊息，並將它新增至記錄。

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>屬性和註釋

# <a name="c"></a>[C#](#tab/csharp)

在 [c # 類別庫](functions-dotnet-class-library.md)中，請使用 [RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs) 屬性。

以下是方法簽章 `RabbitMQTrigger` 中的屬性：

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
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

`RabbitMQTrigger`批註可讓您建立在建立 RabbitMQ 訊息時執行的函數。 可用的設定選項包括佇列名稱和連接字串名稱。 如需其他參數詳細資料，請流覽 [RabbitMQTrigger JAVA 批註](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java)。

如需詳細資訊，請參閱觸發程式 [範例](#example) 。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `RabbitMQTrigger` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設定為 "RabbitMQTrigger"。|
|**direction** | n/a | 必須設定為 "in"。|
|**name** | n/a | 代表函式程式碼中佇列的變數名稱。 |
|**queueName**|**QueueName**| 要接收訊息的佇列名稱。 |
|**主機 名**|**HostName**|如果使用 ConnectStringSetting) ，則會忽略 ( <br>佇列的主機名稱 (例如： 10.26.45.210) |
|**userNameSetting**|**UserNameSetting**|如果使用 ConnectionStringSetting) ，則會忽略 ( <br>應用程式設定的名稱，其中包含要存取佇列的使用者名稱。 例如 UserNameSetting： "% < UserNameFromSettings >%"|
|**passwordSetting**|**PasswordSetting**|如果使用 ConnectionStringSetting) ，則會忽略 ( <br>應用程式設定的名稱，其中包含用來存取佇列的密碼。 例如 PasswordSetting： "% < PasswordFromSettings >%"|
|**connectionStringSetting**|**ConnectionStringSetting**|包含 RabbitMQ 訊息佇列連接字串之應用程式設定的名稱。 請注意，如果您直接指定連接字串，而不是透過 local.settings.js中的應用程式設定，則觸發程式將無法運作。  (例如： In *function.js*： connectionStringSetting： "rabbitMQConnection" <br> 在 *local.settings.json*： "rabbitMQConnection"： "< ActualConnectionstring >" ) |
|**port**|**通訊埠**|如果使用 ConnectionStringSetting) 取得或設定使用的埠， (會忽略。 預設值為0，指向 rabbitmq 用戶端的預設通訊埠設定：5672。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

# <a name="c"></a>[C#](#tab/csharp)

預設訊息類型為 [RabbitMQ 事件](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html)，而 `Body` RabbitMQ 事件的屬性可以讀取為下列類型：

* `An object serializable as JSON` -訊息會以有效的 JSON 字串傳遞。
* `string`
* `byte[]`
* `POCO` -訊息會格式化為 c # 物件。 如需完整範例，請參閱 c # [範例](#example)。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

預設訊息類型為 [RabbitMQ 事件](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html)，而 `Body` RabbitMQ 事件的屬性可以讀取為下列類型：

* `An object serializable as JSON` -訊息會以有效的 JSON 字串傳遞。
* `string`
* `byte[]`
* `POCO` -訊息會格式化為 c # 物件。 如需完整範例，請參閱 c # 腳本 [範例](#example)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

佇列訊息可透過內容系結取得。<NAME> 其中 <NAME> 符合 function.js中定義的名稱。 如果承載是 JSON，此值會還原序列化為物件。

# <a name="python"></a>[Python](#tab/python)

請參閱 Python [範例](#example)。

# <a name="java"></a>[Java](#tab/java)

請參閱 JAVA [屬性和批註](#attributes-and-annotations)。

---

## <a name="dead-letter-queues"></a>無效信件佇列
無法從 RabbitMQ 觸發程式控制或設定無效信件佇列和交換。  若要使用無效信件佇列，請預先設定 RabbitMQ 中觸發程式所使用的佇列。 請參閱 [RabbitMQ 檔](https://www.rabbitmq.com/dlx.html)。

## <a name="hostjson-settings"></a>host.json 設定

本節說明2.x 版和更高版本中此系結的可用通用設定。 下列檔案的範例 *host.js* 只包含此系結的設定。 如需有關全域設定的詳細資訊，請參閱 [ Azure Functions 版本的參考host.js](functions-host-json.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------|
|prefetchCount|30|取得或設定訊息接收者可以同時要求並快取的訊息數目。|
|queueName|n/a| 要接收訊息的佇列名稱。|
|connectionString|n/a|RabbitMQ 訊息佇列連接字串。 請注意，直接在這裡指定連接字串，而不是透過應用程式設定。|
|連接埠|0|如果使用 connectionString) 取得或設定使用的埠， (會忽略。 預設值為0，指向 rabbitmq 用戶端的預設通訊埠設定：5672。|

## <a name="local-testing"></a>本機測試

> [!NOTE]
> ConnectionString 會優先于 "hostName"、"userName" 和 "password"。 如果全部都已設定，connectionString 會覆寫另二個。

如果您在本機測試時沒有連接字串，您應該在 *host.js* 的 "rabbitMQ" 區段中設定 "hostName" 設定和 "userName" 和 "password" （如果適用）：

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "userNameSetting",
            "password": "passwordSetting"
        }
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------|
|hostName|n/a|如果使用 connectionString) ，則會忽略 ( <br>佇列的主機名稱 (例如： 10.26.45.210) |
|userName|n/a|如果使用 connectionString) ，則會忽略 ( <br>存取佇列的名稱 |
|密碼|n/a|如果使用 connectionString) ，則會忽略 ( <br>用來存取佇列的密碼|


## <a name="enable-runtime-scaling"></a>啟用執行時間調整

為了讓 RabbitMQ 觸發程式可以擴充至多個實例，必須啟用 **執行時間調整監視** 設定。 

在入口網站中，您可以在函式  >  應用程式的 [**設定函數執行時間設定**] 下找到此設定。

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

在 CLI 中，您可以使用下列命令來啟用 **執行時間調整監視** ：

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

## <a name="monitoring-rabbitmq-endpoint"></a>監視 RabbitMQ 端點
若要監視特定 RabbitMQ 端點的佇列和交換：

* 啟用 [RabbitMQ 管理外掛程式](https://www.rabbitmq.com/management.html)
* 流覽至 HTTP：//{node-hostname}：15672，並使用您的使用者名稱和密碼登入。

## <a name="next-steps"></a>後續步驟

- [傳送 RabbitMQ 訊息 Azure Functions (輸出系結) ](./functions-bindings-rabbitmq-output.md)
