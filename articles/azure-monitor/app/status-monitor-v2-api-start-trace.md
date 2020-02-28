---
title: Azure 應用程式 Insights 代理程式 API 參考
description: Application Insights 代理程式 API 參考。 開始追蹤。 從狀態監視器和 Application Insights SDK 收集 ETW 記錄檔。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671217"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Application Insights 代理程式 API：開始-ApplicationInsightsMonitoringTrace

本文說明的 Cmdlet 是[ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述

從無程式碼附加執行時間收集[ETW 事件](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal)。 此 Cmdlet 是執行[PerfView](https://github.com/microsoft/perfview)的替代方法。

收集的事件會即時列印到主控台，並儲存至 ETL 檔案。 [PerfView](https://github.com/microsoft/perfview)可以開啟輸出 ETL 檔案以供進一步調查。

此 Cmdlet 將會執行，直到達到超時持續時間（預設為5分鐘），或以手動方式停止（`Ctrl + C`）。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有系統管理員許可權的 PowerShell 會話。

## <a name="examples"></a>範例

### <a name="how-to-collect-events"></a>如何收集事件

一般來說，我們會要求您收集事件，以調查您的應用程式未被檢測的原因。

無程式碼附加執行時間會在 IIS 啟動時以及應用程式啟動時發出 ETW 事件。

若要收集這些事件：
1. 在具有系統管理員許可權的 cmd 主控台中，執行 `iisreset /stop` 以關閉 IIS 和所有 web 應用程式。
2. 執行此 Cmdlet
3. 在具有系統管理員許可權的 cmd 主控台中，執行 `iisreset /start` 以啟動 IIS。
4. 嘗試流覽至您的應用程式。
5. 在您的應用程式完成載入之後，您可以手動將它停止（`Ctrl + C`）或等候超時。

### <a name="what-events-to-collect"></a>要收集的事件

收集事件時，您有三個選項：
1. 使用參數 `-CollectSdkEvents` 來收集從 Application Insights SDK 發出的事件。
2. 使用參數 `-CollectRedfieldEvents` 來收集狀態監視器和 Redfield 執行時間發出的事件。 當診斷 IIS 和應用程式啟動時，這些記錄會很有説明。
3. 同時使用這兩個參數來收集這兩個事件種類。
4. 根據預設，如果未指定任何參數，則會收集這兩個事件種類。


## <a name="parameters"></a>參數

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**選擇性。** 使用此參數來設定此腳本應該收集事件的時間長度。 預設值為 5 分鐘。

### <a name="-logdirectory"></a>-LogDirectory
**選擇性。** 使用此參數來設定 ETL 檔案的輸出目錄。 根據預設，會在 PowerShell 模組目錄中建立此檔案。 腳本執行期間會顯示完整路徑。


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**選擇性。** 使用此參數來收集 Application Insights SDK 事件。

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**選擇性。** 使用此參數來收集來自狀態監視器和 Redfield 執行時間的事件。

### <a name="-verbose"></a>-Verbose
**一般參數。** 使用此參數來輸出詳細記錄。



## <a name="output"></a>輸出


### <a name="example-of-application-startup-logs"></a>應用程式開機記錄的範例
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

其他疑難排解：

- 在此查看其他疑難排解步驟： https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- 請參閱[API 參考](status-monitor-v2-overview.md#powershell-api-reference)，以瞭解您可能遺漏的參數。
- 如果您需要其他協助，您可以在[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)上與我們聯繫。



 使用 Application Insights 代理程式執行更多工具：
 - 使用我們的指南來[疑難排解](status-monitor-v2-troubleshoot.md)Application Insights 代理程式。
 - [取得](status-monitor-v2-api-get-config.md)設定，以確認已正確記錄您的設定。
 - [取得狀態](status-monitor-v2-api-get-status.md)以檢查監視。
