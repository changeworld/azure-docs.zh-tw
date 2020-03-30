---
title: 耐用的協調器代碼約束 - Azure 函數
description: Azure 持久函數的業務流程函數重播和代碼約束。
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4ed604302ca187ad4953e865d68dc73030a37c02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562134"
---
# <a name="orchestrator-function-code-constraints"></a>協調器函數代碼約束

持久函數是 Azure[函數](../functions-overview.md)的擴展，允許您生成有狀態的應用。 可以使用[協調器函數](durable-functions-orchestrations.md)協調函數應用中其他持久函數的執行。 協調器功能具有狀態、可靠且可能長時間運行。

## <a name="orchestrator-code-constraints"></a>協調器程式碼條件約束

協調器函數使用[事件源](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)來確保可靠的執行並維護本地變數狀態。 協調器代碼的[重播行為](durable-functions-orchestrations.md#reliability)對可以在協調器函數中編寫的代碼類型創建約束。 例如，協調器函數必須是*確定性*的：協調器函數將重玩多次，並且每次必須產生相同的結果。

### <a name="using-deterministic-apis"></a>使用確定性 API

本節提供了一些簡單的準則，可説明確保您的代碼具有確定性。

協調器函數可以調用其目的語言中的任何 API。 但是，協調器函數只調用確定性 API 非常重要。 *確定性 API*是一種 API，它始終返回給定相同輸入的相同值，無論何時或多久調用它。

下表顯示了您應該避免的 API 示例，因為它們*不是*確定性的。 這些限制僅適用于協調器函數。 其他函數類型沒有此類限制。

| API 類別 | 原因 | 因應措施 |
| ------------ | ------ | ---------- |
| 日期和時間  | 返回當前日期或時間的 API 是非確定性的，因為返回的值對於每個重播都不同。 | 使用`CurrentUtcDateTime`.NET 中的 API`currentUtcDateTime`或 JavaScript 中的 API，可以安全地重播。 |
| GUID 和 UUD  | 返回隨機 GUID 或 UUID 的 API 是無確定性的，因為生成的值對於每個重播都不同。 | 在`NewGuid`.NET`newGuid`或 JavaScript 中使用以安全地生成隨機 GUID。 |
| 亂數 | 返回亂數的 API 是無確定性的，因為生成的值對於每個重播都不同。 | 使用活動函數將亂數返回到業務流程。 活動函數的傳回值始終安全進行重播。 |
| 繫結 | 輸入和輸出綁定通常執行 I/O，並且是非確定性的。 協調器函數不得直接使用業務流程[用戶端](durable-functions-bindings.md#orchestration-client)和[實體用戶端](durable-functions-bindings.md#entity-client)綁定。 | 在用戶端或活動函數中使用輸入和輸出綁定。 |
| 網路 | 網路調用涉及外部系統，並且是非確定性的。 | 使用活動函數進行網路調用。 如果需要從協調器函數進行 HTTP 調用，也可以使用[持久的 HTTP API](durable-functions-http-features.md#consuming-http-apis)。 |
| 阻止 API | 阻止 API（`Thread.Sleep`如 .NET 和類似的 API）可能會導致協調器函數的性能和規模問題，應避免。 在 Azure 函數消耗計畫中，它們甚至會導致不必要的運行時費用。 | 使用替代方法在 API 可用時阻止它們。 例如，用於`CreateTimer`引入業務流程執行中的延遲。 [持久計時器](durable-functions-timers.md)延遲不會計入協調器函數的執行時間。 |
| 非同步 API | 協調器代碼不得啟動任何非同步作業，`IDurableOrchestrationContext`除非使用 API 或`context.df`物件的 API。 例如，不能`Task.Run``Task.Delay``HttpClient.SendAsync`在 .NET 或`setTimeout`和`setInterval`JavaScript 中使用 。 持久任務框架在單一執行緒上運行協調器代碼。 它無法與其他非同步 API 呼叫的任何其他執行緒進行交互。 | 協調器函數應僅進行持久非同步調用。 活動函數應進行任何其他非同步 API 呼叫。 |
| 非同步 JavaScript 函數 | 不能將 JavaScript 協調器函式宣告為`async`，因為 node.js 運行時不保證非同步函數是確定性的。 | 聲明 JavaScript 協調器函數為同步產生器函數。 |
| 執行緒 API | 持久任務框架在單一執行緒上運行協調器代碼，並且不能與任何其他執行緒進行交互。 將新執行緒引入業務流程的執行可能會導致非確定性執行或鎖死。 | 協調器函數幾乎絕不應使用執行緒 API。 例如，在 .NET 中，`ConfigureAwait(continueOnCapturedContext: false)`避免使用 。這可確保任務延續在協調器函數的原始上運行`SynchronizationContext`。 如果需要此類 API，將其使用限制為僅活動函數。 |
| 靜態變數 | 避免在協調器函數中使用非恒定的靜態變數，因為它們的值可能會隨時間而變化，從而導致非確定性運行時行為。 | 使用常量，或將靜態變數的使用限制為活動函數。 |
| 環境變數 | 不要在協調器函數中使用環境變數。 它們的值可能會隨時間而變化，從而導致非確定性的運行時行為。 | 只能在用戶端函數或活動函數中引用環境變數。 |
| 無限迴圈 | 在協調器函式中避免無限迴圈。 由於持久任務框架會隨著業務流程函數的進行而保存執行歷史記錄，因此無限迴圈可能會導致業務流程器實例耗盡記憶體。 | 對於無限迴圈方案，請使用 .NET`ContinueAsNew`或`continueAsNew`JavaScript 中的 API 重新開機函數執行並放棄以前的執行歷史記錄。 |

雖然最初應用這些限制似乎很困難，但實際上它們很容易理解。

持久任務框架嘗試檢測違反上述規則的行為。 如果發現衝突，則框架將引發**非確定性"異常**"。 但是，此檢測行為不會捕獲所有違規行為，您也不應依賴它。

## <a name="versioning"></a>版本控制

一個持久的編排可能會持續運行數天、數月、數年，甚至[永恆](durable-functions-eternal-orchestrations.md)。 對持久函數應用所做的任何代碼更新都可能會影響未完成的業務流程，可能會破壞業務流程的重播行為。 因此，在對代碼進行更新時，必須仔細計畫。 有關如何對代碼進行版本解的更詳細說明，請參閱[版本控制文章](durable-functions-versioning.md)。

## <a name="durable-tasks"></a>長期工作

> [!NOTE]
> 本章節描述長期工作架構的內部實作詳細資料。 您可以在不知道此資訊的情況下使用持久函數。 它只是用來協助您了解重新執行行為。

可以在協調器函數中安全地等待的任務有時稱為*持久任務*。 持久任務框架創建和管理這些任務。 示例包括**調用活動 Async、Waitfor****外來事件**和 .NET 協調器函數中的**創建計時器**返回的任務。

這些持久任務由 .NET 中`TaskCompletionSource`的物件清單在內部管理。 在重播期間，這些任務作為協調器代碼執行的一部分創建。 當調度程式枚舉相應的歷史記錄事件時，它們就完成了。

任務使用單一執行緒同步執行，直到重播所有歷史記錄。 在歷史記錄重播結束時未完成的持久任務執行了適當的操作。例如，消息可能已排隊調用活動函數。

本節對運行時行為的描述應有助於瞭解協調器函數不能使用`await`或`yield`在非持久任務中的原因。 原因有二：調度程式執行緒不能等待任務完成，並且該任務的任何回檔都可能損壞協調器函數的跟蹤狀態。 已實施一些運行時檢查，以説明檢測這些違規行為。

要瞭解有關持久任務框架如何執行協調器函數的更多，請參閱[GitHub 上的持久任務原始程式碼](https://github.com/Azure/durabletask)。 特別是，請參閱[TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs)和[TaskOrchestrationCoNtext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解如何調用子業務流程](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [了解如何處理版本控制](durable-functions-versioning.md)
