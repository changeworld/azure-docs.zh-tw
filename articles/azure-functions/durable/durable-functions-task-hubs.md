---
title: Durable Functions 中的工作中樞 - Azure
description: 了解在 Azure Functions 的 Durable Functions 擴充中，什麼是工作中樞。 瞭解如何配置任務集線器。
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: ffb3d590aebe80994de1e7e834a2eba5777df9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262481"
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
* 包含大型消息負載的存儲容器（如果適用）。

當協調器、實體或活動功能運行或計畫運行時，所有這些資源都會在預設 Azure 存儲帳戶中自動創建。 [效能和延展性](durable-functions-perf-and-scale.md)一文說明如何使用這些資源。

## <a name="task-hub-names"></a>工作中樞名稱

工作中樞可透過 host.json** 檔案中所宣告的名稱來加以識別，如下列範例所示：

### <a name="hostjson-functions-20"></a>主機.json （功能 2.0）

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

任務集線器也可以使用應用設置進行配置，如以下`host.json`示例檔所示：

### <a name="hostjson-functions-10"></a>主機.json （函數 1.0）

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>主機.json （功能 2.0）

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

以下代碼演示如何編寫使用[業務流程用戶端綁定](durable-functions-bindings.md#orchestration-client)處理配置為應用設置的任務中心的功能：

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
> 前面的 C# 示例適用于持久函數 2.x。 對於持久函數 1.x，必須使用`DurableOrchestrationContext`而不是`IDurableOrchestrationContext`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

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

工作中樞名稱必須以字母開頭，且只包含字母和數字。 如果未指定，將使用預設任務中心名稱，如下表所示：

| 耐用的擴展版本 | 預設任務中心名稱 |
| - | - |
| 2.x | 在 Azure 中部署時，任務中心名稱派生自_函數應用_的名稱。 在 Azure 之外運行時，預設任務中心名稱為`TestHubName`。 |
| 1.x | 所有環境的預設任務中心名稱為`DurableFunctionsHub`。 |

有關擴展版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

> [!NOTE]
> 當共用儲存體帳戶中有多個工作中樞時，可透過名稱來區分各個工作中樞。 如果您有多個函式應用程式共用了共用儲存體帳戶，您必須明確地在 host.json** 檔案中為每個工作中樞設定不同的名稱。 否則，多個函數應用將相互競爭消息，這可能導致未定義的行為，包括業務流程意外"卡住"在`Pending`或`Running`狀態中。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解如何處理業務流程版本控制](durable-functions-versioning.md)
