---
title: 長期協調流程 - Azure Functions
description: Azure Durable Functions 的協調流程功能簡介。
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 1b349b1e3c4a2fac4cd260dbe83469a776951ab0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87033637"
---
# <a name="durable-orchestrations"></a>長期協調流程

Durable Functions 是 [Azure Functions](../functions-overview.md) 的擴充功能。 您可以使用「協調器函式」  來協調函式應用程式內其他 Durable Functions 的執行。 協調器函式具有下列特性：

* 協調器函式會使用程序程式碼來定義函式工作流程。 不需要宣告式結構描述或設計工具。
* 協調器函式能以同步和非同步方式呼叫其他 durable functions。 所呼叫函式的輸出會可靠地儲存至本機變數。
* 協調器函式既持久又可靠。 當函式「等候」或「暫止」時，會自動設定其執行進度的檢查點。 處理序回收或 VM 重新開機時，本機狀態決不會消失。
* 協調器函式可能是長時間執行的。 「協調流程執行個體」  的總使用期限可以是秒、天、月或永不結束。

本文提供協調器功能的總覽，以及它們如何協助您解決各種應用程式開發挑戰。 如果您還不熟悉 Durable Functions 應用程式中可用的函式類型，請先閱讀 [Durable Function 類型](durable-functions-types-features-overview.md)一文。

## <a name="orchestration-identity"></a>協調流程身分識別

協調流程的每個「執行個體」  都有執行個體識別碼 (也稱為「執行個體識別碼」  )。 根據預設，每個執行個體識別碼都是自動產生的 GUID。 不過，執行個體識別碼也可以是任何使用者產生的字串值。 每個協調流程執行個體識別碼在[工作中樞](durable-functions-task-hubs.md)內必須是唯一的。

以下是一些關於執行個體識別碼的規則：

* 執行個體識別碼必須介於 1 到 256 個字元之間。
* 執行個體識別碼的開頭不得是 `@`。
* 執行個體識別碼不得包含 `/`、`\`、`#` 或 `?` 字元。
* 執行個體識別碼不得包含控制字元。

> [!NOTE]
> 通常建議盡可能使用自動產生的執行個體識別碼。 使用者產生的執行個體識別碼適用於協調流程執行個體與某個外部應用程式特定執行個體 (例如採購單或文件) 之間有一對一對應的案例。

