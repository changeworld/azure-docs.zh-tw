---
title: Durable Functions 中的工作中樞 - Azure
description: 了解在 Azure Functions 的 Durable Functions 擴充中，什麼是工作中樞。 瞭解如何設定工作中樞。
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: afee79aecaad97ec4b441df0758166073b2413cf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083101"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的工作中樞

[Durable Functions](durable-functions-overview.md) 中的「工作中樞」** 是協調流程所使用之 Azure 儲存體資源的邏輯容器。 協調器和活動函式必須屬於相同的工作中樞，才能彼此互動。

如果多個函式應用程式共用儲存體帳戶，則每個函式應用程式「必須」** 設有個別的工作中樞名稱。 儲存體帳戶可以包含多個工作中樞。 下圖說明每個函式應用程式在共用和專用儲存體帳戶中的一個工作中樞。

![顯示共用和專用儲存體帳戶的圖表。](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure 儲存體資源

工作中樞由下列儲存體資源所組成：

* 一或多個控制佇列。
* 一個工作項目佇列。
* 一個記錄資料表。
* 一個執行個體資料表。
* 一個儲存體容器，含有一或多個租用 blob。
* 包含大型訊息裝載的儲存體容器（如果適用的話）。

當 orchestrator、entity 或 activity 函式執行或已排程執行時，會在預設 Azure 儲存體帳戶中自動建立所有這些資源。 [效能和延展性](durable-functions-perf-and-scale.md)一文說明如何使用這些資源。

## <a name="task-hub-names"></a>工作中樞名稱

工作中樞是以符合下列規則的名稱來識別：

* 只包含英數位元
* 以字母開頭
* 的最小長度為3個字元，最大長度為45個字元

工作中樞名稱會在檔案的*host.js*中宣告，如下列範例所示：

### <a name="hostjson-functions-20"></a>host.js開啟（函數2.0）

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

您也可以使用應用程式設定來設定工作中樞，如下列範例檔案所示 `host.json` ：

### <a name="hostjson-functions-10"></a>host.js開啟（函數1.0）

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.js開啟（函數2.0）

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

工作中樞名稱將設定為 `MyTaskHub` 應用程式設定的值。 下列 `local.settings.json` 示範如何將 `MyTaskHub` 設定定義為 `samplehubname`：

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

下列程式碼示範如何撰寫使用[協調流程用戶端](durable-functions-bindings.md#orchestration-client)系結的函式，以使用設定為應用程式設定的工作中樞：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> 先前的 c # 範例適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用， `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`function.json` 檔案中的工作中樞屬性會透過應用程式設定來設定：

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

`function.json` 檔案中的工作中樞屬性會透過應用程式設定來設定：

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

工作中樞名稱必須以字母開頭，且只包含字母和數字。 如果未指定，則會使用預設的工作中樞名稱，如下表所示：

| 持久性延伸模組版本 | 預設工作中樞名稱 |
| - | - |
| 2.x | 部署在 Azure 中時，工作中樞名稱是衍生自函式_應用程式_的名稱。 在 Azure 外部執行時，預設的工作中樞名稱是 `TestHubName` 。 |
| 1.x | 所有環境的預設工作中樞名稱是 `DurableFunctionsHub` 。 |

如需有關延伸模組版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

> [!NOTE]
> 當共用儲存體帳戶中有多個工作中樞時，可透過名稱來區分各個工作中樞。 如果您有多個函式應用程式共用了共用儲存體帳戶，您必須明確地在 host.json** 檔案中為每個工作中樞設定不同的名稱。 否則，多個函式應用程式會針對訊息彼此競爭，這可能會導致未定義的行為，包括在或狀態下意外「停滯」的協調流程 `Pending` `Running` 。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解如何處理協調流程版本設定](durable-functions-versioning.md)
