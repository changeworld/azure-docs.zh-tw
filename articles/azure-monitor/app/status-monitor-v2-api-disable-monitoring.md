---
title: Azure 應用程式見解代理 API 引用
description: 應用程式見解代理 API 引用。 禁用應用程式見解監視。 在不重新部署網站的情況下監控網站性能。 與本地、VM 或 Azure 上託管的ASP.NET Web 應用配合使用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 0678186012678a68fa80a23685f8b346c8c5b859
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671285"
---
# <a name="application-insights-agent-api-disable-applicationinsightsmonitoring"></a>應用程式見解代理 API：禁用應用程式見解監視

本文介紹了一個 Cmdlet，它是[Az.應用程式監視器 PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述

禁用目的電腦上的監視。
此 Cmdlet 將刪除對 IIS 應用程式Host.config 的編輯，並刪除登錄機碼。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有管理員許可權的 PowerShell 會話。

## <a name="examples"></a>範例

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

## <a name="parameters"></a>參數 

### <a name="-verbose"></a>-Verbose
**通用參數。** 使用此開關可顯示詳細日誌。

## <a name="output"></a>輸出


#### <a name="example-output-from-successfully-disabling-monitoring"></a>成功禁用監視的示例輸出

```
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


## <a name="next-steps"></a>後續步驟

 使用應用程式見解代理執行更多操作：
 - 使用我們的指南對應用程式見解代理[進行故障排除](status-monitor-v2-troubleshoot.md)。
