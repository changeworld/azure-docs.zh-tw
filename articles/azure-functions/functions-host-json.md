---
title: 適用於 Azure Functions 2.x 的 host.json 參考
description: Azure Functions host.json 檔案與 v2 執行階段的參考文件。
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 7b3c9d15fc8cf3a1651c44a5656f731a7820e344
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405494"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure 函數 2.x 及更高版本的 host.json 引用 

> [!div class="op_single_selector" title1="選擇正在使用的 Azure 函數執行時的版本: "]
> * [版本 1](functions-host-json-v1.md)
> * [版本 2*](functions-host-json.md)

*host.json* 中繼資料檔案所包含的全域設定選項會影響函式應用程式的所有函式。 本文列出了從 Azure 函數運行時的版本 2.x 開始的可用設置。  

> [!NOTE]
> 本文適用於 Azure 函數 2.x 和更高版本。  有關 Functions 1.x 中 host.json 的參考，請參閱[適用於 Azure Functions 1.x 的 host.json 參考](functions-host-json-v1.md)。

其他功能應用配置選項在[應用設置](functions-app-settings.md)(針對已部署的應用)或[本地.settings.json](functions-run-local.md#local-settings-file)檔(用於本地開發)中管理。

與綁定相關的 host.json 中的配置將同樣應用於函數應用中的每個函數。 

## <a name="sample-hostjson-file"></a>範例 host.json 檔案

版本 2.x_ 的以下範例*host.json*檔指定了所有可能的選項(不包括任何僅供內部使用的選項)。

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

控制應用程式的選項,包括[取樣選項](./functions-monitoring.md#configure-sampling)。

有關完整的 JSON 結構,請參閱前面的[範例 host.json 檔](#sample-hostjson-file)。

> [!NOTE]
> 記錄取樣可能會造成一些執行不會顯示在 Application Insights 監視器刀鋒視窗。 為了避免日誌採樣,請添加到`excludedTypes: "Request"``samplingSettings`值。

| 屬性 | 預設 | 描述 |
| --------- | --------- | --------- | 
| 取樣設定 | n/a | 請參考[應用程式的資訊檢視.取樣設定](#applicationinsightssamplingsettings)。 |
| 啟用即時測量 | true | 啟用即時指標集合。 |
| 開啟相依追蹤 | true | 啟用依賴項跟蹤。 |
| 開啟效能計數器集合 | true | 啟用庫杜性能計數器集合。 |
| 即時參數初始化延遲 | 00:00:15 | 僅供內部使用。 |
| HTTP 自動收集選項 | n/a | 請參考[應用程式的發解.HTTP 自動收集選項](#applicationinsightshttpautocollectionoptions)。 |
| 快照設定 | n/a | 請參考[應用程式的發解.快照設定](#applicationinsightssnapshotconfiguration)。 |

### <a name="applicationinsightssamplingsettings"></a>應用程式見解.取樣設定

|屬性 | 預設 | 描述 |
| --------- | --------- | --------- | 
| isEnabled | true | 啟用或停用取樣。 | 
| maxTelemetryItemsPerSecond | 20 | 每台伺服器主機上每秒記錄的遙測項的目標數量。 如果應用在許多主機上運行,請減小此值以保持在總體流量目標速率內。 | 
| 評估間隔 | 01:00:00 | 重新評估當前遙測速率的時間間隔。 評估是以移動平均來執行。 如果您的遙測會突然暴增，您可能想要縮短此間隔。 |
| 初始取樣百分比| 1.0 | 在採樣過程開始時應用的初始採樣百分比可動態更改百分比。 在調試時不要降低值。 |
| 取樣百分比增加超時 | 00:00:01 | 當採樣百分比值發生更改時,此屬性確定不久之後允許應用程式見解再次提高採樣百分比以捕獲更多數據。 |
| 取樣百分比減少超時 | 00:00:01 | 當採樣百分比值發生更改時,此屬性確定不久之後允許應用程式見解再次降低採樣百分比以捕獲更少的數據。 |
| 最小取樣百分比 | 0.1 | 由於採樣百分比不同,此屬性確定允許的最小採樣百分比。 |
| 最大採樣百分比 | 0.1 | 由於採樣百分比不同,此屬性確定允許的最大採樣百分比。 |
| 移動平均比率 | 1.0 | 在計算移動平均時，指派給最新的值的權數。 使用等於或小於 1 的值。 較小的值會讓演算法不易受突然的變更影響。 |
| 排除類型 | null | 不希望採樣類型的分號分隔清單。 識別型態是: `Dependency` `Event` `Exception``PageView``Request`、、、、、`Trace`和 。 傳輸指定類型的所有實例;對未指定的類型進行採樣。 |
| 包括類型 | null | 要採樣的類型的分號分隔清單;空清單表示所有類型的。 此處列出的覆蓋`excludedTypes`類型中列出的類型。 識別型態是: `Dependency` `Event` `Exception``PageView``Request`、、、、、`Trace`和 。 對指定類型的實例進行採樣;未指定或隱含的類型在沒有採樣的情況下傳輸。 |

### <a name="applicationinsightshttpautocollectionoptions"></a>應用程式見解.HTTP自動收集選項

|屬性 | 預設 | 描述 |
| --------- | --------- | --------- | 
| 開啟Httptrigger 擴充資訊集合 | true | 啟用或禁用 HTTP 觸發器的擴展 HTTP 請求資訊:傳入請求關聯標頭、多檢測密鑰支援、HTTP 方法、路徑和回應。 |
| 開啟W3C分散式追蹤 | true | 啟用或禁用對 W3C 分散式跟蹤協定的支援(並啟用舊相關架構)。 預設情況下啟用(如果`enableHttpTriggerExtendedInfoCollection`為 true)。 如果`enableHttpTriggerExtendedInfoCollection`為 false,則此標誌僅適用於傳出請求,不適用於傳入請求。 |
| 開啟回應標頭注入 | true | 啟用或禁用將多元件相關標頭注入回應。 啟用注入允許應用程式見解構造應用程式映射,以何時使用多個檢測密鑰。 預設情況下啟用(如果`enableHttpTriggerExtendedInfoCollection`為 true)。 如果`enableHttpTriggerExtendedInfoCollection`為 false,則此設定不適用。 |

### <a name="applicationinsightssnapshotconfiguration"></a>應用程式見解.快照設定

有關快照的詳細資訊,請參閱[在 .NET 應用中除錯異常的快照](/azure/azure-monitor/app/snapshot-debugger),以及[啟用應用程式見解快照除錯器或查看快照的疑難解答問題](/azure/azure-monitor/app/snapshot-debugger-troubleshoot)。

|屬性 | 預設 | 描述 |
| --------- | --------- | --------- | 
| 代理端點 | null | 用於連接到應用程式見解快照調試器服務的終結點。 如果為 null,則使用預設終結點。 |
| 捕獲快照記憶體權重 | 0.5 | 檢查是否有足夠的記憶體拍攝快照時,當前進程記憶體大小給出的權重。 預期值大於 0 正確的分數(0 <捕获快照内存权重< 1)。 |
| 失敗的要求限制 | 3 | 禁用遙測處理器之前請求快照的失敗請求數的限制。|
| 句柄 未追蹤的例外 | true | 啟用或禁用跟蹤應用程式見解遙測未跟蹤的異常。 |
| isEnabled | true | 開啟或關閉快照集合 | 
| 開啟開發者模式 | false | 在開發人員模式下啟用或禁用快照集合。 |
| 開啟時開啟 | true | 啟用或禁用快照創建,即使應用程式見解探查器正在收集詳細的分析會話。 |
| 開啟的例外捕捉點 | false | 啟用或禁用異常篩選。 |
| 是低優先順序快照載入器 | true | 確定是否以低於正常優先順序運行快照載入進程。 |
| 最大收集平面大小 | 50 | 我們可以在 1 到 9999 之間隨時跟蹤的最大問題數。 |
| 所需的最大快照 | 3 | 為單個問題收集的快照的最大數量,範圍為 1 到 999。 在應用程式中,問題可能被視為單個引發語句。 一旦為問題收集的快照數達到此值,在重置問題計數器(請參閱`problemCounterResetInterval`)並再次`thresholdForSnapshotting`達到 限制之前,將不再為該問題收集快照。 |
| 問題計數器重置間隔 | 24:00:00 | 在一分鐘到七天之間重置問題計數器的頻率。 達到此間隔時,所有問題計數將重置為零。 已達到執行快照閾值但尚未生成`maximumSnapshotsRequired`中的 快照數的現有問題保持活動狀態。 |
| 提供匿名遙測 | true | 確定是否向 Microsoft 發送匿名使用方式和錯誤遙測。 如果您聯繫 Microsoft 以幫助解決快照調試器的問題,則可能會使用此遙測數據。 它還用於監視使用模式。 |
| 重新連接間隔 | 00:15:00 | 我們重新連接到快照調試器終結點的頻率。 允許的範圍為一分鐘到一天。 |
| 影子複製資料夾 | null | 指定用於捲影複製二進位檔案的資料夾。 如果未設置,則按順序嘗試以下環境變數指定的資料夾:Fabric_Folder_App_Temp、本地應用數據、APPDATA、TEMP。 |
| 共用載入程式 | true | 如果為 true,則只有一個快照 Uploader 實例將收集和上載共享檢測密鑰的多個應用的快照。 如果設置為 false,則快照 Uploader 對於每個(進程名稱、檢測鍵)元組將是唯一的。 |
| 快照在低優先順序線程 | true | 確定是否處理低 IO 優先順序線程中的快照。 創建快照是一種快速操作,但是,為了將快照上載到快照調試器服務,必須首先將其寫入磁碟作為小型轉儲。 這發生在快照載入器過程中。 將此值設置為 true 使用低優先等級 IO 編寫小型轉儲,這不會與您的應用程式競爭資源。 將此值設置為 false 會加快小型轉儲的創建速度,但代價是減慢應用程式的速度。 |
| 快照PerDay限制 | 30 | 一天(24 小時)內允許的最大快照數。 此限制也在應用程式見解服務端強制執行。 每個應用程式的上載速率限制為每天 50 個(即每個檢測密鑰)。 此值有助於防止創建其他快照,這些快照最終將在上載期間被拒絕。 值為零將完全刪除限制,不建議這樣做。 |
| 快照Perten分鐘限制 | 1 | 10 分鐘內允許的最大快照數。 儘管此值沒有上限,但請謹慎增加此值,因為它可能會影響應用程式的性能。 創建快照速度很快,但創建快照的小型轉儲並將其上載到快照調試器服務是一個速度慢得多的操作,它將與應用程式爭奪資源(CPU和I/O)。 |
| 暫存資料夾 | null | 指定用於編寫小型轉儲和上傳器日誌檔的資料夾。 沒有設定,則使用 *%TEMP%\轉印*。 |
| 用於快照的閾值 | 1 | 應用程式見解在請求快照之前需要查看異常的次數。 |
| 上傳器代理 | null | 覆蓋快照上載程式進程中使用的代理伺服器。 如果您的應用程式通過代理伺服器連接到互聯網,則可能需要使用此設置。 快照收集器在應用程式的進程中運行,並將使用相同的代理設置。 但是,快照上傳程式作為單獨的進程運行,您可能需要手動配置代理伺服器。 如果此值為空,則快照收集器將嘗試通過檢查 System.Net.WebRequest.DefaultWebProxy 並將該值傳遞給快照上傳程式來自動檢測代理的位址。 如果此值不是空,則不使用自動檢測,此處指定的代理伺服器將在快照上傳器中使用。 |

## <a name="cosmosdb"></a>cosmosDb

可在 [Cosmos DB 觸發程序和繫結](functions-bindings-cosmosdb-v2-output.md#host-json)中找到組態設定。

## <a name="durabletask"></a>durableTask

可在 [Durable Functions 的繫結](durable/durable-functions-bindings.md#host-json)中找到組態設定。

## <a name="eventhub"></a>eventHub

可在[事件中樞觸發程序和繫結](functions-bindings-event-hubs-output.md#host-json)中找到組態設定。 

## <a name="extensions"></a>擴充功能

傳回包含所有繫結特定設定 (例如 [http](#http) 和 [eventHub](#eventhub)) 之物件的屬性。

## <a name="extensionbundle"></a>擴充包 

擴展包允許您向函數應用添加一組相容的函數綁定擴展。 要瞭解更多資訊,請參閱[本地開發擴展捆綁套件](functions-bindings-register.md#extension-bundles)。

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

工作主機所執行的函式清單。 空陣列表示已執行所有函式。 預定只能在[本機執行](functions-run-local.md)時使用。 在 Azure 的函數應用程式中，您應該改為依照[如何停用 Azure Functions 中的函式](disable-function.md)中的步驟來停用特定函式，而不是使用此設定。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

指出所有函式的逾時持續期間。 它遵循時間跨度字串格式。 在無伺服器的使用情況方案中，有效範圍是從 1 秒到 10 分鐘，而預設值是 5 分鐘。  

在高級計劃中,有效範圍為 1 秒到 60 分鐘,預設值為 30 分鐘。

在專用(應用服務)計劃中,沒有總體限制,預設值為 30 分鐘。 的值`-1`表示無界執行,但建議保留固定的上限。

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
|logLevel|n/a|為應用程式中的函式定義記錄類別篩選的物件。 版本 2.x 和更高版本遵循日誌類別篩選ASP.NET核心佈局。 此設定允許您篩選特定函數的日誌記錄。 如需詳細資訊，請參閱 ASP.NET Core 文件中的[記錄篩選](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)。 |
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

## <a name="manageddependency"></a>託管依賴性

託管依賴項是當前僅受基於 PowerShell 的功能支援的一項功能。 它使依賴項能夠由服務自動管理。 當屬性`enabled`設定`true`為`requirements.psd1`時, 將處理檔。 發佈任何次要版本時,將更新依賴項。 有關詳細資訊,請參閱 PowerShell 一文中的[託管依賴項](functions-reference-powershell.md#dependency-management)。

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

此值指示 host.json 的架構版本。 從 v2 執行時或更高版本的函數應用需要版本字`"version": "2.0"`串 。 v2 和 v3 之間沒有 host.json 架構更改。

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
