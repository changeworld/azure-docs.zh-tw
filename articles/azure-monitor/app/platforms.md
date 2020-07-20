---
title: Application Insights：語言、平臺和整合 |Microsoft Docs
description: 適用于 Application Insights 的語言、平臺和整合
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: 153d4ad3d95c182dcc4f2aa3bad857d7e1984cc2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82891119"
---
# <a name="supported-languages"></a>支援的語言

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>支援的平台和架構

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>已部署之應用程式的檢測（無程式碼，以代理程式為基礎）
* [Azure VM 和 Azure 虛擬機器擴展集](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET - 適用於即時應用程式](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure 雲端服務](../../azure-monitor/app/cloudservices.md)，包括 web 和背景工作角色
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>透過程式碼進行檢測（Sdk）
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
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
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [串流分析](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>不支援的 SDK
我們知道有數個其他支援社區的 Sdk 存在。 不過，Azure 監視器只會在使用此頁面上所列的支援 Sdk 時提供支援。 我們會持續評估機會以擴充我們對其他語言的支援，因此請遵循我們的[GitHub 公告](https://github.com/microsoft/ApplicationInsights-Announcements/issues)頁面來接收最新的 SDK 新聞。 
