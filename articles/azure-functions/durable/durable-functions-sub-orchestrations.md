---
title: Durable Functions 的子協調流程 - Azure
description: 如何在 Azure Functions 的 Durable Functions 擴充中，從協調流程呼叫協調流程。
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: d4d599063f727510cbf504ea3d121bdabfe001c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261512"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的子協調流程

除了呼叫活動函式，協調器函式還可以呼叫其他協調器函式。 例如，可以從較小的協調器函式程式庫構建更大的業務流程。 或者，也可以平行執行協調器函式的多個執行個體。

協調器函數可以使用 .NET 或 JavaScript`CallSubOrchestratorAsync`中的`CallSubOrchestratorWithRetryAsync`或`callSubOrchestrator`方法`callSubOrchestratorWithRetry`調用另一個協調器函數。 [錯誤處理和補償](durable-functions-error-handling.md#automatic-retry-on-failure)一文提供自動重試的詳細資訊。

從呼叫端的觀點來看，子協調器函式的行為就像活動函式一樣。 子協調器函式可以傳回值、擲回例外狀況，還可以由父代協調器函式來等候。 
## <a name="example"></a>範例

下列範例說明 IoT (物聯網) 情節，其中有多個需要佈建的裝置。 以下函數表示需要為每個設備執行的預配工作流：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

---

這個協調器函式可直接用於一次性裝置佈建，也可以當作更大協調流程的一部分。 在後一種情況下，父協調器函數可以計畫`DeviceProvisioningOrchestration`使用`CallSubOrchestratorAsync`（.NET） 或`callSubOrchestrator`（JavaScript） API 的實例。

以下示範如何平行執行多個協調器函式。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> 前面的 C# 示例適用于持久函數 2.x。 對於持久函數 1.x，必須使用`DurableOrchestrationContext`而不是`IDurableOrchestrationContext`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

---

> [!NOTE]
> 子業務流程必須在與父業務流程相同的函數應用中定義。 如果需要在另一個函數應用中調用並等待業務流程，請考慮使用對 HTTP API 的內置支援和 HTTP 202 輪詢消費者模式。 有關詳細資訊，請參閱 HTTP[功能](durable-functions-http-features.md)主題。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解如何設置自訂業務流程狀態](durable-functions-custom-orchestration-status.md)