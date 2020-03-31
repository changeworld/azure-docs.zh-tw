---
title: 應用程式見解：語言、平臺和集成 |微軟文檔
description: 適用于應用程式見解的語言、平臺和集成
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: cbda252fece638393ee7757dc165c4b009a0195e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136718"
---
# <a name="supported-languages"></a>支援的語言

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [JAVA](../../azure-monitor/app/java-get-started.md)
* [JAVAscript](../../azure-monitor/app/javascript.md)
* [節點.JS](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>支援的平台和架構

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>已部署的應用程式的檢測（無代碼、基於代理）
* [Azure VM 和 Azure 虛擬機器縮放集](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET - 適用於即時應用程式](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure 雲服務](../../azure-monitor/app/cloudservices.md)，包括 Web 和輔助角色
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>通過代碼（SDK）進行檢測
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [節點.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](../../azure-monitor/app/opencensus-python.md)
* [通用 Windows app](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Windows 桌面應用程式、服務和背景工作角色](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>記錄架構
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net、NLog 或 System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java、Log4J 或 Logback](../../azure-monitor/app/java-trace-logs.md)
* [LogStash 外掛程式](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure 監視器](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>匯出和資料分析
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [流分析](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>不支援的 SDK
我們知道存在其他幾個社區支援的 SDK。 但是，Azure 監視器僅在使用此頁面上列出的受支援的 SDK 時提供支援。 我們不斷評估機會，以擴大對其他語言的支援，因此請按照[我們的 GitHub 公告](https://github.com/microsoft/ApplicationInsights-Announcements/issues)頁面接收最新的 SDK 新聞。 
