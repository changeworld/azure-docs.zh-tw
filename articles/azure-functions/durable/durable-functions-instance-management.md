---
title: 在 Durable Functions 中管理執行個體 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中管理執行個體。
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 07a96fdd6350d8db38a92c23e510afb05f7416fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277748"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>在 Azure 中管理 Durable Functions 中的執行個體

如果您使用的是 Azure[函數的持久函數](durable-functions-overview.md)擴展，或者想要開始執行此操作，請確保從中獲得最佳使用。 您可以通過瞭解有關如何管理持久函數業務流程實例的詳細瞭解來優化它們。 本文討論每個執行個體管理作業的詳細資料。

例如，您可以啟動和終止實例，也可以查詢實例，包括使用篩選器查詢所有實例和查詢實例的能力。 此外，您可以將事件發送到實例、等待業務流程完成以及檢索 HTTP 管理 Webhook URL。 本文還介紹其他管理操作，包括重退實例、清除實例歷史記錄和刪除任務中心。

在持久函數中，您可以選擇如何實現每個管理操作。 本文提供了使用[Azure 函數核心工具](../functions-run-local.md)用於 .NET （C#） 和 JavaScript 的示例。

## <a name="start-instances"></a>啟動實例

能夠啟動業務流程實例非常重要。 當您使用在另一個函數的觸發器中綁定的持久函數時，通常會這樣做。

