---
title: Durable Functions (Python) 的監視器-Azure
description: 瞭解如何使用 Azure Functions (Python) 的 Durable Functions 擴充功能來執行狀態監視器。
author: davidmrdavid
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: azfuncdf
ms.openlocfilehash: 9083821fa03c09949daaf3166367489248a4d7d2
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029164"
---
# <a name="monitor-scenario-in-durable-functions---github-issue-monitoring-sample"></a>Durable Functions 中的監視案例-GitHub 問題監視範例

監視模式是指工作流程中的彈性「週期性」程序，例如，輪詢直到符合特定條件。 本文會說明使用 Durable Functions 來實作監視的範例。

[!包含持久函式-必要條件]

## <a name="scenario-overview"></a>案例概觀

此範例會監視 GitHub 存放庫中的問題計數，如果有3個以上的開啟問題，則會向使用者發出警示。 您可以使用定期計時器觸發的函式，以固定間隔要求開啟的問題計數。 不過，這個方法有一個問題，那就是 **存留期管理**。 如果只應該傳送一個警示，則在偵測到3個或更多問題時，監視器必須自行停用。 監視模式除了可以結束自身的執行外，還有其他優點：

* 監視器會依間隔 (而非排程) 來執行：計時器觸發程序每小時「執行」一次；監視器會在動作之間「等候」一小時。 除非有指定，否則監視器的動作不會重疊，對於長時間執行的工作來說，這一點很重要。
* 監視器可具有動態間隔：等待時間可以根據某些條件來變更。
* 監視器可於某些條件成立時終止，或由其他程序加以終止。
* 監視器可以採用參數。 此範例會顯示相同的存放庫監視程式如何套用至任何要求的公用 GitHub 存放庫和電話號碼。
* 監視器具有擴充性。 由於每個監視器都是協調流程執行個體，您可以建立多個監視器，而不必建立新函式或定義多個程式碼。
* 監視器可輕鬆整合至大型工作流程。 監視器可以是更複雜之協調流程函式的一個區段，也可以是[子協調流程](durable-functions-sub-orchestrations.md)。

## <a name="configuration"></a>設定

### <a name="configuring-twilio-integration"></a>設定 Twilio 整合

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>函式

本文說明範例應用程式中的函式如下：

* `E3_Monitor`：定期呼叫的 [協調器函數](durable-functions-bindings.md#orchestration-trigger) `E3_TooManyOpenIssues` 。 如果的傳回 `E3_SendAlert` 值為，則會呼叫 `E3_TooManyOpenIssues` `True` 。
* `E3_TooManyOpenIssues`：檢查存放庫是否有太多開啟問題的 [活動](durable-functions-bindings.md#activity-trigger) 函式。 基於差異目的，我們考慮有超過3個開啟的問題太多。
* `E3_SendAlert`：會透過 Twilio 傳送手機簡訊的活動函式。

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor 協調器函式

# <a name="python"></a>[Python](#tab/python)

**E3_Monitor** 函式會使用協調器函式的標準 function.json。

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/function.json)]

以下是實作函式的程式碼：

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/\_\_init\_\_.py)]

---

此協調器函式會執行下列動作：

1. 取得要監視的存放 *庫，以及* 它將傳送 SMS 通知的 *電話號碼* 。
2. 判斷監視器的到期時間。 為求簡單明瞭，這個範例會使用硬式編碼值。
3. 呼叫 **E3_TooManyOpenIssues** ，判斷要求的存放庫是否有太多開啟的問題。
4. 如果有太多問題，請呼叫 **E3_SendAlert** ，以將 SMS 通知傳送給要求的電話號碼。
5. 建立長期計時器以在下一個輪詢間隔繼續進行協調流程。 為求簡單明瞭，這個範例會使用硬式編碼值。
6. 會繼續執行，直到目前的 UTC 時間通過監視的到期時間，或傳送 SMS 警示為止。

