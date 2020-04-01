---
title: Durable Functions 中的永久性協調流程 - Azure
description: 了解如何使用 Azure Functions 的 Durable Functions 擴充來實作永久性協調流程。
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d55e08fecbd1338284607ac59fe354c6fa8cb1ea
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478813"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的永久性協調流程

「永久協調流程」** 是指永不結束的協調流程函式。 想要在彙總工具和任何需要無限迴圈的情節下使用 [Durable Functions](durable-functions-overview.md) 時，永久性協調流程就很有用。

## <a name="orchestration-history"></a>協調流程記錄

如[業務流程歷史記錄](durable-functions-orchestrations.md#orchestration-history)主題中所述,持久任務框架跟蹤每個函數業務流程的歷史記錄。 只要協調器函式繼續排程新的工作，此記錄就會持續成長。 如果協調器函式進入無限迴圈，並持續排程工作，此記錄可能會變得非常大，而造成明顯的效能問題。 「永久協調流程」** 概念是設計讓需要無限迴圈的應用程式減少發生這類問題。

## <a name="resetting-and-restarting"></a>重設和重新啟動

協調器函數不是使用無限迴圈,而是通過調用`ContinueAsNew`[業務流程綁定](durable-functions-bindings.md#orchestration-trigger)的 (.NET) 或`continueAsNew`(JAvaScript) 方法來重置其狀態。 此方法接受單一 JSON 序列化參數，此參數會變成新的輸入來產生下一個協調器函式。

呼叫 `ContinueAsNew` 時，執行個體在結束之前會將訊息加入其本身的佇列。 此訊息會以新的輸入值來重新啟動執行個體。 相同的執行個體識別碼會保留下來，但協調器函式的記錄實際上會截斷。

> [!NOTE]
> 「永久性工作架構」會維護相同的執行個體識別碼，但在內部會為 `ContinueAsNew` 所重設的協調器函式建立新的「執行識別碼」**。 此執行識別碼通常不對外公開，但可能很助於了解偵錯協調流程執行。

## <a name="periodic-work-example"></a>定期工作範例

需要無限期地執行定期工作的程式碼，即為永久協調流程的一個使用案例。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> 前面的 C# 範例適用於持久函數 2.x。 對持久函數 1.x,必須`DurableOrchestrationContext`使用`IDurableOrchestrationContext`而不是 。 有關不同版本之間的差異的詳細資訊,請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

---

此範例與計時器觸發函式之間的差異在於，此處的清理觸發程序時間不是根據排程。 例如，每小時執行函式的 CRON 排程會在 1:00、2:00、3:00 等時間執行它，很可能會陷入重疊問題。 不過，在此範例中，如果清除需要 30 分鐘，則會排程在 1:00、2:30、4:00 等時間，不會有機會重疊。

## <a name="starting-an-eternal-orchestration"></a>開始永恆的編排

使用`StartNewAsync`(.NET)`startNew`或 (JAVAScript) 方法啟動永恆的業務流程,就像任何其他業務流程函數一樣。  

> [!NOTE]
> 如果需要確保運行單例永久業務流程,在啟動業務流程時維護同一實例`id`非常重要。 如需詳細資訊，請參閱[執行個體管理](durable-functions-instance-management.md)。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> 前面的代碼用於持久函數 2.x。 對於持久函數 1.x,必須`OrchestrationClient`使用`DurableClient`屬性而不是 屬性,`DurableOrchestrationClient`並且必須使用`IDurableOrchestrationClient`參數類型而不是 。 有關不同版本之間的差異的詳細資訊,請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```

---

## <a name="exit-from-an-eternal-orchestration"></a>從永久協調流程離開

如果協調器函式最終一定要完成，則您完全「不」** 需要呼叫 `ContinueAsNew`，讓函式自然結束即可。

如果協調器函數位於無限迴圈中,並且需要停止,請使用[業務流程用戶端綁定](durable-functions-bindings.md#orchestration-client)的`TerminateAsync`(.NET)`terminate`或 (JAVAScript) 方法停止它。 如需詳細資訊，請參閱[執行個體管理](durable-functions-instance-management.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何實作單次協調流程](durable-functions-singletons.md)
