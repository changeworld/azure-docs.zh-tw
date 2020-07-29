---
title: Azure 監視代理程式概觀 | Microsoft Docs
description: 本文將詳細說明可支援對裝載於 Azure 或混合式環境中的虛擬機器進行監視的 Azure 代理程式。
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a9786c1f596a9f59e63886fa503bddac58ee7a8e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325333"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure 監視器代理程式的總覽

虛擬機器和其他計算資源需要代理程式來收集監視資料，以測量其客體作業系統和工作負載的效能和可用性。 本文說明 Azure 監視器所使用的代理程式，並協助您判斷需要符合特定環境需求的方式。

> [!NOTE]
> Azure 監視器目前有多個代理程式，因為最新的 Azure 監視器和 Log Analytics 的匯總。 雖然它們的功能可能會有重迭，但每一個都有獨特的功能。 視您的需求而定，您可能需要在虛擬機器上有一或多個代理程式。 

特定虛擬機器的單一代理程式可能無法完全符合一組特定的需求。 例如，您可能會想要使用需要 Azure 診斷延伸模組但也想要利用需要 Log Analytics 代理程式和相依性代理程式之適用於 VM 的 Azure 監視器功能的計量警示。 在這種情況下，您可以使用多個代理程式，而這是一種常見的案例，適用于需要各項功能的客戶。

## <a name="summary-of-agents"></a>代理程式摘要

下表提供 Windows 和 Linux Azure 監視器代理程式的快速比較。 下一節會提供每個詳細資料。 

### <a name="windows-agents"></a>Windows 代理程式

| | 診斷<br>延伸模組（WAD） | Log Analytics<br>代理程式 | 相依性<br>代理程式 |
|:---|:---|:---|:---|
| **支援的環境** | Azure | Azure<br>其他雲端<br>內部部署 | Azure<br>其他雲端<br>內部部署 | 
| **代理程式需求**  | 無 | 無 | 需要 Log Analytics 代理程式 |
| **收集的資料** | 事件記錄<br>ETW 事件<br>效能<br>檔案型記錄<br>IIS 記錄<br>.NET 應用程式記錄檔<br>損毀傾印<br>代理程式診斷記錄 | 事件記錄<br>效能<IIS logs><br>檔案型記錄<br>見解和解決方案<br>其他服務 | 進程詳細資料和相依性<br>網路連接計量 |
| **資料傳送至** | Azure 儲存體<br>Azure 監視器計量<br>事件中樞 | Azure 監視器記錄 | Azure 監視器記錄 |


### <a name="linux-agents"></a>Linux 代理程式

| | 診斷<br>延伸模組（LAD） | Telegraf<br>代理程式 | Log Analytics<br>代理程式 | 相依性<br>代理程式 |
|:---|:---|:---|:---|:---|
| **支援的環境** | Azure | Azure<br>其他雲端<br>內部部署 | Azure<br>其他雲端<br>內部部署 | Azure<br>其他雲端<br>內部部署 |
| **代理程式需求**  | 無 | 無 | 無 | 需要 Log Analytics 代理程式 |
| **收集的資料** | syslog<br>效能 | 效能 | syslog<br>效能| 進程詳細資料和相依性<br>網路連接計量 |
| **資料傳送至** | Azure 儲存體<br>事件中樞 | Azure 監視器計量 | Azure 監視器記錄 | Azure 監視器記錄 |

## <a name="log-analytics-agent"></a>Log Analytics 代理程式

[Log Analytics 代理程式](log-analytics-agent.md)會收集來自客體作業系統的監視資料，以及 Azure、其他雲端提供者和內部部署中虛擬機器的工作負載。 它會將資料收集到 Log Analytics 工作區。 Log Analytics 代理程式是 System Center Operations Manager 所使用的相同代理程式，您可以多路連接代理程式電腦與管理群組通訊並同時 Azure 監視器。 Azure 監視器中的特定見解和解決方案也需要此代理程式。


> [!NOTE]
> 適用于 Windows 的 Log Analytics 代理程式通常稱為 Microsoft Monitoring Agent （MMA）。 適用于 Linux 的 Log Analytics 代理程式通常稱為「OMS 代理程式」。



如果您需要，請使用 Log Analytics 代理程式：

