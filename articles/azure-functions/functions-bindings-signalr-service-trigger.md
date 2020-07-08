---
title: Azure Functions SignalR Service 觸發程式系結
description: 瞭解如何從 Azure Functions 傳送 SignalR Service 訊息。
author: chenyl
ms.topic: reference
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: c2ad9b6c4410a62d5652050406e05be4cde5fab0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830701"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Azure Functions 的 SignalR Service 觸發程式系結

使用*SignalR*觸發程式系結來回應從 Azure SignalR Service 傳送的訊息。 觸發函式時，傳遞至函式的訊息會剖析為 json 物件。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](functions-bindings-signalr-service.md)。

## <a name="example"></a>範例

下列範例顯示的函式會使用觸發程式系結來接收訊息，並記錄訊息。

# <a name="c"></a>[C#](#tab/csharp)

C # 的 SignalR Service 觸發程式系結有兩種程式設計模型。 以類別為基礎的模型和傳統模型。 以類別為基礎的模型可提供一致的 SignalR 伺服器端程式設計體驗。 和傳統模型提供更大的彈性，而且與其他函數系結類似。

### <a name="with-class-based-model"></a>使用以類別為基礎的模型

如需詳細資訊，請參閱以[類別為基礎的模型](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model)。

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>使用傳統模型

傳統模型遵守由 c # 所開發的 Azure Function 慣例。 如果您不熟悉它，可以從[檔](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-class-library)中學習。

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>使用屬性 `[SignalRParameter]` 來簡化`ParameterNames`

由於使用很麻煩 `ParameterNames` ，因此 `SignalRParameter` 會提供來達到相同的目的。

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

以下是 *function.json* 檔案中的繫結資料：

function.json 範例：

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

以下是 c # 腳本程式碼：

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下是 *function.json* 檔案中的繫結資料：

function.json 範例：

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

以下是 *function.json* 檔案中的繫結資料：

function.json 範例：

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

以下是 Python 程式碼：

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>組態

### <a name="signalrtrigger"></a>SignalRTrigger

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `SignalRTrigger` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type**| n/a | 必須設為 `SignalRTrigger`。|
|**direction**| n/a | 必須設為 `in`。|
|**name**| n/a | 函數程式碼中用於觸發程式調用內容物件的變數名稱。 |
|**hubName**|**HubName**| 這個值必須設定為要觸發之函式的 SignalR 中樞名稱。|
|**類別**|**類別**| 這個值必須設定為要觸發之函式的訊息類別。 類別可以是下列其中一個值： <ul><li>**連接**：包含*已連線*和*中斷連接*的事件</li><li>**訊息**：包括 [*連接*] 類別中的所有其他事件除外</li></ul> |
|**event**|**事件**| 這個值必須設定為要觸發之函式的訊息事件。 針對*訊息*類別目錄，事件是用戶端傳送的[調用訊息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的*目標*。 針對連線類別，只會使用 [*已連接*] 和 [已*中斷**連接]* 。 |
|**parameterNames**|**ParameterNames**| 選擇性系結至參數的名稱清單。 |
|**connectionStringSetting**|**ConnectionStringSetting**| 包含 SignalR Service 連接字串 (預設值為 "AzureSignalRConnectionString") 的應用程式設定名稱 |

## <a name="payload"></a>Payload

觸發程序輸入類型會宣告為 `InvocationContext` 或自訂類型。 如果您選擇 `InvocationContext` 取得要求內容的完整存取權。 針對自訂的類型，執行階段會嘗試剖析 JSON 要求本文來設定物件屬性。

### <a name="invocationcontext"></a>InvocationCoNtext

InvocationCoNtext 包含訊息從 SignalR Service 傳送的所有內容。

|InvocationCoNtext 中的屬性 | Description|
|------------------------------|------------|
|引數| 適用于*訊息*類別。 包含[調用訊息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的*引數*|
|錯誤| 適用于已*中斷*連線的事件。 如果連接已關閉但沒有錯誤，或包含錯誤訊息，則它可以是空的。|
|中樞| 訊息所屬的中樞名稱。|
|類別| 訊息的分類。|
|事件| 訊息的事件。|
|ConnectionId| 傳送訊息之用戶端的連接識別碼。|
|UserId| 傳送訊息之用戶端的使用者身分識別。|
|headers| 要求的標頭。|
|查詢| 用戶端連接到服務時的要求查詢。|
|Claims| 用戶端的宣告。|

## <a name="using-parameternames"></a>使用 `ParameterNames`

中的屬性可 `ParameterNames` `SignalRTrigger` 讓您將調用訊息的引數系結至函式的參數。 這可讓您更方便的方式存取的引數 `InvocationContext` 。

假設您有一個 JavaScript SignalR 用戶端嘗試 `broadcast` 在 Azure 函式中使用兩個引數來叫用方法。

```javascript
await connection.invoke("broadcast", message1, message2);
```

您可以從參數存取這兩個引數，以及使用為其指派參數的類型 `ParameterNames` 。

### <a name="remarks"></a>備註

對於參數系結，順序很重要。 如果您使用的是 `ParameterNames` ，中的順序會 `ParameterNames` 符合您在用戶端中叫用之引數的順序。 如果您 `[SignalRParameter]` 在 c # 中使用屬性，Azure 函式方法中的引數順序會符合用戶端中引數的順序。

`ParameterNames`和屬性 `[SignalRParameter]` **不能**同時使用，否則您會收到例外狀況。

## <a name="send-messages-to-signalr-service-trigger-binding"></a>將訊息傳送至 SignalR Service 觸發程式系結

Azure 函式會產生 SignalR Service 觸發程式系結的 URL，其格式如下：

```http
https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>
```

`API_KEY`是由 Azure Function 所產生。 `API_KEY`當您使用 SignalR Service 觸發程式系結時，您可以從 Azure 入口網站取得。
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="API 金鑰":::

您應該在 `UrlTemplate` SignalR Service 的上游設定中，于中設定此 URL。

## <a name="next-steps"></a>後續步驟

* [使用 Azure SignalR Service 來開發與設定 Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md)
* [SignalR Service 觸發程式系結範例](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples/bidirectional-chat)