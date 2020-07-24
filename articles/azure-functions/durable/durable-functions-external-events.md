---
title: 在 Durable Functions 中處理外部事件 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中處理外部事件。
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 3cd04c93d508bd06c4ddd2e05074084202b9fc60
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87014934"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>在 Durable Functions (Azure Functions) 中處理外部事件

協調器函式能夠等候和接聽外部事件。 [Durable Functions](durable-functions-overview.md) 的這項功能通常有助於處理人為互動或其他外部觸發程序。

> [!NOTE]
> 外部事件都是單向的非同步作業。 不適用於傳送事件的用戶端需要來自協調器函式的同步回應的狀況。

## <a name="wait-for-events"></a>等候事件

協調流程觸發程式系結的[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) （.net）、 `waitForExternalEvent` （JavaScript）和 `wait_for_external_event` （ [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger) Python）方法可讓協調器函式以非同步方式等候並接聽外來事件。 接聽協調器函式會宣告事件的「名稱」** 和預期收到的「資料形式」**。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用， `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    approved = context.wait_for_external_event('Approval')
    if approved:
        # approval granted - do the approved action
    else:
        # approval denied - send a notification

main = df.Orchestrator.create(orchestrator_function)
```

---

上述範例會接聽特定單一事件，並於收到該事件時採取動作。

您可以同時接聽多個事件，例如，下列範例中會等候三個可能的事件通知之一。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用， `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    event1 = context.wait_for_external_event('Event1')
    event2 = context.wait_for_external_event('Event2')
    event3 = context.wait_for_external_event('Event3')

    winner = context.task_any([event1, event2, event3])
    if winner == event1:
        # ...
    elif winner == event2:
        # ...
    elif winner == event3:
        # ...

main = df.Orchestrator.create(orchestrator_function)
```

---

前一個範例會接聽多個事件中的「任何」** 事件。 也可以等候「所有」** 事件。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> 先前的程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用， `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

在 .NET 中，如果事件裝載無法轉換為預期的類型 `T`，則會擲回例外狀況。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    application_id = context.get_input()
    
    gate1 = context.wait_for_external_event('CityPlanningApproval')
    gate2 = context.wait_for_external_event('FireDeptApproval')
    gate3 = context.wait_for_external_event('BuildingDeptApproval')

    yield context.task_all([gate1, gate2, gate3])
    yield context.call_activity('IssueBuildingPermit', application_id)

main = df.Orchestrator.create(orchestrator_function)
```

---

`WaitForExternalEvent`會無限期地等候某些輸入。  等候時可以安心卸載函式應用程式。 當此協調流程執行個體有事件抵達時，就會自動甦醒並立即處理事件。

> [!NOTE]
> 如果您的函式應用程式使用取用方案，則協調器函式從 `WaitForExternalEvent` （.net）、（JavaScript）或（Python）等候工作時，不會產生任何計費費用 `waitForExternalEvent` `wait_for_external_event` ，無論它等待多久。

## <a name="send-events"></a>傳送事件

您可以使用[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) （.net）或 `raiseEventAsync` （JavaScript）方法，將外來事件傳送至協調流程。 這些方法是由[協調流程用戶端](durable-functions-bindings.md#orchestration-client)系結所公開。 您也可以使用內建的「[引發事件」 HTTP API](durable-functions-http-api.md#raise-event) ，將外來事件傳送至協調流程。

引發的事件包括*實例識別碼*、*事件名稱*和*eventData*做為參數。 協調器函式會使用 `WaitForExternalEvent` （.net）或 `waitForExternalEvent` （JavaScript） api 來處理這些事件。 事件*名稱*在傳送和接收端上必須相符，才能處理事件。 事件資料也必須是可序列化的 JSON。

就內部而言，「引發事件」機制會將等候協調器函式所挑選的訊息排入佇列。 如果執行個體不是在等候指定的「事件名稱」**，事件訊息就會新增至記憶體內部佇列。 如果協調流程執行個體稍後開始接聽該「事件名稱」**，它將會檢查佇列是否有事件訊息。

> [!NOTE]
> 如果沒有具有指定「執行個體識別碼」** 的協調流程執行個體，則會捨棄事件訊息。

以下的範例佇列觸發函式會將「核准」事件傳送至協調器函式執行個體。 協調流程執行個體識別碼來自佇列訊息的本文。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `OrchestrationClient` 屬性，而不是 `DurableClient` 屬性，而且您必須使用 `DurableOrchestrationClient` 參數類型，而不是 `IDurableOrchestrationClient` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(instance_id:str, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    await client.raise_event(instance_id, 'Approval', True)
```

---

在內部， `RaiseEventAsync` （.net）、 `raiseEvent` （JavaScript）或 `raise_event` （Python）會將等候協調器函式所挑選的訊息。 如果執行個體不是在等候指定的「事件名稱」**，事件訊息就會新增至記憶體內部佇列。 如果協調流程執行個體稍後開始接聽該「事件名稱」**，它將會檢查佇列是否有事件訊息。

> [!NOTE]
> 如果沒有具有指定「執行個體識別碼」** 的協調流程執行個體，則會捨棄事件訊息。

### <a name="http"></a>HTTP

以下是將「核准」事件引發至協調流程實例的 HTTP 要求範例。 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

在此情況下，實例識別碼會硬式編碼為*MyInstanceId*。

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [瞭解如何執行錯誤處理](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [執行等候人為互動的範例](durable-functions-phone-verification.md)