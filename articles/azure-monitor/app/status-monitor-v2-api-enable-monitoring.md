---
title: Azure 應用程式見解代理 API 引用
description: 應用程式見解代理 API 引用。 啟用應用程式洞察監控。 在不重新部署網站的情況下監控網站性能。 與本地、VM 或 Azure 上託管的ASP.NET Web 應用配合使用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: f5d66c5c21f7491e3bdc6bb70f693c3b98bf62dd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536738"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>應用程式見解代理 API:啟用應用程式的解解

本文介紹了一個 cmdlet,它是[Az.應用程式監視器 PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述

啟用對目標電腦上的 IIS 應用的無程式碼附加監視。

此 cmdlet 將修改 IIS 應用程式Host.config 並設置一些註冊表項。
它還將創建一個應用程式 insights.ikey.config 檔案,該檔定義每個應用使用的檢測密鑰。
IIS 將在啟動時載入 RedfieldModule,這將在應用程式啟動時將應用程式見解 SDK 注入應用程式。
重新啟動 IIS,使更改生效。

啟用監視後,我們建議您使用[即時指標](live-stream.md)快速檢查應用是否向我們發送遙測數據。


> [!NOTE] 
> - 要開始,您需要一個檢測密鑰。 有關詳細資訊,請參閱[建立資源](create-new-resource.md#copy-the-instrumentation-key)。
> - 此 cmdlet 要求您查看並接受我們的許可證和隱私聲明。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理員許可權的 PowerShell 作業階段和提升的執行策略。 有關詳細資訊,請參閱[使用提升執行策略執行 PowerShell 作為管理員](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)。

## <a name="examples"></a>範例

### <a name="example-with-a-single-instrumentation-key"></a>使用單一個偵測金鑰的範例
在此示例中,當前計算機上的所有應用都分配了單個檢測密鑰。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>使用偵測鍵對應的範例
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


## <a name="parameters"></a>參數

### <a name="-instrumentationkey"></a>-儀器金鑰
**必填。** 使用此參數提供單個檢測密鑰,供目標計算機上的所有應用使用。

### <a name="-instrumentationkeymap"></a>-儀器鍵映射
**必填。** 使用此參數可提供多個檢測鍵和每個應用使用的檢測鍵的映射。
您可以通過`MachineFilter`設定 為多台電腦創建單個安裝文稿。

> [!IMPORTANT]
> 應用將按規則提供的順序與規則匹配。 因此,應首先指定最具體的規則,最後指定最通用的規則。

#### <a name="schema"></a>結構描述
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **機器篩選器**是計算機或 VM 名稱的必填項。
    - '.*' 將符合所有
    - 電腦名稱"將僅匹配指定的確切名稱的電腦。
- **AppFilter**是 IIS 網站名稱的必填項。 您可以通過執行命令[get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)來取得伺服器上的網站清單。
    - '.*' 將符合所有
    - "站點名稱"將僅與指定的確切名稱的 IIS 網站匹配。
- **需要檢測密鑰**來啟用對與前兩個篩選器匹配的應用的監視。
    - 如果要定義規則以排除監視,請保留此值為空。


### <a name="-enableinstrumentationengine"></a>-啟用偵測引擎
**選。** 使用此開關可使檢測引擎能夠收集有關執行託管進程期間發生的情況的事件和消息。 這些事件和消息包括依賴項結果代碼、HTTP 謂詞和 SQL 命令文本。

默認情況下,檢測引擎會增加開銷並關閉。

### <a name="-acceptlicense"></a>-接受許可
**選。** 使用此開關可以接受無頭安裝中的許可證和隱私聲明。

### <a name="-ignoresharedconfig"></a>-忽略共享設定
當您有一組 Web 伺服器時, 您可能正在使用[分享設定](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)。
無法將 HttpModule 注入此共享配置。
此腳本將失敗,因為消息顯示需要額外的安裝步驟。
使用此開關忽略此檢查並繼續安裝先決條件。 有關詳細資訊,請參閱[已知的"與 iis 衝突共用配置"](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**通用參數。** 使用此開關可顯示詳細日誌。

### <a name="-whatif"></a>-WhatIf 
**通用參數。** 使用此開關可以測試和驗證輸入參數,而無需實際啟用監視。

## <a name="output"></a>輸出


#### <a name="example-output-from-a-successful-enablement"></a>成功啟用的範例輸出

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
 - [流覽指標](../../azure-monitor/platform/metrics-charts.md)以監視性能和使用方式。
- [搜索事件和日誌](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- 對更進階查詢[使用分析](../../azure-monitor/app/analytics.md)。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。
 
 新增更多遙測：
 - [建立 Web 測試](monitor-web-app-availability.md)，確定您的網站保持即時狀態。
- [添加 Web 客戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁代碼中的異常並啟用追蹤呼叫。
- [將應用程式見解 SDK 添加到代碼中](../../azure-monitor/app/asp-net.md),以便插入追蹤和紀錄調用。
 
 使用應用程式見解代理執行更多操作:
 - 使用我們的指南對應用程式見解代理[程式進行故障排除](status-monitor-v2-troubleshoot.md)。
 - [獲取配置](status-monitor-v2-api-get-config.md)以確認您的設置記錄正確。
 - [獲取狀態](status-monitor-v2-api-get-status.md)以檢查監視。
