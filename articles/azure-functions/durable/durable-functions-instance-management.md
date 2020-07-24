---
title: 在 Durable Functions 中管理執行個體 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中管理執行個體。
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: ce85473e80bfccf1bcff3e21408fd91e4cd428a4
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131322"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>在 Azure 中管理 Durable Functions 中的執行個體

如果您使用 Azure Functions 的[Durable Functions](durable-functions-overview.md)延伸模組，或想要開始這麼做，請確定您已充分利用它。 您可以深入瞭解如何管理 Durable Functions 協調流程實例，藉此將其優化。 本文討論每個執行個體管理作業的詳細資料。

例如，您可以啟動和終止實例，而且可以查詢實例，包括使用篩選準則查詢所有實例和查詢實例的能力。 此外，您可以將事件傳送至實例、等候協調流程完成，並取出 HTTP 管理 webhook Url。 本文也涵蓋其他管理作業，包括倒帶實例、清除實例歷程記錄，以及刪除工作中樞。

在 Durable Functions 中，您可以選擇要如何執行每個管理作業。 本文提供的範例會使用適用于 .NET （c #）、JavaScript 和 Python 的[Azure Functions Core Tools](../functions-run-local.md) 。

## <a name="start-instances"></a>啟動實例

務必要能夠啟動協調流程的實例。 當您在另一個函式的觸發程式中使用 Durable Functions 系結時，通常會進行這項工作。

