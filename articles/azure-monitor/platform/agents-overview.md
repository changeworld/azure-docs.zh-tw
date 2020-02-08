---
title: Azure 監視代理程式概觀 | Microsoft Docs
description: 本文將詳細說明可支援對裝載於 Azure 或混合式環境中的虛擬機器進行監視的 Azure 代理程式。
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: ae799e9a852b8700399ef695c54b3348174b560c
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069399"
---
# <a name="overview-of-the-azure-monitor-agents"></a>Azure 監視器代理程式的總覽 
計算資源（例如虛擬機器）會產生資料，以監視其效能和可用性，就像[其他雲端資源](../insights/monitor-azure-resource.md)一樣。 不過，計算資源也有需要監視的客體作業系統和工作負載。 從資源內部收集此監視資料需要代理程式。 本文說明 Azure 監視器所使用的代理程式，並協助您判斷需要符合特定環境需求的方式。

## <a name="summary-of-agents"></a>代理程式摘要

> [!NOTE]
> Azure 監視器目前有多個代理程式，因為最新的 Azure 監視器和 Log Analytics 的匯總。 這兩個代理程式都會共用一些功能，而其他功能對特定的代理程式是唯一的。 視您的需求而定，您可能需要其中一個代理程式或兩者。 

Azure 監視器有三個代理程式，各自提供特定功能。 視您的需求而定，您可以在虛擬機器和其他計算資源上安裝單一代理程式或多個。

