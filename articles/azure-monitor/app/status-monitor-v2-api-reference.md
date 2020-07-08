---
title: Azure 應用程式 Insights .Net 代理程式 API 參考
description: Application Insights 代理程式 API 參考。 在不重新部署網站的情況下監視網站效能。 適用于內部部署、Vm 或 Azure 上裝載的 ASP.NET web 應用程式。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 02762c4b3af735eb0b4c19aaf450b2b3a416a2be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81733670"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Azure 監視器 Application Insights 代理程式 API 參考

本文說明的 Cmdlet 是[ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

> [!NOTE] 
> - 若要開始使用，您需要有檢測金鑰。 如需詳細資訊，請參閱[建立資源](create-new-resource.md#copy-the-instrumentation-key)。
> - 此 Cmdlet 會要求您審查並接受我們的授權及隱私權聲明。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有系統管理員許可權的 PowerShell 會話，以及更高的執行原則。 如需詳細資訊，請參閱[使用提高許可權的執行原則以系統管理員身分執行 PowerShell](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)。
> - 此 Cmdlet 會要求您審查並接受我們的授權及隱私權聲明。
> - 檢測引擎會增加額外的負荷，並預設為關閉。


## <a name="enable-instrumentationengine"></a>啟用-InstrumentationEngine

藉由設定一些登錄機碼來啟用檢測引擎。
重新開機 IIS，變更才會生效。

檢測引擎可以補充 .NET Sdk 所收集的資料。
它會收集描述 managed 進程執行的事件和訊息。 這些事件和訊息包括相依性結果碼、HTTP 指令動詞和[SQL 命令文字](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)。

啟用檢測引擎：
- 您已經使用 Enable Cmdlet 啟用監視功能，但未啟用檢測引擎。
- 您已使用 .NET Sdk 手動檢測應用程式，並想要收集其他遙測。

### <a name="examples"></a>範例

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>參數

#### <a name="-acceptlicense"></a>-AcceptLicense
**選擇性.** 使用此參數可接受無周邊安裝中的授權和隱私權聲明。

#### <a name="-verbose"></a>-Verbose
**一般參數。** 使用此參數來輸出詳細記錄。

### <a name="output"></a>輸出


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>成功啟用檢測引擎的範例輸出

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>啟用-ApplicationInsightsMonitoring

在目的電腦上啟用 IIS 應用程式的無程式碼附加監視。

此 Cmdlet 會修改 IIS applicationHost.config 並設定一些登錄機碼。
它也會建立一個 applicationinsights.ikey.config 檔案，此檔案會定義每個應用程式所使用的檢測金鑰。
IIS 會在啟動時載入 RedfieldModule，這會在應用程式啟動時將 Application Insights SDK 插入應用程式中。
重新開機 IIS，讓您的變更生效。

啟用監視之後，建議您使用[即時計量](live-stream.md)來快速檢查您的應用程式是否正在傳送遙測。

### <a name="examples"></a>範例

#### <a name="example-with-a-single-instrumentation-key"></a>具有單一檢測金鑰的範例
在此範例中，會將單一檢測金鑰指派給目前電腦上的所有應用程式。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>含檢測金鑰組應的範例
在此範例中：
- `MachineFilter`使用萬用字元來符合目前的電腦 `'.*'` 。
- `AppFilter='WebAppExclude'`提供 `null` 檢測金鑰。 將不會檢測指定的應用程式。
- `AppFilter='WebAppOne'`為指定的應用程式指派唯一的檢測金鑰。
- `AppFilter='WebAppTwo'`為指定的應用程式指派唯一的檢測金鑰。
- 最後， `AppFilter` 也會使用 `'.*'` 萬用字元來比對先前規則不符合的所有 web 應用程式，並指派預設的檢測金鑰。
- 為了方便閱讀，會加入空格。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


### <a name="parameters"></a>參數

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**必要。** 使用此參數來提供單一檢測金鑰，供目的電腦上的所有應用程式使用。

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必要。** 使用此參數可提供多個檢測金鑰，以及每個應用程式所使用之檢測金鑰的對應。
您可以藉由設定，針對數部電腦建立單一安裝腳本 `MachineFilter` 。

> [!IMPORTANT]
> 應用程式會依照規則的提供順序來比對規則。 因此，您應該先指定最特定的規則，最後是最常見的規則。

##### <a name="schema"></a>結構描述
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter**是電腦或 VM 名稱的必要 c # RegEx。
    - '. * ' 會符合所有
    - ' ComputerName ' 只會比對具有指定之確切名稱的電腦。
- **AppFilter**是 IIS 網站名稱的必要 c # RegEx。 您可以執行[iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)命令，以取得伺服器上的網站清單。
    - '. * ' 會符合所有
    - ' SiteName ' 只會比對具有指定之確切名稱的 IIS 網站。
- 若要啟用符合上述兩個篩選器的應用程式監視，則需要**InstrumentationKey** 。
    - 如果您想要定義規則以排除監視，請將此值保留為 null。


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**選擇性.** 使用此參數可讓檢測引擎收集有關執行 managed 進程期間所發生之事件和訊息的資訊。 這些事件和訊息包括相依性結果碼、HTTP 指令動詞和 SQL 命令文字。

檢測引擎會增加額外負荷，並預設為關閉。

#### <a name="-acceptlicense"></a>-AcceptLicense
**選擇性.** 使用此參數可接受無周邊安裝中的授權和隱私權聲明。

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
當您有一部 web 伺服器的叢集時，您可能會使用[共用](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)設定。
無法將 HttpModule 插入此共用設定。
此腳本將會失敗，並出現需要額外安裝步驟的訊息。
使用此參數來忽略這項檢查，並繼續安裝必要條件。 如需詳細資訊，請參閱[已知衝突-使用-iis 共用-](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)設定

#### <a name="-verbose"></a>-Verbose
**一般參數。** 使用此參數來顯示詳細記錄。

#### <a name="-whatif"></a>-WhatIf 
**一般參數。** 使用此參數來測試及驗證您的輸入參數，而不實際啟用監視。

### <a name="output"></a>輸出

#### <a name="example-output-from-a-successful-enablement"></a>成功啟用的輸出範例

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

## <a name="disable-instrumentationengine"></a>停用-InstrumentationEngine

藉由移除一些登錄機碼來停用檢測引擎。
重新開機 IIS，變更才會生效。

### <a name="examples"></a>範例

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>參數 

#### <a name="-verbose"></a>-Verbose
**一般參數。** 使用此參數來輸出詳細記錄。

### <a name="output"></a>輸出


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>成功停用檢測引擎的範例輸出

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>停用-ApplicationInsightsMonitoring

停用目的電腦上的監視。
此 Cmdlet 會移除對 IIS applicationHost.config 的編輯，並移除登錄機碼。

### <a name="examples"></a>範例

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>參數 

#### <a name="-verbose"></a>-Verbose
**一般參數。** 使用此參數來顯示詳細記錄。

### <a name="output"></a>輸出


##### <a name="example-output-from-successfully-disabling-monitoring"></a>成功停用監視的輸出範例

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


## <a name="get-applicationinsightsmonitoringconfig"></a>ApplicationInsightsMonitoringConfig

取得設定檔，並將值列印到主控台。

### <a name="examples"></a>範例

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>參數

不需要任何參數。

### <a name="output"></a>輸出


##### <a name="example-output-from-reading-the-config-file"></a>讀取設定檔案的輸出範例

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>ApplicationInsightsMonitoringStatus

此 Cmdlet 提供有關狀態監視器的疑難排解資訊。
使用此 Cmdlet 來調查監視狀態、PowerShell 模組的版本，以及檢查執行中的進程。
此 Cmdlet 會報告版本資訊，以及監視所需之金鑰檔的相關資訊。

### <a name="examples"></a>範例

#### <a name="example-application-status"></a>範例：應用程式狀態

執行命令 `Get-ApplicationInsightsMonitoringStatus` 以顯示 [網站] 的監視狀態。

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

在此範例中為;
- [**機器識別碼**] 是用來唯一識別您的伺服器的匿名識別碼。 如果您建立支援要求，我們將需要此識別碼來尋找伺服器的記錄。
- IIS 中的**預設網站**已停止
- **DemoWebApp111**已在 IIS 中啟動，但未收到任何要求。 此報表顯示沒有執行中的進程（ProcessId：找不到）。
- **DemoWebApp222**正在執行，且正在進行監視（已檢測： true）。 根據使用者設定，檢測金鑰 xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxx-xxxx-xxxxxxxxx123 與此網站相符。
- **DemoWebApp333**已使用 Application Insights SDK 手動進行檢測。 狀態監視器偵測到 SDK，且不會監視此網站。


#### <a name="example-powershell-module-information"></a>範例： PowerShell 模組資訊

執行命令 `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` 以顯示目前模組的相關資訊：

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

#### <a name="example-runtime-status"></a>範例：執行時間狀態

您可以檢查檢測電腦上的處理常式，以查看是否已載入所有 Dll。 如果監視運作正常，則至少應載入12個 Dll。

執行命令 `Get-ApplicationInsightsMonitoringStatus -InspectProcess` ：


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

#### <a name="no-parameters"></a>（無參數）

根據預設，此 Cmdlet 會報告 web 應用程式的監視狀態。
使用此選項來檢查您的應用程式是否已成功檢測。
您也可以檢查哪一個檢測金鑰符合您的網站。


#### <a name="-powershellmodule"></a>-PowerShellModule
**選擇項**。 使用此參數來報告監視所需的 Dll 版本號碼和路徑。
如果您需要識別任何 DLL 的版本，包括 Application Insights SDK，請使用此選項。

#### <a name="-inspectprocess"></a>-InspectProcess

**選擇項**。 使用此參數來報告 IIS 是否正在執行。
它也會下載外部工具，以判斷必要的 Dll 是否已載入 IIS 執行時間。


如果此程式因任何原因而失敗，您可以手動執行下列命令：
- iisreset.exe/status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp.exe |findstr/I "InstrumentationEngine AI。 ApplicationInsights
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp.exe |findstr/I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**選擇項**。 只能搭配 InspectProcess 使用。 使用此參數來略過在下載其他工具之前出現的使用者提示。


## <a name="set-applicationinsightsmonitoringconfig"></a>設定-ApplicationInsightsMonitoringConfig

設定設定檔，而不進行完整重新安裝。
重新開機 IIS，讓您的變更生效。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有系統管理員許可權的 PowerShell 會話。


### <a name="examples"></a>範例

#### <a name="example-with-a-single-instrumentation-key"></a>具有單一檢測金鑰的範例
在此範例中，會將單一檢測金鑰指派給目前電腦上的所有應用程式。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>含檢測金鑰組應的範例
在此範例中：
- `MachineFilter`使用萬用字元來符合目前的電腦 `'.*'` 。
- `AppFilter='WebAppExclude'`提供 `null` 檢測金鑰。 將不會檢測指定的應用程式。
- `AppFilter='WebAppOne'`為指定的應用程式指派唯一的檢測金鑰。
- `AppFilter='WebAppTwo'`為指定的應用程式指派唯一的檢測金鑰。
- 最後， `AppFilter` 也會使用 `'.*'` 萬用字元來比對先前規則不符合的所有 web 應用程式，並指派預設的檢測金鑰。
- 為了方便閱讀，會加入空格。

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>參數

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**必要。** 使用此參數來提供單一檢測金鑰，供目的電腦上的所有應用程式使用。

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必要。** 使用此參數可提供多個檢測金鑰，以及每個應用程式所使用之檢測金鑰的對應。
您可以藉由設定，針對數部電腦建立單一安裝腳本 `MachineFilter` 。

> [!IMPORTANT]
> 應用程式會依照規則的提供順序來比對規則。 因此，您應該先指定最特定的規則，最後是最常見的規則。

##### <a name="schema"></a>結構描述
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter**是電腦或 VM 名稱的必要 c # RegEx。
    - '. * ' 會符合所有
    - ' ComputerName ' 只會符合指定名稱的電腦。
- **AppFilter**是電腦或 VM 名稱的必要 c # RegEx。
    - '. * ' 會符合所有
    - ' ApplicationName ' 只會符合具有指定名稱的 IIS 應用程式。
- 若要啟用符合上述兩個篩選器的應用程式監視，則需要**InstrumentationKey** 。
    - 如果您想要定義規則以排除監視，請將此值保留為 null。


#### <a name="-verbose"></a>-Verbose
**一般參數。** 使用此參數來顯示詳細記錄。


### <a name="output"></a>輸出

根據預設，沒有任何輸出。

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>透過-InstrumentationKey 設定設定檔的範例詳細資訊輸出

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>透過-InstrumentationKeyMap 設定設定檔的範例詳細資訊輸出

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

## <a name="start-applicationinsightsmonitoringtrace"></a>開始-ApplicationInsightsMonitoringTrace

從無程式碼附加執行時間收集[ETW 事件](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal)。 此 Cmdlet 是執行[PerfView](https://github.com/microsoft/perfview)的替代方法。

收集的事件會即時列印到主控台，並儲存至 ETL 檔案。 [PerfView](https://github.com/microsoft/perfview)可以開啟輸出 ETL 檔案以供進一步調查。

此 Cmdlet 將會執行，直到達到超時持續時間（預設為5分鐘），或以手動方式停止（ `Ctrl + C` ）。

### <a name="examples"></a>範例

#### <a name="how-to-collect-events"></a>如何收集事件

一般來說，我們會要求您收集事件，以調查您的應用程式未被檢測的原因。

無程式碼附加執行時間會在 IIS 啟動時以及應用程式啟動時發出 ETW 事件。

若要收集這些事件：
1. 在具有系統管理員許可權的 cmd 主控台中，執行 `iisreset /stop` 以關閉 IIS 和所有 web 應用程式。
2. 執行此 Cmdlet
3. 在具有系統管理員許可權的 cmd 主控台中，執行 `iisreset /start` 以啟動 IIS。
4. 嘗試流覽至您的應用程式。
5. 在您的應用程式完成載入之後，您可以手動停止它（ `Ctrl + C` ），或等候超時。

#### <a name="what-events-to-collect"></a>要收集的事件

收集事件時，您有三個選項：
1. 使用參數 `-CollectSdkEvents` 來收集從 APPLICATION INSIGHTS SDK 發出的事件。
2. 使用參數 `-CollectRedfieldEvents` 來收集狀態監視器和 Redfield 執行時間發出的事件。 當診斷 IIS 和應用程式啟動時，這些記錄會很有説明。
3. 同時使用這兩個參數來收集這兩個事件種類。
4. 根據預設，如果未指定任何參數，則會收集這兩個事件種類。


### <a name="parameters"></a>參數

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**選擇性.** 使用此參數來設定此腳本應該收集事件的時間長度。 預設值為 5 分鐘。

#### <a name="-logdirectory"></a>-LogDirectory
**選擇性.** 使用此參數來設定 ETL 檔案的輸出目錄。 根據預設，會在 PowerShell 模組目錄中建立此檔案。 腳本執行期間會顯示完整路徑。


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**選擇性.** 使用此參數來收集 Application Insights SDK 事件。

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**選擇性.** 使用此參數來收集來自狀態監視器和 Redfield 執行時間的事件。

#### <a name="-verbose"></a>-Verbose
**一般參數。** 使用此參數來輸出詳細記錄。



### <a name="output"></a>輸出


#### <a name="example-of-application-startup-logs"></a>應用程式開機記錄的範例
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
 - [探索計量](../../azure-monitor/app/metrics-explorer.md)以監視效能和使用量。
- [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- 使用[分析](../../azure-monitor/app/analytics.md)進行更先進的查詢。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。
 
 新增更多遙測：
 - [建立 Web 測試](monitor-web-app-availability.md)，確定您的網站保持即時狀態。
- [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md)，以查看來自網頁程式碼的例外狀況，並啟用追蹤呼叫。
- [將 APPLICATION INSIGHTS SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md)，讓您可以插入追蹤和記錄呼叫。
 
 使用 Application Insights 代理程式執行更多工具：
 - 使用我們的指南來[疑難排解](status-monitor-v2-troubleshoot.md)Application Insights 代理程式。






