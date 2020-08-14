---
title: Azure Functions Twilio 繫結
description: 了解如何搭配使用 Twilio 繫結與 Azure Functions。
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: devx-track-csharp, H1Hack27Feb2017
ms.openlocfilehash: 9a3ffeb4ff58cc4af2309268f1a95cd9d354de8b
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206638"
---
# <a name="twilio-binding-for-azure-functions"></a>Azure Functions 的 Twilio 繫結

本文說明如何在 Azure Functions 中使用 [Twilio](https://www.twilio.com/) 繫結傳送文字簡訊。 Azure Functions 支援 Twilio 的輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>套件 - Functions 1.x

[Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet 套件 1.x 版中提供了 Twilio 繫結。 套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) GitHub 存放庫中。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>封裝-函數2.x 和更新版本

[Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet 套件 3.x 版中提供了 Twilio 繫結。 套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) GitHub 存放庫中。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

<a id="example"></a>

## <a name="example---functions-2x-and-higher"></a>範例-函數2.x 和更新版本

# <a name="c"></a>[C#](#tab/csharp)

下列範例顯示可在受到佇列訊息觸發時傳送文字簡訊的 [C# 函式](functions-dotnet-class-library.md)。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;
namespace TwilioQueueOutput
{
    public static class QueueTwilio
    {
        [FunctionName("QueueTwilio")]
        [return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
        public static CreateMessageOptions Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
        ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed: {order}");

            var message = new CreateMessageOptions(new PhoneNumber(order["mobileNumber"].ToString()))
            {
                Body = $"Hello {order["name"]}, thanks for your order!"
            };

            return message;
        }
    }
}
```

本範例會使用含有方法傳回值的 `TwilioSms` 屬性。 替代方式是使用具有 `out CreateMessageOptions` 參數或 `ICollector<CreateMessageOptions>` 或 `IAsyncCollector<CreateMessageOptions>` 參數的屬性。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

下列範例說明 *function.json* 檔案中的 Twilio 輸出繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 此函式會使用 `out` 參數來傳送文字簡訊。

以下是 *function.json* 檔案中的繫結資料：

function.json 範例：

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

以下是 C# 指令碼：

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    message.Body = msg;
}
```

您無法使用非同步程式碼中的 out 參數。 以下是非同步的 C# 指令碼範例：

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    smsText.Body = msg;

    await message.AddAsync(smsText);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例說明 *function.json* 檔案中的 Twilio 輸出繫結，以及使用此繫結的 [JavaScript 函式](functions-reference-node.md)。

以下是 *function.json* 檔案中的繫結資料：

function.json 範例：

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. The "To" number 
    // must be specified in code. 
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下列範例顯示如何使用輸出系結來傳送 SMS 訊息，如下列 *function.js*所定義。

```json
    {
      "type": "twilioSms",
      "name": "twilioMessage",
      "accountSidSetting": "TwilioAccountSID",
      "authTokenSetting": "TwilioAuthToken",
      "from": "+1XXXXXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }
```

您可以將已序列化的 JSON 物件傳遞給 `func.Out` 參數，以傳送 SMS 訊息。

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, twilioMessage: func.Out[str]) -> func.HttpResponse:

    message = req.params.get('message')
    to = req.params.get('to')

    value = {
      "body": message,
      "to": to
    }

    twilioMessage.set(json.dumps(value))

    return func.HttpResponse(f"Message sent")
```

# <a name="java"></a>[Java](#tab/java)

下列範例顯示如何使用 [TwilioSmsOutput](/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) 注釋來傳送 SMS 訊息。 `to` `from` 屬性定義中需要、和的值， `body` 即使您以程式設計方式覆寫它們也一樣。

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class TwilioOutput {

    @FunctionName("TwilioOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST },
                authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @TwilioSmsOutput(
                name = "twilioMessage",
                accountSid = "AzureWebJobsTwilioAccountSID",
                authToken = "AzureWebJobsTwilioAuthToken",
                to = "+1XXXXXXXXXX",
                body = "From Azure Functions",
                from = "+1XXXXXXXXXX") OutputBinding<String> twilioMessage,
            final ExecutionContext context) {

        String message = request.getQueryParameters().get("message");
        String to = request.getQueryParameters().get("to");

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"body\": \"%s\",")
            .append("\"to\": \"%s\"")
            .append("}");

        final String body = String.format(builder.toString(), message, to);

        twilioMessage.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Message sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>屬性和註釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用 [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) 屬性。

如需可設定的屬性內容相關資訊，請參閱[設定](#configuration)。 以下是方法簽章中的 `TwilioSms` 屬性範例：

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

如需完整範例，請參閱 [c # 範例](#example)。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 指令碼不支援屬性。

# <a name="java"></a>[Java](#tab/java)

將 [TwilioSmsOutput](/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) 注釋放在 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) `T` 可能是任何原生 JAVA 類型（例如 `int` 、 `String` 、 `byte[]` 或 POJO 類型）的參數上。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `TwilioSms` 屬性。

| v1 function.json 屬性 | v2 function.json 屬性 | 屬性內容 |描述|
|---------|---------|---------|----------------------|
|**type**|**type**| 必須設為 `twilioSms`。|
|**direction**|**direction**| 必須設為 `out`。|
|**name**|**name**| 用於 Twilio 簡訊文字訊息之函式程式碼中的變數名稱。 |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| 此值必須設定為保留您 Twilio 帳戶 Sid () 的應用程式設定名稱 `TwilioAccountSid` 。 若未設定，預設的應用程式設定名稱是 "AzureWebJobsTwilioAccountSid"。 |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| 此值必須設定為保留您 Twilio 驗證權杖 () 的應用程式設定名稱 `TwilioAccountAuthToken` 。 若未設定，預設的應用程式設定名稱是 "AzureWebJobsTwilioAuthToken"。 |
|**to**| N/A - 在程式碼中指定 | **若要**| 此值設定為簡訊文字傳送至的電話號碼。|
|**from**|**from** | **From**| 此值設定為從中傳送簡訊文字的電話號碼。|
|**body**|**body** | **本文**| 如果您不需要在函式程式碼中動態設定 SMS 文字訊息，則此值可以用來硬式編碼 SMS 文字訊息。 |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