多個 orchestrator 實例可以藉由呼叫協調器函式多次來執行。 您可以指定要監視的存放庫，以及要傳送 SMS 警示的電話號碼。 最後，請注意協調器函式在等候計時器時 *並未* 執行，因此不會向您收取費用。


### <a name="e3_toomanyopenissues-activity-function"></a>E3_TooManyOpenIssues 活動函式

如同其他範例一樣，協助程式活動函式是使用 `activityTrigger` 觸發程序繫結的一般函式。 **E3_TooManyOpenIssues** 函式會取得存放庫目前開啟的問題清單，並判斷是否有「太多」這些問題：每個範例都有3個以上。

# <a name="python"></a>[Python](#tab/python)

function.json 定義如下：

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

實作如下。

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/\_\_init\_\_.py)]

---

### <a name="e3_sendalert-activity-function"></a>E3_SendAlert 活動函式

**E3_SendAlert** 函式會使用 Twilio 系結來傳送 SMS 訊息，通知終端使用者至少有3個開啟的問題等候解決。

# <a name="python"></a>[Python](#tab/python)

其 function.json 很簡單：

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

以下是傳送手機簡訊的程式碼：

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_SendAlert/\_\_init\_\_.py)]

---

## <a name="run-the-sample"></a>執行範例

您將需要 [GitHub](https://github.com/) 帳戶。 您可以使用它來建立暫時的公用存放庫，讓您可以開啟問題。

使用範例中所包含的 HTTP 觸發函式，您可以透過傳送下列 HTTP POST 要求來啟動協調流程：

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "repo": "<your github handle>/<a new github repo under your user>", "phone": "+1425XXXXXXX" }
```

例如，如果您的 GitHub 使用者名稱是 `foo` ，而您的儲存機制是， `bar` 則的值 `"repo"` 應該是 `"foo/bar"` 。

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** 實例會啟動並查詢提供的存放庫是否有開啟問題。 如果找到至少3個開啟的問題，則會呼叫活動函式來傳送警示;否則，它會設定計時器。 當計時器時間結束時，協調流程會恢復執行。

在 Azure Functions 入口網站中查看函式記錄，就能看到協調流程的活動。

```
[2020-12-04T18:24:30.007Z] Executing 'Functions.HttpStart' (Reason='This function was programmatically 
called via the host APIs.', Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6)
[2020-12-04T18:24:30.769Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=058e656e-bcb1-418c-95b3-49afcd07bd08)
[2020-12-04T18:24:30.847Z] Started orchestration with ID = '788420bb31754c50acbbc46e12ef4f9c'.
[2020-12-04T18:24:30.932Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=058e656e-bcb1-418c-95b3-49afcd07bd08, Duration=174ms)
[2020-12-04T18:24:30.955Z] Executed 'Functions.HttpStart' (Succeeded, Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6, Duration=1028ms)
[2020-12-04T18:24:31.229Z] Executing 'Functions.E3_TooManyOpenIssues' (Reason='(null)', Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3)
[2020-12-04T18:24:31.772Z] Executed 'Functions.E3_TooManyOpenIssues' (Succeeded, Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3, Duration=555ms)
[2020-12-04T18:24:40.754Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=23915e4c-ddbf-46f9-b3f0-53289ed66082)
[2020-12-04T18:24:40.789Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=23915e4c-ddbf-46f9-b3f0-53289ed66082, Duration=38ms)
(...trimmed...)
```

協調流程會在達到其超時時間時完成，或偵測到超過3個開啟的問題。 您也可以在其他函式中使用 API，或叫用 `terminate` 上述202回應中所參考的 **TERMINATEPOSTURI** HTTP POST webhook。 若要使用 webhook，請以 `{text}` 提早終止的原因取代。 HTTP POST URL 看起來會大致如下：

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>後續步驟

這個範例已示範如何使用 Durable Functions 與[長期計時器](durable-functions-timers.md)和條件式邏輯來監視外部來源的狀態。 下一個範例會說明如何使用外部事件和[長期計時器](durable-functions-timers.md)來處理人為互動。

> [!div class="nextstepaction"]
> [執行人為互動範例](durable-functions-phone-verification.md)
