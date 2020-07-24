---
title: 持久協調器程式碼條件約束-Azure Functions
description: Azure Durable Functions 的協調流程函式重新執行和程式碼條件約束。
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 14e0b86f11c3eabf93e7d4f0ebf563e59c0c21e9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081860"
---
# <a name="orchestrator-function-code-constraints"></a>協調器函式程式碼條件約束

Durable Functions 是[Azure Functions](../functions-overview.md)的延伸模組，可讓您建立具狀態應用程式。 您可以[使用協調器函式來](durable-functions-orchestrations.md)協調函式應用程式中其他持久性函式的執行。 協調器函式是具狀態、可靠且可能長時間執行的功能。

## <a name="orchestrator-code-constraints"></a>協調器程式碼條件約束

協調器函式會使用[事件來源](/azure/architecture/patterns/event-sourcing)來確保可靠的執行，並維護本機變數狀態。 Orchestrator 程式碼的重新執行[行為](durable-functions-orchestrations.md#reliability)會針對您可以在協調器函式中撰寫的程式碼類型建立條件約束。 例如，協調器函式必須*具有決定性*：協調器函數會多次重新執行，而且每次都必須產生相同的結果。

### <a name="using-deterministic-apis"></a>使用決定性 Api

本節提供一些簡單的指導方針，可協助確保您的程式碼具決定性。

協調器函式可以呼叫其目的語言中的任何 API。 不過，協調器函式必須只呼叫具決定性的 Api。 *決定性 api*是一種 api，不論呼叫的時間或頻率為何，一律會傳回相同的輸入值。

下表顯示您應該避免的 Api 範例，因為它們*不*具決定性。 這些限制僅適用于協調器函式。 其他函數類型沒有這類限制。

| API 類別 | 原因 | 因應措施 |
| ------------ | ------ | ---------- |
| 日期和時間  | 傳回目前日期或時間的 Api 不具決定性，因為每次重新執行時傳回的值都不同。 | 使用 `CurrentUtcDateTime` .net 中的 api 或 JavaScript 中的 `currentUtcDateTime` api，這對於重新執行是安全的。 |
| Guid 和 Uuid  | 傳回隨機 GUID 或 UUID 的 Api 不具決定性，因為每次重新執行時產生的值都不同。 | `NewGuid`在 .net 或 JavaScript 中使用 `newGuid` ，以安全地產生隨機的 guid。 |
| 亂數字 | 傳回亂數字的 Api 不具決定性，因為每次重新執行時產生的值都不同。 | 使用活動函式，將亂數字傳回至協調流程。 活動函式的傳回值一律可安全地重新執行。 |
| 繫結 | 輸入和輸出系結通常會執行 i/o，而且不具決定性。 協調器函式不能直接使用，甚至是[協調流程用戶端](durable-functions-bindings.md#orchestration-client)和[實體用戶端](durable-functions-bindings.md#entity-client)系結。 | 在用戶端或活動函式內使用輸入和輸出系結。 |
| 網路 | 網路呼叫牽涉到外部系統，而且不具決定性。 | 使用活動功能進行網路呼叫。 如果您需要從協調器函式進行 HTTP 呼叫，您也可以使用持久的[Http api](durable-functions-http-features.md#consuming-http-apis)。 |
| 封鎖 Api | 如 `Thread.Sleep` .net 和類似的 api 等封鎖 api，可能會導致協調器函式的效能和規模問題，因此應予以避免。 在 Azure Functions 耗用量方案中，甚至可能導致不必要的執行時間費用。 | 使用替代方案來封鎖 Api （如果有的話）。 例如，使用 `CreateTimer` 在協調流程執行中引進延遲。 [持久性計時器](durable-functions-timers.md)延遲不會計入協調器函式的執行時間。 |
| 非同步 Api | 協調器程式碼絕不能啟動任何非同步作業，除非使用 `IDurableOrchestrationContext` api 或 `context.df` 物件的 api。 例如，您無法 `Task.Run` `Task.Delay` `HttpClient.SendAsync` 在 .net 或 JavaScript 中使用、和 `setTimeout` `setInterval` 。 長期工作架構會在單一執行緒上執行協調器程式碼。 它無法與其他非同步 Api 可能呼叫的任何其他執行緒互動。 | 協調器函式應該只會進行持久的非同步呼叫。 活動函式應該進行任何其他的非同步 API 呼叫。 |
| 非同步 JavaScript 函數 | 您無法將 JavaScript 協調器函式宣告為， `async` 因為 node.js 執行時間並不保證非同步函數具決定性。 | 將 JavaScript 協調器函數宣告為同步產生器函式。 |
| 執行緒 Api | 長期工作架構會在單一執行緒上執行協調器程式碼，而且無法與任何其他執行緒互動。 將新的執行緒引進協調流程的執行，可能會導致不具決定性的執行或鎖死。 | 協調器函式幾乎不應使用執行緒 Api。 例如，在 .NET 中，請避免使用 `ConfigureAwait(continueOnCapturedContext: false)` ; 這可確保工作接續會在協調器函式的原始上執行 `SynchronizationContext` 。 如果需要這類 Api，請將其僅限於活動功能。 |
| 靜態變數 | 避免在協調器函式中使用非常數的靜態變數，因為它們的值可能會隨著時間而改變，因而導致不具決定性的運行 | 使用常數，或將靜態變數的使用限制為活動函式。 |
| 環境變數 | 請勿在協調器函式中使用環境變數。 其值可能會隨著時間而改變，因而導致不具決定性的執行時間行為。 | 環境變數必須只能從用戶端函式或活動函式中參考。 |
| 無限迴圈 | 在協調器函式中避免無限迴圈。 由於長期工作架構會在協調流程函式進行時儲存執行歷程記錄，因此無限迴圈可能會導致 orchestrator 實例耗盡記憶體。 | 針對無限迴圈案例，請使用如 `ContinueAsNew` .net 或 `continueAsNew` JavaScript 中的 api 來重新開機函數執行，並捨棄先前的執行歷程記錄。 |

雖然套用這些條件約束的一開始可能很困難，但實際上它們很容易遵循。

長期工作架構會嘗試偵測先前規則的違規。 如果發現違規，架構會擲回**NonDeterministicOrchestrationException**例外狀況。 不過，此偵測行為不會攔截所有違規，因此您不應該依賴它。

## <a name="versioning"></a>版本控制

長期協調流程可能會持續執行數天、數月、數年，甚至[eternally](durable-functions-eternal-orchestrations.md)。 對影響未完成之協調流程 Durable Functions 應用程式所做的任何程式碼更新，可能會中斷協調流程的重新執行行為。 這就是為什麼要在更新程式碼時仔細規劃的原因。 如需如何版本化程式碼的詳細說明，請參閱版本設定一[文](durable-functions-versioning.md)。

## <a name="durable-tasks"></a>長期工作

> [!NOTE]
> 本章節描述長期工作架構的內部實作詳細資料。 您可以使用長期函式，而不需要知道這種資訊。 它只是用來協助您了解重新執行行為。

可以在協調器函式中安全等候的工作，有時*稱為「* 長期工作」。 長期工作架構會建立並管理這些工作。 範例包括 .NET 協調器函式中**CallActivityAsync**、 **WaitForExternalEvent**和**CreateTimer**所傳回的工作。

這些長期工作由 .NET 中的物件清單在內部管理 `TaskCompletionSource` 。 在重新執行期間，這些工作會建立為協調器程式碼執行的一部分。 當發送器列舉對應的歷程記錄事件時，就會完成。

工作會使用單一執行緒以同步方式執行，直到所有歷程記錄都已重新執行為止。 不是由歷程記錄重新執行結束而完成的持久性工作，會採取適當的動作。例如，訊息可能會排入佇列以呼叫活動函式。

本節的執行時間行為描述應可協助您瞭解協調器函式為何無法 `await` `yield` 在非持久工作中使用或。 有兩個原因：發送器執行緒無法等候工作完成，而且該工作的任何回呼可能會損毀協調器函式的追蹤狀態。 某些執行時間檢查已準備好協助偵測這些違規。

若要深入瞭解長期工作架構如何執行協調器函式，請參閱[GitHub 上](https://github.com/Azure/durabletask)的長期工作原始程式碼。 特別是，請參閱[TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs)和[TaskOrchestrationCoNtext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解如何叫用子協調流程](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [了解如何處理版本控制](durable-functions-versioning.md)
