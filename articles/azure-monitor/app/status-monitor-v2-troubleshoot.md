---
title: Azure 應用程式見解代理故障排除和已知問題 |微軟文件
description: 已知問題的應用程式見解代理和故障排除示例。 在不重新部署網站的情況下監控網站性能。 與本地、VM 或 Azure 上託管的ASP.NET Web 應用配合使用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 9bb22b12a7b3e972ff144bd121db4288801e2488
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732942"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>容錯排除應用程式見解代理伺服器(以前稱為狀態監視器 v2)

啟用監視時,可能會遇到阻止數據收集的問題。
本文列出了所有已知問題,並提供了故障排除示例。
如果您遇到此處未列出的問題,您可以在[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)上與我們聯繫。

## <a name="known-issues"></a>已知問題

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>套用的 bin 目錄中的衝突 DLL

如果 bin 目錄中存在這些 DLL 中的任何一個,則監視可能會失敗:

- 微軟.應用程式洞察.dll
- 微軟.AspNet.遙測相關.dll
- 系統.診斷.診斷來源.dll

其中一些 DLL 包含在 Visual Studio 預設應用範本中,即使您的應用不使用它們也是如此。
您可以使用故障排除工具檢視症狀行為:

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset 和應用載入(無遙測)。 使用系統內部調查(Handle.exe 和 ListDlL.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>與 IIS 分享設定衝突

如果您有一組 Web 伺服器,則可能是在使用[共享設定](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)。
無法將 HttpModule 注入此共享配置。
在每個 Web 伺服器上執行啟用命令,以便將 DLL 安裝到每個伺服器的 GAC 中。

執行啟用命令後,完成以下步驟:
1. 跳到共享設定目錄並找到應用程式Host.config 檔案。
2. 將此行新增到設定的模組部份:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS 嵌入應用程式

我們不會在版本 1.0 中檢測 IIS 中的嵌套應用程式。
我們正在[這裏](https://github.com/microsoft/ApplicationInsights-Home/issues/369)跟蹤這個問題。

### <a name="advanced-sdk-configuration-isnt-available"></a>高級 SDK 設定不可用。

SDK 配置不會向版本 1.0 中的最終用戶公開。
我們正在[這裏](https://github.com/microsoft/ApplicationInsights-Home/issues/375)跟蹤這個問題。

    
    
## <a name="troubleshooting"></a>疑難排解
    
### <a name="troubleshooting-powershell"></a>容毀電源外殼

#### <a name="determine-which-modules-are-available"></a>確定哪些模組可用
可以使用`Get-Module -ListAvailable`命令確定安裝了哪些模組。

#### <a name="import-a-module-into-the-current-session"></a>將模組匯入目前工作階段
如果模組尚未載入 PowerShell 工作階段中,則可以使用`Import-Module <path to psd1>`命令 手動載入該模組。


### <a name="troubleshooting-the-application-insights-agent-module"></a>對應用程式見解代理模組進行故障排除

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>列出應用程式來解代理模組中可用的指令
執行指令`Get-Command -Module Az.ApplicationMonitor`以取得可用指令:

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

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>決定應用程式見解代理模組的目前版本
執行此`Get-ApplicationInsightsMonitoringStatus -PowerShellModule`指令以顯示有關模組的以下資訊:
   - PowerShell 模組版本
   - 應用程式洞察 SDK 版本
   - PowerShell 模組的檔案路徑
    
有關如何使用此 cmdlet 的詳細資訊,請查看[API 參考](status-monitor-v2-api-reference.md)。


### <a name="troubleshooting-running-processes"></a>容錯排除正在執行的程序

您可以檢查已檢測電腦上的進程,以確定是否載入了所有 DLL。
如果監視工作,應至少載入 12 個 DLL。

使用`Get-ApplicationInsightsMonitoringStatus -InspectProcess`命令檢查 DLL。

有關如何使用此 cmdlet 的詳細資訊,請查看[API 參考](status-monitor-v2-api-reference.md)。


### <a name="collect-etw-logs-by-using-perfview"></a>使用 PerfView 收集 ETW 紀錄

#### <a name="setup"></a>安裝程式

1. 從[GitHub](https://github.com/Microsoft/perfview/releases)下載 PerfView.exe 和 PerfView64.exe 。
2. 啟動 PerfView64.exe。
3. 展開 [進階選項]  。
4. 清除這些選擇的盒:
    - **Zip**
    - **合併**
    - **.NET 符號集合**
5. 設定這些**額外提供者**:`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>收集記錄

1. 在具有管理權限的命令控制台中`iisreset /stop`, 執行此指令以關閉 IIS 和所有 Web 應用。
2. 在 PerfView 中,選擇 **「開始集合**」。。
3. 在具有管理權限的命令控制台中`iisreset /start`, 執行此指令以啟動 IIS。
4. 嘗試流覽到你的應用。
5. 載入應用後,傳回到 PerfView 並選擇 **「停止收集**」。



## <a name="next-steps"></a>後續步驟

- 檢視[API 引用](status-monitor-v2-overview.md#powershell-api-reference)以瞭解您可能錯過的參數。
- 如果您遇到此處未列出的問題,您可以在[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)上與我們聯繫。
