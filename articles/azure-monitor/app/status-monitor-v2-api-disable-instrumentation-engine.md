---
title: Azure 應用程式 Insights 代理程式 API 參考
description: Application Insights 代理程式 API 參考。 停用-InstrumentationEngine。 在不重新部署網站的情況下監視網站效能。 適用于內部部署、Vm 或 Azure 上裝載的 ASP.NET web 應用程式。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dafa12db95a10df467bf0d042cfd9395720648b2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671370"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Application Insights 代理程式 API：停用-InstrumentationEngine

本文說明的 Cmdlet 是[ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述
藉由移除一些登錄機碼來停用檢測引擎。
重新開機 IIS，變更才會生效。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有系統管理員許可權的 PowerShell 會話。

## <a name="examples"></a>範例

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>參數 

### <a name="-verbose"></a>-Verbose
**一般參數。** 使用此參數來輸出詳細記錄。

## <a name="output"></a>輸出


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>成功停用檢測引擎的範例輸出

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>後續步驟

 使用 Application Insights 代理程式執行更多工具：
 - 使用我們的指南來[疑難排解](status-monitor-v2-troubleshoot.md)Application Insights 代理程式。
