---
title: Azure 應用程式見解代理 API 引用
description: 應用程式見解代理 API 引用。 啟用檢測引擎。 在不重新部署網站的情況下監控網站性能。 與本地、VM 或 Azure 上託管的ASP.NET Web 應用配合使用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b3f298ac31cc584cd16553186359c87f69f27aad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671347"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>應用程式見解代理 API：啟用檢測引擎

本文介紹了一個 Cmdlet，它是[Az.應用程式監視器 PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述

通過設置某些登錄機碼啟用檢測引擎。
重新開機 IIS 使更改生效。

儀錶引擎可以補充 .NET SDK 收集的資料。
它收集描述託管進程的執行的事件和消息。 這些事件和消息包括依賴項結果代碼、HTTP 謂詞和[SQL 命令文本](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)。

在以下情況時啟用檢測引擎：
- 您已經啟用了啟用 Cmdlet 的監視，但未啟用檢測引擎。
- 您已使用 .NET SDK 手動檢測應用，並希望收集其他遙測資料。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有管理員許可權的 PowerShell 會話。

> [!NOTE] 
> - 此 Cmdlet 要求您查看並接受我們的許可證和隱私權聲明。
> - 預設情況下，檢測引擎會增加額外的開銷並關閉。

## <a name="examples"></a>範例

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>參數

### <a name="-acceptlicense"></a>-接受許可
**選。** 使用此開關可以接受無頭安裝中的許可證和隱私權聲明。

### <a name="-verbose"></a>-Verbose
**通用參數。** 使用此開關可以輸出詳細日誌。

## <a name="output"></a>輸出


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>成功啟用檢測引擎的示例輸出

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
 - [獲取配置](status-monitor-v2-api-get-config.md)以確認您的設置記錄正確。
 - [獲取狀態](status-monitor-v2-api-get-status.md)以檢查監視。
