---
title: Azure 應用程式見解代理 API 引用
description: 應用程式見解代理 API 引用。 獲取應用程式洞察監控配置。 在不重新部署網站的情況下監控網站性能。 與本地、VM 或 Azure 上託管的ASP.NET Web 應用配合使用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 0cb5991b381acc9a0ec36113d0e15a7731b5074d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537486"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>應用程式見解代理 API:取得應用程式的監控設定

本文介紹了一個 cmdlet,它是[Az.應用程式監視器 PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述

獲取配置檔並將值列印到主控台。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理員權限的 PowerShell 作業階段。

## <a name="examples"></a>範例

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>參數

無需任何參數。

## <a name="output"></a>輸出


#### <a name="example-output-from-reading-the-config-file"></a>讀取設定檔的範例輸出

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>後續步驟

  檢視遙測：
 - [流覽指標](../../azure-monitor/platform/metrics-charts.md)以監視性能和使用方式。
- [搜索事件和日誌](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- 對更進階查詢使用[分析](../../azure-monitor/app/analytics.md)。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。
 
 新增更多遙測：
 - [建立 Web 測試](monitor-web-app-availability.md)，確定您的網站保持即時狀態。
- [添加 Web 客戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁代碼中的異常並啟用追蹤呼叫。
- [將應用程式見解 SDK 添加到代碼中](../../azure-monitor/app/asp-net.md),以便插入追蹤和紀錄調用。
 
 使用應用程式見解代理執行更多操作:
 - 使用我們的指南對應用程式見解代理[程式進行故障排除](status-monitor-v2-troubleshoot.md)。
 - 使用[「設定設定](status-monitor-v2-api-set-config.md)」cmdlet 對設定進行更改。
