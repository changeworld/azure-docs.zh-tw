---
title: Azure 應用程式 Insights 代理程式 API 參考
description: Application Insights 代理程式 API 參考。 ApplicationInsightsMonitoringConfig。 在不重新部署網站的情況下監視網站效能。 適用于內部部署、Vm 或 Azure 上裝載的 ASP.NET web 應用程式。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 1226b3e10adf786ed3335844a5d3f4e530911705
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671234"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Application Insights 代理程式 API： ApplicationInsightsMonitoringConfig

本檔描述的 Cmdlet 是[ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述

設定設定檔，而不進行完整重新安裝。
重新開機 IIS，讓您的變更生效。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有系統管理員許可權的 PowerShell 會話。


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
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
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
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter**是電腦或C# VM 名稱的必要 RegEx。
    - '. * ' 會符合所有
    - ' ComputerName ' 只會符合指定名稱的電腦。
- **AppFilter**是電腦或C# VM 名稱的必要 RegEx。
    - '. * ' 會符合所有
    - ' ApplicationName ' 只會符合具有指定名稱的 IIS 應用程式。
- 若要啟用符合上述兩個篩選器的應用程式監視，則需要**InstrumentationKey** 。
    - 如果您想要定義規則以排除監視，請將此值保留為 null。


### <a name="-verbose"></a>-Verbose
**一般參數。** 使用此參數來顯示詳細記錄。


## <a name="output"></a>輸出

根據預設，沒有任何輸出。

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>透過-InstrumentationKey 設定設定檔的範例詳細資訊輸出

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>透過-InstrumentationKeyMap 設定設定檔的範例詳細資訊輸出

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
 - [探索計量](../../azure-monitor/app/metrics-explorer.md)以監視效能和使用量。
- [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- [流量分析](../../azure-monitor/app/analytics.md)進行更先進的查詢。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。
 
 新增更多遙測：
 - [建立 web 測試](monitor-web-app-availability.md)，以確保您的網站保持上線。
- [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md)，以查看來自網頁程式碼的例外狀況，並啟用追蹤呼叫。
- [將 APPLICATION INSIGHTS SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md)，讓您可以插入追蹤和記錄呼叫
 
 使用 Application Insights 代理程式執行更多工具：
 - 使用我們的指南來[疑難排解](status-monitor-v2-troubleshoot.md)Application Insights 代理程式。
 - [取得](status-monitor-v2-api-get-config.md)設定，以確認已正確記錄您的設定。
 - [取得狀態](status-monitor-v2-api-get-status.md)以檢查監視。
