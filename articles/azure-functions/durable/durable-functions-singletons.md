---
title: Durable Functions 的單次個體 - Azure
description: 如何在 Azure Functions 的 Durable Functions 擴充中使用單一資料庫。
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4eff7c4c91ed664fcf1f4fc7a8be2d43d24e5c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262804"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的單次協調器

對於後臺作業，通常需要確保一次只運行一個特定協調器的實例。 通過在創建業務流程時將特定實例 ID 分配給協調器，可以在[持久函數](durable-functions-overview.md)中確保此類單例行為。

## <a name="singleton-example"></a>單次個體範例

下面的示例顯示了創建單例後臺作業業務流程的 HTTP 觸發器函數。 程式碼可確保一個指定的執行個體識別碼只存在一個執行個體。

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

> [!NOTE]
> 前面的 C# 代碼用於持久函數 2.x。 對於持久函數 1.x，必須使用`OrchestrationClient`屬性而不是`DurableClient`屬性，並且必須使用`DurableOrchestrationClient`參數類型而不是`IDurableOrchestrationClient`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

**函數.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

---

根據預設，執行個體識別碼是隨機產生的 GUID。 但是，在前面的示例中，實例 ID 在 URL 路由資料中傳遞。 代碼調用`GetStatusAsync`（C#）`getStatus`或 （JavaScript） 以檢查具有指定 ID 的實例是否已運行。 如果沒有運行此類實例，則使用該 ID 創建新實例。

> [!NOTE]
> 在此範例中有潛在的競爭條件。 如果 **HttpStartSingle** 的兩個實例同時執行，則兩個函式呼叫都將會報告成功，但現在只會實際啟動一個協調流程執行個體。 視您的需求而定，這可能會有非預期的副作用。 基於這個理由，請務必確定不會有兩個要求同時執行此觸發程序函式。

協調器函數的實現詳細資訊實際上並不重要。 它可能是會啟動並完成的一般協調器函式，也可能是永遠執行的函式 (也就是[永久性協調流程](durable-functions-eternal-orchestrations.md))。 重點是一次只有一個執行個體在執行。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解業務流程的原生 HTTP 功能](durable-functions-http-features.md)
