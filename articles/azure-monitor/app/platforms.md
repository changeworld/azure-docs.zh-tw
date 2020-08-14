---
title: Application Insights：語言、平臺和整合 |Microsoft Docs
description: 適用于 Application Insights 的語言、平臺和整合
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: d3bb1473d94db04d1dedf2b09e19b83197cc6e0e
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225099"
---
# <a name="supported-languages"></a>支援的語言

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-get-started.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>支援的平台和架構

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>已部署之應用程式的檢測 (無程式碼，以代理程式為基礎的) 
* [Azure VM 和 Azure 虛擬機器擴展集](./azure-vm-vmss-apps.md)
* [Azure App Service](./azure-web-apps.md)
* [ASP.NET - 適用於即時應用程式](./monitor-performance-live-website-now.md)
* [Azure 雲端服務](./cloudservices.md)，包括 web 和背景工作角色
* [Azure Functions](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>透過程式碼 (Sdk) 進行檢測
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Android](../learn/mobile-center-quickstart.md) (App Center)
* [iOS](../learn/mobile-center-quickstart.md) (App Center)
* [Java EE](./java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](./opencensus-python.md)
* [通用 Windows app](../learn/mobile-center-quickstart.md) (App Center)
* [Windows 桌面應用程式、服務和背景工作角色](./windows-desktop.md)
* [React](./javascript-react-plugin.md)
* [React Native](./javascript-react-native-plugin.md) \(英文\)

## <a name="logging-frameworks"></a>記錄架構
* [ILogger](./ilogger.md)
* [Log4Net、NLog 或 System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java、Log4J 或 Logback](./java-trace-logs.md)
* [LogStash 外掛程式](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure 監視器](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>匯出和資料分析
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [串流分析](./export-power-bi.md)

## <a name="unsupported-sdks"></a>不支援的 SDK
我們知道有數個其他支援社區的 Sdk 存在。 不過，Azure 監視器只會在使用此頁面上所列的支援 Sdk 時提供支援。 我們會持續評估機會以擴充我們對其他語言的支援，因此請遵循我們的 [GitHub 公告](https://github.com/microsoft/ApplicationInsights-Announcements/issues) 頁面來接收最新的 SDK 新聞。 

