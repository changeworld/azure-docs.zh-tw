---
title: 在 Durable Functions 中處理錯誤 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中處理錯誤。
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78371909"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>在 Durable Functions (Azure Functions) 中處理錯誤

長期函式協調流程會在程式碼中執行，而且可以使用程式設計語言的內建錯誤處理功能。 在協調流程中加入錯誤處理和補償，並不需要學習任何新的概念。 不過，有一些行為值得注意。

## <a name="errors-in-activity-functions"></a>活動函式中的錯誤

在活動函式中擲回的任何例外狀況會封送處理回到協調器函式，並以 `FunctionFailedException`的形式擲回。 您可以在協調器函式中撰寫符合需求的錯誤處理和補償程式碼。

例如，假設有下列協調器函式會從一個帳戶轉帳到另一個帳戶：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> 先前C#的範例適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `DurableOrchestrationContext`，而不是 `IDurableOrchestrationContext`。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

---

如果第一個**CreditAccount**函式呼叫失敗，協調器函式會藉由將資金貸回來源帳戶來補償。

## <a name="automatic-retry-on-failure"></a>失敗時自動重試

當您呼叫活動函式或子協調流程函式時，您可以指定自動重試原則。 下列範例會嘗試呼叫函式最多 3 次，並於每次重試之間等待 5 秒鐘：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> 先前C#的範例適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `DurableOrchestrationContext`，而不是 `IDurableOrchestrationContext`。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

---

上一個範例中的活動函數呼叫會採用參數來設定自動重試原則。 有數個選項可自訂自動重試原則：

* **嘗試次數上限**：重試嘗試次數上限。
* **第一次重試間隔**：第一次重試嘗試之前等候的時間量。
* **輪詢係數**：用來決定輪詢增加速率的係數。 預設值為 1。
* **最大重試間隔**：重試嘗試之間等候的最大時間量。
* **重試逾時**：花費在重試的最大時間量。 預設行為是無限期地重試。
* **控制碼**：可以指定使用者定義的回呼，以決定是否應該重試函數。

## <a name="function-timeouts"></a>函式逾時

如果協調器函式中的函式呼叫花費太多時間來完成，您可能會想要放棄它。 目前，適當的做法是使用 [ (.NET) 或 ](durable-functions-timers.md) (JavaScript) 搭配 `context.CreateTimer` (.NET) 或 `context.df.createTimer` (JavaScript) 來建立`Task.WhenAny`持久性計時器`context.df.Task.any`，如下列範例所示：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> 先前C#的範例適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `DurableOrchestrationContext`，而不是 `IDurableOrchestrationContext`。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

---

> [!NOTE]
> 這項機制並不會實際終止進行中的活動函式執行。 只是讓協調器函式略過結果並繼續執行。 如需詳細資訊，請參閱[計時器](durable-functions-timers.md#usage-for-timeout)文件。

## <a name="unhandled-exceptions"></a>未處理的例外狀況

如果協調器函式失敗並傳回未處理的例外狀況，例外狀況的詳細資料會記錄下來，而在執行個體會以 `Failed` 狀態結束。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解永久性協調流程](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [了解如何診斷問題](durable-functions-diagnostics.md)
