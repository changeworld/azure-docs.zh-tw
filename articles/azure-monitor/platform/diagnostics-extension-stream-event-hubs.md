---
title: 將資料從 Windows Azure 診斷擴展發送到 Azure 事件中心
description: 在 Azure 監視器中配置診斷擴展以將資料發送到 Azure 事件中心，以便可以將資料轉發到 Azure 外部的位置。
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 5e5034e99d37d3681192c2ad066f28acd1c4aeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672526"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>將資料從 Windows Azure 診斷擴展發送到 Azure 事件中心
Azure 診斷擴展是 Azure 監視器中的代理，用於從 Azure 虛擬機器和其他計算資源的客體作業系統和工作負荷收集監視資料。 本文介紹如何將資料從 Windows Azure 診斷擴展 （WAD） 發送到[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，以便可以轉發到 Azure 外部的位置。

## <a name="supported-data"></a>支援的資料

從客體作業系統收集的可發送到事件中心的資料包括以下內容。 WAD 收集的其他資料來源（包括 IIS 日誌和崩潰轉儲）無法發送到事件中心。

* Windows 事件追蹤 (ETW) 事件
* 效能計數器
* Windows 事件日誌，包括 Windows 事件日誌中的應用程式日誌
* Azure 診斷基礎結構記錄

## <a name="prerequisites"></a>Prerequisites

* Windows 診斷擴展 1.6 或更高版本。 有關受支援資源的版本歷程記錄和[Azure 診斷擴展概述](diagnostics-extension-overview.md)，請參閱[Azure 診斷擴展配置架構版本和歷史記錄](diagnostics-extension-versions.md)。
* 必須始終預配事件中心命名空間。 有關詳細資訊[，請參閱從事件中心開始](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)。


## <a name="configuration-schema"></a>配置架構
有關啟用和配置診斷擴展和[Azure 診斷配置架構](diagnostics-extension-schema-windows.md)的不同選項，請參閱[安裝和配置 Windows Azure 診斷擴展 （WAD），](diagnostics-extension-windows-install.md)以便參考配置架構。 本文的其餘部分將介紹如何使用此配置將資料發送到事件中心。 

Azure 診斷始終向 Azure 存儲帳戶發送日誌和指標。 您可以配置一個或多個將資料發送到其他位置*的資料接收器*。 每個接收器都在公共配置的[SinksConfig 元素](diagnostics-extension-schema-windows.md#sinksconfig-element)中定義，在私有配置中包含敏感資訊。 事件中心此配置使用下表中的值。

| 屬性 | 描述 |
|:---|:---|
| 名稱 | 接收器的描述性名稱。 在配置中用於指定要發送到接收器的資料來源。 |
| Url  | 表單\<事件中心-命名空間\>中的事件中心 url servicebus.windows.net/\<事件中心名稱\>。          |
| 共用訪問金鑰名稱 | 具有至少 **"發送**"許可權的事件中心的共用訪問策略的名稱。 |
| SharedAccessKey     | 事件中心共用訪問策略的主鍵或輔助鍵。 |

示例公共配置和私有配置如下所示。 這是一個最小配置，具有單個效能計數器和事件日誌，用於說明如何配置和使用事件中心資料接收器。 有關更複雜的示例，請參閱[Azure 診斷配置架構](diagnostics-extension-schema-windows.md)。

### <a name="public-configuration"></a>公用組態

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120
        },
        "PerformanceCounters": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
                "DataSource": [
                {
                    "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                }
            ]
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>私人組態

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>設定選項
要將資料發送到資料接收器，請在資料來源的節點上指定**接收器**屬性。 放置**接收器**屬性的位置決定了分配的範圍。 在下面的示例中，**接收器**屬性被定義為**效能計數器**節點，這將導致將所有子效能計數器發送到事件中心。

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


在下面的示例中，**接收器**屬性直接應用於三個計數器，這將導致僅將這些效能計數器發送到事件中心。 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>驗證配置
可以使用多種方法驗證資料是否已發送到事件中心。 ne 簡單方法是使用事件中心捕獲，如在[Azure Blob 存儲或 Azure 資料湖存儲中通過 Azure 事件中心捕獲事件](../../event-hubs/event-hubs-capture-overview.md)中所述。 


## <a name="troubleshoot-event-hubs-sinks"></a>針對事件中樞接收進行疑難排解

- 查看 Azure 存儲表**WAD 診斷基礎結構日誌表**，其中包含 Azure 診斷本身的日誌和錯誤。 其中一個選項是使用類似 [Azure 儲存體總管](https://www.storageexplorer.com) 的工具連接到此儲存體帳戶、檢視此資料表，並且新增過去 24 小時內時間戳記的查詢。 您可以使用此工具來匯出 .csv 檔案，並在 Microsoft Excel 之類的應用程式中開啟。 Excel 能輕鬆地搜尋電話卡字串 (如 **EventHubs**)，以便查看系統回報了哪些錯誤。  

- 檢查已成功佈建您的事件中樞。 配置的 Private **Config**部分中的所有連接資訊必須及閘戶中所看到的資源值匹配。 請確保門戶中定義了 SAS 策略（示例中的*SendRule），* 並且已授予 *"發送*"許可權。  

## <a name="next-steps"></a>後續步驟

* [事件中心概觀](../../event-hubs/event-hubs-about.md)
* [建立事件中樞](../../event-hubs/event-hubs-create.md)
* [事件中樞常見問題集](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



