---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 2ccff72be66a88b9bf0a5e9eb9c29ade8397804b
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356188"
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
- 在適當的情況下， ([執行重試原則](#retry-policies-preview)) 

### <a name="use-structured-error-handling"></a>使用結構化錯誤處理

捕捉和記錄錯誤對於監視您應用程式的健康情況是很重要的。 任何函式程式碼的最上層最上層都應該包含 try/catch 區塊。 在 catch 區塊中，您可以捕捉並記錄錯誤。

## <a name="retry-policies-preview"></a>重試原則 (預覽) 

您可以在函數應用程式中，針對任何觸發程式類型的任何函式定義重試原則。  重試原則會重新執行函式，直到成功執行為止，或直到重試次數的上限為止。  您可以為應用程式中的所有函式或個別功能定義重試原則。  根據預設，函式應用程式不會將訊息重試 (除了 [觸發程式來源) 上具有重試原則的特定觸發](#using-retry-support-on-top-of-trigger-resilience) 程式。  每當執行導致未攔截的例外狀況時，就會評估重試原則。  最佳做法是，您應該攔截程式碼中的所有例外狀況，並重新擲回任何應該會導致重試的錯誤。  在執行的重試原則完成之前，不會寫入事件中樞和 Azure Cosmos DB 檢查點，這表示在該分割區上進行的進度會暫停，直到目前的批次完成為止。

### <a name="retry-policy-options"></a>重試原則選項

您可以使用下列選項來定義重試原則。

**最大重試計數** 是在最終失敗前重試執行的次數上限。 值 `-1` ，表示要無限期地重試。 目前的重試計數會儲存在實例的記憶體中。 實例可能會在重試嘗試之間發生失敗。  當實例在重試原則期間失敗時，重試計數會遺失。  當實例失敗時，事件中樞、Azure Cosmos DB 和佇列儲存體等觸發程式就能夠繼續處理，並在新的實例上重試批次，重試計數會重設為零。  其他觸發程式（例如 HTTP 和計時器）不會在新的實例上繼續。  這表示最大重試計數是最佳的工作，而且在某些罕見的情況下，執行可能會重試超過最大值，或對 HTTP 和計時器等觸發程式的重試次數小於最大值。

**重試策略** 會控制重試的行為。  以下是兩個支援的重試選項：

| 選項 | 說明|
|---|---|
|**`fixedDelay`**| 每次重試之間可以經過一段指定的時間，|
| **`exponentialBackoff`**| 第一次重試會等待最小延遲。 在後續重試時，時間會以指數方式新增至每次重試的初始持續時間，直到達到延遲上限為止。  指數反向會新增一些小型的隨機載入，以在高輸送量案例中錯開重試。|

#### <a name="app-level-configuration"></a>應用層級設定

您可以使用檔案，為應用程式中的所有函 [ `host.json` ](../articles/azure-functions/functions-host-json.md#retry)式定義重試原則。 

#### <a name="function-level-configuration"></a>函數層級設定

可以針對特定函式定義重試原則。  函數專屬的設定優先于應用層級設定。

#### <a name="fixed-delay-retry"></a>修正延遲重試

# <a name="c"></a>[C#](#tab/csharp)

重試需要 NuGet 套件 [Microsoft。](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

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

重試需要 NuGet 套件 [Microsoft。](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

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

|function.json 屬性  |Attribute 屬性 | 說明 |
|---------|---------|---------| 
|策略|n/a|必要。 要使用的重試策略。 有效值為 `fixedDelay` 或 `exponentialBackoff`。|
|maxRetryCount|n/a|必要。 每個函數執行所允許的重試次數上限。 `-1` 表示要無限期地重試。|
|delayInterval|n/a|使用策略時，將在重試之間使用的延遲 `fixedDelay` 。|
|minimumInterval|n/a|使用策略時的最小重試延遲 `exponentialBackoff` 。|
|maximumInterval|n/a|使用策略時的最大重試延遲 `exponentialBackoff` 。| 

### <a name="retry-limitations-during-preview"></a>預覽期間的重試限制

- 針對 .NET 專案，您可能需要以手動方式提取 [3.0.23 的版本，>=](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) 。
- 在取用方案中，應用程式可能會在重試佇列中的最後一則訊息時相應減少為零。
- 在取用方案中，應用程式可能會在執行重試時縮小規模。  為了獲得最佳結果，請選擇重試間隔 <= 00:01:00，<= 5 次重試。

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>在觸發程式恢復功能之上使用重試支援

函數應用程式的重試原則與觸發程式提供的任何重試或復原無關。  函數重試原則只會在觸發程式復原重試的最上方分層。  例如，如果使用 Azure 服務匯流排，佇列預設會有10個訊息傳遞計數。  預設的傳遞計數表示在10次嘗試傳遞佇列訊息之後，服務匯流排將會寄不出信件訊息。  您可以為具有服務匯流排觸發程式的函式定義重試原則，但重試將會在服務匯流排傳遞嘗試之上分層。  

比方說，如果您使用預設的服務匯流排傳遞計數10，且定義了函式重試原則5。  訊息會先清除佇列，並將服務匯流排傳遞帳戶遞增至1。  如果每次執行失敗，則在五次嘗試觸發相同訊息之後，該訊息就會被標示為已放棄。  服務匯流排會立即 requeue 訊息，它會觸發函式，並將傳遞計數遞增為2。  最後，在50最終嘗試 (10 個服務匯流排傳遞數 * 每個傳遞) 有五個函式重試，訊息會被放棄並在服務匯流排上觸發寄不出的信件。

> [!WARNING]
> 不建議將服務匯流排佇列之類的觸發程式的傳遞計數設定為1，這表示在單一函式重試迴圈之後，訊息會立即失效。  這是因為觸發程式會以重試方式提供復原功能，而函式重試原則則是最佳的工作，而且可能會導致小於所需的重試總次數。

### <a name="triggers-with-additional-resiliency-or-retries"></a>具有額外復原或重試的觸發程式

下列觸發程式支援在觸發程式來源上重試：

* [Azure Blob 儲存體](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure 佇列儲存體](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure 服務匯流排 (佇列/主題)](../articles/azure-functions/functions-bindings-service-bus.md)

根據預設，大部分的觸發程式會重試最多五次的要求。 第五次重試之後，這兩個 Azure 佇列儲存體都會將訊息寫入 [有害佇列](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages)。  預設的服務匯流排佇列和主題原則會在嘗試10次後，將訊息寫入寄不出的 [信件佇列](../articles/service-bus-messaging/service-bus-dead-letter-queues.md) 。