* [Azure 診斷擴充功能](#azure-diagnostic-extension)
* [Log Analytics 代理程式](#log-analytics-agent)
* [相依性代理程式](#dependency-agent)

下表提供不同代理程式的快速比較。 如需每個專案的詳細資訊，請參閱本文的其餘部分。

| | Azure 診斷擴充功能 | Log Analytics 代理程式 | 相依性代理程式 |
|:---|:---|:---|:---|
| 支援的環境 | Azure | Azure<br>其他雲端<br>內部部署 | Azure<br>其他雲端<br>內部部署 |
| 作業系統 | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| 代理程式相依性  | None | None | 需要 Log Analytics 代理程式 |
| 收集的資料 | 事件記錄<br>ETW 事件<br>syslog<br>效能<br>IIS 記錄<br>.NET 應用程式追蹤輸出記錄<br>損毀傾印 | 事件記錄<br>syslog<br>效能<br>IIS 記錄<br>自訂的記錄<br>解決方案中的資料 | 進程詳細資料和相依性<br>網路連接計量 |
| 資料傳送至 | Azure 儲存體<br>Azure 監視器計量<br>事件中樞 | Azure 監視器記錄 | Azure 監視器記錄 |



## <a name="azure-diagnostic-extension"></a>Azure 診斷擴充功能
[Azure 診斷延伸](../../azure-monitor/platform/diagnostics-extension-overview.md)模組會從客體作業系統和 Azure 計算資源的工作負載收集監視資料。 它主要會將資料收集到 Azure 儲存體。 您可以設定 Azure 監視器將資料從儲存體複製到 Log Analytics 工作區。 您也可以將來賓效能資料收集到 Azure 監視器計量中。

Azure 診斷延伸模組通常稱為 Windows Azure 診斷（WAD）或 Linux Azure 診斷（LAD）延伸模組。


### <a name="scenarios-supported"></a>支援的案例

Azure 診斷延伸模組支援的案例包括下列各項：

* 從 Azure 計算資源收集記錄和效能資料。
* 將記錄和效能資料從客體作業系統封存到 Azure 儲存體。
* 使用[Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md)之類的工具，來查看儲存體中的監視資料。
* 收集計量資料庫中的效能資料，以利用[Azure 監視器計量](data-platform-metrics.md)所支援的功能，例如近乎即時的[度量警示](../../azure-monitor/platform/alerts-metric-overview.md)和[自動](autoscale-overview.md)調整。 
* 將監視資料從[儲存體收集到 Log Analytics 工作區](azure-storage-iis-table.md)，以利用[Azure 監視器記錄](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)（例如[記錄查詢](../log-query/log-query-overview.md)）所支援的功能。
* 使用[Azure 事件中樞](diagnostics-extension-stream-event-hubs.md)將監視資料傳送至協力廠商工具。
* 使用[開機診斷](../../virtual-machines/troubleshooting/boot-diagnostics.md)調查 VM 的開機問題。
* 將資料從 VM 中執行的應用程式複製[到 Application Insights](diagnostics-extension-to-application-insights.md) ，以與其他應用程式監視進行整合。

## <a name="log-analytics-agent"></a>Log Analytics 代理程式
[Log Analytics 代理程式](log-analytics-agent.md)會收集來自客體作業系統的監視資料，以及 Azure、其他雲端提供者和內部部署中虛擬機器的工作負載。 它會將資料收集到 Log Analytics 工作區。

Log Analytics 代理程式是 System Center Operations Manager 所使用的相同代理程式，並多路連接代理程式電腦與您的管理群組進行通訊，並同時 Azure 監視器。 Azure 監視器中的特定解決方案也需要此代理程式。

適用于 Windows 的 Log Analytics 代理程式通常稱為 Microsoft 管理代理程式（MMA）。 適用于 Linux 的 Log Analytics 代理程式通常稱為「OMS 代理程式」。


### <a name="scenarios-supported"></a>支援的案例

Log Analytics 代理程式支援的案例包括下列各項：

* 從 Azure、其他雲端提供者和內部部署中的虛擬機器收集記錄和效能資料。 
* 將監視資料收集到 Log Analytics 工作區，以利用[Azure 監視器記錄](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)（例如[記錄查詢](../log-query/log-query-overview.md)）所支援的功能。
* 使用 Azure 監視器的監視解決方案，例如[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)、[適用于容器的 Azure 監視器](../insights/container-insights-overview.md)等等。  
* 使用需要 Log Analytics 代理程式的[Azure Sentinel](../../sentinel/overview.md)來管理虛擬機器的安全性。
* 使用需要 Log Analytics 代理程式的[Azure 資訊安全中心](../../security-center/security-center-intro.md)來管理虛擬機器的安全性。
* 使用[Azure 自動化](../../automation/automation-intro.md)的功能，透過其生命週期提供 Azure vm 的完整管理。  這些需要 Log Analytics 代理程式的功能範例包括：
  * 作業系統更新的 [Azure 自動化更新管理](../../automation/automation-update-management.md)。
  * [Azure 自動化狀態設定](../../automation/automation-dsc-overview.md)以維持一致的設定狀態。
  * 使用 [Azure 自動化變更追蹤與清查](../../automation/change-tracking.md)來追蹤組態變更。

## <a name="dependency-agent"></a>相依性代理程式
相依性代理程式會收集有關虛擬機器上執行之進程的探索資料，以及外部進程相依性。 [服務對應](../insights/service-map.md)和對應功能[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)需要此代理程式。 相依性代理程式需要 Log Analytics 代理程式，並將資料寫入 Azure 監視器中的 Log Analytics 工作區。


## <a name="using-multiple-agents"></a>使用多個代理程式
針對特定的虛擬機器，您可能需要使用 Azure 診斷擴充功能或 Log Analytics 代理程式的特定需求。 例如，您可能會想要使用需要 Azure 診斷擴充功能的計量警示。 但是，您可能也會想要使用適用於 VM 的 Azure 監視器的對應功能，這項功能需要相依性代理程式和 Log Analytics 代理程式。 在此情況下，您可以使用多個代理程式，這是一種常見的案例，適用于需要每一項功能的客戶。

### <a name="considerations"></a>考量

- 相依性代理程式需要在相同的虛擬機器上安裝 Log Analytics 代理程式。
- 在 Linux Vm 上，Log Analytics 代理程式必須安裝在 Azure 診斷擴充功能之前。


## <a name="next-steps"></a>後續步驟

- 請參閱 [Log Analytics 代理程式概觀](../../azure-monitor/platform/log-analytics-agent.md)，以檢閱將代理程式部署至 Azure、資料中心或其他雲端環境所裝載的機器時需符合哪些要求，以及支援的方法。

