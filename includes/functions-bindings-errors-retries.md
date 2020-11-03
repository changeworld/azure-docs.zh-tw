---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 285c3bf37e9d6de042cb028745fc8b094d34c3a1
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284408"
---
在 Azure Functions 中引發的錯誤可能來自下列任何來源：

- 使用內建的 Azure Functions[觸發程式和](..\articles\azure-functions\functions-triggers-bindings.md)系結
- 基礎 Azure 服務的 Api 呼叫
- REST 端點的呼叫
- 用戶端程式庫、套件或協力廠商 Api 的呼叫

若要避免資料遺失或遺漏訊息，請務必遵循良好的錯誤處理做法。 建議的錯誤處理作法包括下列動作：

- [啟用 Application Insights](../articles/azure-functions/functions-monitoring.md)
- [使用結構化錯誤處理](#use-structured-error-handling)
- [等冪性的設計](../articles/azure-functions/functions-idempotent.md)
- 在適當的情況下， ([執行重試原則](#retry-policies)) 

### <a name="use-structured-error-handling"></a>使用結構化錯誤處理

捕捉和記錄錯誤對於監視您應用程式的健康情況是很重要的。 任何函式程式碼的最上層最上層都應該包含 try/catch 區塊。 在 catch 區塊中，您可以捕捉並記錄錯誤。

## <a name="retry-policies"></a>重試原則

您可以在函數應用程式中，針對任何觸發程式類型的任何函式定義重試原則。  重試原則會重新執行函式，直到成功執行為止，或直到重試次數的上限為止。  您可以為應用程式中的所有函式或個別功能定義重試原則。  根據預設，函式應用程式不會將訊息重試 (除了 [觸發程式來源) 上具有重試原則的特定觸發](#trigger-specific-retry-support) 程式。  每當執行導致未攔截的例外狀況時，就會評估重試原則。  最佳做法是，您應該攔截程式碼中的所有例外狀況，並重新擲回任何應該會導致重試的錯誤。  在執行的重試原則完成之前，不會寫入事件中樞和 Azure Cosmos DB 檢查點，這表示在該分割區上進行的進度會暫停，直到目前的批次完成為止。

### <a name="retry-policy-options"></a>重試原則選項

您可以使用下列選項來定義重試原則。

**最大重試計數** 是在最終失敗前重試執行的次數上限。 值 `-1` ，表示要無限期地重試。 目前的重試計數會儲存在實例的記憶體中。 實例可能會在重試嘗試之間發生失敗。  當實例在重試原則期間失敗時，重試計數會遺失。  當實例失敗時，事件中樞、Azure Cosmos DB 和佇列儲存體等觸發程式就能夠繼續處理，並在新的實例上重試批次，重試計數會重設為零。  其他觸發程式（例如 HTTP 和計時器）不會在新的實例上繼續。  這表示最大重試計數是最佳的工作，而且在某些罕見的情況下，執行可能會重試超過最大值，或對 HTTP 和計時器等觸發程式的重試次數小於最大值。

**重試策略** 會控制重試的行為。  以下是兩個支援的重試選項：

| 選項 | 描述|
|---|---|
|**`fixedDelay`**| 每次重試之間可以經過一段指定的時間，|
| **`exponentialBackoff`**| 第一次重試會等待最小延遲。 在後續重試時，時間會以指數方式新增至每次重試的初始持續時間，直到達到延遲上限為止。  指數反向會新增一些小型的隨機載入，以在高輸送量案例中錯開重試。|

#### <a name="app-level-configuration"></a>應用層級設定

您可以使用檔案，為應用程式中的所有函 [ `host.json` ](../articles/azure-functions/functions-host-json.md#retry)式定義重試原則。 

#### <a name="function-level-configuration"></a>函數層級設定

可以針對特定函式定義重試原則。  函數專屬的設定優先于應用層級設定。

#### <a name="fixed-delay-retry"></a>修正延遲重試

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

以下是檔案 *function.js* 的重試原則：

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下是檔案 *function.js* 的重試原則：


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

以下是檔案 *function.js* 的重試原則：

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

以下是檔案 *function.js* 的重試原則：


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>指數輪詢重試

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

以下是檔案 *function.js* 的重試原則：

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下是檔案 *function.js* 的重試原則：

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

以下是檔案 *function.js* 的重試原則：

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

以下是檔案 *function.js* 的重試原則：

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|function.json 屬性  |Attribute 屬性 | 描述 |
|---------|---------|---------| 
|策略|n/a|必要。 要使用的重試策略。 有效值為 `fixedDelay` 或 `exponentialBackoff`。|
|maxRetryCount|n/a|必要。 每個函數執行所允許的重試次數上限。 `-1` 表示要無限期地重試。|
|delayInterval|n/a|使用策略時，將在重試之間使用的延遲 `fixedDelay` 。|
|minimumInterval|n/a|使用策略時的最小重試延遲 `exponentialBackoff` 。|
|maximumInterval|n/a|使用策略時的最大重試延遲 `exponentialBackoff` 。| 

## <a name="trigger-specific-retry-support"></a>觸發程式特定的重試支援

部分觸發程式會在觸發程式來源上提供重試。  除了函式應用程式主機重試原則的取代之外，還可以使用這些觸發程式重試。  如果需要固定的重試次數，您應該使用一般主機重試原則的觸發程式特定重試原則。  下列觸發程式支援在觸發程式來源上重試：

* [Azure Blob 儲存體](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure 佇列儲存體](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure 服務匯流排 (佇列/主題)](../articles/azure-functions/functions-bindings-service-bus.md)

根據預設，這些會觸發最多五次的重試要求。 第五次重試之後，Azure 佇列儲存體和 Azure 服務匯流排觸發程式都會將訊息寫入 [有害佇列](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages)。
