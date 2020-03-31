---
title: 適用於 Azure Functions 1.x 的 host.json 參考
description: Azure Functions host.json 檔案與 v1 執行階段的參考文件。
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 2b00e2343e0959e07b195e2e98c6719a1893b8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277046"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>適用於 Azure Functions 1.x 的 host.json 參考

> [!div class="op_single_selector" title1="選擇正在使用的 Azure 函數運行時的版本： "]
> * [版本 1](functions-host-json-v1.md)
> * [版本 2](functions-host-json.md)

*host.json* 中繼資料檔案所包含的全域設定選項會影響函式應用程式的所有函式。 本文列出 v1 執行階段可用的設定。 JSON 結構描述位於 http://json.schemastore.org/host。

> [!NOTE]
> 本文適用於 Azure Functions 1.x。  有關函數 2.x 和更高版本中的 host.json 的引用，請參閱[Azure 函數 2.x 的 host.json 引用](functions-host-json.md)。

其他函數應用程式設定選項的管理是在[應用程式設定](functions-app-settings.md)中進行。

有些 host.json 設定只有在本機執行時，才會在 [local.settings.json](functions-run-local.md#local-settings-file) 檔案中使用。

## <a name="sample-hostjson-file"></a>範例 host.json 檔案

下列範例 *host.json* 檔案已指定所有可能的選項。


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

本文的下列各節說明每個最上層屬性。 除非另有說明，否則全部都是選擇項目。

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

[Azure Cosmos DB 觸發器和綁定的](functions-bindings-cosmosdb.md)配置設置。

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------|
|GatewayMode|閘道|連線到 Azure Cosmos DB 服務時函式所使用的連線模式。 選項為 `Direct` 和 `Gateway`|
|通訊協定|Https|連線到 Azure Cosmos DB 服務時函式所使用的連線通訊協定。  請參閱[此處以了解這兩種模式](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n/a|要在應用程式的所有函式上使用的租用前置詞。|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

[事件中樞觸發程序和繫結](functions-bindings-event-hubs.md)的組態設定。

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

工作主機所執行的函式清單。 空陣列表示已執行所有函式。 預定只能在[本機執行](functions-run-local.md)時使用。 在 Azure 的函數應用程式中，您應該改為依照[如何停用 Azure Functions 中的函式](disable-function.md)中的步驟來停用特定函式，而不是使用此設定。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

指出所有函式的逾時持續期間。 在無伺服器的使用情況方案中，有效範圍是從 1 秒到 10 分鐘，而預設值是 5 分鐘。 在應用服務方案中，沒有總體限制，預設值為_null，_ 表示沒有超時。

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

[主機健康情況監視器](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)的組態設定。

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|已啟用|true|指定是否已啟用此功能。 | 
|healthCheckInterval|10 秒|定期背景健康情況檢查之間的時間間隔。 | 
|healthCheckWindow|2 分鐘|與 `healthCheckThreshold` 設定搭配使用的滑動時間範圍。| 
|healthCheckThreshold|6|在主機回收起始之前，健康情況檢查可以失敗的最大次數。| 
|counterThreshold|0.80|系統會將效能計數器視為狀況不良的閾值。| 

## <a name="http"></a>http

[HTTP 觸發程序和繫結](functions-bindings-http-webhook.md)的組態設定。

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|dynamicThrottlesEnabled|false|啟用時，此設定會促使要求處理管線定期檢查系統效能計數器，例如連線/執行緒/處理程序/記憶體/CPU/其他，而且如果這些計數器中任一個超過內建的臨界值上限 (80%)，則要求會遭到拒絕，並包含 429「忙碌」的回應，直到計數器回到正常水平。|
|maxConcurrentRequests|無界 （`-1`）|將並存執行的最大 HTTP 函數數。 這可讓您控制並行作業，幫助您管理資源使用率。 例如，您可能具有一個使用大量系統資源（記憶體/cpu/通訊端）的 HTTP 函數，以便當併發性過高時，它會導致問題。 或者，如果函式對第三方服務發出傳出要求，則需要限制這些呼叫的速率。 在這些情況下，套用節流會有所幫助。|
|maxOutstandingRequests|無界 （`-1`）|在任何指定時間保留的未完成要求數目上限。 此限制包括已排入佇列但尚未開始執行的要求，以及任何進行中的執行。 會以 429「忙碌」回應來拒絕任何超過此限制的連入要求。 這樣可讓呼叫者採用以時間為基礎的重試策略，並且也協助您控制要求延遲的上限。 此動作只會控制在指令碼主機執行路徑內發生的佇列處理。 其他佇列 (例如 ASP.NET 要求佇列) 仍然有效，且不受此設定的影響。|
|routePrefix|api|適用於所有路由的路由前置詞。 若要移除預設前置詞，請使用空字串。 |

## <a name="id"></a>id

作業主機的唯一識別碼。 可以是已移除虛線的小寫 GUID。 在本機執行時為必要項目。 在 Azure 中執行時，建議您不要設定識別碼值。 當省略 `id` 時，在 Azure 中會自動產生識別碼。 

如果您在多個函數應用程式中共用儲存體帳戶，請確定每個函數應用程式具有不同的 `id`。 您可以省略 `id` 屬性或將每個函數應用程式的 `id` 手動設定為不同的值。 計時器觸發程序會使用儲存體鎖定，以確保當函數應用程式相應放大至多個執行個體時，只會有一個計時器執行個體。 如果兩個函數應用程式共用相同的 `id`，且每一個都是使用計時器觸發程序，則只有一個計時器會執行。

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

控制 [ILogger 物件](functions-monitoring.md#write-logs-in-c-functions)或 [context.log](functions-monitoring.md#write-logs-in-javascript-functions) 所寫入記錄的篩選。

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|categoryFilter|n/a|指定依類別的篩選| 
|defaultLevel|資訊|針對 `categoryLevels` 陣列中未指定的任何類別，會將這個層級和以上層級的記錄傳送至 Application Insights。| 
|categoryLevels|n/a|一個類別陣列，指定針對每個類別傳送至 Application Insights 的最小記錄層級。 這裡指定的類別控制所有開頭為相同值的類別，但會優先使用較長的值。 在上述範例 *host.json* 檔案中，所有開頭為 "Host.Aggregator" 的類別都會記錄在 `Information` 層級。 所有開頭為 "Host" 的其他類別 (例如 "Host.Executor") 都會記錄於 `Error` 層級。| 

## <a name="queues"></a>queues

[儲存體佇列觸發程序和繫結](functions-bindings-storage-queue.md)的組態設定。

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|maxPollingInterval|60000|佇列輪詢之間的間隔上限 (毫秒)。| 
|visibilityTimeout|0|處理訊息失敗時，重試之間的時間間隔。| 
|batchSize|16|Functions 執行階段會同時擷取，並以平行方式處理的佇列訊息數目。 當要處理的數目減少到 `newBatchThreshold` 時，執行階段就會取得另一個批次，並開始處理那些訊息。 因此，每個函式並行處理之訊息的上限為 `batchSize` 加上 `newBatchThreshold`。 這項限制個別套用至每個佇列觸發的函式。 <br><br>如果您需要避免平行執行在單一佇列上收到的訊息，可以將 `batchSize` 設定為 1。 不過，只要您的函式應用程式在單一虛擬機器 (VM) 上執行，這項設定就只會將並行排除。 如果函式應用程式相應放大為多個 VM，則每個 VM 可以執行每個佇列觸發之函式的一個執行個體。<br><br>最大值 `batchSize` 為 32。 | 
|maxDequeueCount|5|將訊息移至有害佇列之前，嘗試處理訊息的次數。| 
|newBatchThreshold|batchSize/2|每當要同時處理的訊息數目下降至這個數字時，執行階段就會擷取另一個批次。| 

## <a name="sendgrid"></a>SendGrid

[SendGrind 輸出綁定](functions-bindings-sendgrid.md)的配置設置

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|從|n/a|所有函式的寄件者電子郵件地址。| 

## <a name="servicebus"></a>serviceBus

[服務匯流排觸發程序和繫結](functions-bindings-service-bus.md)的組態設定。

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|maxConcurrentCalls|16|訊息幫浦應該起始之回呼的並行呼叫數上限。 Functions 執行階段預設會並行處理多個訊息。 若要指示執行階段一次只處理一個佇列或主題訊息，請將 `maxConcurrentCalls` 設定為 1。 | 
|prefetchCount|n/a|基礎 MessageReceiver 將使用的預設 PrefetchCount。| 
|autoRenewTimeout|00:05:00|將自動更新訊息鎖定的最大持續時間。| 

## <a name="singleton"></a>singleton

Singleton 鎖定行為的組態設定。 如需詳細資訊，請參閱[單一支援的 GitHub 問題](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)。

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|lockPeriod|00:00:15|取得函式層級鎖定的期間。 鎖定會自動更新。| 
|listenerLockPeriod|00:01:00|接聽程式鎖定所需的期間。| 
|listenerLockRecoveryPollingInterval|00:01:00|啟動時無法取得接聽程式鎖定時，用於接聽程式鎖定復原的時間間隔。| 
|lockAcquisitionTimeout|00:01:00|執行階段將嘗試取得鎖定的時間量上限。| 
|lockAcquisitionPollingInterval|n/a|鎖定取得嘗試之間的間隔。| 

## <a name="tracing"></a>tracing

*1.x 版*

使用 `TraceWriter` 物件所建立記錄的組態設定。 請參閱 [C# 記錄](functions-reference-csharp.md#logging)和 [Node.js 記錄](functions-reference-node.md#writing-trace-output-to-the-console)。

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|consoleLevel|info|主控台記錄的追蹤層級。 選項為：`off`、`error`、`warning`、`info` 和 `verbose`。|
|fileLoggingMode|debugOnly|檔案記錄的追蹤層級。 選項為 `never`、`always`、`debugOnly`。| 

## <a name="watchdirectories"></a>watchDirectories

應該監視其變更的一組[共用程式碼目錄](functions-reference-csharp.md#watched-directories)。  請確定，這些目錄中的程式碼變更時，函式會反映變更。

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何更新 host.json 檔案](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [請參閱環境變數中的全域設定](functions-app-settings.md)
