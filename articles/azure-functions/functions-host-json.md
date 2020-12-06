---
title: 適用於 Azure Functions 2.x 的 host.json 參考
description: Azure Functions host.json 檔案與 v2 執行階段的參考文件。
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 96d6b884e9e2c835316af01140c6fc7208ee5ab9
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746075"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure Functions 2.x 和更新版本的 host.json 參考 (機器翻譯) 

> [!div class="op_single_selector" title1="選取您要使用的 Azure Functions 執行階段版本： "]
> * [第 1 版](functions-host-json-v1.md)
> * [第2版 +](functions-host-json.md)

*host.json* 中繼資料檔案所包含的全域設定選項會影響函式應用程式的所有函式。 本文列出從2.x 版的 Azure Functions 執行時間開始可用的設定。  

> [!NOTE]
> 本文適用于 Azure Functions 2.x 和更新版本。  有關 Functions 1.x 中 host.json 的參考，請參閱[適用於 Azure Functions 1.x 的 host.json 參考](functions-host-json-v1.md)。

其他函式應用程式設定選項會在您的 [應用程式設定](functions-app-settings.md) 中管理 (適用于已部署的應用程式) 或您在檔案 (進行本機開發) 的 [local.settings.js](functions-run-local.md#local-settings-file) 。

與系結相關 host.js中的設定會同樣套用至函數應用程式中的每個函式。 

您也可以使用應用程式設定來覆 [寫或套用每個環境的設定](#override-hostjson-values) 。

## <a name="sample-hostjson-file"></a>範例 host.json 檔案

下列範例 *host.json* 2.x 版的檔案，已指定所有可能的選項 (不包括僅供內部使用) 的任何選項。

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
    "retry": {
      "strategy": "fixedDelay",
      "maxRetryCount": 5,
      "delayInterval": "00:00:05"
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

控制 Application Insights 的選項，包括 [取樣選項](./configure-monitoring.md#configure-sampling)。

如需完整的 JSON 結構，請參閱先前的 [範例 host.json file](#sample-hostjson-file)。

> [!NOTE]
> 記錄取樣可能會造成一些執行不會顯示在 Application Insights 監視器刀鋒視窗。 若要避免記錄取樣，請新增 `excludedTypes: "Request"` 至 `samplingSettings` 值。

| 屬性 | 預設 | 描述 |
| --------- | --------- | --------- | 
| samplingSettings | n/a | 請參閱 [applicationInsights. samplingSettings](#applicationinsightssamplingsettings)。 |
| enableLiveMetrics | true | 啟用即時計量收集。 |
| enableDependencyTracking | true | 啟用相依性追蹤。 |
| enablePerformanceCountersCollection | true | 啟用 Kudu 效能計數器集合。 |
| liveMetricsInitializationDelay | 00:00:15 | 僅供內部使用。 |
| HTTPAutoCollectionOptions | n/a | 請參閱 [applicationInsights. HTTPAutoCollectionOptions](#applicationinsightshttpautocollectionoptions)。 |
| snapshotConfiguration | n/a | 請參閱 [applicationInsights. snapshotConfiguration](#applicationinsightssnapshotconfiguration)。 |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights. samplingSettings

如需這些設定的詳細資訊，請參閱 [Application Insights 中的取樣](../azure-monitor/app/sampling.md)。 

|屬性 | 預設 | 描述 |
| --------- | --------- | --------- | 
| isEnabled | true | 啟用或停用取樣。 | 
| maxTelemetryItemsPerSecond | 20 | 每部伺服器主機上每秒記錄的遙測專案的目標數目。 如果您的應用程式在多部主機上執行，請減少此值以維持在整體的流量速率內。 | 
| evaluationInterval | 01:00:00 | 重新評估目前的遙測速率的間隔。 評估是以移動平均來執行。 如果您的遙測會突然暴增，您可能想要縮短此間隔。 |
| initialSamplingPercentage| 100.0 | 取樣程式開始時套用的初始取樣百分比，以動態方式改變百分比。 當您正在進行調試時，請勿減少值。 |
| samplingPercentageIncreaseTimeout | 00:00:01 | 當取樣百分比值變更時，這個屬性會決定之後 Application Insights 可以再次引發取樣百分比來捕捉更多資料。 |
| samplingPercentageDecreaseTimeout | 00:00:01 | 當取樣百分比值變更時，這個屬性會決定允許的 Application Insights 再次減少取樣百分比，以抓取較少的資料。 |
| minSamplingPercentage | 0.1 | 當取樣百分比改變時，這個屬性會決定允許的最小取樣百分比。 |
| maxSamplingPercentage | 100.0 | 當取樣百分比改變時，這個屬性會決定允許的取樣百分比上限。 |
| movingAverageRatio | 1.0 | 在計算移動平均時，指派給最新的值的權數。 使用等於或小於 1 的值。 較小的值會讓演算法不易受突然的變更影響。 |
| excludedTypes | null | 您不想要取樣的類型清單（以分號分隔）。 可辨識的類型為： `Dependency` 、 `Event` 、 `Exception` 、 `PageView` 、 `Request` 和 `Trace` 。 系統會傳送指定類型的所有實例;系統會取樣未指定的類型。 |
| includedTypes | null | 要取樣的類型清單（以分號分隔）;空白清單表示所有類型。 此處所列的覆寫類型中所列的類型 `excludedTypes` 。 可辨識的類型為： `Dependency` 、 `Event` 、 `Exception` 、 `PageView` 、 `Request` 和 `Trace` 。 取樣指定類型的實例;未指定或隱含的型別會在沒有取樣的情況下傳輸。 |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights. HTTPAutoCollectionOptions

|屬性 | 預設 | 描述 |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | 啟用或停用 HTTP 觸發程式的延伸 HTTP 要求資訊：連入要求相互關聯標頭、多重檢測金鑰支援、HTTP 方法、路徑和回應。 |
| enableW3CDistributedTracing | true | 啟用或停用 W3C 分散式追蹤通訊協定 (的支援，並開啟舊版的相互關聯架構) 。 若為 true，則預設 `enableHttpTriggerExtendedInfoCollection` 為啟用。 如果 `enableHttpTriggerExtendedInfoCollection` 為 false，此旗標只會套用至連出要求，而不會套用至連入要求。 |
| enableResponseHeaderInjection | true | 啟用或停用將多元件相互關聯標頭插入回應中的功能。 啟用插入可讓 Application Insights 在使用多個檢測金鑰時，建立應用程式對應。 若為 true，則預設 `enableHttpTriggerExtendedInfoCollection` 為啟用。 如果為 false，則不適用此設定 `enableHttpTriggerExtendedInfoCollection` 。 |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights. snapshotConfiguration

如需快照集的詳細資訊，請參閱 [.net 應用程式例外狀況的偵錯工具](../azure-monitor/app/snapshot-debugger.md) ，以及 [疑難排解啟用 Application Insights 快照偵錯工具或查看快照](../azure-monitor/app/snapshot-debugger-troubleshoot.md)集的問題。

|屬性 | 預設 | 描述 |
| --------- | --------- | --------- | 
| agentEndpoint | null | 用來連接到 Application Insights 快照偵錯工具服務的端點。 如果是 null，則會使用預設端點。 |
| captureSnapshotMemoryWeight | 0.5 | 檢查是否有足夠的記憶體可建立快照集時，提供給目前進程記憶體大小的加權。 預期的值是大於0的適當小數 (0 < CaptureSnapshotMemoryWeight < 1) 。 |
| failedRequestLimit | 3 | 在停用遙測處理器之前，要求快照集失敗要求數目的限制。|
| handleUntrackedExceptions | true | 啟用或停用追蹤未受 Application Insights 遙測追蹤的例外狀況。 |
| isEnabled | true | 啟用或停用快照集集合 | 
| isEnabledInDeveloperMode | false | 啟用或停用開發人員模式中的快照集集合。 |
| isEnabledWhenProfiling | true | 即使 Application Insights Profiler 正在收集詳細的分析會話，也會啟用或停用快照集建立。 |
| isExceptionSnappointsEnabled | false | 啟用或停用例外狀況的篩選。 |
| isLowPrioritySnapshotUploader | true | 決定是否要在低於一般優先權的情況下執行 Snapshotuploader.exe 進程。 |
| maximumCollectionPlanSize | 50 | 可在一段時間內從1到9999的最大問題數目。 |
| maximumSnapshotsRequired | 3 | 針對單一問題所收集的快照集數目上限，範圍從1到999。 在您的應用程式中，可能會將問題視為個別的 throw 語句。 一旦針對問題收集的快照集數目達到此值，就不會針對該問題收集更多快照集，除非重設問題計數器 (查看 `problemCounterResetInterval`) ，然後 `thresholdForSnapshotting` 再重新達到限制。 |
| problemCounterResetInterval | 24:00:00 | 從一分鐘到七天的範圍中，將問題計數器重設的頻率。 達到此間隔時，所有問題計數都會重設為零。 現有的問題已經達到執行快照集的臨界值，但尚未產生中的快照集數目 `maximumSnapshotsRequired` ，仍會保持作用中狀態。 |
| provideAnonymousTelemetry | true | 決定是否要將匿名的使用方式和錯誤遙測傳送給 Microsoft。 如果您與 Microsoft 聯繫，以協助疑難排解快照偵錯工具的問題，則可以使用此遙測。 它也可用來監視使用模式。 |
| reconnectInterval | 00:15:00 | 重新連線至快照偵錯工具端點的頻率。 允許的範圍是一分鐘到一天。 |
| shadowCopyFolder | null | 指定用於陰影複製二進位檔的資料夾。 如果未設定，則會依序嘗試下列環境變數所指定的資料夾： Fabric_Folder_App_Temp、LOCALAPPDATA、APPDATA、TEMP。 |
| shareUploaderProcess | true | 若為 true，則只有一個 Snapshotuploader.exe 實例會針對共用 InstrumentationKey 的多個應用程式收集和上傳快照集。 如果設定為 false，則 Snapshotuploader.exe 對每個 (ProcessName、InstrumentationKey) 元組都是唯一的。 |
| snapshotInLowPriorityThread | true | 決定是否要在低 IO 優先權執行緒中處理快照集。 建立快照集是快速的作業，但為了將快照集上傳至快照偵錯工具服務，必須先將它寫入磁片做為小型傾印。 這會發生在 Snapshotuploader.exe 過程中。 將此值設定為 true 會使用低優先順序 IO 來寫入小型傾印，而不會與您的應用程式競爭資源。 將此值設定為 false 可加速建立小型傾印，但會降低應用程式的速度。 |
| snapshotsPerDayLimit | 30 | 一天內允許的快照集數目上限 (24 小時) 。 這項限制也會在 Application Insights 服務端強制執行。 每個應用程式每日上傳的速率限制為 50 (也就是每個檢測金鑰) 。 此值有助於防止建立將在上傳期間被拒絕的其他快照集。 值為零會完全移除限制，這不是建議的做法。 |
| snapshotsPerTenMinutesLimit | 1 | 10分鐘內允許的快照集數目上限。 雖然此值沒有上限，但請小心增加生產工作負載的效能，因為這可能會影響應用程式的效能。 建立快照集是快速的，但建立快照集的小型傾印，並將其上傳至快照偵錯工具服務是一項速度較慢的作業，它會與您的應用程式競爭 (CPU 和 i/o) 的資源。 |
| tempFolder | null | 指定要寫入小型傾印和上載者記錄檔的資料夾。 如果未設定，則會使用 *%TEMP%\Dumps* 。 |
| >thresholdforsnapshotting | 1 | Application Insights 在要求快照集之前，需要查看例外狀況的次數。 |
| uploaderProxy | null | 覆寫快照集上載程式中使用的 proxy 伺服器。 如果您的應用程式透過 proxy 伺服器連接到網際網路，您可能需要使用此設定。 Snapshot Collector 會在應用程式的進程內執行，並且會使用相同的 proxy 設定。 不過，快照集上傳程式會以個別的進程執行，您可能需要手動設定 proxy 伺服器。 如果這個值為 null，則 Snapshot Collector 會嘗試透過檢查 WebRequest DefaultWebProxy，並將值傳遞至快照集上傳者，來自動偵測 proxy 的位址。 如果這個值不是 null，則不會使用自動偵測，而在此指定的 proxy 伺服器將用於快照集上載中。 |

## <a name="cosmosdb"></a>cosmosDb

可在 [Cosmos DB 觸發程序和繫結](functions-bindings-cosmosdb-v2-output.md#host-json)中找到組態設定。

## <a name="customhandler"></a>customHandler

自訂處理常式的設定。 如需詳細資訊，請參閱 [Azure Functions 自訂處理常式](functions-custom-handlers.md#configuration)。

```json
"customHandler": {
  "description": {
    "defaultExecutablePath": "server",
    "workingDirectory": "handler",
    "arguments": [ "--port", "%FUNCTIONS_CUSTOMHANDLER_PORT%" ]
  },
  "enableForwardingHttpRequest": false
}
```

|屬性 | 預設 | 描述 |
| --------- | --------- | --------- |
| defaultExecutablePath | n/a | 要作為自訂處理常式進程啟動的可執行檔。 這是使用自訂處理常式時的必要設定，且其值與函式應用程式根目錄相關。 |
| workingDirectory | *函數應用程式根目錄* | 要在其中啟動自訂處理常式進程的工作目錄。 這是選擇性的設定，而且其值會相對於函式應用程式根目錄。 |
| 引數 | n/a | 要傳遞至自訂處理常式進程的命令列引數陣列。 |
| enableForwardingHttpRequest | false | 如果設定，只會將所有包含 HTTP 觸發程式和 HTTP 輸出的函式轉寄給原始 HTTP 要求，而不是自訂處理常式 [要求](functions-custom-handlers.md#request-payload)承載。 |

## <a name="durabletask"></a>durableTask

可在 [Durable Functions 的繫結](durable/durable-functions-bindings.md#host-json)中找到組態設定。

## <a name="eventhub"></a>eventHub

可在[事件中樞觸發程序和繫結](functions-bindings-event-hubs-trigger.md#host-json)中找到組態設定。 

## <a name="extensions"></a>擴充功能

傳回包含所有繫結特定設定 (例如 [http](#http) 和 [eventHub](#eventhub)) 之物件的屬性。

## <a name="extensionbundle"></a>extensionBundle 

延伸模組套件組合可讓您將一組相容的函式系結延伸模組新增至函式應用程式。 若要深入瞭解，請參閱 [本機開發的延伸](functions-bindings-register.md#extension-bundles)模組組合。

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

工作主機所執行的函式清單。 空陣列表示已執行所有函式。 預定只能在[本機執行](functions-run-local.md)時使用。 在 Azure 的函數應用程式中，您應該改為依照[如何停用 Azure Functions 中的函式](disable-function.md)中的步驟來停用特定函式，而不是使用此設定。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

指出所有函式的逾時持續期間。 它會遵循 timespan 字串格式。 

| 方案類型 | 預設 (最小)  | 最大 (最小值)  |
| -- | -- | -- |
| 耗用量 | 5 | 10 |
| Premium<sup>1</sup> | 30 | -1 (未系結的) <sup>2</sup> |
| 專用 (App Service)  | 30 | -1 (未系結的) <sup>2</sup> |

<sup>1</sup> 個 Premium 方案執行只保證60分鐘，但技術上沒有限制。   
<sup>2</sup> ：值表示未系結的 `-1` 執行，但建議保持固定上限。

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
    "fileLoggingMode": "debugOnly",
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
|logLevel|n/a|為應用程式中的函式定義記錄類別篩選的物件。 2.x 版和更新版本會依照記錄類別篩選的 ASP.NET Core 配置。 此設定可讓您篩選特定功能的記錄。 如需詳細資訊，請參閱 ASP.NET Core 文件中的[記錄篩選](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering)。 |
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

受控相依性是目前只有以 PowerShell 為基礎的函式支援的功能。 它可讓服務自動管理相依性。 當 `enabled` 屬性設定為時 `true` ， `requirements.psd1` 就會處理檔案。 釋放任何次要版本時，會更新相依性。 如需詳細資訊，請參閱 PowerShell 文章中的 [受控](functions-reference-powershell.md#dependency-management) 相依性。

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

可在[儲存體佇列觸發程序和繫結](functions-bindings-storage-queue-output.md#host-json)中找到組態設定。  

## <a name="retry"></a>retry

控制應用程式中所有執行的 [重試原則](./functions-bindings-error-pages.md#retry-policies-preview) 選項。

```json
{
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 2,
        "delayInterval": "00:00:03"  
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|策略|null|必要。 要使用的重試策略。 有效值為 `fixedDelay` 或 `exponentialBackoff`。|
|maxRetryCount|null|必要。 每個函數執行所允許的重試次數上限。 `-1` 表示要無限期地重試。|
|delayInterval|null|使用策略重試之間的延遲 `fixedDelay` 。|
|minimumInterval|null|使用策略時的最小重試延遲 `exponentialBackoff` 。|
|maximumInterval|null|使用策略時的最大重試延遲 `exponentialBackoff` 。| 

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

此值指出 host.js的架構版本。 `"version": "2.0"`目標為 v2 執行時間（或更新版本）的函式應用程式需要版本字串。 V2 和 v3 之間的架構變更沒有 host.js。

## <a name="watchdirectories"></a>watchDirectories

應該監視其變更的一組[共用程式碼目錄](functions-reference-csharp.md#watched-directories)。  請確定，這些目錄中的程式碼變更時，函式會反映變更。

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="override-hostjson-values"></a>覆寫值上的 host.js

在某些情況下，您可能會想要在特定環境的檔案 host.js中設定或修改特定設定，而不需要變更檔案本身的 host.js。  您可以覆寫值的特定 host.js，以將相等的值建立為應用程式設定。 當執行時間以格式尋找應用程式設定時 `AzureFunctionsJobHost__path__to__setting` ，它會覆寫位於 JSON 中之設定的對等 host.js`path.to.setting` 。 當表示為應用程式設定時， `.` 用來表示 JSON 階層的點 () 會以雙底線 (`__`) 取代。 

例如，假設您想要在本機執行時停用應用程式深入解析取樣。 如果您變更本機 host.js檔案以停用 Application Insights，則在部署期間可能會將這種變更推送至您的生產應用程式。 更安全的做法是改為在檔案中建立應用程式設定 `"AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"` `local.settings.json` 。 您可以在下列檔案中看到 `local.settings.json` 未發行的內容：

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "{storage-account-connection-string}",
        "FUNCTIONS_WORKER_RUNTIME": "{language-runtime}",
        "AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"
    }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何更新 host.json 檔案](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [請參閱環境變數中的全域設定](functions-app-settings.md)
