---
title: Durable Functions 中的計時器 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中實作永久性計時器。
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261478"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的計時器

[Durable Functions](durable-functions-overview.md) 提供「永久性計時器」**，用於協調器函式中實作延遲，或在非同步動作上設定逾時。 永久性計時器應該用於協調器函式中，以代替 `Thread.Sleep` 和 `Task.Delay` (C#)，或 `setTimeout()` 和 `setInterval()` (JavaScript)。

通過調用`CreateTimer`[業務流程綁定](durable-functions-bindings.md#orchestration-trigger)的 （.NET） 方法或`createTimer`（JavaScript） 方法來創建持久計時器。 該方法返回在指定的日期和時間完成的任務。

## <a name="timer-limitations"></a>計時器限制

當您創建在下午 4：30 過期的計時器時，基礎持久任務框架會對僅在下午 4：30 時變為可見的消息進行排隊。 在 Azure 函數消耗計畫中運行時，新可見的計時器消息將確保在適當的 VM 上啟動函數應用。

> [!NOTE]
> * 耐用計時器目前限制為 7 天。 如果需要較長的延遲，可以使用`while`迴圈中的計時器 API 來類比它們。
> * 在為持久`CurrentUtcDateTime`計時器計算`DateTime.UtcNow`觸發時間時，`currentUtcDateTime`始終在`Date.now` `Date.UTC` .NET 或 JavaScript 中使用而不是或在 JavaScript 中使用。 有關詳細資訊，請參閱[協調器函數代碼約束](durable-functions-code-constraints.md)一文。

## <a name="usage-for-delay"></a>延遲的使用方式

下列範例說明如何使用永久性計時器來延遲執行。 例如，在 10 天內每天發佈計費通知。

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
> 前面的 C# 示例以持久函數 2.x 為目標。 對於持久函數 1.x，必須使用`DurableOrchestrationContext`而不是`IDurableOrchestrationContext`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

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
> 前面的 C# 示例以持久函數 2.x 為目標。 對於持久函數 1.x，必須使用`DurableOrchestrationContext`而不是`IDurableOrchestrationContext`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

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

---

> [!WARNING]
> 如果代碼`CancellationTokenSource`不會等待完成，請使用`cancel()`（.NET） 或調用返回`TimerTask`的 （JavaScript） 取消持久計時器。 在完成或取消所有未完成的任務之前，持久任務框架不會將業務流程的狀態更改為"已完成"。

此取消機制不終止正在進行的活動功能或子業務流程執行。 只是讓協調器函式略過結果並繼續執行。 如果函數應用使用消耗計畫，您仍會按放棄的活動函數佔用的任何時間和記憶體計費。 根據預設，在取用量方案中執行的函式會在五分鐘後逾時。 如果超過此限制，Azure Functions 主機會重新開機來停止所有執行，以避免計費失控狀況發生。 [函式逾時可設定](../functions-host-json.md#functiontimeout)。

有關如何在協調器函數中實現超時的更深入示例，請參閱["人機交互&超時 - 電話驗證](durable-functions-phone-verification.md)"一文。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何引發和處理外部事件](durable-functions-external-events.md)
