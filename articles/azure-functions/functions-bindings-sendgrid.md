---
title: Azure Functions SendGrid 繫結
description: Azure Functions SendGrid 繫結參考。
author: craigshoemaker
ms.topic: reference
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 9ed2b81c12c698822b9542bb6903189c865b572b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277462"
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions SendGrid 繫結

本文說明如何在 Azure Functions 中使用 [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) 繫結傳送電子郵件。 Azure Functions 支援適用於 SendGrid 的輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>套件 - Functions 1.x

[Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet 套件版本 2.x 中提供了 SendGrid 繫結。 套件的原始程式碼位於 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) GitHub 存放庫中。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>封裝-函數2.x 和更新版本

[Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet 套件版本 3.x 中提供了 SendGrid 繫結。 套件的原始程式碼位於 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) GitHub 存放庫中。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下列範例說明的 [C# 函式](functions-dotnet-class-library.md)，可使用服務匯流排佇列觸發程序和 SendGrid 輸出繫結。

### <a name="synchronous"></a>同步

```cs
using SendGrid.Helpers.Mail;

...

[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

### <a name="asynchronous"></a>非同步的

```cs
using SendGrid.Helpers.Mail;

...

[FunctionName("SendEmail")]
public static async void Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
}

public class OutgoingEmail
{
 public string To { get; set; }
 public string From { get; set; }
 public string Subject { get; set; }
 public string Body { get; set; }
}
```

若您在名為「AzureWebJobsSendGridApiKey」的應用程式設定中有 API 金鑰，則可以省略設定屬性的 `ApiKey` 內容。

# <a name="c-script"></a>[C # 腳本](#tab/csharp-script)

下列範例說明 *function.json* 檔案中的 SendGrid 輸出繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。

以下是 *function.json* 檔案中的繫結資料：

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
        }
    ]
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 C# 指令碼程式碼：

```csharp
#r "SendGrid"

using System;
using SendGrid.Helpers.Mail;
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例說明 *function.json* 檔案中的 SendGrid 輸出繫結，以及使用此繫結的 [JavaScript 函式](functions-reference-node.md)。

以下是 *function.json* 檔案中的繫結資料：

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context, input) {
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

# <a name="python"></a>[Python](#tab/python)

下列範例顯示 HTTP 觸發的函式，它會使用 SendGrid 系結來傳送電子郵件。 您可以在系結設定中提供預設值。 例如，在函式中設定了*from*電子郵件地址 *。* 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "sendGrid",
      "name": "sendGridMessage",
      "direction": "out",
      "apiKey": "SendGrid_API_Key",
      "from": "sender@contoso.com"
    }
  ]
}
```

下列函式會顯示如何提供選擇性屬性的自訂值。

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, sendGridMessage: func.Out[str]) -> func.HttpResponse:

    value = "Sent from Azure Functions"

    message = {
        "personalizations": [ {
          "to": [{
            "email": "user@contoso.com"
            }]}],
        "subject": "Azure Functions email with SendGrid",
        "content": [{
            "type": "text/plain",
            "value": value }]}

    sendGridMessage.set(json.dumps(message))

    return func.HttpResponse(f"Sent")
```

# <a name="java"></a>[Java](#tab/java)

下列範例會使用 JAVA `@SendGridOutput`函式執行時間連結[庫](/java/api/overview/azure/functions/runtime)中的注釋，以使用 SendGrid 輸出系結來傳送電子郵件。

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerSendGrid {

    @FunctionName("HttpTriggerSendGrid")
    public HttpResponseMessage run(

        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.GET, HttpMethod.POST },
            authLevel = AuthorizationLevel.FUNCTION)
                HttpRequestMessage<Optional<String>> request,

        @SendGridOutput(
            name = "message",
            dataType = "String",
            apiKey = "SendGrid_API_Key",
            to = "user@contoso.com",
            from = "sender@contoso.com",
            subject = "Azure Functions email with SendGrid",
            text = "Sent from Azure Functions")
                OutputBinding<String> message,

        final ExecutionContext context) {

        final String toAddress = "user@contoso.com";
        final String value = "Sent from Azure Functions";

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"personalizations\": [{ \"to\": [{ \"email\": \"%s\"}]}],")
            .append("\"content\": [{\"type\": \"text/plain\", \"value\": \"%s\"}]")
            .append("}");

        final String body = String.format(builder.toString(), toAddress, value);

        message.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>屬性和注釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用 [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) 屬性。

如需可設定的屬性內容相關資訊，請參閱[設定](#configuration)。 以下是方法簽章中的 `SendGrid` 屬性範例：

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

如需完整範例，請參閱 [C# 範例](#example)。

# <a name="c-script"></a>[C # 腳本](#tab/csharp-script)

C # 腳本不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="java"></a>[Java](#tab/java)

[SendGridOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java)注釋可讓您藉由提供設定值，以宣告方式設定 SendGrid 系結。 如需詳細資訊，請參閱[範例](#example)[和設定](#configuration)章節。

---

## <a name="configuration"></a>設定

下表列出*函數. json*檔案和`SendGrid`屬性/注釋中可用的系結設定屬性。

| *function. json*屬性 | 屬性/注釋屬性 | 描述 | 選擇性 |
|--------------------------|-------------------------------|-------------|----------|
| type |n/a| 必須設為 `sendGrid`。| 否 |
| direction |n/a| 必須設為 `out`。| 否 |
| 名稱 |n/a| 函數程式碼中用於要求或要求主體的變數名稱。 當只有一個傳回值時，此值為 `$return`。 | 否 |
| apiKey | ApiKey | 包含您 API 金鑰的應用程式設定名稱。 如果未設定，預設的應用程式設定名稱是*AzureWebJobsSendGridApiKey*。| 否 |
| to| 至 | 收件者的電子郵件地址。 | 是 |
| 從| 從 | 寄件者的電子郵件地址。 |  是 |
| subject| 主體 | 電子郵件的主旨。 | 是 |
| text| Text | 電子郵件內容。 | 是 |

選擇性屬性可能會在系結中定義預設值，並以程式設計方式新增或覆寫。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

本節說明2.x 版和更高版本中可供此系結使用的全域設定。 下面的範例 host. json 檔案僅包含此系結的2.x 版和設定。 如需有關2.x 版和更早版本中的全域設定的詳細資訊，請參閱[Azure Functions 的 host. json 參考](functions-host-json.md)。

> [!NOTE]
> 有關 Functions 1.x 中 host.json 的參考，請參閱[適用於 Azure Functions 1.x 的 host.json 參考](functions-host-json-v1.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|從|n/a|所有函式的寄件者電子郵件地址。| 


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
