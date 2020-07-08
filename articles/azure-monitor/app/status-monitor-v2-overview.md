---
title: Azure 應用程式 Insights 代理程式總覽 |Microsoft Docs
description: Application Insights 代理程式的總覽。 在不重新部署網站的情況下監視網站效能。 適用于內部部署、Vm 或 Azure 上裝載的 ASP.NET web 應用程式。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 626a8c96fdc247db174e2cbcd2e5c99cec43d2ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81770955"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>為內部部署伺服器部署 Azure 監視器 Application Insights 代理程式

> [!IMPORTANT]
> 針對 Application Insights 代理程式的內部部署和非 Azure 雲端部署，建議使用本指引。 以下是[Azure 虛擬機器和虛擬機器擴展集部署](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)的建議方法。

Application Insights 代理程式（先前稱為狀態監視器 V2）是發佈至[PowerShell 資源庫](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)的 PowerShell 模組。
它會取代[狀態監視器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)。
遙測會傳送至 Azure 入口網站，您可以在其中[監視](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)您的應用程式。

> [!NOTE]
> 此模組目前僅支援以 IIS 裝載之 .NET web 應用程式的無程式碼檢測。 使用 SDK 來檢測 ASP.NET Core、JAVA 和 Node.js 應用程式。

## <a name="powershell-gallery"></a>PowerShell 資源庫

Application Insights 代理程式位於此處： https://www.powershellgallery.com/packages/Az.ApplicationMonitor 。

![PowerShell 資源庫](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructions
- 請參閱開始使用的[指示](status-monitor-v2-get-started.md)，以取得簡單的程式碼範例。
- 如需深入瞭解如何開始使用，請參閱[詳細指示](status-monitor-v2-detailed-instructions.md)。

## <a name="powershell-api-reference"></a>PowerShell API 參考
- [停用-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-applicationinsightsmonitoring)
- [停用-InstrumentationEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-instrumentationengine)
- [啟用-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring)
- [啟用-InstrumentationEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-instrumentationengine)
- [ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringconfig)
- [ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus)
- [設定-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#set-applicationinsightsmonitoringconfig)
- [開始-ApplicationInsightsMonitoringTrace](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>疑難排解
- [疑難排解](status-monitor-v2-troubleshoot.md)
- [已知問題](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>常見問題集

- Application Insights 代理程式是否支援 proxy 安裝？

  *是*。 有多種方式可以下載 Application Insights 代理程式。 如果您的電腦可存取網際網路，您可以使用參數上架到 PowerShell 資源庫 `-Proxy` 。
您也可以手動下載模組，並將它安裝在您的電腦上，或直接使用它。
每個選項都會在[詳細指示](status-monitor-v2-detailed-instructions.md)中加以說明。

- 狀態監視器 v2 是否支援 ASP.NET Core 應用程式？

  *否*。 如需啟用 ASP.NET Core 應用程式監視的指示，請參閱[ASP.NET Core 應用程式的 Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)。 ASP.NET Core 應用程式不需要安裝 StatusMonitor。 即使 ASP.NET Core 應用程式裝載于 IIS 中，也是如此。

- 如何? 確認啟用成功嗎？

  - [ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus)指令程式可以用來驗證啟用是否成功。
  - 建議您使用[即時計量](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)來快速判斷您的應用程式是否正在傳送遙測。

  - 您也可以使用[Log Analytics](../log-query/get-started-portal.md)來列出目前正在傳送遙測的所有雲端角色：
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>後續步驟

檢視遙測：

* [探索計量](../../azure-monitor/platform/metrics-charts.md)以監視效能和使用量。
* [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
* [流量分析](../../azure-monitor/app/analytics.md)進行更先進的查詢。
* [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。

新增更多遙測：

* [建立 Web 測試](monitor-web-app-availability.md)，確定您的網站保持即時狀態。
* [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md)，以查看來自網頁程式碼的例外狀況，並啟用追蹤呼叫。
* [將 APPLICATION INSIGHTS SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md)，讓您可以插入追蹤和記錄呼叫。

