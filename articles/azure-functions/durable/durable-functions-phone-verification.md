---
title: 長期函式中的人為互動和逾時 - Azure
description: 了解如何處理 Azure Functions 之長期函式延伸模組中的人為互動和逾時。
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4e0f71369bc02fdce5625d9c74e1d52264ed86be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335752"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>長期函式中的人為互動 - 電話驗證範例

這個範例會示範如何建置[長期函式](durable-functions-overview.md)協調流程，其中牽涉到人為互動。 每當在自動化程序中牽涉到真人，處理程序必須能夠傳送通知給人員，並以非同步方式接收回應。 它也必須允許人員沒有空的可能性。 (此最後一個部分就是逾時變得重要的地方。)

這個範例會實作以 SMS 為基礎的電話驗證系統。 這些類型的流量通常會在驗證客戶的電話號碼時，或針對多重要素驗證 (MFA) 使用。 這是一個強有力的示例，因為整個實現是使用幾個小函數完成的。 不需要外部資料存放區，例如資料庫。

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>案例概觀

電話驗證是用來驗證您應用程式的使用者不是濫發垃圾郵件者，而且確實是他們所聲稱的人。 多重要素驗證是保護使用者帳戶免於駭客入侵的常見使用案例。 實作您自己的電話驗證的挑戰，是它需要與真人的**可設定狀態互動**。 終端使用者通常會提供一些代碼 (例如，4 位數數字)，並且必須在**合理的時間量**之內回應。

一般而言，Azure Functions 是無狀態 (如同其他平台上的其他許多雲端端點)，所以這些互動類型會明確牽涉到在外部的資料庫或其他某些永續性存放區中管理狀態。 此外，互動必須細分成可以協調在一起的多個函式。 例如，您需要至少一個函式以決定程式碼、保存在某處，以及將它傳送到使用者的電話。 而且您還需要至少一個其他的函式，以接收來自使用者的回應，並且以某種方式將其對應回原始函式呼叫，以進行程式碼驗證。 逾時也是確保安全性的重要層面。 它可以很快變得相當複雜。

當您使用長期函式時，此案例的複雜性就會大幅減少。 如同您在這個範例所見，協調器函式可以輕鬆地管理可設定狀態互動，不需要涉及任何外部資料存放區。 因為協調器函式是「長期」的**，所以這些互動流量也非常可靠。

## <a name="configuring-twilio-integration"></a>設定 Twilio 整合

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>函式

本文會逐步解說範例應用程式中的下列函式：

* `E4_SmsPhoneVerification`：執行電話驗證過程（包括管理超時和重試）的[協調器函數](durable-functions-bindings.md#orchestration-trigger)。
* `E4_SendSmsChallenge`：通過文本消息發送代碼[的活動函數](durable-functions-bindings.md#activity-trigger)。

### <a name="e4_smsphoneverification-orchestrator-function"></a>E4_SmsPhoneVerification協調器功能

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> 起初可能不明顯，不過這個協調器函式完全具有決定性。 它是確定性的，`CurrentUtcDateTime`因為屬性用於計算計時器過期時間，並且它在協調器代碼中此時每次重播時返回相同的值。 此行為對於確保每次重複調用`winner``Task.WhenAny`時的相同結果非常重要。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

**E4_SmsPhoneVerification** 函式會針對協調器函式使用標準 function.json**。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

以下是實作函式的程式碼：

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> 起初可能不明顯，不過這個協調器函式完全具有決定性。 它是確定性的，`currentUtcDateTime`因為屬性用於計算計時器過期時間，並且它在協調器代碼中此時每次重播時返回相同的值。 此行為對於確保每次重複調用`winner``context.df.Task.any`時的相同結果非常重要。

---

一旦啟動，此協調器函式會執行下列作業：

1. 取得電話號碼，SMS 通知會「傳送」** 給該號碼。
2. 呼叫 **E4_SendSmsChallenge** 以將 SMS 訊息傳送給使用者，並且傳回預期的 4 位數挑戰碼。
3. 建立長期計時器，它會觸發從目前時間起算 90 秒的時間。
4. 與計時器同時，等候來自使用者的 **SmsChallengeResponse** 事件。

使用者會收到具有四位數代碼的 SMS 訊息。 他們有 90 秒的時間將相同的四位數代碼發送回協調器函數實例以完成驗證過程。 如果提交錯誤的代碼，有額外三次嘗試可以進行修正 (在相同的 90 秒時間內)。

> [!WARNING]
> 如果您已經在上述範例中接受挑戰回應，且不再需要使用計時器，請務必[取消計時器](durable-functions-timers.md)。

## <a name="e4_sendsmschallenge-activity-function"></a>E4_SendSmsChallenge活動功能

**E4_SendSmsChallenge**函數使用 Twilio 綁定向最終使用者發送帶有四位代碼的 SMS 消息。

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> 您需要安裝`Microsoft.Azure.WebJobs.Extensions.Twilio`Nuget 包才能運行示例代碼。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

function.json** 定義如下：

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

下面是生成四位質詢代碼併發送 SMS 消息的代碼：

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

---

## <a name="run-the-sample"></a>執行範例

使用範例中所包含的 HTTP 觸發函式，您可以透過傳送下列 HTTP POST 要求來啟動協調流程：

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

協調器函式會接收提供的電話號碼，並且立即傳送 SMS 訊息給該電話號碼，訊息包含隨機產生的 4 位數驗證碼&mdash;，例如 2168**。 此函式接著會在 90 秒的時間內等候回應。

要回復代碼，您可以在另一個函數中使用[`RaiseEventAsync`（.NET）`raiseEvent`或 （JavaScript）](durable-functions-instance-management.md)或調用上述 202 回應中引用的**SendEventUrl** HTTP `{eventName}` POST Webhook，替換為事件`SmsChallengeResponse`的名稱：

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

如果您計時器到期之前傳送，協調流程就會完成且 `output` 欄位會設定為 `true`，指出驗證成功。

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

如果您讓計時器到期，或者如果您輸入錯誤的代碼四次，您可以查詢狀態，並且看到 `false` 協調流程函式輸出，指出電話驗證失敗。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>後續步驟

此示例演示了持久函數的一些高級功能，特別是`WaitForExternalEvent`API。 `CreateTimer` 您已經看到這些功能如何與 `Task.WaitAny` 結合，以實作可靠的逾時系統，對於與真人的互動通常相當有用。 藉由閱讀一系列深入探討特定主題的文章，您可以深入了解如何使用長期函式。

> [!div class="nextstepaction"]
> [系列中第一篇文章](durable-functions-bindings.md)
