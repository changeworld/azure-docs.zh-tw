---
title: 'Azure 監視器 (preview 中的資料收集規則) '
description: 資料收集規則的總覽 (Dcr) 在 Azure 監視器中，包括其內容和結構，以及您可以如何建立及使用它們。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: af5324373359cea643a3e31b6bb94e614ddb7e36
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88082976"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Azure 監視器 (preview 中的資料收集規則) 
資料收集規則 (DCR) 定義傳入 Azure 監視器的資料，以及指定應該傳送或儲存資料的位置。 本文概述資料收集規則，包括其內容和結構，以及您可以如何建立及使用它們。

## <a name="input-sources"></a>輸入來源
資料收集規則目前支援下列輸入來源：

- 具有 Azure 監視器代理程式的 Azure 虛擬機器。 請參閱[設定 Azure 監視器代理程式 (預覽) 的資料收集](data-collection-rule-azure-monitor-agent.md)。



## <a name="components-of-a-data-collection-rule"></a>資料集合規則的元件
資料集合規則包含下列元件。

| 元件 | 描述 |
|:---|:---|
| 資料來源 | 監視資料的唯一來源，其本身的格式和方法會公開其資料。 資料來源的範例包括 Windows 事件記錄檔、效能計數器和 syslog。 每個資料來源都會符合特定的資料來源類型，如下所述。 |
| 串流 | 唯一的控制碼，描述將轉換並架構化為一種類型的一組資料來源。 每個資料來源都需要一個或多個資料流程，而一個資料流程可能會由多個資料來源使用。 資料流程中的所有資料來源都會共用一個通用的架構。 例如，當您想要將特定資料來源傳送至相同 Log Analytics 工作區中的多個資料表時，請使用多個資料流程。 |
| Destinations | 應傳送資料的目的地集合。 範例包括 Log Analytics 工作區、Azure 監視器計量和 Azure 事件中樞。 | 
| 資料流程 | 應該將哪些資料流程傳送到哪些目的地的定義。 | 

下圖顯示資料收集規則及其關聯性的元件

[![DCR 的圖表](media/data-collection-rule/data-collection-rule-components.png)](media/data-collection-rule/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>資料來源類型
每個資料來源都有一個資料來源類型。 每個類型都會定義一組必須為每個資料來源指定的唯一屬性。 目前可用的資料來源類型如下表所示。

| 資料來源類型 | 描述 | 
|:---|:---|
| 擴充功能 | 以 VM 擴充功能為基礎的資料來源 |
| performanceCounters | Windows 和 Linux 的效能計數器 |
| syslog | Linux 虛擬機器上的 Syslog 事件 |
| windowsEventLogs | Windows 事件記錄檔 |


## <a name="limits"></a>限制
下表列出目前適用于每個資料集合規則的限制。

| 限制 | 值 |
|:---|:---|
| 資料來源數目上限 | 10 |
| 效能中的最大計數器規範 | 100 |
| SysLog 中的最大設施名稱 | 20 |
| EventLog 中的最大 XPath 查詢 | 100 |
| 資料流程上限 | 10 |
| 資料流程上限 | 10 |
| 延伸模組的最大數目 | 10 |
| 延伸模組設定的大小上限 | 32 Kb |
| Log Analytics 工作區上限 | 10 |


## <a name="create-a-dcr"></a>建立 DCR
目前有兩個可用來建立 DCR 的方法：

- [使用 [Azure 入口網站](data-collection-rule-azure-monitor-agent.md)建立資料集合規則，並將它與一或多個虛擬機器相關聯。
- 直接編輯 JSON 中的資料收集規則，並使用 REST API 進行提交。

## <a name="sample-data-collection-rule"></a>範例資料收集規則
以下的範例資料集合規則適用于使用 Azure 管理代理程式的虛擬機器，並具有下列詳細資料：

- 效能資料
  - 每隔15秒會收集特定處理器、記憶體、邏輯磁片和實體磁片計數器，並每分鐘上傳一次。
  - 每隔30秒會收集特定的進程計數器，並每隔5分鐘上傳一次。
- Windows 事件
  - 收集 Windows 安全性事件，並每分鐘上傳一次。
  - 收集 Windows 應用程式和系統事件，並每隔5分鐘上傳一次。
- syslog
  - 從 cron 設施收集 Debug、Critical 和緊急事件。
  - 從 syslog 設備收集警示、重大和緊急事件。
- Destinations
  - 將所有資料傳送至名為 centralTeamWorkspace 的 Log Analytics 工作區。
  - 將效能資料傳送至目前訂用帳戶中 Azure 監視器計量。

```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "sylogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-WindowsEvent"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>後續步驟

- 使用 Azure 監視器代理程式，從虛擬機器[建立資料集合規則](data-collection-rule-azure-monitor-agent.md)和與其關聯。