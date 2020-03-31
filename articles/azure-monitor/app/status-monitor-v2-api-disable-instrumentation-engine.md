---
title: Azure 應用程式見解代理 API 引用
description: 應用程式見解代理 API 引用。 禁用檢測引擎。 在不重新部署網站的情況下監控網站性能。 與本地、VM 或 Azure 上託管的ASP.NET Web 應用配合使用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dafa12db95a10df467bf0d042cfd9395720648b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671370"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>應用程式見解代理 API：禁用檢測引擎

本文介紹了一個 Cmdlet，它是[Az.應用程式監視器 PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述
通過刪除某些登錄機碼來禁用檢測引擎。
重新開機 IIS 使更改生效。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有管理員許可權的 PowerShell 會話。

## <a name="examples"></a>範例

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>參數 

### <a name="-verbose"></a>-Verbose
**通用參數。** 使用此開關可以輸出詳細日誌。

## <a name="output"></a>輸出


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>成功禁用檢測引擎的示例輸出

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>後續步驟

 使用應用程式見解代理執行更多操作：
 - 使用我們的指南對應用程式見解代理[進行故障排除](status-monitor-v2-troubleshoot.md)。
