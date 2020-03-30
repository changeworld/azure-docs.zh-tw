---
title: Azure 應用程式見解代理 API 引用
description: 應用程式見解代理 API 引用。 設置應用程式見解監控配置。 在不重新部署網站的情況下監控網站性能。 與本地、VM 或 Azure 上託管的ASP.NET Web 應用配合使用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 1226b3e10adf786ed3335844a5d3f4e530911705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671234"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>應用程式見解代理 API：設置應用程式見解監控配置

本文檔描述了一個 Cmdlet，它是[Az.應用程式監視器 PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述

設置設定檔，而不執行完全重新安裝。
重新開機 IIS，使更改生效。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有管理員許可權的 PowerShell 會話。


## <a name="examples"></a>範例

### <a name="example-with-a-single-instrumentation-key"></a>使用單個檢測金鑰的示例
在此示例中，將為其當前電腦上的所有應用分配一個檢測金鑰。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>使用檢測鍵映射的示例
在此範例中：
- `MachineFilter`使用萬用字元匹配`'.*'`當前電腦。
- `AppFilter='WebAppExclude'`提供`null`檢測金鑰。 無法檢測指定的應用。
- `AppFilter='WebAppOne'`為指定的應用分配唯一的檢測金鑰。
- `AppFilter='WebAppTwo'`為指定的應用分配唯一的檢測金鑰。
- 最後，`AppFilter``'.*'`還使用萬用字元匹配與早期規則不匹配的所有 Web 應用，並分配預設檢測金鑰。
- 添加空間以進行可讀性。

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

## <a name="parameters"></a>參數

### <a name="-instrumentationkey"></a>-儀器金鑰
**必填。** 使用此參數提供單個檢測金鑰，供目的電腦上的所有應用使用。

### <a name="-instrumentationkeymap"></a>-儀器鍵映射
**必填。** 使用此參數可提供多個檢測鍵和每個應用使用的檢測鍵的映射。
您可以通過設置`MachineFilter`為多台電腦創建單個安裝腳本。

> [!IMPORTANT]
> 應用將按規則提供的順序與規則匹配。 因此，應首先指定最具體的規則，最後指定最通用的規則。

#### <a name="schema"></a>結構描述
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **機器篩選器**是電腦或 VM 名稱的必填項。
    - '.*' 將匹配所有
    - "電腦名稱稱"將僅匹配具有指定名稱的電腦。
- **AppFilter**是電腦或 VM 名稱的必填項。
    - '.*' 將匹配所有
    - "應用程式名稱"將僅匹配具有指定名稱的 IIS 應用。
- **需要檢測金鑰**來啟用對與前兩個篩選器匹配的應用的監視。
    - 如果要定義規則以排除監視，請保留此值為空。


### <a name="-verbose"></a>-Verbose
**通用參數。** 使用此開關可顯示詳細日誌。


## <a name="output"></a>輸出

預設情況下，沒有輸出。

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>通過 -儀器金鑰設置設定檔的詳細輸出示例

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>通過 -儀器鍵映射設置設定檔的詳細輸出示例

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

## <a name="next-steps"></a>後續步驟

  檢視遙測：
 - [流覽指標](../../azure-monitor/app/metrics-explorer.md)以監視性能和使用方式。
- [搜索事件和日誌](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- 對更高級的查詢[流量分析](../../azure-monitor/app/analytics.md)。
- [創建儀表板](../../azure-monitor/app/overview-dashboard.md)。
 
 新增更多遙測：
 - [建立 Web 測試](monitor-web-app-availability.md)，確定您的網站保持即時狀態。
- [添加 Web 用戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁代碼中的異常並啟用跟蹤調用。
- [將應用程式見解 SDK 添加到代碼中](../../azure-monitor/app/asp-net.md)，以便插入跟蹤和日誌調用
 
 使用應用程式見解代理執行更多操作：
 - 使用我們的指南對應用程式見解代理[進行故障排除](status-monitor-v2-troubleshoot.md)。
 - [獲取配置](status-monitor-v2-api-get-config.md)以確認您的設置記錄正確。
 - [獲取狀態](status-monitor-v2-api-get-status.md)以檢查監視。
