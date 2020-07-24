---
title: Azure 應用程式 Insights 代理程式疑難排解和已知問題 |Microsoft Docs
description: Application Insights 代理程式和疑難排解範例的已知問題。 在不重新部署網站的情況下監視網站效能。 適用于內部部署、Vm 或 Azure 上裝載的 ASP.NET web 應用程式。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 07b5ae99b98c6c6bbf4dc903cd1b583eb655a825
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87041410"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>疑難排解 Application Insights 代理程式（先前稱為狀態監視器 v2）

當您啟用監視時，可能會遇到阻礙資料收集的問題。
本文列出所有已知問題，並提供疑難排解範例。
如果您遇到此處未列出的問題，可以在[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)上與我們聯繫。

## <a name="known-issues"></a>已知問題

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>應用程式的 bin 目錄中有衝突的 Dll

如果其中有任何 Dll 出現在 bin 目錄中，監視可能會失敗：

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

其中一些 Dll 會包含在 Visual Studio 預設應用程式範本中，即使您的應用程式未使用它們也是一樣。
您可以使用疑難排解工具來查看徵兆行為：

- PerfView
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset 和應用程式負載（不含遙測）。 使用 Sysinternals （Handle.exe 和 ListDLLs.exe）進行調查：
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>與 IIS 共用設定衝突

如果您有一部 web 伺服器的叢集，您可能會使用[共用](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)設定。
無法將 HttpModule 插入此共用設定。
在每部 web 伺服器上執行 Enable 命令，將 DLL 安裝到每部伺服器的 GAC 中。

執行 [啟用] 命令之後，請完成下列步驟：
1. 移至共用設定目錄，並尋找 applicationHost.config 檔案。
2. 將下面這一行新增至設定的 [模組] 區段：
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS 嵌套應用程式

我們不會在1.0 版的 IIS 中檢測嵌套應用程式。
我們會在此追蹤[此問題。](https://github.com/microsoft/ApplicationInsights-Home/issues/369)

### <a name="advanced-sdk-configuration-isnt-available"></a>無法使用 Advanced SDK 設定。

SDK 設定不會公開給版本1.0 中的使用者。
我們會在此追蹤[此問題。](https://github.com/microsoft/ApplicationInsights-Home/issues/375)

    
    
## <a name="troubleshooting"></a>疑難排解
    
### <a name="troubleshooting-powershell"></a>疑難排解 PowerShell

#### <a name="determine-which-modules-are-available"></a>判斷哪些模組可供使用
您可以使用 `Get-Module -ListAvailable` 命令來判斷已安裝的模組。

#### <a name="import-a-module-into-the-current-session"></a>將模組匯入目前的會話
如果模組尚未載入 PowerShell 會話中，您可以使用命令手動載入 `Import-Module <path to psd1>` 。


### <a name="troubleshooting-the-application-insights-agent-module"></a>針對 Application Insights 代理程式模組進行疑難排解

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>列出 Application Insights 代理程式模組中可用的命令
執行命令 `Get-Command -Module Az.ApplicationMonitor` 以取得可用的命令：

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>判斷 Application Insights 代理程式模組的目前版本
執行 `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` 命令以顯示模組的下列相關資訊：
   - PowerShell 模組版本
   - Application Insights SDK 版本
   - PowerShell 模組的檔案路徑
    
如需如何使用此 Cmdlet 的詳細說明，請參閱[API 參考](status-monitor-v2-api-reference.md)。


### <a name="troubleshooting-running-processes"></a>執行中進程的疑難排解

您可以檢查檢測電腦上的處理常式，以判斷是否已載入所有 Dll。
如果監視運作正常，則至少應載入12個 Dll。

使用 `Get-ApplicationInsightsMonitoringStatus -InspectProcess` 命令來檢查 dll。

如需如何使用此 Cmdlet 的詳細說明，請參閱[API 參考](status-monitor-v2-api-reference.md)。


### <a name="collect-etw-logs-by-using-perfview"></a>使用 PerfView 收集 ETW 記錄檔

#### <a name="setup"></a>安裝程式

1. 從[GitHub](https://github.com/Microsoft/perfview/releases)下載 PerfView.exe 和 PerfView64.exe。
2. 啟動 PerfView64.exe。
3. 展開 [進階選項]。
4. 清除這些核取方塊：
    - **位址**
    - **合併式**
    - **.NET 符號集合**
5. 設定這些**額外的提供者**：`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>收集記錄檔

1. 在具有系統管理員許可權的命令主控台中，執行 `iisreset /stop` 命令以關閉 IIS 和所有 web 應用程式。
2. 在 PerfView 中，選取 [**開始收集**]。
3. 在具有系統管理員許可權的命令主控台中，執行 `iisreset /start` 命令以啟動 IIS。
4. 嘗試流覽至您的應用程式。
5. 載入應用程式之後，請返回 PerfView，然後選取 [**停止收集**]。



## <a name="next-steps"></a>接下來的步驟

- 請參閱[API 參考](status-monitor-v2-overview.md#powershell-api-reference)，以瞭解您可能遺漏的參數。
- 如果您遇到此處未列出的問題，可以在[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)上與我們聯繫。
