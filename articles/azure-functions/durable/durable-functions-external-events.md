---
title: 在 Durable Functions 中處理外部事件 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中處理外部事件。
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0877161f8d668141c8efb7c06b10643bf209341f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76262957"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>在 Durable Functions (Azure Functions) 中處理外部事件

協調器函式能夠等候和接聽外部事件。 [Durable Functions](durable-functions-overview.md) 的這項功能通常有助於處理人為互動或其他外部觸發程序。

> [!NOTE]
> 外部事件都是單向的非同步作業。 不適用於傳送事件的用戶端需要來自協調器函式的同步回應的狀況。

## <a name="wait-for-events"></a>等候事件

`WaitForExternalEvent` [協調流程觸發](durable-functions-bindings.md#orchestration-trigger)程式系`waitForExternalEvent`結的（.net）和（JavaScript）方法可讓協調器函式以非同步方式等候並接聽外來事件。 接聽協調器函式會宣告事件的「名稱」** 和預期收到的「資料形式」**。

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
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用`DurableOrchestrationContext` ，而不是`IDurableOrchestrationContext`。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

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
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用`DurableOrchestrationContext` ，而不是`IDurableOrchestrationContext`。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

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
> 先前的程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用`DurableOrchestrationContext` ，而不是`IDurableOrchestrationContext`。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

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

---

`WaitForExternalEvent`會無限期地等候某些輸入。  等候時可以安心卸載函式應用程式。 當此協調流程執行個體有事件抵達時，就會自動甦醒並立即處理事件。

> [!NOTE]
> 如果函數應用程式使用使用情況方案，則協調器函式等候來自 `WaitForExternalEvent` (.NET) 或 `waitForExternalEvent` (JavaScript) 的工作時，不論等多久都不會產生費用。

## <a name="send-events"></a>傳送事件

[協調流程用戶端](durable-functions-bindings.md#orchestration-client)系`raiseEvent`結的`RaiseEventAsync` （.net）或（javascript）方法會傳送`WaitForExternalEvent` （.net）或`waitForExternalEvent` （javascript）等候的事件。  `RaiseEventAsync` 方法接受 *eventName* 和 *eventData* 作為參數。 事件資料必須是 JSON 可序列化。

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
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions `OrchestrationClient` 1.x，您必須使用屬性，而不是`DurableClient`屬性，而且您必須使用`DurableOrchestrationClient`參數類型，而不是`IDurableOrchestrationClient`。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

在內部，`RaiseEventAsync` (.NET) 或 `raiseEvent` (JavaScript) 會將訊息加入佇列，供等候協調器函式取用。 如果執行個體不是在等候指定的「事件名稱」**，事件訊息就會新增至記憶體內部佇列。 如果協調流程執行個體稍後開始接聽該「事件名稱」**，它將會檢查佇列是否有事件訊息。

> [!NOTE]
> 如果沒有具有指定「執行個體識別碼」** 的協調流程執行個體，則會捨棄事件訊息。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解如何執行錯誤處理](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [執行等候人為互動的範例](durable-functions-phone-verification.md)