協調流程的執行個體識別碼是大部分[執行個體管理作業](durable-functions-instance-management.md)的必要參數。 它們對診斷也很重要，例如在 Application Insights 中[搜尋協調流程追蹤資料](durable-functions-diagnostics.md#application-insights) ，以進行疑難排解或分析用途。 基於這個理由，建議將產生的執行個體識別碼儲存到某個外部位置 (例如，資料庫或在應用程式記錄中)，以便稍後輕鬆地參考它們。

## <a name="reliability"></a>可靠性

協調器函式可藉由使用[事件來源](/azure/architecture/patterns/event-sourcing)設計模式，可靠地維持其執行狀態。 長期工作架構會使用僅限附加的存放區來記錄函式協調流程所採取的一系列完整動作，而不會直接存放協調流程的目前狀態。 相較於「傾印」完整執行階段狀態，僅限附加的存放區有許多優點。 優點包括提升效能、延展性和回應能力。 您也可以取得交易資料的最終一致性，以及完整的稽核和歷程記錄。 稽核線索支援可靠的補償動作。

Durable Functions 會以透明的方式使用事件來源。 實際上，協調器函式中的 `await` (C#) 或 `yield` (JavaScript/Python) 運算子會暫止將協調器執行緒的控制交回給長期工作架構發送器。 發送器接著會將協調器函式所排程的任何新的動作 (例如，呼叫一或多個子函式或排程長期計時器) 認可至儲存體。 此透明認可動作會附加至協調流程執行個體的執行歷程記錄。 歷程記錄會儲存於儲存體資料表。 認可動作接著會將訊息新增至佇列以排程實際的工作。 此時，協調器函式即可從記憶體卸載。

當協調流程函式在收到更多要執行的工作時 (例如，收到回應訊息或長期計時器過期)，協調器會甦醒，並從頭開始重新執行整個函式，以重建本機狀態。 在重新執行期間，如果此程式碼嘗試呼叫函式 (或進行任何其他非同步工作)，長期工作架構便會諮詢目前協調流程的執行歷程記錄。 如果它發現[活動函式](durable-functions-types-features-overview.md#activity-functions)已執行並產生某種結果，便會重新執行該函式的結果，而協調器程式碼則會繼續執行。 重新執行會繼續，直到函式程式碼完成或直到已排程新的非同步工作為止。

> [!NOTE]
> 為了讓重新執行模式能夠正確且可靠地運作，協調器函式程式碼必須具有「決定性」  。 如需協調器函式程式碼限制的詳細資訊，請參閱[協調器函式程式碼條件約束](durable-functions-code-constraints.md)主題。

> [!NOTE]
> 如果協調器函式發出記錄訊息，重新執行行為可能會導致發出重複的記錄訊息。 請參閱[記錄](durable-functions-diagnostics.md#logging)主題，以深入了解發生此行為的原因及其因應措施。

## <a name="orchestration-history"></a>協調流程記錄

長期工作架構的事件來源行為會與您撰寫的協調器函式程式碼緊密結合。 假設您有一個活動鏈結協調器函式，如下列協調器函式所示：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    result1 = yield context.call_activity('SayHello', "Tokyo")
    result2 = yield context.call_activity('SayHello', "Seattle")
    result3 = yield context.call_activity('SayHello', "London")
    return [result1, result2, result3]

main = df.Orchestrator.create(orchestrator_function)
```
---

在每個 `await` (C#) 或 `yield` (JavaScript/Python) 陳述式中，長期工作架構會將函式的執行狀態檢查點記入某個長期儲存體後端 (通常為 Azure 資料表儲存體)。 此狀態就是「協調流程歷程記錄」。

### <a name="history-table"></a>記錄資料表

一般而言，長期工作架構會在每個檢查點執行下列作業：

1. 將執行歷程記錄儲存到 Azure 儲存體資料表。
2. 將協調器想要叫用之函式的訊息加入佇列。
3. 將協調器本身的訊息加入佇列，例如，長期計時器訊息。&mdash;

一旦檢查點完成，協調器函式就可以安心地從記憶體移除，直到有更多工作要執行。

> [!NOTE]
> Azure 儲存體不提供將資料儲存至資料表儲存體和佇列之間的任何交易式保證。 為了處理失敗，長期函式儲存體提供者會使用「最終一致性」模式。 這些模式可確保如果在檢查點中間發生損毀或連線中斷，不會遺失任何資料。

完成時，稍早顯示的函式歷程記錄在 Azure 資料表儲存體中看起來如下表所示 (針對示範目的縮寫)：

| PartitionKey (InstanceId)                     | EventType             | 時間戳記               | 輸入 | 名稱             | 結果                                                    | 狀態 |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Completed           |

資料行值的一些附註：

* **PartitionKey**：包含協調流程的執行個體識別碼。
* **EventType**：代表事件的類型。 可以是下列其中一個類型：
  * **OrchestrationStarted**：協調器函式會從等候繼續運作或是第一次執行。 `Timestamp` 資料行用來填入 `CurrentUtcDateTime` (.NET)、`currentUtcDateTime` (JavaScript) 和 `current_utc_datetime` (Python) API 的決定性值。
  * **ExecutionStarted**：協調器函式第一次開始執行。 此事件也包含 `Input` 資料行中的函式輸入。
  * **TaskScheduled**：活動函式已排程。 活動函式的名稱是在 `Name` 資料行中擷取。
  * **TaskCompleted**：活動函式已完成。 函式的結果是在 `Result` 資料行中。
  * **TimerCreated**：建立長期計時器。 `FireAt` 資料行包含計時器到期的已排程 UTC 時間。
  * **TimerFired**：長期計時器已引發。
  * **EventRaised**：外部事件傳送至協調流程執行個體。 `Name` 資料行會擷取事件的名稱，而 `Input` 資料行會擷取事件的裝載。
  * **OrchestratorCompleted**：協調器函式已等候。
  * **ContinueAsNew**：協調器函式已完成，並且以新的狀態自行重新啟動。 `Result` 資料行包含值，可作為重新啟動執行個體的輸入。
  * **ExecutionCompleted**：協調器函式即將完成 (或失敗)。 函式或錯誤詳細資料的輸出會儲存在 `Result` 資料行。
* **時間戳記**：歷程記錄事件的 UTC 時間戳記。
* **Name**：被叫用之函式的名稱。
* **輸入**：函式的 JSON 格式輸入。
* **Result**：函式的輸出，也就是它的傳回值。

> [!WARNING]
> 它作為偵錯工具相當有用，請勿在此資料表上採用任何相依性。 當 Durable Functions 擴充功能進化時，此相依性可能會改變。

每次函式從 `await` (C#) 或 `yield` (JavaScript/Python) 繼續時，長期工作架構就會從頭重新執行協調器函式。 在每次重新執行時，它會查詢執行歷程記錄以判斷是否已發生目前的非同步作業。  如果作業已發生，架構會立即重新執行該作業的輸出，而且會移至下一個 `await` (C#) 或 `yield` (JavaScript/Python)。 此程序會繼續，直到重新執行整個歷程記錄為止。 重新執行目前的歷程記錄後，本機變數就會還原為其先前的值。

## <a name="features-and-patterns"></a>功能與模式

下列各節會說明協調器函式的功能和模式。

### <a name="sub-orchestrations"></a>子協調流程

協調器函式可以呼叫活動函式，以及其他協調器函式。 例如，您可以從協調器函式庫中編譯更大的協調流程。 或者，也可以平行執行協調器函式的多個執行個體。

如需詳細資訊和範例，請參閱[子協調流程](durable-functions-sub-orchestrations.md)一文。

### <a name="durable-timers"></a>永久性計時器

協調流程可以排程「長期計時器」來實作延遲，或設定非同步動作的逾時處理。 在協調器函式中使用長期計時器，以代替 `Thread.Sleep` 和 `Task.Delay` (C#)，或 `setTimeout()` 和 `setInterval()` (JavaScript)，或 `time.sleep()` (Python)。

如需詳細資訊和範例，請參閱[長期計時器](durable-functions-timers.md)一文。

### <a name="external-events"></a>外部事件

協調器函式可以等待外部事件以更新協調流程執行個體。 此 Durable Functions 功能通常有助於處理人為互動或其他外部回呼。

如需詳細資訊和範例，請參閱[外部事件](durable-functions-external-events.md)一文。

### <a name="error-handling"></a>錯誤處理

協調器函式可以使用程式設計語言的錯誤處理功能。 協調流程程式碼支援 `try`/`catch` 之類的現有模式。

協調器函式也可以將重試原則新增至其所呼叫的活動或子協調器函式。 如果活動或子協調器函式因例外狀況而失敗，則指定的重試原則會自動延遲，並在指定的次數內重試執行。

> [!NOTE]
> 如果協調器函式中有未處理的例外狀況，則協調流程執行個體將會以 `Failed` 狀態完成。 協調流程執行個體失敗後便無法重試。

如需詳細資訊和範例，請參閱[錯誤處理](durable-functions-error-handling.md)一文。

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>重要區段 (Durable Functions 2.x，目前僅限 .NET)

協調流程執行個體為單一執行緒，因此不需要擔心協調流程內的競爭情況。 不過，當協調流程與外部系統互動時，可能會發生競爭情況。 若要減輕與外部系統互動時的競爭情況，協調器函式可以在 .NET 中使用 `LockAsync` 方法來定義「重要區段」。

下列範例程式碼顯示可定義重要區段的協調器函式。 它會使用 `LockAsync` 方法輸入重要區段。 此方法需要將一或多個參考傳遞至[長期實體](durable-functions-entities.md)，其會持久地管理鎖定狀態。 此協調流程一次只有一個執行個體可以在重要區段中執行程式碼。

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

`LockAsync` 會取得長期鎖定，並傳回可在處置時結束重要區段的 `IDisposable`。 這個 `IDisposable` 結果可以與 `using` 區塊一起使用，以獲得用語法表示的重要區段。 當協調器函式進入重要區段時，只有一個執行個體可以執行該程式碼區塊。 任何其他嘗試進入重要區段的執行個體都會遭到封鎖，直到前一個執行個體結束重要區段為止。

重要區段功能也適用於協調長期實體的變更。 如需重要區段的詳細資訊，請參閱[長期實體：「實體協調」](durable-functions-entities.md#entity-coordination)主題。

> [!NOTE]
> 重要區段可在 Durable Functions 2.0 和更新版本中取得。 目前，只有 .NET 協調流程會實作這項功能。

### <a name="calling-http-endpoints-durable-functions-2x"></a>呼叫 HTTP 端點 (Durable Functions 2.x)

如[協調器函式程式碼條件約束](durable-functions-code-constraints.md)所述，系統不允許協調器函式進行 I/O。 此限制的一般因應措施是將任何需要在活動函式中進行 I/O 的程式碼包裝起來。 與外部系統互動的協調流程經常使用活動函式來進行 HTTP 呼叫，並將結果傳回協調流程。

# <a name="c"></a>[C#](#tab/csharp)

為了簡化這種常見模式，協調器函式可以使用 `CallHttpAsync` 方法，直接叫用 HTTP API。

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    res = yield context.call_http('GET', url)
    if res.status_code >= 400:
        # handing of error code goes here
```
---

除了支援基本要求/回應模式之外，此方法還支援一般非同步 HTTP 202 輪詢模式的自動處理，也支援使用[受控身分識別](../../active-directory/managed-identities-azure-resources/overview.md)向外部服務進行驗證。

如需詳細資訊和詳細範例，請參閱 [HTTP 功能](durable-functions-http-features.md)一文。

> [!NOTE]
> Durable Functions 2.0 和更新版本中提供直接從協調器函式呼叫 HTTP 端點的功能。

### <a name="passing-multiple-parameters"></a>傳遞多個參數。

您不可能直接將多個參數傳遞至活動函式。 建議傳入物件陣列或複合物件。

# <a name="c"></a>[C#](#tab/csharp)

在 .NET 中，您也可以使用 [ValueTuples](/dotnet/csharp/tuples) 物件。 下列範例會使用附加 [C# 7](/dotnet/csharp/whats-new/csharp-7#tuples) 的 [ValueTuples](/dotnet/csharp/tuples) 新功能：

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="orchestrator"></a>協調器

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="getweather-activity"></a>`GetWeather` 活動

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="orchestrator"></a>協調器

```python
from collections import namedtuple
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    Location = namedtuple('Location', ['city', 'state'])
    location = Location(city='Seattle', state= 'WA')

    weather = yield context.call_activity("GetWeather", location)

    # ...

```
#### <a name="getweather-activity"></a>`GetWeather` 活動

```python
from collections import namedtuple

Location = namedtuple('Location', ['city', 'state'])

def main(location: Location) -> str:
    city, state = location
    return f"Hello {city}, {state}!"
```

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [協調器程式碼條件約束](durable-functions-code-constraints.md)