* 從 Azure 外部的虛擬或實體機器收集記錄和效能資料。 
* 將資料傳送至 Log Analytics 工作區，以利用[Azure 監視器記錄](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)（例如[記錄查詢](../log-query/log-query-overview.md)）所支援的功能。
* 使用[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)可讓您大規模監視您的虛擬機器，並監視其進程和其他資源和外部進程的相依性。  
* 使用[Azure 資訊安全中心](../../security-center/security-center-intro.md)或[Azure Sentinel](../../sentinel/overview.md)管理虛擬機器的安全性。
* 使用[Azure 自動化的更新管理](../../automation/automation-update-management.md)、 [Azure 自動化狀態設定](../../automation/automation-dsc-overview.md)，或[Azure 自動化變更追蹤和清查](../../automation/change-tracking.md)，以提供 Azure vm 的全面管理
* 使用不同的[解決方案](../monitor-reference.md#insights-and-core-solutions)來監視特定服務或應用程式。

Log Analytics 代理程式的限制包括：

- 無法將資料傳送至 Azure 監視器計量、Azure 儲存體或 Azure 事件中樞。

## <a name="azure-diagnostics-extension"></a>Azure 診斷延伸模組

[Azure 診斷延伸](diagnostics-extension-overview.md)模組會收集來自客體作業系統的監視資料，以及 Azure 虛擬機器和其他計算資源的工作負載。 它主要會將資料收集到 Azure 儲存體，但也可讓您定義資料接收，以同時將資料傳送至其他目的地，例如 Azure 監視器計量和 Azure 事件中樞。

如果您需要，請使用 Azure 診斷擴充功能：

- 將資料傳送至 Azure 儲存體進行封存，或使用[Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md)之類的工具加以分析。
- 將資料傳送至[Azure 監視器計量](data-platform-metrics.md)以使用[計量瀏覽器](metrics-getting-started.md)進行分析，並利用近乎即時計量[警示](./alerts-metric-overview.md)和[自動](autoscale-overview.md)調整等功能（僅限 Windows）。
- 使用[Azure 事件中樞](diagnostics-extension-stream-event-hubs.md)將資料傳送給協力廠商工具。
- 收集[開機診斷](../../virtual-machines/troubleshooting/boot-diagnostics.md)以調查 VM 開機問題。

Azure 診斷延伸模組的限制包括：

- 只能與 Azure 資源搭配使用。
- 將資料傳送至 Azure 監視器記錄的功能有限。

## <a name="telegraf-agent"></a>Telegraf 代理程式

[InfluxData Telegraf 代理程式](collect-custom-metrics-linux-telegraf.md)可用來收集 Linux 電腦的效能資料，以 Azure 監視器計量。

如果您需要，請使用 Telegraf 代理程式：

* 將資料傳送至[Azure 監視器計量](data-platform-metrics.md)以使用[計量瀏覽器](metrics-getting-started.md)進行分析，並利用近乎即時計量[警示](./alerts-metric-overview.md)和[自動](autoscale-overview.md)調整等功能（僅限 Linux）。 



## <a name="dependency-agent"></a>相依性代理程式

相依性代理程式會收集有關虛擬機器上執行之進程的探索資料，以及外部進程相依性。 

如果您需要，請使用 Dependency agent：

* 使用對應功能[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)或[服務對應](../insights/service-map.md)方案。

使用 Dependency agent 時，請考慮下列事項：

- 相依性代理程式需要在相同的虛擬機器上安裝 Log Analytics 代理程式。
- 在 Linux Vm 上，Log Analytics 代理程式必須安裝在 Azure 診斷擴充功能之前。

## <a name="extensions-compared-to-agents"></a>與代理程式相較之下的延伸模組

適用于[Windows](../../virtual-machines/extensions/oms-windows.md)和[Linux](../../virtual-machines/extensions/oms-linux.md)的 log analytics 擴充功能會在 Azure 虛擬機器上安裝 log analytics 代理程式。 適用于[Windows](../../virtual-machines/extensions/agent-dependency-windows.md)和[Linux](../../virtual-machines/extensions/agent-dependency-linux.md)的 Azure 監視器相依性延伸模組會在 Azure 虛擬機器上安裝 dependency agent。 這些是上述相同的代理程式，但可讓您透過[虛擬機器擴充](../../virtual-machines/extensions/overview.md)功能進行管理。 您應該盡可能使用延伸模組來安裝和管理代理程式。


## <a name="next-steps"></a>後續步驟

取得每個代理程式的詳細資料，如下所示：

- [Log Analytics 代理程式總覽](log-analytics-agent.md)
- [Azure 診斷延伸模組概觀](diagnostics-extension-overview.md)
- [使用 InfluxData Telegraf 代理程式收集 Linux VM 的自訂計量](collect-custom-metrics-linux-telegraf.md)

