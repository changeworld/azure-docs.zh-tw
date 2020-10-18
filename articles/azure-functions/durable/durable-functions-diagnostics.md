---
title: 長期函式中的診斷 - Azure
description: 了解如何在 Azure Functions 的「長期函式」延伸模組中實作診斷問題。
author: cgillum
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: azfuncdf
ms.openlocfilehash: 4714b9330c4a9d9cd390a58f814e3cdb4b591038
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168136"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>在 Azure 中診斷 Durable Functions

診斷[長期函式](durable-functions-overview.md)的問題有數個選項。 其中一些選項與一般函式相同，某些則是長期函式特有的。

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) 是在 Azure Functions 中診斷和監視的建議方式。 一樣適用於長期函式。 如需如何在您的函式應用程式中利用 Application Insights 的概觀，請參閱[監視 Azure Functions](../functions-monitoring.md)。

Azure Functions 長期延伸模組也會發出「追蹤事件」**，可讓您追蹤協調流程的端對端執行。 您可以使用 Azure 入口網站中的 [Application Insights 分析](../../azure-monitor/log-query/log-query-overview.md) 工具來尋找和查詢這些追蹤事件。

### <a name="tracking-data"></a>追蹤資料

協調流程執行個體的每個生命週期事件會讓追蹤事件寫入到 Application Insights 中的**追蹤**集合。 這個事件包含具有數個欄位的 **customDimensions** 裝載。  欄位名稱前面都會加上 `prop__`。

* **hubName**：您的協調流程執行所在之工作中樞的名稱。
* **appName**︰函式應用程式的名稱。 當您有多個函式應用程式共用相同的 Application Insights 實例時，此欄位會很有用。
* **slotName**：[部署位置](../functions-deployment-slots.md)，目前的函式應用程式在其中執行。 當您使用部署位置來為協調流程進行版本時，此欄位會很有用。
* **functionName**：協調器或活動函式的名稱。
* **functionType**：函式的類型，例如**協調器**或**活動**。
* **instanceId**：協調流程執行個體的唯一識別碼。
* **state**：執行個體的生命週期執行狀態。 有效值包括：
  * **已排程**：函式已排程執行，但是尚未開始執行。
  * **已啟動**：函式已開始執行，但是尚未等候或已完成。
  * **等候**：協調器已排程一些工作，並且正在等候完成。
  * **接聽**：協調器正在接聽外部事件通知。
  * **已完成**：函式已順利完成。
  * **失敗**：函式失敗，發生錯誤。
* **原因**：與追蹤事件相關聯的其他資料。 例如，如果執行個體正在等候外部事件通知，這個欄位會指出它正在等候之事件的名稱。 如果函式失敗，此欄位將包含錯誤詳細資料。
* **isReplay**：布林值，指出追蹤事件是否要重新執行。
* **extensionVersion**：長期工作延伸模組的版本。 當您在延伸模組中報告可能的 bug 時，版本資訊特別重要。 如果在長時間執行執行個體執行時發生更新，它可能會報告多個版本。
* **sequenceNumber**：事件的執行序號。 與時間戳記結合，有助於依執行時間排序事件。 請注意，如果主機會在執行個體執行中重新啟動，這個數字將重設為零，所以務必一律先依 timestamp，然後依 sequenceNumber 進行排序。**

發出給 Application Insights 的追蹤資料的詳細資訊可在 `logger` (函數 1.x) 或檔案的 `logging` (函數 2.0) 區段中設定 `host.json` 。

#### <a name="functions-10"></a>函數1。0

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-20"></a>函數2。0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

根據預設，會發出所有非重新執行的追蹤事件。 藉由將 `Host.Triggers.DurableTask` 設定為 `"Warning"` 或 `"Error"` 可以降低資料量，在此情況下，追蹤事件只會針對例外情況發出。

若要啟用發出詳細的協調流程重新執行事件，在 `durableTask` 底下的 `host.json` 檔案中 `LogReplayEvents` 可以設定為 `true`，如下所示：

#### <a name="functions-10"></a>函數1。0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>函數2。0

```json
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> 根據預設，Application Insights 遙測是由 Azure Functions 執行階段取樣，以避免過於頻繁發出資料。 這會在短期內發生太多生命週期事件時，造成追蹤資訊遺失。 [Azure Functions 監視文章](../configure-monitoring.md#configure-sampling)會說明如何設定這個行為。

### <a name="single-instance-query"></a>單一執行個體查詢

下列查詢顯示 [Hello Sequence](durable-functions-sequence.md) 函式協調流程之單一執行個體的歷史追蹤資料。 它是使用 [Kusto 查詢語言](/azure/data-explorer/kusto/query/)所撰寫的。 它會篩選重新執行，以便僅顯示「邏輯」** 執行路徑。 您可以藉由排序 `timestamp` 和 `sequenceNumber` 來排序事件，如下列查詢中所示：

```kusto
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