[業務流程用戶端綁定](durable-functions-bindings.md#orchestration-client)上的`StartNewAsync`（.NET） 或`startNew`（JavaScript） 方法啟動一個新實例。 在內部，此方法將消息排入控制項佇列中，然後使用使用[業務流程觸發器綁定](durable-functions-bindings.md#orchestration-trigger)的指定名稱觸發函數的開始。

協調流程程序已成功排定時，此非同步作業會完成。

啟動新業務流程實例的參數如下所示：

* **Name**：要排程的協調器函式的名稱。
* **Input**：應該當作輸入傳給協調器函式的任何 JSON 可序列化資料。
* **InstanceId**：(選擇性) 執行個體的唯一識別碼。 如果不指定此參數，該方法將使用隨機 ID。

> [!TIP]
> 使用隨機識別碼作為執行個體識別碼。 當您跨多個 VM 縮放協調器函數時，隨機實例 I 有助於確保相等的負載分佈。 使用非隨機實例 ID 的適當時間是 ID 必須來自外部源，或者實現[單例協調器](durable-functions-singletons.md)模式時。

以下代碼是啟動新業務流程實例的示例函數：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> 前面的 C# 代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`OrchestrationClient`屬性而不是`DurableClient`屬性，並且必須使用`DurableOrchestrationClient`參數類型而不是`IDurableOrchestrationClient`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

<a name="javascript-function-json"></a>除非另有說明，此頁上的示例使用具有以下函數.json 的 HTTP 觸發器。

**函數.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
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

> [!NOTE]
> 此示例針對持久函數版本 2.x。 在版本 1.x`orchestrationClient`中，`durableClient`使用 而不是 。

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

還可以使用[Azure 函數核心工具](../functions-run-local.md)`durable start-new`命令直接啟動實例。 它需要以下參數：

* （必需） ：要啟動的函數的名稱。 ** `function-name` **
* （可選）：輸入函數，內聯或通過 JSON 檔。 ** `input` ** 對於檔，將首碼添加到具有`@`的檔的路徑，如`@path/to/file.json`。
* （可選）：業務流程實例的 ID。 ** `id` ** 如果不指定此參數，該命令將使用隨機 GUID。
* （可選）：包含要使用的存儲連接字串的應用程式設定的名稱。 ** `connection-string-setting` ** 預設為 AzureWebJobsStorage。
* （可選）：要使用的持久函數任務中心的名稱。 ** `task-hub-name` ** 預設為 DurableFunctionsHub。 您還可以使用持久任務：HubName 在[host.json](durable-functions-bindings.md#host-json)中設置此設置。

> [!NOTE]
> 核心工具命令假定您從函數應用的根目錄運行它們。 如果顯式提供 和`connection-string-setting``task-hub-name`參數，則可以從任何目錄中運行命令。 儘管可以在不運行函數應用主機的情況下運行這些命令，但您可能會發現，除非主機正在運行，否則無法觀察到某些效果。 例如，`start-new`命令將啟動消息排入目標任務中心，但業務流程實際上不會運行，除非有一個函數應用主機進程運行，可以處理該消息。

以下命令啟動名為 HelloWorld 的函數，並將檔`counter-data.json`的內容傳遞給它：

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>查詢實例

作為管理業務流程努力的一部分，您很可能需要收集有關業務流程實例狀態的資訊（例如，它是正常完成的還是失敗的）。

[業務流程用戶端綁定](durable-functions-bindings.md#orchestration-client)上的`GetStatusAsync``getStatus`（.NET） 或 （JavaScript） 方法查詢業務流程實例的狀態。

它會以 `instanceId` (必要)、`showHistory` (選用)、`showHistoryOutput` (選用) 和 `showInput` (選用) 作為參數。

* **`showHistory`**：如果設置為`true`，則回應包含執行歷史記錄。
* **`showHistoryOutput`**：如果設置為`true`，則執行歷史記錄包含活動輸出。
* **`showInput`**：如果設置為`false`，回應將不包含函數的輸入。 預設值是 `true`。

此方法會傳回具有下列屬性的物件：

* **Name**：協調器函式的名稱。
* **InstanceId**：協調流程的執行個體識別碼 (應該與 `instanceId` 輸入相同)。
* **CreatedTime**：協調器函式開始執行的時間。
* **LastUpdatedTime**：協調流程前次執行檢查點檢查的時間。
* **Input**：函式的 JSON 值輸入。 如果`showInput`此欄位為 false，則不填充此欄位。
* **CustomStatus**：JSON 格式的自訂協調流程狀態。
* **Output**：函式的 JSON 值輸出 (如果函式已完成)。 如果協調器函數失敗，此屬性包括失敗詳細資訊。 如果協調器函數已終止，則此屬性包括終止的原因（如果有）。
* **RuntimeStatus**：下列其中一個值：
  * **擱置**：已排程的執行個體尚未開始執行。
  * **Running**：執行個體已開始執行。
  * **Completed**：執行個體已正常完成。
  * **ContinuedAsNew**：執行個體本身以新的記錄重新啟動。 此狀態為瞬態。
  * **Failed**：函式失敗，發生錯誤。
  * **Terminated**：執行個體已突然停止。
* **歷程記錄**：協調流程的執行記錄。 只有在 `showHistory` 設為 `true` 時，才會填入此欄位。

如果實例不存在`null`，此方法將返回`undefined`（.NET） 或 （JavaScript）。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> 前面的 C# 代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`OrchestrationClient`屬性而不是`DurableClient`屬性，並且必須使用`DurableOrchestrationClient`參數類型而不是`IDurableOrchestrationClient`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

有關函數.json 配置，請參閱[啟動實例](#javascript-function-json)。

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

還可以通過使用[Azure 函數核心工具](../functions-run-local.md)`durable get-runtime-status`命令直接獲取業務流程實例的狀態。 它需要以下參數：

* （必需） ：業務流程實例的 ID。 ** `id` **
* （可選）：如果設置為**`show-input`**`true`，回應包含函數的輸入。 預設值是 `false`。
* （可選）：如果設置為**`show-output`**`true`，回應將包含函數的輸出。 預設值是 `false`。
* （可選）：包含要使用的存儲連接字串的應用程式設定的名稱。 ** `connection-string-setting` ** 預設值為 `AzureWebJobsStorage`。
* （可選）：要使用的持久函數任務中心的名稱。 ** `task-hub-name` ** 預設值為 `DurableFunctionsHub`。 也可以使用持久任務：HubName 在[host.json](durable-functions-bindings.md#host-json)中設置它。

以下命令檢索具有 0ab8c55a6644d68a3a8b220b12d209c 的業務流程實例 ID的實例的狀態（包括輸入和輸出）。 它假定您正在從函數應用`func`的根目錄中運行命令：

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

可以使用 命令`durable get-history`檢索業務流程實例的歷史記錄。 它需要以下參數：

* （必需） ：業務流程實例的 ID。 ** `id` **
* （可選）：包含要使用的存儲連接字串的應用程式設定的名稱。 ** `connection-string-setting` ** 預設值為 `AzureWebJobsStorage`。
* （可選）：要使用的持久函數任務中心的名稱。 ** `task-hub-name` ** 預設值為 `DurableFunctionsHub`。 也可以使用持久任務：HubName 在 host.json 中設置它。

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>查詢所有實例

與一次查詢業務流程中的一個實例不同，您可能會發現一次查詢所有這些實例更有效。

您可以使用 `GetStatusAsync` (.NET) 或 `getStatusAll` (JavaScript) 方法來查詢所有協調流程執行個體的狀態。 在 .NET 中，您可以`CancellationToken`傳遞物件，以防要取消它。 此方法會和使用參數的 `GetStatusAsync`方法一樣傳回具有相同屬性的物件。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> 前面的 C# 代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`OrchestrationClient`屬性而不是`DurableClient`屬性，並且必須使用`DurableOrchestrationClient`參數類型而不是`IDurableOrchestrationClient`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

有關函數.json 配置，請參閱[啟動實例](#javascript-function-json)。

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

通過使用[Azure 函數核心工具](../functions-run-local.md)`durable get-instances`命令，還可以直接查詢實例。 它需要以下參數：

* （可選）：此命令支援分頁。 ** `top` ** 此參數會對應至每個要求擷取的執行個體數目。 預設值為 10。
* （可選）：用於指示要檢索的實例的頁面或部分的權杖。 ** `continuation-token` ** 每個 `get-instances` 執行都會將權杖傳回給下一組執行個體。
* （可選）：包含要使用的存儲連接字串的應用程式設定的名稱。 ** `connection-string-setting` ** 預設值為 `AzureWebJobsStorage`。
* （可選）：要使用的持久函數任務中心的名稱。 ** `task-hub-name` ** 預設值為 `DurableFunctionsHub`。 也可以使用持久任務：HubName 在[host.json](durable-functions-bindings.md#host-json)中設置它。

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>具有篩選器的查詢實例

如果您並不真正需要標準實例查詢可以提供的所有資訊，該怎麼辦？ 例如，如果您只是在尋找業務流程創建時間或業務流程運行時狀態，該怎麼辦？ 您可以通過應用篩選器來縮小查詢範圍。

使用`GetStatusAsync`（.NET）`getStatusBy`或 （JavaScript） 方法獲取與一組預定義的篩選器匹配的業務流程實例的清單。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> 前面的 C# 代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`OrchestrationClient`屬性而不是`DurableClient`屬性，並且必須使用`DurableOrchestrationClient`參數類型而不是`IDurableOrchestrationClient`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

有關函數.json 配置，請參閱[啟動實例](#javascript-function-json)。

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

在 Azure 函數核心工具中，還可以將`durable get-instances`命令與篩選器一起使用。 除了`top`上述 、`continuation-token`和`connection-string-setting``task-hub-name`參數之外，您還可以使用三個篩選器參數 （、`created-after``created-before`和`runtime-status`）。

* （可選）：檢索在此日期/時間 （UTC） 之後創建的實例。 ** `created-after` ** 可接受 ISO 8601 格式的日期時間。
* （可選）：檢索在此日期/時間 （UTC） 之前創建的實例。 ** `created-before` ** 可接受 ISO 8601 格式的日期時間。
* （可選）：檢索具有特定狀態的實例（例如，正在運行或已完成）。 ** `runtime-status` ** 可以提供多個狀態 (以空格分隔)。
* （可選）：每個請求檢索的實例數。 ** `top` ** 預設值為 10。
* （可選）：用於指示要檢索的實例的頁面或部分的權杖。 ** `continuation-token` ** 每個 `get-instances` 執行都會將權杖傳回給下一組執行個體。
* （可選）：包含要使用的存儲連接字串的應用程式設定的名稱。 ** `connection-string-setting` ** 預設值為 `AzureWebJobsStorage`。
* （可選）：要使用的持久函數任務中心的名稱。 ** `task-hub-name` ** 預設值為 `DurableFunctionsHub`。 也可以使用持久任務：HubName 在[host.json](durable-functions-bindings.md#host-json)中設置它。

如果不提供`created-after`任何篩選器 （、`created-before`或`runtime-status`），則命令將簡單地檢索`top`實例，而不考慮運行時狀態或創建時間。

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>終止實例

如果業務流程實例執行時間過長，或者只需在它完成之前出於任何原因停止它，則可以選擇終止它。

您可以使用[業務流程用戶端綁定](durable-functions-bindings.md#orchestration-client)的`TerminateAsync`（.NET）`terminate`或 （JavaScript） 方法終止實例。 這兩個參數是`instanceId`和`reason`字串，它們寫入日誌和實例狀態。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> 前面的 C# 代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`OrchestrationClient`屬性而不是`DurableClient`屬性，並且必須使用`DurableOrchestrationClient`參數類型而不是`IDurableOrchestrationClient`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

有關函數.json 配置，請參閱[啟動實例](#javascript-function-json)。

---

終止的實例最終將轉換為狀態`Terminated`。 但是，這種轉換不會立即發生。 相反，終止操作將與該實例的其他操作一起在任務中心排隊。 您可以使用[實例查詢](#query-instances)API 來瞭解終止實例實際到達`Terminated`狀態的次。

> [!NOTE]
> 實例終止當前不會傳播。 活動函數和子業務流程運行到完成，無論您是否已終止調用它們的業務流程實例。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

還可以通過使用[Azure 函數核心工具](../functions-run-local.md)`durable terminate`命令直接終止業務流程實例。 它需要以下參數：

* （必需） ：要終止的業務流程實例的 ID。 ** `id` **
* （可選）：終止的原因。 ** `reason` **
* （可選）：包含要使用的存儲連接字串的應用程式設定的名稱。 ** `connection-string-setting` ** 預設值為 `AzureWebJobsStorage`。
* （可選）：要使用的持久函數任務中心的名稱。 ** `task-hub-name` ** 預設值為 `DurableFunctionsHub`。 也可以使用持久任務：HubName 在[host.json](durable-functions-bindings.md#host-json)中設置它。

以下命令終止 ID為 0ab8c55a6644d68a3a8b220b12d209c 的業務流程實例：

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>將事件發送到實例

在某些情況下，協調器函數能夠等待和偵聽外來事件非常重要。 這包括等待[人機交互](durable-functions-overview.md#human)的[監視器函數](durable-functions-overview.md#monitoring)和函數。

使用`RaiseEventAsync`（.NET） 方法或`raiseEvent`[業務流程用戶端綁定](durable-functions-bindings.md#orchestration-client)的 （JavaScript） 方法將事件通知發送到正在運行的實例。 可以處理這些事件的實例是等待調用`WaitForExternalEvent`（.NET） 或屈服于`waitForExternalEvent`（JavaScript） 調用的實例。

（.NET）`RaiseEventAsync`和`raiseEvent`（JavaScript） 的參數如下所示：

* **InstanceId**：執行個體的唯一識別碼。
* **EventName**：要傳送的事件名稱。
* **EventData**：要傳送至執行個體的 JSON 可序列化裝載。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> 前面的 C# 代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`OrchestrationClient`屬性而不是`DurableClient`屬性，並且必須使用`DurableOrchestrationClient`參數類型而不是`IDurableOrchestrationClient`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

有關函數.json 配置，請參閱[啟動實例](#javascript-function-json)。

---

> [!NOTE]
> 如果沒有具有指定「執行個體識別碼」的協調流程執行個體，則會捨棄事件訊息。 如果實例存在，但尚未等待事件，則事件將存儲在實例狀態，直到準備好接收和處理它。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

還可以通過使用[Azure 函數核心工具](../functions-run-local.md)`durable raise-event`命令將事件直接引發到業務流程實例。 它需要以下參數：

* （必需） ：業務流程實例的 ID。 ** `id` **
* **`event-name`**：要引發的事件的名稱。
* （可選）：要發送到業務流程實例的資料。 ** `event-data` ** 這可以是 JSON 檔的路徑，也可以直接在命令列上提供資料。
* （可選）：包含要使用的存儲連接字串的應用程式設定的名稱。 ** `connection-string-setting` ** 預設值為 `AzureWebJobsStorage`。
* （可選）：要使用的持久函數任務中心的名稱。 ** `task-hub-name` ** 預設值為 `DurableFunctionsHub`。 也可以使用持久任務：HubName 在[host.json](durable-functions-bindings.md#host-json)中設置它。

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>等候協調流程完成

在長時間運行的業務流程中，您可能需要等待並獲取業務流程的結果。 在這些情況下，在業務流程上定義超時期也很有用。 如果超過超時，則應返回業務流程的狀態，而不是結果。

`WaitForCompletionOrCreateCheckStatusResponseAsync` （.NET） 或`waitForCompletionOrCreateCheckStatusResponse`（JavaScript） 方法可用於同步從業務流程實例獲取實際輸出。 預設情況下，這些方法對`timeout`使用預設值 10 秒，對 使用 1`retryInterval`秒。  

以下是範例 HTTP 觸發函式，它會示範如何使用這個 API：

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

有關函數.json 配置，請參閱[啟動實例](#javascript-function-json)。

---

使用以下行調用函數。 超時使用 2 秒，重試間隔使用 0.5 秒：

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

取決於從協調流程執行個體取得回應所需的時間，會有兩種情況：

* 業務流程實例在定義的超時內完成（在本例中為 2 秒），回應是同步交付的實際業務流程實例輸出：

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* 業務流程實例無法在定義的超時內完成，回應是[HTTP API URL 發現](durable-functions-http-api.md)中描述的預設實例：

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Webhook URL 的格式可能會有所不同，具體取決於正在運行的 Azure 函數主機的版本。 上述範例適用於 Azure Functions 2.0 主機。

## <a name="retrieve-http-management-webhook-urls"></a>檢索 HTTP 管理 Webhook URL

您可以使用外部系統監視或將事件引發到業務流程。 外部系統可以通過 WEBhook URL 與持久函數通信，這些 URL 是 HTTP [API URL 發現](durable-functions-http-features.md#http-api-url-discovery)中描述的預設回應的一部分。 也可以使用[業務流程用戶端綁定](durable-functions-bindings.md#orchestration-client)以程式設計方式訪問 Webhook URL。 `CreateHttpManagementPayload` （.NET） 或`createHttpManagementPayload`（JavaScript） 方法可用於獲取包含這些 Webhook URL 的可序列化物件。

`CreateHttpManagementPayload` （.NET） 和`createHttpManagementPayload`（JavaScript） 方法有一個參數：

* **實例 Id**：實例的唯一 ID。

這些方法返回具有以下字串屬性的物件：

* **Id**：協調流程的執行個體識別碼 (應該與 `InstanceId` 輸入相同)。
* **StatusQueryGetUri**：協調流程執行個體的狀態 URL。
* **SendEventPostUri**：協調流程執行個體的「引發事件」URL。
* **TerminatePostUri**：協調流程執行個體的「終止」URL。
* **清除歷史記錄刪除：** 業務流程實例的"清除歷史記錄"URL。

函數可以將這些物件的實例發送到外部系統，以監視或引發相應業務流程上的事件，如以下示例所示：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> 前面的 C# 代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`DurableActivityContext`而不是`IDurableActivityContext`，必須使用`OrchestrationClient`屬性而不是 屬性`DurableClient`，並且必須使用`DurableOrchestrationClient`參數類型而不是`IDurableOrchestrationClient`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

有關函數.json 配置，請參閱[啟動實例](#javascript-function-json)。

---

## <a name="rewind-instances-preview"></a>倒帶實例（預覽）

如果由於意外原因出現業務流程失敗，則可以使用為此目的構建的 API*將實例倒帶*到以前正常狀態。

> [!NOTE]
> 此 API 並非要取代適當的錯誤處理和重試原則。 相反地，它只是要用於協調流程執行個體因非預期原因而失敗的情況。 有關錯誤處理和重試策略的詳細資訊，請參閱[錯誤處理](durable-functions-error-handling.md)一文。

使用[業務流程用戶端綁定](durable-functions-bindings.md#orchestration-client)的`RewindAsync`（.NET） 或`rewind`（JavaScript） 方法將業務流程重新置於 *"運行"* 狀態。 此方法還將重新運行導致業務流程失敗的活動或子業務流程執行失敗。

例如，假設您有一個涉及一系列[人工審批的](durable-functions-overview.md#human)工作流。 假設有一系列活動功能，通知某人需要他們的批准，並等待即時回應。 在所有核准活動收到回應或超時後，假設另一個活動由於應用程式佈建錯誤（如不正確資料庫連接字串）而失敗。 結果就是深入工作流程的協調流程失敗。 使用`RewindAsync`（.NET）`rewind`或 （JavaScript） API，應用程式管理員可以修復配置錯誤，並將失敗的業務流程回退到故障前的狀態。 不需要重新批准任何人工交互步驟，業務流程現在可以成功完成。

> [!NOTE]
> *倒帶*功能不支援使用持久計時器的倒帶業務流程實例。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> 前面的 C# 代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`OrchestrationClient`屬性而不是`DurableClient`屬性，並且必須使用`DurableOrchestrationClient`參數類型而不是`IDurableOrchestrationClient`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

有關函數.json 配置，請參閱[啟動實例](#javascript-function-json)。

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

還可以使用[Azure 函數核心工具](../functions-run-local.md)`durable rewind`命令直接倒帶業務流程實例。 它需要以下參數：

* （必需） ：業務流程實例的 ID。 ** `id` **
* （可選）：重帶業務流程實例的原因。 ** `reason` **
* （可選）：包含要使用的存儲連接字串的應用程式設定的名稱。 ** `connection-string-setting` ** 預設值為 `AzureWebJobsStorage`。
* （可選）：要使用的持久函數任務中心的名稱。 ** `task-hub-name` ** 預設情況下，使用[host.json](durable-functions-bindings.md#host-json)檔中的任務中心名稱。

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>清除實例歷史記錄

要刪除與業務流程關聯的所有資料，可以清除實例歷史記錄。 例如，您可能希望刪除與已完成實例關聯的任何 Azure 表行和大型消息 Blob。 為此，請使用[業務流程用戶端綁定](durable-functions-bindings.md#orchestration-client)的`PurgeInstanceHistoryAsync`（.NET） 或`purgeInstanceHistory`（JavaScript） 方法。

此方法有兩個重載。 第一個重載通過業務流程實例的 ID 清除歷史記錄：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

有關函數.json 配置，請參閱[啟動實例](#javascript-function-json)。

---

下一個示例顯示計時器觸發的函數，該函數清除指定時間間隔後完成的所有業務流程實例的歷史記錄。 在這種情況下，它會刪除 30 天或 30 天以上完成的所有實例的資料。 它計畫每天運行一次，上午 12 點：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> 前面的 C# 代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`OrchestrationClient`屬性而不是`DurableClient`屬性，並且必須使用`DurableOrchestrationClient`參數類型而不是`IDurableOrchestrationClient`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

該方法`purgeInstanceHistoryBy`可用於有條件地清除多個實例的實例歷史記錄。

**函數.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
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

> [!NOTE]
> 此示例針對持久函數版本 2.x。 在版本 1.x`orchestrationClient`中，`durableClient`使用 而不是 。

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

---

> [!NOTE]
> 要使清除歷史記錄操作成功，目標實例的運行時狀態必須**為"已完成**"、"**終止**"或 **"失敗**"。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

可以使用[Azure 函數核心工具](../functions-run-local.md)`durable purge-history`命令清除業務流程實例的歷史記錄。 與上一節中的第二個 C# 示例類似，它將清除在指定時間間隔內創建的所有業務流程實例的歷史記錄。 您可以按運行時狀態進一步篩選清除的實例。 此命令有數個參數：

* （可選）：清除在此日期/時間 （UTC） 之後創建的實例的歷史記錄。 ** `created-after` ** 可接受 ISO 8601 格式的日期時間。
* （可選）：清除在此日期/時間 （UTC） 之前創建的實例的歷史記錄。 ** `created-before` ** 可接受 ISO 8601 格式的日期時間。
* （可選）：清除具有特定狀態的實例的歷史記錄（例如，正在運行或已完成）。 ** `runtime-status` ** 可以提供多個狀態 (以空格分隔)。
* （可選）：包含要使用的存儲連接字串的應用程式設定的名稱。 ** `connection-string-setting` ** 預設值為 `AzureWebJobsStorage`。
* （可選）：要使用的持久函數任務中心的名稱。 ** `task-hub-name` ** 預設情況下，使用[host.json](durable-functions-bindings.md#host-json)檔中的任務中心名稱。

以下命令刪除 2018 年 11 月 14 日之前在 7：35 （UTC） 之前創建的所有失敗實例的歷史記錄。

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>刪除任務中心

使用[Azure 函數核心工具](../functions-run-local.md)`durable delete-task-hub`命令，可以刪除與特定任務中心關聯的所有存儲專案，包括 Azure 存儲表、佇列和 Blob。 此命令有兩個參數：

* （可選）：包含要使用的存儲連接字串的應用程式設定的名稱。 ** `connection-string-setting` ** 預設值為 `AzureWebJobsStorage`。
* （可選）：要使用的持久函數任務中心的名稱。 ** `task-hub-name` ** 預設情況下，使用[host.json](durable-functions-bindings.md#host-json)檔中的任務中心名稱。

以下命令刪除與`UserTest`任務中心關聯的所有 Azure 存儲資料。

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何處理版本控制](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [用於實例管理的內置 HTTP API 引用](durable-functions-http-api.md)
