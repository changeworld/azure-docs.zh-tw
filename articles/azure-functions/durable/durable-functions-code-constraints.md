---
title: 持久協調器程式碼條件約束-Azure Functions
description: 適用于 Azure Durable Functions 的協調流程函數重新執行和程式碼條件約束。
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: ee1561e85e769bf8a82ce96d5ce010eece92a0fa
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392611"
---
# <a name="orchestrator-function-code-constraints"></a>協調器函式程式碼條件約束

Durable Functions 是 [Azure Functions](../functions-overview.md) 的延伸模組，可讓您建立可設定狀態的應用程式。 您可以 [使用協調器函式來](durable-functions-orchestrations.md) 協調函式應用程式內其他持久性函式的執行。 協調器函式是具狀態、可靠且可能長時間執行的功能。

## <a name="orchestrator-code-constraints"></a>協調器程式碼條件約束

協調器函式會使用 [事件來源](/azure/architecture/patterns/event-sourcing) 來確保可靠的執行，並維護本機變數狀態。 協調器程式碼的重新執行 [行為](durable-functions-orchestrations.md#reliability) 會建立可以在協調器函式中撰寫的程式碼類型條件約束。 例如，協調器函式必須具 *決定性* ：協調器函式會多次執行，而且每次都必須產生相同的結果。

### <a name="using-deterministic-apis"></a>使用決定性 Api

本節提供一些簡單的指導方針，可協助確保您的程式碼具決定性。

協調器函式可以呼叫其目的語言的任何 API。 不過，協調器函式必須只呼叫具決定性的 Api。 具 *決定性的 api* 是一種 api，它一律會傳回相同的值，無論呼叫的時間或頻率為何。

下表顯示 Api 的範例，您應該避免這些 Api，因為它們 *不* 具決定性。 這些限制僅適用于協調器函式。 其他函數類型沒有這類限制。

| API 類別 | 原因 | 因應措施 |
| ------------ | ------ | ---------- |
| 日期和時間  | 傳回目前日期或時間的 Api 不具決定性，因為每次重新執行時傳回的值不同。 | 使用 `CurrentUtcDateTime` .net 中的 api、 `currentUtcDateTime` JavaScript 中的 Api 或 `current_utc_datetime` Python 中的 api，這對於重新執行是安全的。 |
| Guid 和 Uuid  | 傳回隨機 GUID 或 UUID 的 Api 不具決定性，因為每次重新執行時產生的值不同。 | `NewGuid`在 .net 或 `newGuid` JavaScript 中使用，以安全地產生隨機 guid。 |
| 亂數字 | 傳回亂數字的 Api 不具決定性，因為每次重新執行時產生的值會不同。 | 使用活動函式，將亂數字傳回至協調流程。 活動函式的傳回值一律可以安全地重新執行。 |
| 繫結 | 輸入和輸出系結通常會進行 i/o，而且不具決定性。 協調器函式不能直接使用，甚至是 [協調流程用戶端](durable-functions-bindings.md#orchestration-client) 和 [實體用戶端](durable-functions-bindings.md#entity-client) 系結。 | 在用戶端或活動函式內使用輸入和輸出系結。 |
| 網路 | 網路呼叫涉及外部系統，且不具決定性。 | 使用活動函式進行網路呼叫。 如果您需要從協調器函式進行 HTTP 呼叫，也可以使用 [持久的 HTTP api](durable-functions-http-features.md#consuming-http-apis)。 |
| 封鎖 Api | `Thread.Sleep`在 .net 和類似的 api 中封鎖 api 可能會導致協調器函式的效能和調整問題，應予以避免。 在 Azure Functions 取用方案中，甚至可能會產生不必要的執行時間費用。 | 使用可用的替代方案來封鎖 Api。 例如，使用  `CreateTimer` 在協調流程執行時引入延遲。 [持久計時器](durable-functions-timers.md) 延遲不會計入協調器函式的執行時間。 |
| 非同步 Api | 除了使用 `IDurableOrchestrationContext` api、 `context.df` JavaScript 中的 Api 或 Python 中的 api，Orchestrator 程式碼絕不能啟動任何非同步作業 `context` 。 例如，您不能 `Task.Run` `Task.Delay` `HttpClient.SendAsync` 在 .Net 或 `setTimeout` 和 JavaScript 中使用、和 `setInterval` 。 長期工作架構會在單一執行緒上執行協調器程式碼。 它無法與其他非同步 Api 可能呼叫的任何其他執行緒互動。 | 協調器函式應該只進行持久的非同步呼叫。 活動函式應進行任何其他非同步 API 呼叫。 |
| 非同步 JavaScript 函數 | 您無法將 JavaScript 協調器函式宣告為， `async` 因為 node.js 執行時間並不保證非同步函式具決定性。 | 將 JavaScript 協調器函式宣告為同步產生器函式 |
| Python 協同程式 | 您無法將 Python 協調器函式宣告為協同程式，例如 使用關鍵字宣告它們 `async` ，因為協同程式語義不會與 Durable Functions 重新執行模型相符。 | 將 Python 協調器函式宣告為產生器，這表示您應該預期 `context` API 會使用 `yield` 而不是 `await` 。   |
| 執行緒 Api | 長期工作架構會在單一執行緒上執行協調器程式碼，而無法與任何其他執行緒互動。 將新執行緒引入協調流程的執行，可能會導致非決定性的執行或鎖死。 | 協調器函式幾乎不應使用執行緒 Api。 例如，在 .NET 中，請避免使用 `ConfigureAwait(continueOnCapturedContext: false)` ; 這可確保工作接續執行于協調器函式的原始上 `SynchronizationContext` 。 如果需要這類 Api，請將其僅限於活動函式。 |
| 靜態變數 | 請避免在協調器函式中使用非常數的靜態變數，因為它們的值可能會隨著時間而變更，而導致非決定性的運行 | 使用常數，或將靜態變數的使用限制為活動函式。 |
| 環境變數 | 請勿在協調器函式中使用環境變數。 它們的值可能會隨著時間而改變，而導致不具決定性的執行時間行為。 | 環境變數只能從用戶端函式或活動函式中參考。 |
| 無限迴圈 | 在協調器函式中避免無限迴圈。 由於長期工作架構會在協調流程函式進行時儲存執行歷程記錄，因此無限迴圈可能會導致 orchestrator 實例用盡記憶體。 | 針對無限迴圈案例，請使用 `ContinueAsNew` .net、 `continueAsNew` JavaScript 或 Python 中的 api `continue_as_new` 來重新開機函式執行，並捨棄先前的執行歷程記錄。 |

雖然套用這些條件約束在一開始可能有點困難，但實際上它們很容易遵循。

長期工作架構會嘗試偵測之前的規則違規。 如果發現違規，架構會擲回 **NonDeterministicOrchestrationException** 例外狀況。 不過，此偵測行為不會攔截所有違規，您也不應該依賴它。

## <a name="versioning"></a>版本控制

長期協調流程可能會連續執行數天、數個月、年或甚至 [eternally](durable-functions-eternal-orchestrations.md)。 對影響未完成協調流程的 Durable Functions 應用程式進行的任何程式碼更新，都可能會中斷協調流程的重新執行行為。 這就是為什麼在更新程式碼時，請務必仔細規劃。 如需如何設定程式碼版本的詳細說明，請參閱 [版本控制文章](durable-functions-versioning.md)。

## <a name="durable-tasks"></a>長期工作

> [!NOTE]
> 本章節描述長期工作架構的內部實作詳細資料。 您可以使用長期函式，而不需要知道此資訊。 它只是用來協助您了解重新執行行為。

可以在協調器函式中安全等候的工作，有時 *稱為「* 長期工作」。 長期工作架構會建立和管理這些工作。 範例包括 .NET 協調器函式中 **>callactivityasync** 、 **>waitforexternalevent** 和 **>createtimer** 所傳回的工作。

這些長期工作會由 .NET 中的物件清單在內部進行管理 `TaskCompletionSource` 。 在重新執行期間，會在執行 orchestrator 程式碼時建立這些工作。 因為發送器會列舉對應的記錄事件，所以已完成。

工作會使用單一執行緒以同步方式執行，直到所有歷程記錄都已重新執行為止。 歷程記錄重新執行結束時未完成的長期工作會執行適當的動作。例如，可能會將訊息排入佇列，以呼叫活動函式。

本節的執行時間行為描述應該可協助您瞭解協調器函式無法使用 `await` 或 `yield` 在非持久性工作中的原因。 有兩個原因：發送器執行緒無法等候工作完成，而且該工作的任何回呼都可能會損毀協調器函式的追蹤狀態。 有一些執行時間檢查可協助偵測這些違規。

若要深入瞭解長期工作架構如何執行協調器函式，請參閱 [GitHub 上的持久工作原始程式碼](https://github.com/Azure/durabletask)。 尤其是，請參閱 [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) 和 [TaskOrchestrationCoNtext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解如何叫用子協調流程](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [了解如何處理版本控制](durable-functions-versioning.md)