結果是一份追蹤事件的清單，顯示協調流程的執行路徑，包括依執行時間以遞增順序排序任何活動函式。

![Application Insights 單一實例排序的查詢](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>執行個體摘要查詢

下列查詢會顯示在指定時間範圍內執行之所有協調流程執行個體的狀態。

```kusto
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

結果是執行個體識別碼的清單，以及其目前的執行階段狀態。

![Application Insights 單一實例查詢](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="durable-task-framework-logging"></a>持久的工作架構記錄

持久的延伸記錄有助於瞭解協調流程邏輯的行為。 不過，這些記錄不一定會包含足夠的資訊來偵測架構層級效能和可靠性問題。 從長期延伸模組的 **v 2.3.0** 開始，基礎長期工作架構所發出的記錄 (DTFx) 也可供收集。

查看 DTFx 發出的記錄時，請務必瞭解 DTFx 引擎是由兩個元件所組成：核心分派引擎 (`DurableTask.Core`) ，以及 (Durable Functions 預設使用的其中一個支援的儲存提供者 `DurableTask.AzureStorage`) 。

* **DurableTask**：包含協調流程執行和低層級排程的相關資訊。
* **DurableTask. AzureStorage**：包含與 Azure 儲存體成品互動相關的資訊，包括內部佇列、blob，以及用來儲存和提取內部協調流程狀態的儲存體資料表。

您可以藉由更新函式 `logging/logLevel` 應用程式 ** 在檔案上的host.js** 區段來啟用這些記錄。 下列範例顯示如何從和啟用警告和錯誤記錄檔 `DurableTask.Core` `DurableTask.AzureStorage` ：

```json
{
  "version": "2.0",
  "logging": {
    "logLevel": {
      "DurableTask.AzureStorage": "Warning",
      "DurableTask.Core": "Warning"
    }
  }
}
```

如果您已啟用 Application Insights，這些記錄將會自動新增至 `trace` 集合。 您可以使用 Kusto 查詢搜尋其他記錄的相同方式來搜尋這些專案 `trace` 。

> [!NOTE]
> 針對生產應用程式，建議您 `DurableTask.Core` `DurableTask.AzureStorage` 使用篩選器來啟用和記錄 `"Warning"` 。 較高的詳細資訊篩選準則 `"Information"` 很適合用來偵測效能問題。 不過，這些記錄事件是大量的，而且可能會大幅增加 Application Insights 資料儲存體成本。

下列 Kusto 查詢顯示如何查詢 DTFx 記錄。 查詢最重要的部分是 `where customerDimensions.Category startswith "DurableTask"` 因為它會將結果篩選為和分類中的 `DurableTask.Core` 記錄 `DurableTask.AzureStorage` 。

```kusto
traces
| where customDimensions.Category startswith "DurableTask"
| project
    timestamp,
    severityLevel,
    Category = customDimensions.Category,
    EventId = customDimensions.EventId,
    message,
    customDimensions
| order by timestamp asc 
```
結果是一組由長期工作架構記錄提供者所寫入的記錄。

![Application Insights DTFx 查詢結果](./media/durable-functions-diagnostics/app-insights-dtfx.png)

如需可用記錄事件的詳細資訊，請參閱 GitHub 上的長期工作 [架構結構化記錄檔](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Core/Logging#durabletaskcore-logging)。

## <a name="app-logging"></a>應用程式記錄

直接從協調器函式寫入記錄時，請務必記住協調器重新執行行為。 例如，請考慮下列協調器函式：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)
```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    logging.info("Calling F1.")
    yield context.call_activity("F1")
    logging.info("Calling F2.")
    yield context.call_activity("F2")
    logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

產生的記錄資料會看起來像下列範例輸出：

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> 請記住，當記錄宣告要呼叫 F1、F2 和 F3 時，這些函式只會在第一次遇到時「實際」** 被呼叫。 會略過重新執行期間發生的後續呼叫，且輸出會重新執行至協調器邏輯。

如果您只想在非重新執行的執行中寫入記錄，您可以撰寫條件運算式，只在「正在重新執行」旗標為時記錄 `false` 。 請考慮上面的範例，但是這次使用重新執行檢查。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

從 Durable Functions 2.0 開始，.NET 協調器函數也可以選擇建立 `ILogger` ，以便在重新執行期間自動篩選出記錄語句。 這項自動篩選是使用 [IDurableOrchestrationCoNtext. CreateReplaySafeLogger (ILogger) ](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durablecontextextensions.createreplaysafelogger) API 來完成。

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

> [!NOTE]
> 先前的 c # 範例適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext` 。 如需版本之間差異的詳細資訊，請參閱 [Durable Functions 版本](durable-functions-versions.md) 文章。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    if not context.is_replaying:
        logging.info("Calling F1.")
    yield context.call_activity("F1")
    if not context.is_replaying:
        logging.info("Calling F2.")
    yield context.call_activity("F2")
    if not context.is_replaying:
        logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

在先前提及的變更中，記錄輸出如下所示：

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>自訂狀態

自訂協調流程狀態可讓您為協調器函式設定自訂狀態值。 外部用戶端可以透過 [HTTP 狀態查詢 api](durable-functions-http-api.md#get-instance-status) 或透過特定語言的 API 呼叫，來看到此自訂狀態。 自訂協調流程狀態能更進一步監視協調器函式。 例如，協調器函式程式碼可以叫用「設定自訂狀態」 API，以更新長時間執行作業的進度。 用戶端 (例如網頁或其他外部系統) 則無法定期查詢 HTTP 狀態查詢 API，以取得更豐富的進度資訊。 以下提供在協調器函式中設定自訂狀態值的範例程式碼：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> 先前的 c # 範例適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext` 。 如需版本之間差異的詳細資訊，請參閱 [Durable Functions 版本](durable-functions-versions.md) 文章。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    # ...do work...

    # update the status of the orchestration with some arbitrary data
    custom_status = {'completionPercentage': 90.0, 'status': 'Updating database records'}
    context.set_custom_status(custom_status)
    # ...do more work...

    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

當協調流程執行時，外部用戶端能擷取該自訂狀態：

```http
GET /runtime/webhooks/durabletask/instances/instance123?code=XYZ

```

用戶端將取得下列回應：

```json
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> 自訂狀態承載僅限為 16 KB 的 UTF-16 JSON 文字，因為它必須符合 Azure 資料表儲存體資料行的大小。 如果您需要較大的承載，可以使用外部儲存體。

## <a name="debugging"></a>偵錯

Azure Functions 支援直接偵錯函式程式碼，相同支援適用於長期函式，無論是在 Azure 中或在本機執行。 不過，在偵錯時有一些要注意的行為：

* 重新**執行：** 收到新的輸入時，協調器函式[會定期重新](durable-functions-orchestrations.md#reliability)執行。 此行為表示協調器函式的單一 *邏輯* 執行可能會導致多次叫用相同的中斷點，尤其是在函式程式碼早期設定時。
* **Await**：當協調器函式 `await` 中遇到時，它會將控制權傳回給永久性工作架構發送器。 如果是第一次遇到特定的 `await` ，則 *永遠* 不會繼續相關聯的工作。 因為工作絕不會繼續，所以不可能在 Visual Studio) *中進入 await* (F10。 跨越只有在重新執行工作時可行。
* **訊息超時**： Durable Functions 會在內部使用佇列訊息，以驅動 orchestrator、activity 和 entity 函數的執行。 在多部 VM 的環境中，中斷至偵錯一段延伸的時間可能會造成另一個 VM 選取訊息，導致重複執行。 這種行為也會結束一般佇列觸發函式，但是務必要在此內容中指出，因為佇列是實作詳細資料。
* **停止和啟動**：長期函式中的訊息會在 debug 會話之間保存。 如果您在長期函式執行時停止偵錯工具並終止本機主機進程，該函式可能會在未來的 debug 會話中自動重新執行。 這種行為在非預期的情況下可能會造成混淆。 在「偵錯工具」會話之間清除 [內部儲存體佇列](durable-functions-perf-and-scale.md#internal-queue-triggers) 中的所有訊息，是避免此行為的一種方法。

> [!TIP]
> 在協調器函式中設定中斷點時，如果您只想要中斷非重新執行的執行，您可以設定條件式中斷點，只有在「正在重新執行」值為時才會中斷 `false` 。

## <a name="storage"></a>儲存體

根據預設，長期函式會將狀態儲存在 Azure 儲存體。 此行為表示您可以使用 [Microsoft Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md)之類的工具來檢查協調流程的狀態。

![Azure 儲存體總管螢幕擷取畫面](./media/durable-functions-diagnostics/storage-explorer.png)

這對於偵錯相當有用，因為您會確實看到協調流程的狀態。 也可以檢查佇列中的訊息來了解哪些工作擱置 (在某些情況下停滯)。

> [!WARNING]
> 可以很方便地查看資料表儲存體中的執行歷程記錄，而且避免此資料表上有任何相依性。 當 Durable Functions 擴充功能進化時，此相依性可能會改變。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入瞭解如何在 Azure Functions 中監視](../functions-monitoring.md)
