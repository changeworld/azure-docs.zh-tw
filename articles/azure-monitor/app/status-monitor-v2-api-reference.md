---
title: Azure 應用程式見解 .Net 代理 API 引用
description: 應用程式見解代理 API 引用。 在不重新部署網站的情況下監控網站性能。 與本地、VM 或 Azure 上託管的ASP.NET Web 應用配合使用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 02762c4b3af735eb0b4c19aaf450b2b3a416a2be
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733670"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Azure 監視器應用程式見解代理 API 參考

本文介紹了一個 cmdlet,它是[Az.應用程式監視器 PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

> [!NOTE] 
> - 要開始,您需要一個檢測密鑰。 有關詳細資訊,請參閱[建立資源](create-new-resource.md#copy-the-instrumentation-key)。
> - 此 cmdlet 要求您查看並接受我們的許可證和隱私聲明。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理員許可權的 PowerShell 作業階段和提升的執行策略。 有關詳細資訊,請參閱[使用提升執行策略執行 PowerShell 作為管理員](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)。
> - 此 cmdlet 要求您查看並接受我們的許可證和隱私聲明。
> - 默認情況下,檢測引擎會增加額外的開銷並關閉。


## <a name="enable-instrumentationengine"></a>開啟偵測引擎

通過設置某些註冊表項啟用檢測引擎。
重新啟動 IIS 使更改生效。

儀錶引擎可以補充 .NET SDK 收集的數據。
它收集描述託管進程的執行的事件和消息。 這些事件與訊息包括相依項結果碼、HTTP 謂詞與[SQL 命令文字](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)。

在以下情況時啟用偵測引擎:
- 您已經啟用了啟用 cmdlet 的監視,但未啟用檢測引擎。
- 您已使用 .NET SDK 手動檢測應用,並希望收集其他遙測數據。

### <a name="examples"></a>範例

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>參數

#### <a name="-acceptlicense"></a>-接受許可
**選。** 使用此開關可以接受無頭安裝中的許可證和隱私聲明。

#### <a name="-verbose"></a>-Verbose
**通用參數。** 使用此開關可以輸出詳細日誌。

### <a name="output"></a>輸出


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>成功啟用偵測引擎的範例輸出

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>開啟應用程式洞察監控

啟用對目標電腦上的 IIS 應用的無程式碼附加監視。

此 cmdlet 將修改 IIS 應用程式Host.config 並設置一些註冊表項。
它還將創建一個應用程式 insights.ikey.config 檔案,該檔定義每個應用使用的檢測密鑰。
IIS 將在啟動時載入 RedfieldModule,這將在應用程式啟動時將應用程式見解 SDK 注入應用程式。
重新啟動 IIS,使更改生效。

啟用監視後,我們建議您使用[即時指標](live-stream.md)快速檢查應用是否向我們發送遙測數據。

### <a name="examples"></a>範例

#### <a name="example-with-a-single-instrumentation-key"></a>使用單一個偵測金鑰的範例
在此示例中,當前計算機上的所有應用都分配了單個檢測密鑰。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>使用偵測鍵對應的範例
在此範例中：
- `MachineFilter`使用通配符匹配`'.*'`當前計算機。
- `AppFilter='WebAppExclude'`提供`null`檢測金鑰。 無法檢測指定的應用。
- `AppFilter='WebAppOne'`為指定的應用分配唯一的檢測密鑰。
- `AppFilter='WebAppTwo'`為指定的應用分配唯一的檢測密鑰。
- 最後,`AppFilter``'.*'`還使用通配符匹配與早期規則不匹配的所有 Web 應用,並分配默認檢測密鑰。
- 添加空間以進行可讀性。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


### <a name="parameters"></a>參數

#### <a name="-instrumentationkey"></a>-儀器金鑰
**必填。** 使用此參數提供單個檢測密鑰,供目標計算機上的所有應用使用。

#### <a name="-instrumentationkeymap"></a>-儀器鍵映射
**必填。** 使用此參數可提供多個檢測鍵和每個應用使用的檢測鍵的映射。
您可以通過`MachineFilter`設定 為多台電腦創建單個安裝文稿。

> [!IMPORTANT]
> 應用將按規則提供的順序與規則匹配。 因此,應首先指定最具體的規則,最後指定最通用的規則。

##### <a name="schema"></a>結構描述
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **機器篩選器**是計算機或 VM 名稱的必填項。
    - '.*' 將符合所有
    - 電腦名稱"將僅匹配指定的確切名稱的電腦。
- **AppFilter**是 IIS 網站名稱的必填項。 您可以通過執行命令[get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)來取得伺服器上的網站清單。
    - '.*' 將符合所有
    - "站點名稱"將僅與指定的確切名稱的 IIS 網站匹配。
- **需要檢測密鑰**來啟用對與前兩個篩選器匹配的應用的監視。
    - 如果要定義規則以排除監視,請保留此值為空。


#### <a name="-enableinstrumentationengine"></a>-啟用偵測引擎
**選。** 使用此開關可使檢測引擎能夠收集有關執行託管進程期間發生的情況的事件和消息。 這些事件和消息包括依賴項結果代碼、HTTP 謂詞和 SQL 命令文本。

默認情況下,檢測引擎會增加開銷並關閉。

#### <a name="-acceptlicense"></a>-接受許可
**選。** 使用此開關可以接受無頭安裝中的許可證和隱私聲明。

#### <a name="-ignoresharedconfig"></a>-忽略共享設定
當您有一組 Web 伺服器時, 您可能正在使用[分享設定](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)。
無法將 HttpModule 注入此共享配置。
此腳本將失敗,因為消息顯示需要額外的安裝步驟。
使用此開關忽略此檢查並繼續安裝先決條件。 有關詳細資訊,請參閱[已知的"與 iis 衝突共用配置"](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Verbose
**通用參數。** 使用此開關可顯示詳細日誌。

#### <a name="-whatif"></a>-WhatIf 
**通用參數。** 使用此開關可以測試和驗證輸入參數,而無需實際啟用監視。

### <a name="output"></a>輸出

#### <a name="example-output-from-a-successful-enablement"></a>成功啟用的範例輸出

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="disable-instrumentationengine"></a>關閉偵測引擎

通過刪除某些註冊表項來禁用檢測引擎。
重新啟動 IIS 使更改生效。

### <a name="examples"></a>範例

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>參數 

#### <a name="-verbose"></a>-Verbose
**通用參數。** 使用此開關可以輸出詳細日誌。

### <a name="output"></a>輸出


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>成功關閉偵測引擎的範例輸出

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>關閉應用程式的程式見解監控

禁用目標計算機上的監視。
此 cmdlet 將刪除對 IIS 應用程式Host.config 的編輯,並刪除註冊表項。

### <a name="examples"></a>範例

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>參數 

#### <a name="-verbose"></a>-Verbose
**通用參數。** 使用此開關可顯示詳細日誌。

### <a name="output"></a>輸出


##### <a name="example-output-from-successfully-disabling-monitoring"></a>成功關閉監視的範例輸出

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="get-applicationinsightsmonitoringconfig"></a>取得應用程式洞察監控組態

獲取配置檔並將值列印到主控台。

### <a name="examples"></a>範例

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>參數

無需任何參數。

### <a name="output"></a>輸出


##### <a name="example-output-from-reading-the-config-file"></a>讀取設定檔的範例輸出

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>取得應用程式的監控狀態

此 cmdlet 提供有關狀態監視器的故障排除資訊。
使用此 cmdlet 可調查 PowerShell 模組的監視狀態和版本,並檢查正在運行的進程。
此 cmdlet 將報告版本資訊和有關監視所需的關鍵檔案的資訊。

### <a name="examples"></a>範例

#### <a name="example-application-status"></a>範例:應用程式狀態

運行該命令`Get-ApplicationInsightsMonitoringStatus`以顯示網站的監視狀態。

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

在此示例中;在此示例中,
- **計算機識別碼**是用於唯一識別伺服器的匿名 ID。 如果您建立支援請求,我們需要此 ID 才能查找伺服器的日誌。
- **預設網站**在 IIS 中停止
- **DemoWebApp111**已在 IIS 中啟動,但尚未收到任何請求。 此報告顯示沒有正在運行的進程(ProcessId:未找到)。
- **DemoWebApp222**正在運行並正在監視(儀器:true)。 根據使用者配置,為此網站匹配了檢測密鑰 xxxxxxxxx-xxxxxx-xxxxxx-xxxxxxxxx123。
- 使用應用程式見解 SDK 手動檢測**DemoWebApp333。** 狀態監視器檢測到 SDK,不會監視此網站。


#### <a name="example-powershell-module-information"></a>範例:PowerShell 模組資訊

執行指令`Get-ApplicationInsightsMonitoringStatus -PowerShellModule`以顯示有關目前的模組的資訊:

```powershell

PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

#### <a name="example-runtime-status"></a>範例:執行時狀態

您可以在檢測的電腦上檢查該過程,以查看是否載入了所有 DLL。 如果監視工作,應至少載入 12 個 DLL。

執行指令`Get-ApplicationInsightsMonitoringStatus -InspectProcess`:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

### <a name="parameters"></a>參數

#### <a name="no-parameters"></a>( 無參數 )

預設情況下,此 cmdlet 將報告 Web 應用程式的監視狀態。
使用此選項可檢查應用程式是否已成功檢測。
您還可以查看與網站匹配的檢測密鑰。


#### <a name="-powershellmodule"></a>-電源外殼模組
**選擇項**。 使用此開關可以報告監視所需的 DLL 的版本號和路徑。
如果需要識別任何 DLL 的版本(包括應用程式見解 SDK),請使用此選項。

#### <a name="-inspectprocess"></a>-檢測過程

**選擇項**。 使用此開關可以報告 IIS 是否正在運行。
它還將下載外部工具,以確定是否將必要的 DLL 載入到 IIS 執行時。


如果此過程由於任何原因失敗,您可以手動執行這些命令:
- iisreset.exe /狀態
- [手柄64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp |發現/I"儀器引擎AI。 應用洞察"
- [清單dlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp |發現/I "儀器引擎 AI 應用程式見解"


#### <a name="-force"></a>-Force

**選擇項**。 僅與檢查過程一起使用。 使用此開關可跳過下載其他工具之前顯示的使用者提示。


## <a name="set-applicationinsightsmonitoringconfig"></a>設定應用程式洞察監控設定

設定設定檔,而不執行完全重新安裝。
重新啟動 IIS,使更改生效。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理員權限的 PowerShell 作業階段。


### <a name="examples"></a>範例

#### <a name="example-with-a-single-instrumentation-key"></a>使用單一個偵測金鑰的範例
在此範例中,將為其當前電腦上的所有應用分配一個檢測密鑰。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>使用偵測鍵對應的範例
在此範例中：
- `MachineFilter`使用通配符匹配`'.*'`當前計算機。
- `AppFilter='WebAppExclude'`提供`null`檢測金鑰。 無法檢測指定的應用。
- `AppFilter='WebAppOne'`為指定的應用分配唯一的檢測密鑰。
- `AppFilter='WebAppTwo'`為指定的應用分配唯一的檢測密鑰。
- 最後,`AppFilter``'.*'`還使用通配符匹配與早期規則不匹配的所有 Web 應用,並分配默認檢測密鑰。
- 添加空間以進行可讀性。

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>參數

#### <a name="-instrumentationkey"></a>-儀器金鑰
**必填。** 使用此參數提供單個檢測密鑰,供目標計算機上的所有應用使用。

#### <a name="-instrumentationkeymap"></a>-儀器鍵映射
**必填。** 使用此參數可提供多個檢測鍵和每個應用使用的檢測鍵的映射。
您可以通過`MachineFilter`設定 為多台電腦創建單個安裝文稿。

> [!IMPORTANT]
> 應用將按規則提供的順序與規則匹配。 因此,應首先指定最具體的規則,最後指定最通用的規則。

##### <a name="schema"></a>結構描述
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **機器篩選器**是計算機或 VM 名稱的必填項。
    - '.*' 將符合所有
    - 電腦名稱將僅符合具有指定名稱的電腦。
- **AppFilter**是計算機或 VM 名稱的必填項。
    - '.*' 將符合所有
    - "應用程式名稱"將僅匹配具有指定名稱的 IIS 應用。
- **需要檢測密鑰**來啟用對與前兩個篩選器匹配的應用的監視。
    - 如果要定義規則以排除監視,請保留此值為空。


#### <a name="-verbose"></a>-Verbose
**通用參數。** 使用此開關可顯示詳細日誌。


### <a name="output"></a>輸出

默認情況下,沒有輸出。

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>使用 -儀器金鑰設定檔的詳細輸出範例

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>以 -儀器鍵映射設定設定檔的詳細輸出範例

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="start-applicationinsightsmonitoringtrace"></a>啟動-應用程式洞察監控追蹤

從無程式碼附加執行時收集[ETW 事件](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal)。 這個cmdlet是運行[PerfView](https://github.com/microsoft/perfview)的替代方案。

收集的事件將即時列印到主控台並儲存到 ETL 檔中。 [PerfView](https://github.com/microsoft/perfview)可以打開輸出 ETL 檔以進行進一步調查。

此 cmdlet 將運行,直到它達到超時持續時間(預設 5 分鐘)`Ctrl + C`或手動停止 ()。

### <a name="examples"></a>範例

#### <a name="how-to-collect-events"></a>如何收集事件

通常,我們會要求您收集事件,以調查為什麼您的應用程式未被檢測。

無程式碼附加執行時將在IIS啟動和應用程式啟動時發出ETW事件。

要收集這些事件,
1. 在具有管理許可權的cmd控制台中`iisreset /stop`,執行"關閉IIS"和所有Web應用。
2. 執行此 cmdlet
3. 在具有管理許可權的cmd控制台中`iisreset /start`,執行"啟動IIS"。
4. 嘗試流覽到你的應用。
5. 應用完成載入後,可以手動停止它`Ctrl + C`() 或等待超時。

#### <a name="what-events-to-collect"></a>要收集哪些事件

收集事件時有三個選項:
1. 使用該開關`-CollectSdkEvents`收集從應用程式見解 SDK 發出的事件。
2. 使用該開關`-CollectRedfieldEvents`收集狀態監視器和紅場運行時發出的事件。 這些日誌在診斷 IIS 和應用程式啟動時非常有用。
3. 使用這兩個開關收集這兩種事件類型。
4. 默認情況下,如果未指定任何開關,將收集兩種事件類型。


### <a name="parameters"></a>參數

#### <a name="-maxdurationinminutes"></a>-最大持續時間
**選。** 使用此參數可以設置此腳本應收集事件的時間。 預設值為 5 分鐘。

#### <a name="-logdirectory"></a>-日誌目錄
**選。** 使用此開關可以設置 ETL 檔的輸出目錄。 預設情況下,此檔將在 PowerShell 模組目錄中創建。 完整路徑將在腳本執行期間顯示。


#### <a name="-collectsdkevents"></a>-收集Sdk事件
**選。** 使用此開關收集應用程式見解 SDK 事件。

#### <a name="-collectredfieldevents"></a>-收集雷德菲爾德活動
**選。** 使用此開關可以從狀態監視器和雷德菲爾德運行時收集事件。

#### <a name="-verbose"></a>-Verbose
**通用參數。** 使用此開關可以輸出詳細日誌。



### <a name="output"></a>輸出


#### <a name="example-of-application-startup-logs"></a>應用程式啟動紀錄範例
```powershell
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

  檢視遙測：
 - [流覽指標](../../azure-monitor/app/metrics-explorer.md)以監視性能和使用方式。
- [搜索事件和日誌](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- 對更進階查詢使用[分析](../../azure-monitor/app/analytics.md)。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。
 
 新增更多遙測：
 - [建立 Web 測試](monitor-web-app-availability.md)，確定您的網站保持即時狀態。
- [添加 Web 客戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁代碼中的異常並啟用追蹤呼叫。
- [將應用程式見解 SDK 添加到代碼中](../../azure-monitor/app/asp-net.md),以便插入追蹤和紀錄調用。
 
 使用應用程式見解代理執行更多操作:
 - 使用我們的指南對應用程式見解代理[程式進行故障排除](status-monitor-v2-troubleshoot.md)。






