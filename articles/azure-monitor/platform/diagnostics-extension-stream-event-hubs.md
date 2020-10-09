---
title: 將資料從 Windows Azure 診斷延伸模組傳送至 Azure 事件中樞
description: 在 Azure 監視器中設定診斷延伸模組，以將資料傳送至 Azure 事件中樞，讓您可以將資料轉送至 Azure 外部的位置。
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 979535b1f9a237f6975908178fb1e5ed819181b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82233460"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>將資料從 Windows Azure 診斷延伸模組傳送至 Azure 事件中樞
Azure 診斷擴充功能是 Azure 監視器中的代理程式，可收集來自客體作業系統的監視資料，以及 Azure 虛擬機器和其他計算資源的工作負載。 本文說明如何將資料從 Windows Azure 診斷延伸模組 (WAD) 傳送至 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/) ，以便您可以轉寄至 Azure 以外的位置。

## <a name="supported-data"></a>支援的資料

從可傳送至事件中樞之客體作業系統收集到的資料包含下列各項。 WAD 所收集的其他資料來源（包括 IIS 記錄和損毀傾印）無法傳送至事件中樞。

* Windows 事件追蹤 (ETW) 事件
* 效能計數器
* Windows 事件記錄檔，包括 Windows 事件記錄檔中的應用程式記錄檔
* Azure 診斷基礎結構記錄

## <a name="prerequisites"></a>Prerequisites

* Windows 診斷延伸模組1.6 或更新版本。 如需支援的資源，請參閱 [Azure 診斷延伸模組設定架構版本和](diagnostics-extension-versions.md) 歷程記錄，以取得版本歷程記錄和 [Azure 診斷延伸模組總覽](diagnostics-extension-overview.md) 。
* 必須一律布建事件中樞命名空間。 如需詳細資訊，請參閱 [開始使用事件中樞](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) 。


## <a name="configuration-schema"></a>設定架構
請參閱 [安裝和設定 Windows Azure 診斷延伸模組 (WAD) ](diagnostics-extension-windows-install.md) ，以取得啟用和設定診斷擴充功能的不同選項，以及設定架構參考 [Azure 診斷設定架構](diagnostics-extension-schema-windows.md) 。 本文的其餘部分將說明如何使用此設定將資料傳送至事件中樞。 

Azure 診斷一律會將記錄和計量傳送至 Azure 儲存體帳戶。 您可以設定一或多個將資料傳送到其他位置的 *資料接收* 。 每個接收都是在公用設定的 [SinksConfig](diagnostics-extension-schema-windows.md#sinksconfig-element) 專案中，以私用設定中的機密資訊定義。 事件中樞的這項設定會使用下表中的值。

| 屬性 | 說明 |
|:---|:---|
| 名稱 | 接收器的描述性名稱。 用於設定中，以指定要傳送至接收的資料來源。 |
| Url  | 事件中樞的 Url，格式為 \<event-hubs-namespace\> servicebus.windows.net/ \<event-hub-name\> 。          |
| SharedAccessKeyName | 至少有 **傳送** 授權單位之事件中樞的共用存取原則名稱。 |
| SharedAccessKey     | 來自事件中樞共用存取原則的主要或次要金鑰。 |

範例公用和私用設定如下所示。 這是最基本的設定，其中包含單一效能計數器和事件記錄檔，以說明如何設定和使用事件中樞資料接收。 如需更複雜的範例，請參閱 [Azure 診斷設定架構](diagnostics-extension-schema-windows.md) 。

### <a name="public-configuration"></a>公用組態

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
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
            }
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
若要將資料傳送至資料接收，請在資料來源的節點上指定 **接收** 屬性。 當您放置 **接收** 屬性時，會決定指派的範圍。 在下列範例中，會將 **接收器** 屬性定義為 **PerformanceCounters** 節點，這會造成所有子效能計數器傳送至事件中樞。

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


在下列範例中， **接收器** 屬性會直接套用至三個計數器，而這些計數器將只會將這些效能計數器傳送至事件中樞。 

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

## <a name="validating-configuration"></a>正在驗證設定
您可以使用各種方法來驗證要將資料傳送至事件中樞。 單純的簡單方法是使用事件中樞捕獲，如 [Azure Blob 儲存體或 Azure Data Lake Storage 的透過 Azure 事件中樞的 [捕捉事件](../../event-hubs/event-hubs-capture-overview.md)] 中所述。 


## <a name="troubleshoot-event-hubs-sinks"></a>針對事件中樞接收進行疑難排解

- 查看 Azure 儲存體資料表 **WADDiagnosticInfrastructureLogsTable** ，其中包含 Azure 診斷本身的記錄和錯誤。 其中一個選項是使用類似 [Azure 儲存體總管](https://www.storageexplorer.com) 的工具連接到此儲存體帳戶、檢視此資料表，並且新增過去 24 小時內時間戳記的查詢。 您可以使用此工具來匯出 .csv 檔案，並在 Microsoft Excel 之類的應用程式中開啟。 Excel 能輕鬆地搜尋電話卡字串 (如 **EventHubs**)，以便查看系統回報了哪些錯誤。  

- 檢查已成功佈建您的事件中樞。 設定的 [ **privateconfig.json** ] 區段中的所有連接資訊必須符合您在入口網站中所見的資源值。 請確定您已在入口網站中的範例)  (*SendRule* 定義 SAS 原則，並授與 *傳送* 許可權。  

## <a name="next-steps"></a>接下來的步驟

* [事件中心概觀](../../event-hubs/event-hubs-about.md)
* [建立事件中樞](../../event-hubs/event-hubs-create.md)
* [事件中樞常見問題集](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



