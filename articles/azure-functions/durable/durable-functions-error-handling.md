---
title: 在 Durable Functions 中處理錯誤 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中處理錯誤。
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277852"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>在 Durable Functions (Azure Functions) 中處理錯誤

持久函數業務流程在代碼中實現，可以使用程式設計語言的內置錯誤處理功能。 實際上，不需要學習任何新概念，以將錯誤處理和補償添加到業務流程中。 不過，有一些行為值得注意。

## <a name="errors-in-activity-functions"></a>活動函式中的錯誤

在活動函數中引發的任何異常將重新封送回協調器函數，並作為 引發作為`FunctionFailedException`。 您可以在協調器函式中撰寫符合需求的錯誤處理和補償程式碼。

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
> 前面的 C# 示例適用于持久函數 2.x。 對於持久函數 1.x，必須使用`DurableOrchestrationContext`而不是`IDurableOrchestrationContext`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

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

如果第一個**CreditAccount**函式呼叫失敗，協調器功能通過將資金貸記回源帳戶來進行補償。

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
> 前面的 C# 示例適用于持久函數 2.x。 對於持久函數 1.x，必須使用`DurableOrchestrationContext`而不是`IDurableOrchestrationContext`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

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

上例中的活動函式呼叫採用用於配置自動重試策略的參數。 自訂自動重試策略有多種選項：

* **嘗試次數上限**：重試嘗試次數上限。
* **第一次重試間隔**：第一次重試嘗試之前等候的時間量。
* **輪詢係數**：用來決定輪詢增加速率的係數。 預設值為 1。
* **最大重試間隔**：重試嘗試之間等候的最大時間量。
* **重試逾時**：花費在重試的最大時間量。 預設行為是無限期地重試。
* **控制碼**：可以指定使用者定義的回檔，以確定是否應重試函數。

## <a name="function-timeouts"></a>函式逾時

如果業務流程函數需要很長時間才能完成，則可能需要放棄函式呼叫。 目前，適當的做法是使用 `context.CreateTimer` (.NET) 或 `context.df.createTimer` (JavaScript) 搭配 `Task.WhenAny` (.NET) 或 `context.df.Task.any` (JavaScript) 來建立[持久性計時器](durable-functions-timers.md)，如下列範例所示：

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
> 前面的 C# 示例適用于持久函數 2.x。 對於持久函數 1.x，必須使用`DurableOrchestrationContext`而不是`IDurableOrchestrationContext`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

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
> [瞭解永恆的編排](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [了解如何診斷問題](durable-functions-diagnostics.md)
