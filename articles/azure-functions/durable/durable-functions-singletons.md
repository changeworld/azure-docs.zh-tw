---
title: Durable Functions 的單次個體 - Azure
description: 如何在 Azure Functions 的 Durable Functions 擴充中使用單一資料庫。
author: cgillum
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: azfuncdf
ms.openlocfilehash: 954b322536c5430608597c2e67c474fefeb5fb25
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004930"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的單次協調器

針對背景工作，您通常需要確保一次只能執行一個特定 orchestrator 的實例。 您可以在建立時將特定實例識別碼指派給協調器，以確保 [Durable Functions](durable-functions-overview.md) 中的這種單一行為。

## <a name="singleton-example"></a>單次個體範例

下列範例顯示會建立單一背景工作協調流程的 HTTP 觸發程式函數。 程式碼可確保一個指定的執行個體識別碼只存在一個執行個體。

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
        return new HttpResponseMessage(HttpStatusCode.Conflict)
        {
            Content = new StringContent($"An instance with ID '{instanceId}' already exists."),
        };
    }
}
```

> [!NOTE]
> 先前的 c # 程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用屬性（attribute），而不是 `OrchestrationClient` `DurableClient` 屬性（attribute），而且必須使用 `DurableOrchestrationClient` 參數類型，而不是 `IDurableOrchestrationClient` 。 如需版本之間差異的詳細資訊，請參閱 [Durable Functions 版本](durable-functions-versions.md) 文章。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**function.json**

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

# <a name="python"></a>[Python](#tab/python)

**function.json**

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

**__ __.py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = req.route_params['instanceId']
    function_name = req.route_params['functionName']

    existing_instance = await client.get_status(instance_id)

    if existing_instance != None:
        event_data = req.get_body()
        instance_id = await client.start_new(function_name, instance_id, event_data)
        logging.info(f"Started orchestration with ID = '{instance_id}'.")
        return client.create_check_status_response(req, instance_id)
    else:
        return {
            'status': 409,
            'body': f"An instance with ID '${instance_id}' already exists"
        }

```

---

根據預設，執行個體識別碼是隨機產生的 GUID。 不過，在上述範例中，實例識別碼會傳入 URL 的路由資料中。 程式碼 `GetStatusAsync` 會呼叫 (c # ) 、 `getStatus` (JavaScript) 或 `get_status` (Python) 來檢查具有指定識別碼的實例是否已在執行中。 如果沒有這類實例正在執行，就會使用該識別碼來建立新的實例。

> [!NOTE]
> 在此範例中有潛在的競爭條件。 如果 **HttpStartSingle** 的兩個實例同時執行，則兩個函式呼叫都將會報告成功，但現在只會實際啟動一個協調流程執行個體。 視您的需求而定，這可能會有非預期的副作用。 基於這個理由，請務必確定不會有兩個要求同時執行此觸發程序函式。

協調器函式的實作為詳細資料實際上並不重要。 它可能是會啟動並完成的一般協調器函式，也可能是永遠執行的函式 (也就是[永久性協調流程](durable-functions-eternal-orchestrations.md))。 重點是一次只有一個執行個體在執行。

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [瞭解協調流程的原生 HTTP 功能](durable-functions-http-features.md)
