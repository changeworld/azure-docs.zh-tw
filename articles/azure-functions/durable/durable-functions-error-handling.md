---
title: 在 Durable Functions 中處理錯誤 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中處理錯誤。
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 023f9dfcc421935c3f7515e847108925d5e5521e
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673642"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>在 Durable Functions (Azure Functions) 中處理錯誤

持久函式協調流程會在程式碼中執行，而且可以使用程式設計語言的內建錯誤處理功能。 您需要學習的新概念，才能在協調流程中新增錯誤處理和補償。 不過，有一些行為值得注意。

## <a name="errors-in-activity-functions"></a>活動函式中的錯誤

在活動函式中擲回的任何例外狀況會封送處理回協調器函式，並擲回為 `FunctionFailedException` 。 您可以在協調器函式中撰寫符合需求的錯誤處理和補償程式碼。

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
> 先前的 c # 範例適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext` 。 如需版本之間差異的詳細資訊，請參閱 [Durable Functions 版本](durable-functions-versions.md) 文章。

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
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    transfer_details = context.get_input()

    yield context.call_activity('DebitAccount', {
         'account': transfer_details['sourceAccount'],
         'amount' : transfer_details['amount']
    })

    try:
        yield context.call_activity('CreditAccount', {
                'account': transfer_details['destinationAccount'],
                'amount': transfer_details['amount'],
            })
    except:
        yield context.call_activity('CreditAccount', {
            'account': transfer_details['sourceAccount'],
            'amount': transfer_details['amount']
        })

main = df.Orchestrator.create(orchestrator_function)
```

---

如果第一個 **CreditAccount** 函式呼叫失敗，協調器函式會將資金貸回來源帳戶以進行補償。

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
> 先前的 c # 範例適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext` 。 如需版本之間差異的詳細資訊，請參閱 [Durable Functions 版本](durable-functions-versions.md) 文章。

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    first_retry_interval_in_milliseconds = 5000
    max_number_of_attempts = 3

    retry_options = df.RetryOptions(first_retry_interval_in_milliseconds, max_number_of_attempts)

    yield context.call_activity_with_retry('FlakyFunction', retry_options)

main = df.Orchestrator.create(orchestrator_function)
```

---

上一個範例中的活動函式呼叫會使用參數來設定自動重試原則。 有幾個選項可供自訂自動重試原則：

* **嘗試次數上限**：重試嘗試次數上限。
* **第一次重試間隔**：第一次重試嘗試之前等候的時間量。
* **輪詢係數**：用來決定輪詢增加速率的係數。 預設值為 1。
* **最大重試間隔**：重試嘗試之間等候的最大時間量。
* **重試逾時**：花費在重試的最大時間量。 預設行為是無限期地重試。
* **控制碼**：可以指定使用者定義的回呼，以決定是否應該重試函數。 

> [!NOTE]
> JavaScript () 中的 Durable Functions 目前不支援使用者定義的回呼 `context.df.RetryOptions` 。


## <a name="function-timeouts"></a>函式逾時

如果執行時間太長而無法完成，您可能會想要放棄協調器函式內的函式呼叫。 目前，您可以使用[](durable-functions-timers.md) `context.CreateTimer` ( .net) 、 `context.df.createTimer` (javascript) ，或 `context.create_timer` (python) 搭配 `Task.WhenAny` ( .net) 、 `context.df.Task.any` (javascript) 或 (python) 來建立持久計時器 `context.task_any` ，如下列範例所示：

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
> 先前的 c # 範例適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext` 。 如需版本之間差異的詳細資訊，請參閱 [Durable Functions 版本](durable-functions-versions.md) 文章。

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
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds = 30)
    
    activity_task = context.call_activity('FlakyFunction')
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any(activity_task, timeout_task)
    if winner == activity_task:
        timeout_task.cancel()
        return True
    else:
        return False

main = df.Orchestrator.create(orchestrator_function)
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
