---
title: Azure Functions SignalR Service 觸發程式系結
description: 瞭解如何從 Azure Functions 傳送 SignalR Service 訊息。
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: 2482a26987ec142880acc51bf470d844655b6e3f
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763503"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Azure Functions 的 SignalR Service 觸發程式系結

使用 *SignalR* 觸發程式系結來回應從 Azure SignalR Service 傳送的訊息。 觸發函式時，傳遞至函式的訊息會剖析為 json 物件。

在 SignalR Service 無伺服器模式中，SignalR Service 使用 [上游](../azure-signalr/concept-upstream.md) 功能將訊息從用戶端傳送至函數應用程式。 函數應用程式會使用 SignalR Service 觸發程式系結來處理這些訊息。 一般架構如下所示： :::image type="content" source="media/functions-bindings-signalr-service/signalr-trigger.png" alt-text="SignalR 觸發程式架構":::

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](functions-bindings-signalr-service.md)。

## <a name="example"></a>範例

下列範例顯示的函式會使用觸發程式系結來接收訊息並記錄訊息。

# <a name="c"></a>[C#](#tab/csharp)

C # 的 SignalR Service 觸發程式系結有兩種程式設計模型。 以類別為基礎的模型和傳統模型。 以類別為基礎的模型可提供一致的 SignalR 伺服器端程式設計體驗。 和傳統模型可提供更大的彈性，並與其他函數系結類似。

### <a name="with-class-based-model"></a>使用以類別為基礎的模型

請參閱以 [類別為基礎的模型](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model) 以取得詳細資料。

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

傳統模型遵守由 c # 開發的 Azure Function 慣例。 如果您不熟悉它，可以從 [檔](./functions-dotnet-class-library.md)中學習。

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>使用屬性 `[SignalRParameter]` 來簡化 `ParameterNames`

使用 `ParameterNames` `SignalRParameter` 時，是為了達成相同目的而提供。

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

## <a name="configuration"></a>設定

### <a name="signalrtrigger"></a>SignalRTrigger

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `SignalRTrigger` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type**| n/a | 必須設為 `SignalRTrigger`。|
|**direction**| n/a | 必須設為 `in`。|
|**name**| n/a | 函數程式碼中用於觸發程式調用內容物件的變數名稱。 |
|**hubName**|**HubName**| 此值必須設定為要觸發之函式的 SignalR 中樞名稱。|
|**類別**|**類別**| 此值必須設定為要觸發之函式的訊息類別。 類別可以是下列其中一個值： <ul><li>**連接**：包括 *已連線和已**中斷* 連線的事件</li><li>**訊息**：包括 *連接* 類別中的其他所有事件（這些事件除外）</li></ul> |
|**event**|**事件**| 必須將此值設定為要觸發之函式的訊息事件。 針對 *訊息* 類別目錄，事件是用戶端傳送之 [調用訊息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的 *目標*。 針對 *連接* 類別，則只會使用 *已連線* 和已 *中斷* 連線。 |
|**parameterNames**|**ParameterNames**|  (選擇性) 系結至參數的名稱清單。 |
|**connectionStringSetting**|**ConnectionStringSetting**| 包含 SignalR Service 連接字串 (預設值為 "AzureSignalRConnectionString") 的應用程式設定名稱 |

## <a name="payload"></a>Payload

觸發程序輸入類型會宣告為 `InvocationContext` 或自訂類型。 如果您選擇 `InvocationContext` 取得要求內容的完整存取權。 針對自訂的類型，執行階段會嘗試剖析 JSON 要求本文來設定物件屬性。

### <a name="invocationcontext"></a>InvocationCoNtext

InvocationCoNtext 包含從 SignalR Service 傳送訊息的所有內容。

|InvocationCoNtext 中的屬性 | 描述|
|------------------------------|------------|
|引數| 適用于 *訊息* 類別。 包含 [調用訊息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的 *引數*|
|[錯誤]| 適用于已 *中斷* 連線的事件。 如果連接已關閉而沒有錯誤，或包含錯誤訊息，則可以是空的。|
|集線器| 訊息所屬的中樞名稱。|
|類別| 訊息的分類。|
|事件| 訊息的事件。|
|ConnectionId| 傳送訊息之用戶端的連接識別碼。|
|UserId| 傳送訊息之用戶端的使用者身分識別。|
|標題| 要求的標頭。|
|查詢| 用戶端連接到服務時的要求查詢。|
|宣告| 用戶端的宣告。|

## <a name="using-parameternames"></a>使用 `ParameterNames`

中的屬性可 `ParameterNames` `SignalRTrigger` 讓您將調用訊息的引數系結至函數的參數。 您所定義的名稱可以當做其他系結中系結 [運算式](../azure-functions/functions-bindings-expressions-patterns.md) 的一部分，或當做程式碼中的參數使用。 這可提供您更方便的方法來存取的引數 `InvocationContext` 。

假設您有一個 JavaScript SignalR 用戶端嘗試 `broadcast` 在 Azure 函式中叫用具有兩個引數的方法 `message1` `message2` 。

```javascript
await connection.invoke("broadcast", message1, message2);
```

設定之後 `parameterNames` ，您定義的名稱會分別對應至用戶端上傳送的引數。 

```cs
[SignalRTrigger(parameterNames: new string[] {"arg1, arg2"})]
```

然後， `arg1` 將包含的內容 `message1` ，而且 `arg2` 將包含的內容 `message2` 。


### <a name="remarks"></a>備註

針對參數系結，順序很重要。 如果您使用 `ParameterNames` ，則中的順序會 `ParameterNames` 與您在用戶端中叫用的引數順序相符。 如果您 `[SignalRParameter]` 在 c # 中使用屬性，Azure 函數方法中的引數順序會與用戶端中的引數順序相符。

`ParameterNames` 和屬性 `[SignalRParameter]` **不能** 同時使用，否則您將會收到例外狀況。

## <a name="signalr-service-integration"></a>SignalR Service 整合

當您使用 SignalR Service 觸發程式系結時，SignalR Service 需要 URL 來存取函數應用程式。 URL 應該在 SignalR Service 端的 **上游設定** 中設定。 

:::image type="content" source="../azure-signalr/media/concept-upstream/upstream-portal.png" alt-text="上游入口網站":::

使用 SignalR Service 觸發程式時，URL 可以簡單且格式化，如下所示：

```http
<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>
```

您 `Function_App_URL` 可以在函式應用程式的 [總覽] 頁面上找到，並 `API_KEY` 由 Azure Function 產生。 您可以從函 `API_KEY` 式 `signalr_extension` 應用程式的 [ **應用程式金鑰** ] 分頁中取得。
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="API 金鑰":::

如果您想要同時使用一個以上的函式應用程式與一個 SignalR Service，上游也可以支援複雜的路由規則。 在 [上游設定](../azure-signalr/concept-upstream.md)中尋找更多詳細資料。

## <a name="step-by-step-sample"></a>逐步執行範例

您可以遵循 GitHub 中的範例，使用 SignalR Service 觸發程式系結和上游功能，在函數應用程式上部署聊天室： [雙向聊天室範例](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)

## <a name="next-steps"></a>後續步驟

* [使用 Azure SignalR Service 來開發與設定 Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md)
* [SignalR Service 觸發程式系結範例](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)
