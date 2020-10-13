---
title: Azure 應用程式 Insights 代理程式總覽 |Microsoft Docs
description: Application Insights 代理程式的總覽。 無須重新部署網站，即可監視網站效能。 適用于內部部署、Vm 或 Azure 中裝載的 ASP.NET web 應用程式。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 0fa0b0d5e3620fc45a104ea31fd3bcbedd673da1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318941"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>為內部部署伺服器部署 Azure 監視器 Application Insights 代理程式

> [!IMPORTANT]
> 針對 Application Insights 代理程式的內部部署和非 Azure 雲端部署，建議使用本指南。 以下是 [Azure 虛擬機器和虛擬機器擴展集部署](./azure-vm-vmss-apps.md)的建議方法。

先前名為狀態監視器 V2) Application Insights 代理程式 (是發佈至 [PowerShell 資源庫](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)的 PowerShell 模組。
它會取代 [狀態監視器](./monitor-performance-live-website-now.md)。
遙測會傳送至 Azure 入口網站，您可以在其中 [監視](./app-insights-overview.md) 您的應用程式。

> [!NOTE]
> 此模組目前僅支援使用 IIS 裝載的 .NET web 應用程式無程式碼檢測。 使用 SDK 來檢測 ASP.NET Core、JAVA 和 Node.js 應用程式。

## <a name="powershell-gallery"></a>PowerShell 資源庫

Application Insights 代理程式位於： https://www.powershellgallery.com/packages/Az.ApplicationMonitor 。

![PowerShell 資源庫](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>指示
- 請參閱 [快速入門指示](status-monitor-v2-get-started.md) ，開始使用簡潔的程式碼範例。
- 請參閱 [詳細的指示](status-monitor-v2-detailed-instructions.md) ，深入瞭解如何開始使用。

## <a name="powershell-api-reference"></a>PowerShell API 參考
- [停用-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#disable-applicationinsightsmonitoring)
- [停用-InstrumentationEngine](./status-monitor-v2-api-reference.md#disable-instrumentationengine)
- [啟用-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring)
- [啟用-InstrumentationEngine](./status-monitor-v2-api-reference.md#enable-instrumentationengine)
- [ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringconfig)
- [ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)
- [設定-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#set-applicationinsightsmonitoringconfig)
- [開始-ApplicationInsightsMonitoringTrace](./status-monitor-v2-api-reference.md#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>疑難排解
- [疑難排解](status-monitor-v2-troubleshoot.md)
- [已知問題](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>常見問題集

- Application Insights 代理程式是否支援 proxy 安裝？

  *是*。 有多種方式可以下載 Application Insights 代理程式。 如果您的電腦可以存取網際網路，您可以使用參數上架至 PowerShell 資源庫 `-Proxy` 。
您也可以手動下載模組，並將它安裝在您的電腦上，或直接使用它。
這些選項中的每一個都會在 [詳細的指示](status-monitor-v2-detailed-instructions.md)中加以說明。

- 狀態監視器 v2 是否支援 ASP.NET Core 應用程式？

  *否*。 如需啟用 ASP.NET Core 應用程式監視的指示，請參閱 [ASP.NET Core 應用程式的 Application Insights](./asp-net-core.md)。 不需要為 ASP.NET Core 應用程式安裝 StatusMonitor。 即使在 IIS 中裝載 ASP.NET Core 的應用程式也是如此。

- 如何? 確認啟用成功？

  - [ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)指令程式可用來確認啟用成功。
  - 建議您使用 [即時計量](./live-stream.md) 來快速判斷您的應用程式是否正在傳送遙測。

  - 您也可以使用 [Log Analytics](../log-query/get-started-portal.md) 來列出目前正在傳送遙測的所有雲端角色：
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>接下來的步驟

檢視遙測：

* [探索計量](../platform/metrics-charts.md) 來監視效能和使用量。
* [搜尋事件和記錄](./diagnostic-search.md) 以診斷問題。
* [流量分析](../log-query/log-query-overview.md) 進行更先進的查詢。
* [建立儀表板](./overview-dashboard.md)。

新增更多遙測：

* [建立 Web 測試](monitor-web-app-availability.md)，確定您的網站保持即時狀態。
* [新增 web 用戶端遙測](./javascript.md) 以查看網頁程式碼的例外狀況，並啟用追蹤呼叫。
* [將 APPLICATION INSIGHTS SDK 新增至您的程式碼](./asp-net.md) ，讓您可以插入追蹤和記錄呼叫。

