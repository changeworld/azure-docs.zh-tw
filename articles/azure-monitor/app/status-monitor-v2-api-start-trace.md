---
title: Azure 應用程式見解代理 API 引用
description: 應用程式見解代理 API 引用。 啟動-跟蹤。 從狀態監視器和應用程式見解 SDK 收集 ETW 日誌。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671217"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>應用程式見解代理 API：啟動應用程式見解監視跟蹤

本文介紹了一個 Cmdlet，它是[Az.應用程式監視器 PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述

從無代碼附加運行時收集[ETW 事件](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal)。 這個Cmdlet是運行[PerfView](https://github.com/microsoft/perfview)的替代方案。

收集的事件將即時列印到主控台並保存到 ETL 檔中。 [PerfView](https://github.com/microsoft/perfview)可以打開輸出 ETL 檔以進行進一步調查。

此 Cmdlet 將運行，直到它達到超時持續時間（預設 5 分鐘）或手動停止`Ctrl + C`（）。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有管理員許可權的 PowerShell 會話。

## <a name="examples"></a>範例

### <a name="how-to-collect-events"></a>如何收集事件

通常，我們會要求您收集事件，以調查為什麼您的應用程式未被檢測。

無代碼附加運行時將在 IIS 啟動和應用程式啟動時發出 ETW 事件。

要收集這些事件，
1. 在具有管理許可權的 cmd 主控台中`iisreset /stop`，執行"關閉 IIS"和所有 Web 應用。
2. 執行此 Cmdlet
3. 在具有管理許可權的 cmd 主控台中`iisreset /start`，執行"啟動 IIS"。
4. 嘗試流覽到你的應用。
5. 應用完成載入後，可以手動停止它 （`Ctrl + C`） 或等待超時。

### <a name="what-events-to-collect"></a>要收集哪些事件

收集事件時有三個選項：
1. 使用該開關`-CollectSdkEvents`收集從應用程式見解 SDK 發出的事件。
2. 使用該開關`-CollectRedfieldEvents`收集狀態監視器和紅場運行時發出的事件。 這些日誌在診斷 IIS 和應用程式啟動時非常有用。
3. 使用這兩個開關收集這兩種事件種類。
4. 預設情況下，如果未指定任何開關，將收集兩種事件種類。


## <a name="parameters"></a>參數

### <a name="-maxdurationinminutes"></a>-最大持續時間
**選。** 使用此參數可以設置此腳本應收集事件的時間。 預設值為 5 分鐘。

### <a name="-logdirectory"></a>-日誌目錄
**選。** 使用此開關可以設置 ETL 檔的輸出目錄。 預設情況下，此檔將在 PowerShell 模組目錄中創建。 完整路徑將在腳本執行期間顯示。


### <a name="-collectsdkevents"></a>-收集Sdk事件
**選。** 使用此開關收集應用程式見解 SDK 事件。

### <a name="-collectredfieldevents"></a>-收集雷德菲爾德活動
**選。** 使用此開關可以從狀態監視器和雷德菲爾德運行時收集事件。

### <a name="-verbose"></a>-Verbose
**通用參數。** 使用此開關可以輸出詳細日誌。



## <a name="output"></a>輸出


### <a name="example-of-application-startup-logs"></a>應用程式開機記錄示例
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>後續步驟

其他故障排除：

- 在此處查看其他故障排除步驟：https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- 查看[API 參考](status-monitor-v2-overview.md#powershell-api-reference)以瞭解您可能錯過的參數。
- 如果您需要其他説明，您可以在[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)上與我們聯繫。



 使用應用程式見解代理執行更多操作：
 - 使用我們的指南對應用程式見解代理[進行故障排除](status-monitor-v2-troubleshoot.md)。
 - [獲取配置](status-monitor-v2-api-get-config.md)以確認您的設置記錄正確。
 - [獲取狀態](status-monitor-v2-api-get-status.md)以檢查監視。
