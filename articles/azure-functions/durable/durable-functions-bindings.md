---
title: 長期函式中的繫結 - Azure
description: 如何針對 Azure Functions 的 Durable Functions 擴充功能使用觸發程序和繫結。
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 1f42c6c9b0086d49e539040334c83cfc0c6feb42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278216"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>長期函式中的繫結 (Azure Functions)

[長期函式](durable-functions-overview.md)延伸模組引進了兩個新的觸發程序繫結，可控制協調器和活動函式的執行。 它也引進了輸出繫結，可作為長期函式執行階段的用戶端。

## <a name="orchestration-trigger"></a>業務流程觸發器

業務流程觸發器使您能夠編寫[持久協調器函數](durable-functions-types-features-overview.md#orchestrator-functions)。 這個觸發程序支援啟動新的協調器函式執行個體，以及繼續使用「等候」工作的現有協調器函式執行個體。

當您使用 Azure Functions 適用的 Visual Studio 工具時，協調流程觸發程序會使用 [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet) .NET 屬性進行設定。

當您以指令碼語言 (例如，JavaScript 或 C# 指令碼處理) 撰寫協調器函式時，協調流程觸發程序會由 *function.json* 檔案之 `bindings` 陣列中的下列 JSON 物件所定義：

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`是用戶端在要啟動此業務流程函數的新實例時必須使用的業務流程的名稱。 這是選用屬性。 如果未指定，會使用函式的名稱。

此觸發程序繫結會在內部輪詢函式應用程式之預設儲存體帳戶中的一系列佇列。 這些佇列是延伸模組的內部實作詳細資料，這就是為什麼它們未在繫結屬性中明確設定。

### <a name="trigger-behavior"></a>觸發程序行為

以下是協調流程觸發程序的一些附註：

* **單一執行緒** - 單一發送器執行緒用於單一主機執行個體上的所有協調器函式執行。 基於這個理由，請務必確保協調器函式程式碼有效率，且不會執行任何 I/O。 也務必要確保此執行緒不會執行除了等候長期函式特定工作類型以外的任何非同步工作。
* **有害訊息處理** - 協調流程觸發程序中並無有害訊息支援。
* **訊息可見度** - 協調流程觸發程序訊息會被清除佇列，並且在可設定持續期間保持不可見。 只要函式應用程式正在執行且狀況良好，就會自動更新這些訊息的可見度。
* **傳回值** - 傳回值會序列化為 JSON，保存到 Azure 資料表儲存體中的協調流程歷程記錄資料表。 協調流程用戶端繫結可以查詢這些傳回值，會在稍後說明。

> [!WARNING]
> 協調器函式應該永不使用協調流程繫結觸發程序繫結以外的任何輸入或輸出繫結。 這麼做有可能會造成長期工作延伸模組的問題，因為這些繫結可能不會遵照單一執行緒和 I/O 規則。 如果要使用其他綁定，請將它們添加到從協調器函式呼叫的活動函數中。

> [!WARNING]
> JavaScript 協調器函式永遠都不應宣告為 `async`。

### <a name="trigger-usage-net"></a>觸發程序使用方式 (.NET)

協調流程觸發程序繫結支援輸入及輸出。 協助了解輸入和輸出處理的一些事項如下：

* **輸入**- .NET 業務流程函數`DurableOrchestrationContext`僅作為參數類型支援。 不支援直接在函式簽章中還原序列化輸入。 代碼必須使用`GetInput<T>`（.NET）`getInput`或 （JavaScript） 方法來獲取協調器函數輸入。 這些輸入必須是 JSON 可序列化類型。
* **輸出** - 協調流程觸發程序支援輸出值以及輸入。 函式的傳回值會用來指派輸出值，而且必須是 JSON 可序列化。 如果 .NET 函式傳回 `Task` 或 `void`，則會將 `null` 值儲存為輸出。

### <a name="trigger-sample"></a>觸發程序範例

以下示例代碼顯示了最簡單的"Hello World"協調器函數可能是什麼樣子：

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> 前面的代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`DurableOrchestrationContext`而不是`IDurableOrchestrationContext`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

#### <a name="javascript-functions-20-only"></a>JavaScript (僅限 Functions 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> JavaScript 中`context`的物件不表示持久業務流程上下文，而是[表示整個函數上下文](../functions-reference-node.md#context-object)。 您可以透過 `context` 物件的 `df` 屬性來存取協調流程方法。

> [!NOTE]
> JavaScript 協調器應使用 `return`。 `durable-functions` 程式庫會負責呼叫 `context.done` 方法。

大部分協調器函式會呼叫活動函式；因此，這裡的 "Hello World" 範例示範如何呼叫活動函式：

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> 前面的代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`DurableOrchestrationContext`而不是`IDurableOrchestrationContext`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

#### <a name="javascript-functions-20-only"></a>JavaScript (僅限 Functions 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>活動觸發器

活動觸發器使您能夠編寫由協調器函式呼叫的函數，稱為[活動函數](durable-functions-types-features-overview.md#activity-functions)。

如果使用 Visual Studio，則使用`ActivityTriggerAttribute`.NET 屬性配置活動觸發器。

如果您使用 VS Code 或 Azure 入口網站來進行開發，則活動觸發程序會由 *function.json* 之 `bindings` 陣列中的下列 JSON 物件定義：

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` 是活動的名稱。 此值是協調器函數用於調用此活動函數的名稱。 這是選用屬性。 如果未指定，會使用函式的名稱。

此觸發程序繫結會在內部輪詢函式應用程式之預設儲存體帳戶中的佇列。 這個佇列是延伸模組的內部實作詳細資料，這就是為什麼它未在繫結屬性中明確設定。

### <a name="trigger-behavior"></a>觸發程序行為

以下是活動觸發程序的一些附註：

* **執行緒** - 與協調流程觸發程序不同，活動觸發程序在執行緒或 I/O 方面沒有任何限制。 它們可以被視為一般函式。
* **有害訊息處理** - 活動觸發程序中沒有任何有害訊息支援。
* **訊息可見度** - 活動觸發程序訊息會被清除佇列，並且在可設定持續期間保持不可見。 只要函式應用程式正在執行且狀況良好，就會自動更新這些訊息的可見度。
* **傳回值** - 傳回值會序列化為 JSON，保存到 Azure 資料表儲存體中的協調流程歷程記錄資料表。

> [!WARNING]
> 活動函式的儲存體後端是實作詳細資料，使用者程式碼不應直接與這些儲存體實體互動。

### <a name="trigger-usage-net"></a>觸發程序使用方式 (.NET)

活動觸發程序繫結支援輸入及輸出，就像協調流程觸發程序一樣。 協助了解輸入和輸出處理的一些事項如下：

* **輸入**- .NET 活動函數`DurableActivityContext`本機用作參數類型。 或者，可以使用 JSON 可序列化的任何參數類型宣告活動函式。 使用 時`DurableActivityContext`，可以調用`GetInput<T>`以提取和取消序列化活動函數輸入。
* **輸出** - 活動函式支援輸出值和輸入。 函式的傳回值會用來指派輸出值，而且必須是 JSON 可序列化。 如果 .NET 函式傳回 `Task` 或 `void`，則會將 `null` 值儲存為輸出。
* **中繼資料**：.NET 活動函式可以繫結至 `string instanceId` 參數，以取得父代協調流程的執行個體識別碼。

### <a name="trigger-sample"></a>觸發程序範例

以下示例代碼顯示了簡單的"Hello World"活動功能可能是什麼樣子：

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> 前面的代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`DurableActivityContext`而不是`IDurableActivityContext`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

.NET `ActivityTriggerAttribute` 繫結的預設參數類型是 `IDurableActivityContext`。 不過，.NET活動觸發程序也支援直接繫結至JSON 可序列化類型 (包括基本類型)，因此，同一個函式可以簡化如下：

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (僅限 Functions 2.0)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript 繫結也能以額外參數形式傳入，因此，同一個函式可以簡化如下：

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>使用輸入和輸出綁定

除了活動觸發器綁定之外，還可以使用常規輸入和輸出綁定。 例如，您可以將輸入帶到活動綁定中，並使用 EventHub 輸出綁定向 EventHub 發送消息：

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>協調流程用戶端

業務流程用戶端綁定使您能夠編寫與協調器函數交互的函數。 這些函數有時稱為[用戶端函數](durable-functions-types-features-overview.md#client-functions)。 例如，您可以下列方式處理協調流程執行個體：

* 啟動它們。
* 查詢其狀態。
* 終止它們。
* 在它們執行時傳送事件給它們。
* 清除執行個體記錄。

如果使用 Visual Studio，則可以使用`OrchestrationClientAttribute`持久函數 1.0 的 .NET 屬性綁定到業務流程用戶端。 從持久函數 2.0 開始，可以使用`DurableClientAttribute`.NET 屬性綁定到業務流程用戶端。

如果使用指令碼語言（例如 *.csx*或 *.js*檔）進行開發，則業務流程觸發器由`bindings`*函數*陣列中的以下 JSON 物件定義：

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` - 在多個函式應用程式共用相同儲存體帳戶，但是必須相互隔離的案例中使用。 若未指定，就會使用 `host.json` 的預設值。 此值必須符合目標協調器函式所使用的值。
* `connectionName` - 包含儲存體帳戶連接字串的應用程式設定名稱。 這個連接字串代表的儲存體帳戶，必須是目標協調器函式使用的相同帳戶。 如果未指定，則會使用函數應用程式的預設儲存體帳戶連接字串。

> [!NOTE]
> 在大部分情況下，建議您省略這些屬性，並依賴預設行為。

### <a name="client-usage"></a>用戶端使用方式

在 .NET 函數中，通常`IDurableOrchestrationClient`綁定到 ，這使您能夠完全訪問持久函數支援的所有業務流程用戶端 API。 在較舊的持久函數 2.x 版本中，改為綁定到類`DurableOrchestrationClient`。 在 JavaScript 中，從`getClient`返回的物件將公開相同的 API。 用戶端物件上的 API 包括：

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

或者，.NET 函數可以綁定`IAsyncCollector<T>`到`T``StartOrchestrationArgs`或`JObject`的位置。

有關這些操作的詳細資訊，`IDurableOrchestrationClient`請參閱 API 文檔。

### <a name="client-sample-visual-studio-development"></a>用戶端範例 (Visual Studio 開發)

以下是範例佇列觸發函式，它會啟動 "HelloWorld" 協調流程。

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> 前面的 C# 代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`OrchestrationClient`屬性而不是`DurableClient`屬性，並且必須使用`DurableOrchestrationClient`參數類型而不是`IDurableOrchestrationClient`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

### <a name="client-sample-not-visual-studio"></a>用戶端範例 (不是 Visual Studio)

如果您不是使用 Visual Studio 進行開發，您可以建立下列 function.json** 檔案。 此範例示範如何設定佇列觸發函式，它使用長期協調流程用戶端繫結：

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> 前面的 JSON 用於持久函數 2.x。 對於持久函數 1.x，必須使用`orchestrationClient`而不是`durableClient`作為觸發器類型。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

以下是啟動新的協調器函式執行個體的特定語言範例。

#### <a name="c-script-sample"></a>C# 腳本示例

下面的示例演示如何使用持久業務流程用戶端綁定從佇列觸發的 C# 函數啟動新的函數實例：

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> 前面的代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`DurableOrchestrationClient`參數類型而不是`IDurableOrchestrationClient`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

#### <a name="javascript-sample"></a>JavaScript 範例

下列範例會示範如何使用長期協調流程用戶端繫結以從 JavaScript 函式啟動新的函式執行個體：

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

啟動執行個體的更多詳細資料可以在[執行個體管理](durable-functions-instance-management.md)中找到。

## <a name="entity-trigger"></a>實體觸發器

實體觸發器允許您創作[實體函數](durable-functions-entities.md)。 此觸發器支援處理特定實體實例的事件。

當您使用 Visual Studio 工具進行 Azure 函數時，將使用`EntityTriggerAttribute`.NET 屬性配置實體觸發器。

> [!NOTE]
> 實體觸發器可從持久函數 2.x 中開始。

此觸發程序繫結會在內部輪詢函式應用程式之預設儲存體帳戶中的一系列佇列。 這些佇列是延伸模組的內部實作詳細資料，這就是為什麼它們未在繫結屬性中明確設定。

### <a name="trigger-behavior"></a>觸發程序行為

以下是有關實體觸發器的一些說明：

* **單線程**：單個調度程式執行緒用於處理特定實體的操作。 如果同時向單個實體發送多條消息，則操作將一次處理。
* **毒消息處理**- 實體觸發器中沒有毒消息支援。
* **消息可見度**- 實體觸發器消息在可配置的持續時間內清除佇列並保持不可見。 只要函式應用程式正在執行且狀況良好，就會自動更新這些訊息的可見度。
* **傳回值**- 實體函數不支援傳回值。 有特定的 API 可用於保存狀態或將值傳遞回業務流程。

執行期間對實體所做的任何狀態更改都將在執行完成後自動保留。

### <a name="trigger-usage-net"></a>觸發程序使用方式 (.NET)

每個實體函數都有一個 參數`IDurableEntityContext`類型，該參數類型具有以下成員：

* **機構名稱**：當前執行實體的名稱。
* **實體金鑰**：當前執行實體的鍵。
* **實體 Id**：當前正在執行的實體的 ID。
* **操作名稱**：當前操作的名稱。
* **哈斯狀態**：實體是否存在，即具有某種狀態。 
* **獲取狀態\<tstate>（）**： 獲取實體的目前狀態。 如果它不存在，則創建它並將其初始化到`default<TState>`。 參數`TState`必須是基元或 JSON 可序列化的類型。 
* **獲取狀態\<T 狀態>（功能）**：獲取實體的目前狀態。 如果不存在，則通過調用提供的`initfunction`參數創建它。 參數`TState`必須是基元或 JSON 可序列化的類型。 
* **SetState（arg）**：創建或更新實體的狀態。 參數`arg`必須是 JSON 可序列化的物件或基元。
* **刪除狀態（）**：刪除實體的狀態。 
* **獲取輸入\<tinput>（）**： 獲取當前操作的輸入。 類型`TInput`參數必須是基元或 JSON 可序列化的類型。
* **返回（arg）**：向調用該操作的業務流程傳回值。 參數`arg`必須是基元或 JSON 可序列化的物件。
* **SignalEntity（實體Id、計畫時間、操作、輸入）：** 向實體發送單向消息。 參數`operation`必須是非空字串，可選`scheduledTimeUtc`必須是叫用作業的 UTC 日期時間，`input`並且參數必須是基元或 JSON 可序列化的物件。
* **創建新業務流程（協調器功能名稱、輸入）：** 啟動新的業務流程。 參數`input`必須是基元或 JSON 可序列化的物件。

可以使用`IDurableEntityContext``Entity.Current`非同步本地屬性訪問傳遞給實體函數的物件。 使用基於類的程式設計模型時，此方法很方便。

### <a name="trigger-sample-c-function-based-syntax"></a>觸發示例（基於 C# 函數的語法）

下列程式碼會舉例說明實作為持久函式的簡單「計數器」** 實體。 此函式會定義三個作業 (`add`、`reset` 和 `get`)，每個作業都會以整數狀態運作。

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

有關基於函數的語法以及如何使用它的詳細資訊，請參閱[基於函數的語法](durable-functions-dotnet-entities.md#function-based-syntax)。

### <a name="trigger-sample-c-class-based-syntax"></a>觸發示例（基於 C# 類的語法）

下列範例等同於使用類別和方法來實作 `Counter` 實體。

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

此實體的狀態是 `Counter` 類型的物件，其中包含可儲存計數器目前值的欄位。 為了將此物件保存在儲存體中，該物件會由 [Json.NET](https://www.newtonsoft.com/json) 程式庫進行序列化和還原序列化。 

如需以類別為基礎的語法及其使用方式的詳細資訊，請參閱[定義實體類別](durable-functions-dotnet-entities.md#defining-entity-classes)。

> [!NOTE]
> 使用實體類別時，具有 `[FunctionName]` 屬性的函式進入點方法「必須」** 宣告為 `static`。 非靜態的進入點方法可能會導致初始化多個物件，並可能導致其他未定義的行為。

實體類具有用於與綁定和 .NET 依賴項注入進行交互的特殊機制。 有關詳細資訊，請參閱[實體構造](durable-functions-dotnet-entities.md#entity-construction)。

### <a name="trigger-sample-javascript"></a>觸發示例（JavaScript）

以下代碼是作為 JavaScript 編寫的持久函數實現的簡單*計數器*實體的示例。 此函式會定義三個作業 (`add`、`reset` 和 `get`)，每個作業都會以整數狀態運作。

**函數.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> 從 `durable-functions` npm 套件 **1.3.0** 版開始，JavaScript 提供了耐久性實體。

## <a name="entity-client"></a>實體用戶端

實體用戶端綁定使您能夠非同步觸發[實體函數](#entity-trigger)。 這些函數有時稱為[用戶端函數](durable-functions-types-features-overview.md#client-functions)。

如果使用 Visual Studio，則可以使用`DurableClientAttribute`.NET 屬性綁定到實體用戶端。

> [!NOTE]
> `[DurableClientAttribute]`還可用於綁定到[業務流程用戶端](#orchestration-client)。

如果您使用指令碼語言（例如 *.csx*或 *.js*檔）進行開發，則實體觸發器由`bindings`*函數*陣列中的以下 JSON 物件定義：

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` - 在多個函式應用程式共用相同儲存體帳戶，但是必須相互隔離的案例中使用。 若未指定，就會使用 `host.json` 的預設值。 此值必須與目標實體函數使用的值匹配。
* `connectionName` - 包含儲存體帳戶連接字串的應用程式設定名稱。 此連接字串表示的存儲帳戶必須與目標實體函數使用的存儲帳戶相同。 如果未指定，則會使用函數應用程式的預設儲存體帳戶連接字串。

> [!NOTE]
> 在大多數情況下，我們建議您省略可選屬性並依賴于預設行為。

### <a name="entity-client-usage"></a>實體用戶端使用方式

在 .NET 函數中，通常`IDurableEntityClient`綁定到 ，這使您能夠完全訪問持久實體支援的所有用戶端 API。 您還可以綁定到`IDurableOrchestrationClient`介面，該介面為實體和業務流程提供對用戶端 API 的訪問。 用戶端物件上的 API 包括：

* **讀取實體Async\<T>**： 讀取實體的狀態。 它返回一個回應，指示目標實體是否存在，如果是，則返回其狀態。
* **SignalEntityAsync**：向實體發送單向消息，並等待其排隊。
* **清單實體 Async**：對多個實體的狀態的查詢。 實體可以按*名稱*和*上次操作時間*進行查詢。

無需在發送信號之前創建目標實體 - 可以從處理信號的實體函數內創建實體狀態。

> [!NOTE]
> 請務必瞭解，從用戶端發送的"信號"只是排隊，稍後會非同步處理。 特別是，通常在`SignalEntityAsync`實體開始操作之前返回，並且無法返回傳回值或觀察異常。 如果需要更強的保證（例如工作流），則應使用*協調器函數*，該函數可以等待實體操作完成，並可以處理傳回值並觀察異常。

### <a name="example-client-signals-entity-directly---c"></a>示例：用戶端信號實體直接 - C#

下面是調用"計數器"實體的佇列觸發函數的示例。

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="example-client-signals-entity-via-interface---c"></a>示例：通過介面用戶端信號實體 - C#

在可能的情況下，我們建議[通過介面訪問實體](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces)，因為它提供了更多的類型檢查。 例如，假設前面提到的`Counter`實體實現了介面`ICounter`，定義如下：

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

然後，用戶端代碼可用於`SignalEntityAsync<ICounter>`組建類型安全代理：

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

參數`proxy`是 生成的 動態實例`ICounter`，該實例在內部將調用`Add`轉換為對`SignalEntityAsync`的等效（未鍵入）調用。

> [!NOTE]
> API`SignalEntityAsync`表示單向操作。 如果實體介面返回`Task<T>`，`T`則參數的值將始終為空或`default`。

特別是，由於不返回任何值，因此發出`Get`操作信號沒有意義。 相反，用戶端可以使用 來`ReadStateAsync`直接存取計數器狀態，也可以啟動叫用作業的`Get`協調器函數。

### <a name="example-client-signals-entity---javascript"></a>示例：用戶端信號實體 - JavaScript

下面是一個佇列觸發函數的示例，該函數在 JavaScript 中發出"計數器"實體的信號。

**函數.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
      },
      {
        "name": "starter",
        "type": "durableClient",
        "direction": "in"
      }
    ],
    "disabled": false
  }
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> 從 `durable-functions` npm 套件 **1.3.0** 版開始，JavaScript 提供了耐久性實體。

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json 設定

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [用於實例管理的內置 HTTP API 引用](durable-functions-http-api.md)
