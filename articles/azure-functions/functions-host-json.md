---
title: 適用於 Azure Functions 2.x 的 host.json 參考
description: Azure Functions host.json 檔案與 v2 執行階段的參考文件。
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 949d4f2c5d8c1d8034ccc392915bc40f1f2fddda
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276994"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure Functions 2.x 和更新版本的 host. json 參考 

> [!div class="op_single_selector" title1="選取您要使用的 Azure Functions 執行階段版本： "]
> * [第 1 版](functions-host-json-v1.md)
> * [第2版 +](functions-host-json.md)

*host.json* 中繼資料檔案所包含的全域設定選項會影響函式應用程式的所有函式。 本文列出從 Azure Functions 執行時間2.x 版開始可用的設定。  

> [!NOTE]
> 本文適用于 Azure Functions 2.x 和更新版本。  有關 Functions 1.x 中 host.json 的參考，請參閱[適用於 Azure Functions 1.x 的 host.json 參考](functions-host-json-v1.md)。

其他函數應用程式設定選項會在您的[應用程式設定](functions-app-settings.md)中管理（適用于已部署的應用程式）或您的[本機. 設定 json](functions-run-local.md#local-settings-file)檔案（用於本機開發）。

與系結相關的 json 設定會平均套用至函數應用程式中的每個函式。 

## <a name="sample-hostjson-file"></a>範例 host.json 檔案

下列2.x 版的範例*主機.* x + 已指定所有可能的選項（不包括僅供內部使用的任何選項）。

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
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
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

本文的下列各節說明每個最上層屬性。 除非另有說明，否則全部都是選擇項目。

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

此設定是 [logging](#logging) 的子系。

控制 Application Insights 的選項，包括[取樣選項](./functions-monitoring.md#configure-sampling)。

如需完整的 JSON 結構，請參閱先前的[範例 host. JSON](#sample-hostjson-file)檔案。

> [!NOTE]
> 記錄取樣可能會造成一些執行不會顯示在 Application Insights 監視器刀鋒視窗。 若要避免記錄取樣，請將 `samplingExcludedTypes: "Request"` 新增至 `applicationInsights` 值。

| 屬性 | 預設 | 描述 |
| --------- | --------- | --------- | 
| samplingSettings | n/a | 請參閱[applicationInsights. samplingSettings](#applicationinsightssamplingsettings)。 |
| samplingExcludedTypes | null | 您不想要取樣的類型清單（以分號分隔）。 可辨識的類型為：相依性、事件、例外狀況、頁面檢視、要求、追蹤。 系統會傳送指定類型的所有實例;未指定的類型會進行取樣。 |
| samplingIncludedTypes | null | 您想要取樣的類型清單（以分號分隔）。空白清單表示所有類型。 在這裡所列的 `samplingExcludedTypes` 覆寫類型中列出的類型。 可辨識的類型為：相依性、事件、例外狀況、頁面檢視、要求、追蹤。 系統會傳送指定類型的所有實例;未指定的類型會進行取樣。 |
| enableLiveMetrics | true | 啟用即時計量集合。 |
| enableDependencyTracking | true | 啟用相依性追蹤。 |
| enablePerformanceCountersCollection | true | 啟用 Kudu 效能計數器集合。 |
| liveMetricsInitializationDelay | 00:00:15 | 僅供內部使用。 |
| HTTPAutoCollectionOptions | n/a | 請參閱[applicationInsights. HTTPAutoCollectionOptions](#applicationinsightshttpautocollectionoptions)。 |
| snapshotConfiguration | n/a | 請參閱[applicationInsights. snapshotConfiguration](#applicationinsightssnapshotconfiguration)。 |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights. samplingSettings

|屬性 | 預設 | 描述 |
| --------- | --------- | --------- | 
| isEnabled | true | 啟用或停用取樣。 | 
| maxTelemetryItemsPerSecond | 20 | 每部伺服器主機上每秒記錄的遙測專案目標數目。 如果您的應用程式在多部主機上執行，請減少此值以維持在整體目標流量的速率。 | 
| evaluationInterval | 01:00:00 | 重新評估目前遙測速率的間隔。 評估是以移動平均來執行。 如果您的遙測會突然暴增，您可能想要縮短此間隔。 |
| initialSamplingPercentage| 1.0 | 取樣程式開始時所套用的初始取樣百分比，以動態方式改變百分比。 當您正在進行調試時，請勿降低價值。 |
| samplingPercentageIncreaseTimeout | 00:00:01 | 當取樣百分比值變更時，這個屬性會決定允許 Application Insights 一次多久之後，再次引發取樣百分比來捕捉更多資料。 |
| samplingPercentageDecreaseTimeout | 00:00:01 | 當取樣百分比值變更時，此屬性會決定允許 Application Insights 一次後，讓取樣百分比更低，以捕獲較少的資料。 |
| minSamplingPercentage | 0.1 | 當取樣百分比不同時，此屬性會決定允許的取樣百分比下限。 |
| maxSamplingPercentage | 0.1 | 當取樣百分比不同時，此屬性會決定允許的取樣百分比上限。 |
| movingAverageRatio | 1.0 | 在計算移動平均時，指派給最新的值的權數。 使用等於或小於 1 的值。 較小的值會讓演算法不易受突然的變更影響。 |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights. HTTPAutoCollectionOptions

|屬性 | 預設 | 描述 |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | 啟用或停用 HTTP 觸發程式的擴充 HTTP 要求資訊：連入要求相互關聯標頭、多檢測金鑰支援、HTTP 方法、路徑和回應。 |
| enableW3CDistributedTracing | true | 啟用或停用 W3C 分散式追蹤通訊協定的支援（並開啟舊版相互關聯架構）。 如果 `enableHttpTriggerExtendedInfoCollection` 為 true，則預設為啟用。 如果 `enableHttpTriggerExtendedInfoCollection` 為 false，則此旗標僅適用于連出要求，而不會套用到連入要求。 |
| enableResponseHeaderInjection | true | 啟用或停用將多重元件相互關聯標頭插入回應中的功能。 啟用插入功能可讓 Application Insights 在使用數個檢測金鑰時，將應用程式對應至。 如果 `enableHttpTriggerExtendedInfoCollection` 為 true，則預設為啟用。 如果 `enableHttpTriggerExtendedInfoCollection` 為 false，則不適用此設定。 |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights. snapshotConfiguration

如需有關快照集的詳細資訊，請參閱[.net 應用程式中的例外](/azure/azure-monitor/app/snapshot-debugger)狀況的 Debug 錯快照集和[疑難排解啟用 Application Insights 快照偵錯工具或查看快照](/azure/azure-monitor/app/snapshot-debugger-troubleshoot)集的問題。

|屬性 | 預設 | 描述 |
| --------- | --------- | --------- | 
| agentEndpoint | null | 用來連接到 Application Insights 快照偵錯工具服務的端點。 如果是 null，則會使用預設端點。 |
| captureSnapshotMemoryWeight | 0.5 | 檢查是否有足夠的記憶體來製作快照集時，指定給目前進程記憶體大小的權數。 預期的值為大於0的適當分數（0 < CaptureSnapshotMemoryWeight < 1）。 |
| failedRequestLimit | 3 | 在停用遙測處理器之前，要求快照集的失敗要求數目限制。|
| handleUntrackedExceptions | true | 啟用或停用 Application Insights 遙測不會追蹤的例外狀況追蹤。 |
| isEnabled | true | 啟用或停用快照集集合 | 
| isEnabledInDeveloperMode | false | 啟用或停用開發人員模式中的快照集集合。 |
| isEnabledWhenProfiling | true | 即使 Application Insights Profiler 正在收集詳細的分析會話，也會啟用或停用快照集建立。 |
| isExceptionSnappointsEnabled | false | 啟用或停用例外狀況的篩選。 |
| isLowPrioritySnapshotUploader | true | 判斷是否要在一般優先順序之下執行 Snapshotuploader.exe 進程。 |
| maximumCollectionPlanSize | 50 | 從1到9999的範圍內，可以隨時追蹤的問題數目上限。 |
| maximumSnapshotsRequired | 3 | 針對單一問題收集的快照集數目上限，範圍從1到999。 在您的應用程式中，可能會將問題視為個別的 throw 語句。 一旦為問題收集的快照集數目達到此值，就不會再收集該問題的快照集，直到重新設定問題計數器為止（請參閱 `problemCounterResetInterval`），然後再次到達 `thresholdForSnapshotting` 限制。 |
| problemCounterResetInterval | 24:00:00 | 從一分鐘到七天的範圍內，重設問題計數器的頻率。 當達到此間隔時，所有問題計數都會重設為零。 現有的問題已經達到執行快照集的閾值，但尚未產生 `maximumSnapshotsRequired`中的快照集數目，仍在作用中。 |
| provideAnonymousTelemetry | true | 決定是否要將匿名使用方式和錯誤遙測傳送給 Microsoft。 如果您聯繫 Microsoft 來協助疑難排解快照偵錯工具的問題，可能會使用此遙測。 它也可用來監視使用模式。 |
| reconnectInterval | 00:15:00 | 我們重新連線到快照偵錯工具端點的頻率。 允許的範圍是一分鐘到一天。 |
| shadowCopyFolder | null | 指定用於陰影複製二進位檔的資料夾。 如果未設定，則會依序嘗試下列環境變數所指定的資料夾： Fabric_Folder_App_Temp、LOCALAPPDATA、APPDATA、TEMP。 |
| shareUploaderProcess | true | 若為 true，則只有一個 Snapshotuploader.exe 實例會針對共用 InstrumentationKey 的多個應用程式收集和上傳快照集。 如果設定為 false，則每個（ProcessName，InstrumentationKey）元組的 Snapshotuploader.exe 都是唯一的。 |
| snapshotInLowPriorityThread | true | 決定是否要處理低 IO 優先順序執行緒中的快照集。 建立快照集是快速的作業，但若要將快照集上傳至快照偵錯工具服務，則必須先將其寫入磁片做為小型傾印。 這會發生在 Snapshotuploader.exe 流程中。 將此值設定為 true 時，會使用低優先順序 IO 來寫入小型傾印，這不會與您的應用程式競爭資源。 將此值設定為 false 可加速建立小型傾印，代價是讓應用程式變慢。 |
| snapshotsPerDayLimit | 30 | 一天內允許的快照集數目上限（24小時）。 Application Insights 服務端也會強制執行此限制。 上傳的速率限制為每個應用程式每天50（也就是每個檢測金鑰）。 這個值有助於防止建立在上傳期間最後會拒絕的其他快照集。 值為零會完全移除限制，這不是建議的做法。 |
| snapshotsPerTenMinutesLimit | 1 | 10分鐘內允許的快照集數目上限。 雖然此值沒有上限，但請謹慎增加生產工作負載，因為它可能會影響應用程式的效能。 建立快照集的速度很快，但建立快照集的小型傾印並上傳至快照偵錯工具服務，是比較慢的作業，會與您的應用程式競爭資源（CPU 和 i/o）。 |
| tempFolder | null | 指定要寫入小型傾印和上載者記錄檔的資料夾。 如果未設定，則會使用 *%TEMP%\Dumps* 。 |
| thresholdForSnapshotting | 1 | Application Insights 需要多少次才能在要求快照之前看到例外狀況。 |
| uploaderProxy | null | 覆寫快照集上載程式中所使用的 proxy 伺服器。 如果您的應用程式透過 proxy 伺服器連接到網際網路，您可能需要使用此設定。 Snapshot Collector 會在應用程式的進程中執行，並使用相同的 proxy 設定。 不過，快照集上傳程式會以個別的進程執行，您可能需要手動設定 proxy 伺服器。 如果這個值是 null，則 Snapshot Collector 會嘗試透過檢查 WebRequest DefaultWebProxy，並將值傳遞給快照集上傳程式，以自動偵測 proxy 的位址。 如果此值不是 null，則不會使用自動偵測，而在這裡指定的 proxy 伺服器將用於快照集上載中。 |

## <a name="cosmosdb"></a>cosmosDb

可在 [Cosmos DB 觸發程序和繫結](functions-bindings-cosmosdb-v2-output.md#host-json)中找到組態設定。

## <a name="durabletask"></a>durableTask

可在 [Durable Functions 的繫結](durable/durable-functions-bindings.md#host-json)中找到組態設定。

## <a name="eventhub"></a>eventHub

可在[事件中樞觸發程序和繫結](functions-bindings-event-hubs-output.md#host-json)中找到組態設定。 

## <a name="extensions"></a>擴充功能

傳回包含所有繫結特定設定 (例如 [http](#http) 和 [eventHub](#eventhub)) 之物件的屬性。

## <a name="extensionbundle"></a>extensionBundle 

擴充功能套件可讓您將一組相容的函式系結延伸模組，新增至函式應用程式。 若要深入瞭解，請參閱[本機開發的延伸](functions-bindings-register.md#extension-bundles)模組組合。

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

工作主機所執行的函式清單。 空陣列表示已執行所有函式。 預定只能在[本機執行](functions-run-local.md)時使用。 在 Azure 的函數應用程式中，您應該改為依照[如何停用 Azure Functions 中的函式](disable-function.md)中的步驟來停用特定函式，而不是使用此設定。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

指出所有函式的逾時持續期間。 它會遵循 timespan 字串格式。 在無伺服器的使用情況方案中，有效範圍是從 1 秒到 10 分鐘，而預設值是 5 分鐘。  

在 Premium 方案中，有效範圍是從1秒到60分鐘，而預設值為30分鐘。

在專用（App Service）方案中，沒有整體限制，而預設值為30分鐘。 值為 `-1` 表示不受限制的執行，但建議保留固定的上限。

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

可在 [HTTP 觸發程序和繫結](functions-bindings-http-webhook-output.md#hostjson-settings)中找到組態設定。

## <a name="logging"></a>logging

控制函數應用程式 (包括 Application Insights) 的記錄行為。

```json
"logging": {
    "fileLoggingMode": "debugOnly"
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------|
|fileLoggingMode|debugOnly|定義已啟用何種檔案記錄層級。  選項為 `never`、`always`、`debugOnly`。 |
|logLevel|n/a|為應用程式中的函式定義記錄類別篩選的物件。 2\.x 版和更新版本會遵循記錄類別篩選的 ASP.NET Core 版面配置。 此設定可讓您篩選特定功能的記錄。 如需詳細資訊，請參閱 ASP.NET Core 文件中的[記錄篩選](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)。 |
|console|n/a| [主控台](#console)記錄設定。 |
|applicationInsights|n/a| [applicationInsights](#applicationinsights) 設定。 |

## <a name="console"></a>console

此設定是 [logging](#logging) 的子系。 它會在非處於偵錯模式時控制主控台記錄。

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|isEnabled|false|啟用或停用主控台記錄。| 

## <a name="manageddependency"></a>managedDependency

受控相依性是目前僅支援以 PowerShell 為基礎的函式的一項功能。 它可讓服務自動管理相依性。 當 [`enabled`] 屬性設定為 [`true`] 時，就會處理 `requirements.psd1` 檔。 發行任何次要版本時，會更新相依性。 如需詳細資訊，請參閱 PowerShell 文章中的[受控](functions-reference-powershell.md#dependency-management)相依性。

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

可在[儲存體佇列觸發程序和繫結](functions-bindings-storage-queue-output.md#host-json)中找到組態設定。  

## <a name="sendgrid"></a>sendGrid

可在 [SendGrid 觸發程序和繫結](functions-bindings-sendgrid.md#host-json)中找到組態設定。

## <a name="servicebus"></a>serviceBus

可在[服務匯流排觸發程序和繫結](functions-bindings-service-bus-output.md#host-json)中找到組態設定。

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

## <a name="version"></a>version

此值表示 host. json 的架構版本。 以 v2 執行時間或更新版本為目標的函式應用程式需要版本字串 `"version": "2.0"`。 V2 和 v3 之間沒有任何主機。 json 架構變更。

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