`StartNewAsync`協調流程用戶端系結上的（.net）、 `startNew` （JavaScript）或 `start_new` （Python）方法會啟動新的實例。 [orchestration client binding](durable-functions-bindings.md#orchestration-client) 就內部而言，這個方法會將訊息將到控制佇列中，然後使用指定的名稱觸發函式的開頭，該函式會使用[協調流程觸發](durable-functions-bindings.md#orchestration-trigger)程式系結。

協調流程程序已成功排定時，此非同步作業會完成。

啟動新協調流程實例的參數如下所示：

* **Name**：要排程的協調器函式的名稱。
* **Input**：應該當作輸入傳給協調器函式的任何 JSON 可序列化資料。
* **InstanceId**：(選擇性) 執行個體的唯一識別碼。 如果您未指定此參數，此方法會使用隨機識別碼。

> [!TIP]
> 使用隨機識別碼作為執行個體識別碼。 當您跨多個 Vm 調整協調器函式時，隨機實例識別碼有助於確保相等的負載分佈。 使用非隨機實例識別碼的適當時間是當識別碼必須來自外部來源，或當您要執行[單一協調](durable-functions-singletons.md)器模式時。

下列程式碼是啟動新協調流程實例的範例函數：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `OrchestrationClient` 屬性，而不是 `DurableClient` 屬性，而且您必須使用 `DurableOrchestrationClient` 參數類型，而不是 `IDurableOrchestrationClient` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>除非另有指定，否則此頁面上的範例會在上使用具有下列 function.js的 HTTP 觸發程式。

**function.json**

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
> 這個範例是以 Durable Functions 2.x 版為目標。 在1.x 版中，請使用， `orchestrationClient` 而不是 `durableClient` 。

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

# <a name="python"></a>[Python](#tab/python)

<a name="javascript-function-json"></a>除非另有指定，否則此頁面上的範例會在上使用具有下列 function.js的 HTTP 觸發程式。

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [    
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
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
> 這個範例是以 Durable Functions 2.x 版為目標。 在1.x 版中，請使用， `orchestrationClient` 而不是 `durableClient` 。

**__ __.py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    
    instance_id = await client.start_new('HelloWorld', None, None)
    logging.log(f"Started orchestration with ID = ${instance_id}.")

```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可以使用 Azure Functions Core Tools 命令，直接啟動實例[Azure Functions Core Tools](../functions-run-local.md) `durable start-new` 。 它需要以下參數：

* ** `function-name` （必要）**：要啟動的函式名稱。
* ** `input` （選擇性）**：對函式的輸入（不論是內嵌或透過 JSON 檔案）。 針對檔案，請將前置詞新增至檔案的路徑 `@` ，例如 `@path/to/file.json` 。
* ** `id` （選擇性）**：協調流程實例的識別碼。 如果您未指定此參數，此命令會使用隨機的 GUID。
* ** `connection-string-setting` （選用）**：應用程式設定的名稱，其中包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* ** `task-hub-name` （選用）**：要使用 Durable Functions 工作中樞的名稱。 預設為 DurableFunctionsHub。 您也可以使用 durableTask： HubName，在[host.js](durable-functions-bindings.md#host-json)中設定此項。

> [!NOTE]
> 核心工具命令會假設您是從函式應用程式的根目錄執行它們。 如果您明確提供 `connection-string-setting` 和 `task-hub-name` 參數，您可以從任何目錄執行命令。 雖然您可以在不執行函式應用程式主機的情況下執行這些命令，但您可能會發現除非主機正在執行，否則您無法觀察到某些效果。 例如，命令會將 `start-new` 開始訊息將到目標工作中樞，但除非有執行的函式應用程式主機進程可以處理訊息，否則不會實際執行協調流程。

下列命令會啟動名為 HelloWorld 的函式，並將檔案的內容傳遞 `counter-data.json` 給它：

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>查詢實例

在您管理協調流程的過程中，您很可能需要收集有關協調流程實例狀態的資訊（例如，它是否已正常完成或失敗）。

`GetStatusAsync`協調流程用戶端系結上的（.net）、 `getStatus` （JavaScript）或 `get_status` （ [orchestration client binding](durable-functions-bindings.md#orchestration-client) Python）方法會查詢協調流程實例的狀態。

它會以 `instanceId` (必要)、`showHistory` (選用)、`showHistoryOutput` (選用) 和 `showInput` (選用) 作為參數。

* **`showHistory`**：如果設定為 `true` ，回應就會包含執行歷程記錄。
* **`showHistoryOutput`**：如果設定為 `true` ，則執行歷程記錄會包含活動輸出。
* **`showInput`**：如果設為 `false` ，回應將不會包含函數的輸入。 預設值是 `true`。

此方法會傳回具有下列屬性的物件：

* **Name**：協調器函式的名稱。
* **InstanceId**：協調流程的執行個體識別碼 (應該與 `instanceId` 輸入相同)。
* **CreatedTime**：協調器函式開始執行的時間。
* **LastUpdatedTime**：協調流程前次執行檢查點檢查的時間。
* **Input**：函式的 JSON 值輸入。 如果為 false，則不會填入此欄位 `showInput` 。
* **CustomStatus**：JSON 格式的自訂協調流程狀態。
* **Output**：函式的 JSON 值輸出 (如果函式已完成)。 如果協調器函式失敗，此屬性會包含失敗詳細資料。 如果協調器函式已終止，此屬性會包含終止的原因（如果有的話）。
* **RuntimeStatus**：下列其中一個值：
  * **擱置**：已排程的執行個體尚未開始執行。
  * **Running**：執行個體已開始執行。
  * **Completed**：執行個體已正常完成。
  * **ContinuedAsNew**：執行個體本身以新的記錄重新啟動。 此狀態為暫時性狀態。
  * **Failed**：函式失敗，發生錯誤。
  * **Terminated**：執行個體已突然停止。
* **歷程記錄**：協調流程的執行記錄。 只有在 `showHistory` 設為 `true` 時，才會填入此欄位。

`null`如果實例不存在，這個方法會傳回（.net）、 `undefined` （JavaScript）或 `None` （Python）。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `OrchestrationClient` 屬性，而不是 `DurableClient` 屬性，而且您必須使用 `DurableOrchestrationClient` 參數類型，而不是 `IDurableOrchestrationClient` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

如需設定的 function.js，請參閱[啟動實例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    status = await client.get_status(instance_id)
    # do something based on the current status
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可以使用[Azure Functions Core Tools](../functions-run-local.md)命令，直接取得協調流程實例的狀態 `durable get-runtime-status` 。 它需要以下參數：

* ** `id` （必要）**：協調流程實例的識別碼。
* ** `show-input` （選擇性）**：如果設為 `true` ，回應會包含函式的輸入。 預設值是 `false`。
* ** `show-output` （選擇性）**：如果設為 `true` ，回應會包含函式的輸出。 預設值是 `false`。
* ** `connection-string-setting` （選用）**：應用程式設定的名稱，其中包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* ** `task-hub-name` （選用）**：要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在[host.js](durable-functions-bindings.md#host-json)中設定它。

下列命令會抓取協調流程實例識別碼為0ab8c55a66644d68a3a8b220b12d209c 之實例的狀態（包括輸入和輸出）。 它假設您是從函式 `func` 應用程式的根目錄執行命令：

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

您可以使用 `durable get-history` 命令來取出協調流程實例的歷程記錄。 它需要以下參數：

* ** `id` （必要）**：協調流程實例的識別碼。
* ** `connection-string-setting` （選用）**：應用程式設定的名稱，其中包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* ** `task-hub-name` （選用）**：要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在 host.js中設定它。

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>查詢所有實例

不是一次查詢協調流程中的一個實例，您可能會發現一次查詢所有專案會更有效率。

您可以使用 `GetStatusAsync` （.net）、 `getStatusAll` （JavaScript）或 `get_status_all` （Python）方法來查詢所有協調流程實例的狀態。 在 .NET 中，您可以 `CancellationToken` 在想要取消物件時傳遞物件。 此方法會和使用參數的 `GetStatusAsync`方法一樣傳回具有相同屬性的物件。

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
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `OrchestrationClient` 屬性，而不是 `DurableClient` 屬性，而且您必須使用 `DurableOrchestrationClient` 參數類型，而不是 `IDurableOrchestrationClient` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instances = await client.get_status_all()

    for instance in instances:
        logging.log(json.dumps(instance))
```

如需設定的 function.js，請參閱[啟動實例](#javascript-function-json)。

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可以使用[Azure Functions Core Tools](../functions-run-local.md)命令直接查詢實例 `durable get-instances` 。 它需要以下參數：

* ** `top` （選擇性）**：此命令支援分頁。 此參數會對應至每個要求擷取的執行個體數目。 預設值為 10。
* ** `continuation-token` （選擇性）**：用來指出要取得之實例頁面或區段的 token。 每個 `get-instances` 執行都會將權杖傳回給下一組執行個體。
* ** `connection-string-setting` （選用）**：應用程式設定的名稱，其中包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* ** `task-hub-name` （選用）**：要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在[host.js](durable-functions-bindings.md#host-json)中設定它。

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>具有篩選的查詢實例

如果您不需要標準實例查詢可以提供的所有資訊，該怎麼辦？ 例如，如果您只是要尋找協調流程建立時間或協調流程執行時間狀態呢？ 您可以藉由套用篩選來縮小查詢範圍。

使用 `GetStatusAsync` （.net）或 `getStatusBy` （JavaScript）方法來取得符合一組預先定義之篩選準則的協調流程實例清單。

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
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `OrchestrationClient` 屬性，而不是 `DurableClient` 屬性，而且您必須使用 `DurableOrchestrationClient` 參數類型，而不是 `IDurableOrchestrationClient` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

如需設定的 function.js，請參閱[啟動實例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import logging
from datetime import datetime
import json
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.OrchestrationRuntimeStatus import OrchestrationRuntimeStatus

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    runtime_status = [OrchestrationRuntimeStatus.Completed, OrchestrationRuntimeStatus.Running]

    instances = await client.get_status_by(
        datetime(2018, 3, 10, 10, 1, 0),
        datetime(2018, 3, 10, 10, 23, 59),
        runtime_status
    )

    for instance in instances:
        logging.log(json.dumps(instance))
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

在 Azure Functions Core Tools 中，您也可以搭配使用 `durable get-instances` 命令與篩選準則。 除了上述的、、 `top` 和 `continuation-token` `connection-string-setting` 參數之外 `task-hub-name` ，您還可以使用三個篩選參數（ `created-after` 、 `created-before` 和 `runtime-status` ）。

* ** `created-after` （選擇性）**：取得在此日期/時間（UTC）之後建立的實例。 可接受 ISO 8601 格式的日期時間。
* ** `created-before` （選擇性）**：取出在此日期/時間（UTC）之前建立的實例。 可接受 ISO 8601 格式的日期時間。
* ** `runtime-status` （選擇性）**：取得具有特定狀態（例如，執行中或已完成）的實例。 可以提供多個狀態 (以空格分隔)。
* ** `top` （選擇性）**：每個要求所抓取的實例數目。 預設值為 10。
* ** `continuation-token` （選擇性）**：用來指出要取得之實例頁面或區段的 token。 每個 `get-instances` 執行都會將權杖傳回給下一組執行個體。
* ** `connection-string-setting` （選用）**：應用程式設定的名稱，其中包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* ** `task-hub-name` （選用）**：要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在[host.js](durable-functions-bindings.md#host-json)中設定它。

如果您未提供任何篩選（ `created-after` 、 `created-before` 或 `runtime-status` ），此命令只會抓取 `top` 實例，而不考慮執行時間狀態或建立時間。

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>終止實例

如果您的協調流程實例執行時間太長，或只是因為任何原因而需要停止它，您可以選擇將它終止。

您可以使用 `TerminateAsync` 協調流程用戶端系結的（.net）、 `terminate` （JavaScript）或 `terminate` （Python [orchestration client binding](durable-functions-bindings.md#orchestration-client) ）方法來終止實例。 這兩個參數是 `instanceId` 和 `reason` 字串，會寫入記錄和實例狀態。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `OrchestrationClient` 屬性，而不是 `DurableClient` 屬性，而且您必須使用 `DurableOrchestrationClient` 參數類型，而不是 `IDurableOrchestrationClient` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

如需設定的 function.js，請參閱[啟動實例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "It was time to be done."
    return client.terminate(instance_id, reason)
```

---

終止的實例最終會轉換成 `Terminated` 狀態。 不過，這種轉換不會立即發生。 相反地，終止作業會在工作中樞內排入佇列，以及該實例的其他作業。 您可以使用[實例查詢](#query-instances)api 來得知終止的實例何時實際達到 `Terminated` 狀態。

> [!NOTE]
> 實例終止目前不會傳播。 活動函式和子協調流程會執行到完成，不論您是否已結束通話它們的協調流程實例。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可以使用 Azure Functions Core Tools 命令，直接終止協調流程實例[Azure Functions Core Tools](../functions-run-local.md) `durable terminate` 。 它需要以下參數：

* ** `id` （必要）**：要終止之協調流程實例的識別碼。
* ** `reason` （選擇性）**：終止的原因。
* ** `connection-string-setting` （選用）**：應用程式設定的名稱，其中包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* ** `task-hub-name` （選用）**：要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在[host.js](durable-functions-bindings.md#host-json)中設定它。

下列命令會終止識別碼為0ab8c55a66644d68a3a8b220b12d209c 的協調流程實例：

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>將事件傳送至實例

在某些情況下，您的協調器函式必須能夠等候並接聽外來事件，這是很重要的。 這包括等候[人為互動](durable-functions-overview.md#human)的[監視](durable-functions-overview.md#monitoring)函式和函式。

使用 `RaiseEventAsync` `raiseEvent` [協調流程用戶端](durable-functions-bindings.md#orchestration-client)系結的（.net）方法或（JavaScript）方法，將事件通知傳送至執行中的實例。 可以處理這些事件的實例，就是等待來電 `WaitForExternalEvent` （.net）或產生 `waitForExternalEvent` （JavaScript）呼叫的實例。

`RaiseEventAsync`（.Net）和（JavaScript）的參數如下所示 `raiseEvent` ：

* **InstanceId**：執行個體的唯一識別碼。
* **EventName**：要傳送的事件名稱。
* **EventData**：要傳送至執行個體的 JSON 可序列化裝載。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `OrchestrationClient` 屬性，而不是 `DurableClient` 屬性，而且您必須使用 `DurableOrchestrationClient` 參數類型，而不是 `IDurableOrchestrationClient` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

如需設定的 function.js，請參閱[啟動實例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    event_data = [1, 2 ,3]
    return client.raise_event(instance_id, 'MyEvent', event_data)
```

---

> [!NOTE]
> 如果沒有具有指定「執行個體識別碼」的協調流程執行個體，則會捨棄事件訊息。 如果實例存在，但尚未等候事件，則事件會儲存在實例狀態中，直到準備好接收和處理為止。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可以使用 Azure Functions Core Tools 命令，直接將事件引發至協調流程實例[Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` 。 它需要以下參數：

* ** `id` （必要）**：協調流程實例的識別碼。
* **`event-name`**：要引發之事件的名稱。
* ** `event-data` （選用）**：要傳送至協調流程實例的資料。 這可以是 JSON 檔案的路徑，或者您可以直接在命令列上提供資料。
* ** `connection-string-setting` （選用）**：應用程式設定的名稱，其中包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* ** `task-hub-name` （選用）**：要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在[host.js](durable-functions-bindings.md#host-json)中設定它。

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>等候協調流程完成

在長時間執行的協調流程中，您可能會想要等候並取得協調流程的結果。 在這些情況下，可以在協調流程上定義超時時間也很有用。 如果超過超時時間，則應該傳回協調流程的狀態，而不是結果。

`WaitForCompletionOrCreateCheckStatusResponseAsync`（.Net）或 `waitForCompletionOrCreateCheckStatusResponse` （JavaScript）方法可以用來以同步方式取得協調流程實例的實際輸出。 根據預設，這些方法會針對使用預設值10秒 `timeout` ，並為1秒 `retryInterval` 。  

以下是範例 HTTP 觸發函式，它會示範如何使用這個 API：

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

如需設定的 function.js，請參閱[啟動實例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

timeout = "timeout"
retry_interval = "retryInterval"

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instance_id = await client.start_new(req.route_params['functionName'], None, req.get_body())
    logging.log(f"Started orchestration with ID = '${instance_id}'.")

    timeout_in_milliseconds = get_time_in_seconds(req, timeout)
    timeout_in_milliseconds = timeout_in_milliseconds if timeout_in_milliseconds != None else 30000
    retry_interval_in_milliseconds = get_time_in_seconds(req, retry_interval)
    retry_interval_in_milliseconds = retry_interval_in_milliseconds if retry_interval_in_milliseconds != None else 1000

    return client.wait_for_completion_or_create_check_status_response(
        req,
        instance_id,
        timeout_in_milliseconds,
        retry_interval_in_milliseconds
    )

def get_time_in_seconds(req: func.HttpRequest, query_parameter_name: str):
    query_value = req.params.get(query_parameter_name)
    return query_value if query_value != None else 1000
```

---

使用下列程式程式碼呼叫函數。 在 [重試間隔] 中，針對 [超時] 和 [0.5 秒] 使用2秒：

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

取決於從協調流程執行個體取得回應所需的時間，會有兩種情況：

* 協調流程實例會在定義的時間內完成（在此案例中為2秒），而回應會是以同步方式傳遞的實際協調流程實例輸出：

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

* 協調流程實例無法在定義的時間內完成，而且回應是[HTTP API URL 探索](durable-functions-http-api.md)中所述的預設值：

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
> Webhook Url 的格式可能會不同，視您執行的 Azure Functions 主機版本而定。 上述範例適用於 Azure Functions 2.0 主機。

## <a name="retrieve-http-management-webhook-urls"></a>取出 HTTP 管理 webhook Url

您可以使用外部系統來監視或引發事件至協調流程。 外部系統可以透過 webhook Url 與 Durable Functions 通訊，這是[HTTP API URL 探索](durable-functions-http-features.md#http-api-url-discovery)中所述之預設回應的一部分。 您也可以使用[協調流程用戶端](durable-functions-bindings.md#orchestration-client)系結，以程式設計方式存取 webhook url。 `CreateHttpManagementPayload`（.Net）或 `createHttpManagementPayload` （JavaScript）方法可以用來取得包含這些 webhook url 的可序列化物件。

`CreateHttpManagementPayload`（.Net）和 `createHttpManagementPayload` （JavaScript）方法有一個參數：

* **instanceId**：實例的唯一識別碼。

方法會傳回具有下列字串屬性的物件：

* **Id**：協調流程的執行個體識別碼 (應該與 `InstanceId` 輸入相同)。
* **StatusQueryGetUri**：協調流程執行個體的狀態 URL。
* **SendEventPostUri**：協調流程執行個體的「引發事件」URL。
* **TerminatePostUri**：協調流程執行個體的「終止」URL。
* **PurgeHistoryDeleteUri**：協調流程實例的「清除歷程記錄」 URL。

函數可以將這些物件的實例傳送至外部系統，以監視或引發對應協調流程上的事件，如下列範例所示：

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
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `DurableActivityContext` 而不是，您必須使用屬性，而不是 `IDurableActivityContext` `OrchestrationClient` `DurableClient` 屬性，而且您必須使用 `DurableOrchestrationClient` 參數類型，而不是 `IDurableOrchestrationClient` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

如需設定的 function.js，請參閱[啟動實例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.cosmosdb.cdb.Document:
    client = df.DurableOrchestrationClient(starter)

    payload = client.create_check_status_response(req, instance_id).get_body().decode()

    return func.cosmosdb.CosmosDBConverter.encode({
        id: instance_id,
        payload: payload
    })
```
---

## <a name="rewind-instances-preview"></a>倒轉實例（預覽）

如果您因為非預期的原因而發生協調流程失敗，*您可以使用*針對該目的所建立的 API，將實例倒轉成先前狀況良好的狀態。

> [!NOTE]
> 此 API 並非要取代適當的錯誤處理和重試原則。 相反地，它只是要用於協調流程執行個體因非預期原因而失敗的情況。 如需有關錯誤處理和重試原則的詳細資訊，請參閱[錯誤處理](durable-functions-error-handling.md)一文。

使用 `RewindAsync` 協調流程用戶端系結的（.net）或 `rewind` （JavaScript）方法，讓協調流程回到執行*Running*中狀態。 [orchestration client binding](durable-functions-bindings.md#orchestration-client) 這個方法也會重新執行導致協調流程失敗的活動或子協調流程執行失敗。

例如，假設您有一個工作流程牽涉到一系列[人工核准](durable-functions-overview.md#human)。 假設有一系列的活動功能會通知某人需要核准，並等候即時回應。 當所有核准活動都收到回應或超時之後，假設另一個活動因為應用程式設定錯誤而失敗，例如不正確資料庫連接字串。 結果就是深入工作流程的協調流程失敗。 使用 `RewindAsync` （.net）或 `rewind` （JAVASCRIPT） API，應用程式系統管理員可以修正設定錯誤，並將失敗的協調流程回溯回到失敗前的狀態。 不需要重新核准任何人為互動步驟，協調流程現在可以順利完成。

> [!NOTE]
> 倒轉*功能不*支援使用持久計時器的倒帶協調流程實例。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `OrchestrationClient` 屬性，而不是 `DurableClient` 屬性，而且您必須使用 `DurableOrchestrationClient` 參數類型，而不是 `IDurableOrchestrationClient` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

如需設定的 function.js，請參閱[啟動實例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python 目前不支援這項功能。

<!-- ```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Orchestrator failed and needs to be revived."
    return client.rewind(instance_id, reason)
``` -->

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可以使用[Azure Functions Core Tools](../functions-run-local.md)命令，直接倒轉協調流程實例 `durable rewind` 。 它需要以下參數：

* ** `id` （必要）**：協調流程實例的識別碼。
* ** `reason` （選擇性）**：倒帶協調流程實例的原因。
* ** `connection-string-setting` （選用）**：應用程式設定的名稱，其中包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* ** `task-hub-name` （選用）**：要使用 Durable Functions 工作中樞的名稱。 根據預設，會使用檔案[上host.js](durable-functions-bindings.md#host-json)中的工作中樞名稱。

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>清除實例歷程記錄

若要移除與協調流程相關聯的所有資料，您可以清除實例歷程記錄。 例如，您可能會想要刪除與已完成實例相關聯的任何 Azure 資料表資料列和大型訊息 blob。 若要這麼做，請使用 `PurgeInstanceHistoryAsync` `purgeInstanceHistory` [協調流程用戶端](durable-functions-bindings.md#orchestration-client)系結的（.Net）或（JavaScript）方法。

這個方法有兩個多載。 第一個多載會依協調流程實例的識別碼清除記錄：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

如需設定的 function.js，請參閱[啟動實例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    return client.purge_instance_history(instance_id)
```

---

下一個範例顯示計時器觸發的函式，它會清除在指定時間間隔之後完成的所有協調流程實例的歷程記錄。 在這種情況下，它會移除所有實例在30天之前完成的資料。 其排程為每天上午12點執行一次：

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
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `OrchestrationClient` 屬性，而不是 `DurableClient` 屬性，而且您必須使用 `DurableOrchestrationClient` 參數類型，而不是 `IDurableOrchestrationClient` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`purgeInstanceHistoryBy`方法可以用來有條件地清除多個實例的實例歷程記錄。

**function.json**

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
> 這個範例是以 Durable Functions 2.x 版為目標。 在1.x 版中，請使用， `orchestrationClient` 而不是 `durableClient` 。

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
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.DurableOrchestrationStatus import OrchestrationRuntimeStatus
from datetime import datetime, timedelta

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    created_time_from = datetime.datetime()
    created_time_to = datetime.datetime.today + timedelta(days = -30)
    runtime_statuses = [OrchestrationRuntimeStatus.Completed]

    return client.purge_instance_history_by(created_time_from, created_time_to, runtime_statuses)
```
---

> [!NOTE]
> 若要讓清除歷程記錄作業成功，目標實例的執行時間狀態必須是 [**已完成**]、[已**終止**] 或 [**失敗**]。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您可以使用[Azure Functions Core Tools](../functions-run-local.md)命令來清除協調流程實例的歷程記錄 `durable purge-history` 。 與上一節中的第二個 c # 範例類似，它會清除在指定的時間間隔期間所建立之所有協調流程實例的歷程記錄。 您可以依執行時間狀態進一步篩選已清除的實例。 此命令有數個參數：

* ** `created-after` （選擇性）**：清除這個日期/時間（UTC）之後所建立之實例的歷程記錄。 可接受 ISO 8601 格式的日期時間。
* ** `created-before` （選擇性）**：清除在此日期/時間（UTC）之前建立之實例的歷程記錄。 可接受 ISO 8601 格式的日期時間。
* ** `runtime-status` （選擇性）**：清除具有特定狀態之實例的歷程記錄（例如，執行中或已完成）。 可以提供多個狀態 (以空格分隔)。
* ** `connection-string-setting` （選用）**：應用程式設定的名稱，其中包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* ** `task-hub-name` （選用）**：要使用 Durable Functions 工作中樞的名稱。 根據預設，會使用檔案[上host.js](durable-functions-bindings.md#host-json)中的工作中樞名稱。

下列命令會刪除2018年11月14日下午 7:35 PM （UTC）之前建立之所有失敗實例的歷程記錄。

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>刪除工作中樞

您可以使用[Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` 命令來刪除與特定工作中樞相關聯的所有儲存體成品，包括 Azure 儲存體資料表、佇列和 blob。 此命令有兩個參數：

* ** `connection-string-setting` （選用）**：應用程式設定的名稱，其中包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* ** `task-hub-name` （選用）**：要使用 Durable Functions 工作中樞的名稱。 根據預設，會使用檔案[上host.js](durable-functions-bindings.md#host-json)中的工作中樞名稱。

下列命令會刪除與工作中樞相關聯的所有 Azure 儲存體資料 `UserTest` 。

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何處理版本控制](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [實例管理的內建 HTTP API 參考](durable-functions-http-api.md)
