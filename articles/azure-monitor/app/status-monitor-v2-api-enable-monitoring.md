---
title: Azure 應用程式 Insights 代理程式 API 參考
description: Application Insights 代理程式 API 參考。 啟用-ApplicationInsightsMonitoring。 在不重新部署網站的情況下監視網站效能。 適用于內部部署、Vm 或 Azure 上裝載的 ASP.NET web 應用程式。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8bbdc96a49fffc91f80d24a9eb0926766f86ee16
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671302"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Application Insights 代理程式 API：啟用-ApplicationInsightsMonitoring

本文說明的 Cmdlet 是[ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述

在目的電腦上啟用 IIS 應用程式的無程式碼附加監視。

此 Cmdlet 會修改 IIS Applicationhost.config 並設定一些登錄機碼。
它也會建立 applicationinsights ikey .config 檔案，以定義每個應用程式所使用的檢測金鑰。
IIS 會在啟動時載入 RedfieldModule，這會在應用程式啟動時將 Application Insights SDK 插入應用程式中。
重新開機 IIS，讓您的變更生效。

啟用監視之後，建議您使用[即時計量](live-stream.md)來快速檢查您的應用程式是否正在傳送遙測。


> [!NOTE] 
> - 若要開始使用，您需要有檢測金鑰。 如需詳細資訊，請參閱[建立資源](create-new-resource.md#copy-the-instrumentation-key)。
> - 此 Cmdlet 會要求您審查並接受我們的授權及隱私權聲明。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有系統管理員許可權的 PowerShell 會話，以及更高的執行原則。 如需詳細資訊，請參閱[使用提高許可權的執行原則以系統管理員身分執行 PowerShell](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)。

## <a name="examples"></a>範例

### <a name="example-with-a-single-instrumentation-key"></a>具有單一檢測金鑰的範例
在此範例中，會將單一檢測金鑰指派給目前電腦上的所有應用程式。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>含檢測金鑰組應的範例
在此範例中：
- `MachineFilter` 會使用 `'.*'` 萬用字元來符合目前的電腦。
- `AppFilter='WebAppExclude'` 提供 `null` 的檢測金鑰。 將不會檢測指定的應用程式。
- `AppFilter='WebAppOne'` 為指定的應用程式指派唯一的檢測金鑰。
- `AppFilter='WebAppTwo'` 為指定的應用程式指派唯一的檢測金鑰。
- 最後，`AppFilter` 也會使用 `'.*'` 萬用字元來比對先前規則不符合的所有 web 應用程式，並指派預設的檢測金鑰。
- 為了方便閱讀，會加入空格。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>參數

### <a name="-instrumentationkey"></a>-InstrumentationKey
**必要。** 使用此參數來提供單一檢測金鑰，供目的電腦上的所有應用程式使用。

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必要。** 使用此參數可提供多個檢測金鑰，以及每個應用程式所使用之檢測金鑰的對應。
您可以藉由設定 `MachineFilter`，為數部電腦建立單一安裝腳本。

> [!IMPORTANT]
> 應用程式會依照規則的提供順序來比對規則。 因此，您應該先指定最特定的規則，最後是最常見的規則。

#### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter**是電腦或C# VM 名稱的必要 RegEx。
    - '. * ' 會符合所有
    - ' ComputerName ' 只會比對具有指定之確切名稱的電腦。
- **AppFilter**是 IIS 網站C#名稱的必要 RegEx。 您可以執行[iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)命令，以取得伺服器上的網站清單。
    - '. * ' 會符合所有
    - ' SiteName ' 只會比對具有指定之確切名稱的 IIS 網站。
- 若要啟用符合上述兩個篩選器的應用程式監視，則需要**InstrumentationKey** 。
    - 如果您想要定義規則以排除監視，請將此值保留為 null。


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**選擇性。** 使用此參數可讓檢測引擎收集有關執行 managed 進程期間所發生之事件和訊息的資訊。 這些事件和訊息包括相依性結果碼、HTTP 指令動詞和 SQL 命令文字。

檢測引擎會增加額外負荷，並預設為關閉。

### <a name="-acceptlicense"></a>-AcceptLicense
**選擇性。** 使用此參數可接受無周邊安裝中的授權和隱私權聲明。

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
當您有一部 web 伺服器的叢集時，您可能會使用[共用](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)設定。
無法將 HttpModule 插入此共用設定。
此腳本將會失敗，並出現需要額外安裝步驟的訊息。
使用此參數來忽略這項檢查，並繼續安裝必要條件。 如需詳細資訊，請參閱[已知衝突-使用-iis 共用-](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)設定

### <a name="-verbose"></a>-Verbose
**一般參數。** 使用此參數來顯示詳細記錄。

### <a name="-whatif"></a>-WhatIf 
**一般參數。** 使用此參數來測試及驗證您的輸入參數，而不實際啟用監視。

## <a name="output"></a>輸出


#### <a name="example-output-from-a-successful-enablement"></a>成功啟用的輸出範例

```
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

## <a name="next-steps"></a>後續步驟

  檢視遙測：
 - [探索計量](../../azure-monitor/app/metrics-explorer.md)以監視效能和使用量。
- [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- [流量分析](../../azure-monitor/app/analytics.md)進行更先進的查詢。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。
 
 新增更多遙測：
 - [建立 web 測試](monitor-web-app-availability.md)，以確保您的網站保持上線。
- [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md)，以查看來自網頁程式碼的例外狀況，並啟用追蹤呼叫。
- [將 APPLICATION INSIGHTS SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md)，讓您可以插入追蹤和記錄呼叫。
 
 使用 Application Insights 代理程式執行更多工具：
 - 使用我們的指南來[疑難排解](status-monitor-v2-troubleshoot.md)Application Insights 代理程式。
 - [取得](status-monitor-v2-api-get-config.md)設定，以確認已正確記錄您的設定。
 - [取得狀態](status-monitor-v2-api-get-status.md)以檢查監視。
