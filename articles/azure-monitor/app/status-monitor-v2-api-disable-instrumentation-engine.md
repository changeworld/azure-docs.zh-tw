---
title: Azure 應用程式見解代理代理已關閉偵測引擎
description: 應用程式見解代理 API 引用。 禁用檢測引擎。 在不重新部署網站的情況下監控網站性能。 與本地、VM 或 Azure 上託管的ASP.NET Web 應用配合使用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 6c52d429b527c437db5bb8aaf7e3cc1f1c52af57
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998375"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>應用程式見解代理 API:停用偵測引擎

本文介紹了一個 cmdlet,它是[Az.應用程式監視器 PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述
通過刪除某些註冊表項來禁用檢測引擎。
重新啟動 IIS 使更改生效。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理員權限的 PowerShell 作業階段。

## <a name="examples"></a>範例

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>參數 

### <a name="-verbose"></a>-Verbose
**通用參數。** 使用此開關可以輸出詳細日誌。

## <a name="output"></a>輸出


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>成功關閉偵測引擎的範例輸出

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>後續步驟

 使用應用程式見解代理執行更多操作:
 - 使用我們的指南對應用程式見解代理[程式進行故障排除](status-monitor-v2-troubleshoot.md)。
