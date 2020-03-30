---
title: Azure 應用程式見解代理 API 引用
description: 應用程式見解代理 API 引用。 獲取應用程式洞察監控配置。 在不重新部署網站的情況下監控網站性能。 與本地、VM 或 Azure 上託管的ASP.NET Web 應用配合使用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 93ab8f613e5634b2eabe7c02189e223d3dfbb0a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671268"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>應用程式見解代理 API：獲取應用程式見解監控配置

本文介紹了一個 Cmdlet，它是[Az.應用程式監視器 PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述

獲取設定檔並將值列印到主控台。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有管理員許可權的 PowerShell 會話。

## <a name="examples"></a>範例

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>參數

無需任何參數。

## <a name="output"></a>輸出


#### <a name="example-output-from-reading-the-config-file"></a>讀取設定檔的示例輸出

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>後續步驟

  檢視遙測：
 - [流覽指標](../../azure-monitor/app/metrics-explorer.md)以監視性能和使用方式。
- [搜索事件和日誌](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- 對更高級的查詢使用[分析](../../azure-monitor/app/analytics.md)。
- [創建儀表板](../../azure-monitor/app/overview-dashboard.md)。
 
 新增更多遙測：
 - [建立 Web 測試](monitor-web-app-availability.md)，確定您的網站保持即時狀態。
- [添加 Web 用戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁代碼中的異常並啟用跟蹤調用。
- [將應用程式見解 SDK 添加到代碼中](../../azure-monitor/app/asp-net.md)，以便插入跟蹤和日誌調用。
 
 使用應用程式見解代理執行更多操作：
 - 使用我們的指南對應用程式見解代理[進行故障排除](status-monitor-v2-troubleshoot.md)。
 - 使用["設置配置](status-monitor-v2-api-set-config.md)"Cmdlet 對配置進行更改。
