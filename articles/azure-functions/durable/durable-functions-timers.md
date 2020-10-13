---
title: Durable Functions 中的計時器 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中實作永久性計時器。
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: bb91f205a9b83b0b4b410644ef6c0fcbbf60876a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876442"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的計時器

[Durable Functions](durable-functions-overview.md) 提供「永久性計時器」**，用於協調器函式中實作延遲，或在非同步動作上設定逾時。 永久性計時器應該用於協調器函式，而不是 `Thread.Sleep` 和 `Task.Delay` (c # ) ，或 `setTimeout()` `setInterval()` (JavaScript) 或 `time.sleep()` (Python) 。

您可以藉由呼叫 `CreateTimer` ( .net) 方法或 `createTimer` [協調流程觸發](durable-functions-bindings.md#orchestration-trigger)程式系結的 (JavaScript) 方法來建立持久計時器。 方法會傳回在指定的日期和時間完成的工作。

## <a name="timer-limitations"></a>計時器限制

當您建立在下午4:30 到期的計時器時，基礎的長期工作架構會將只有在下午4:30 才會顯示的訊息。 在 Azure Functions 取用方案中執行時，新顯示的計時器訊息可確保函式應用程式會在適當的 VM 上啟用。

> [!NOTE]
> * 從長期延伸模組的 [版本 2.3.0](https://github.com/Azure/azure-functions-durable-extension/releases/tag/v2.3.0) 開始，持久計時器是無限制的。 在舊版的延伸模組中，長期計時器的限制為七天。 當您使用較早的版本，且需要超過七天的延遲時，請在迴圈中使用計時器 Api `while` 來模擬此延遲。
> * 在 `CurrentUtcDateTime` `DateTime.UtcNow` 計算永久性 `currentUtcDateTime` `Date.now` `Date.UTC` 計時器的引發時間時，請一律使用而不是在 .Net 中，或使用 JavaScript。 如需詳細資訊，請參閱協調器函式程式 [代碼條件約束](durable-functions-code-constraints.md) 文章。

## <a name="usage-for-delay"></a>延遲的使用方式

下列範例說明如何使用永久性計時器來延遲執行。 此範例會每天發出帳單通知10天。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> 先前的 c # 範例以 Durable Functions 2.x 為目標。 針對 Durable Functions 1.x，您必須使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext` 。 如需版本之間差異的詳細資訊，請參閱 [Durable Functions 版本](durable-functions-versions.md) 文章。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    for i in range(0, 9):
        deadline = context.current_utc_datetime + timedelta(days=1)
        yield context.create_timer(deadline)
        yield context.call_activity("SendBillingEvent")

main = df.Orchestrator.create(orchestrator_function)
```
---

> [!WARNING]
> 在協調器函式中避免無限迴圈。 如需有關如何安全而有效率地實作無限迴圈案例的資訊，請參閱[永久性協調流程](durable-functions-eternal-orchestrations.md)。

## <a name="usage-for-timeout"></a>逾時的使用方式

此範例說明如何使用永久性計時器來實作逾時。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> 先前的 c # 範例以 Durable Functions 2.x 為目標。 針對 Durable Functions 1.x，您必須使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext` 。 如需版本之間差異的詳細資訊，請參閱 [Durable Functions 版本](durable-functions-versions.md) 文章。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds=30)
    activity_task = context.call_activity("GetQuote")
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any([activity_task, timeout_task])
    if winner == activity_task:
        timeout_task.cancel()
        return True
    elif winner == timeout_task:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!WARNING]
> `CancellationTokenSource` `cancel()` `TimerTask` 如果您的程式碼不會等候它完成，請使用 ( .net) 或在傳回的 (JavaScript) 上呼叫來取消永久性計時器。 在完成或取消所有未完成的工作之前，長期工作架構不會將協調流程的狀態變更為「已完成」。

這項取消機制不會終止進行中的活動函式或子協調流程執行。 只是讓協調器函式略過結果並繼續執行。 如果您的函式應用程式使用取用方案，您仍需支付已放棄的活動函式所耗用的任何時間和記憶體。 根據預設，在取用量方案中執行的函式會在五分鐘後逾時。 如果超過此限制，Azure Functions 主機會重新開機來停止所有執行，以避免計費失控狀況發生。 [函式逾時可設定](../functions-host-json.md#functiontimeout)。

如需如何在協調器函式中執行超時的更深入範例，請參閱「 [人為互動 & 超時-電話驗證」一](durable-functions-phone-verification.md) 文。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何引發和處理外部事件](durable-functions-external-events.md)
