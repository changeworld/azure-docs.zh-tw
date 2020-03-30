---
title: Azure 監視代理程式概觀 | Microsoft Docs
description: 本文將詳細說明可支援對裝載於 Azure 或混合式環境中的虛擬機器進行監視的 Azure 代理程式。
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249083"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure 監視器代理概述

虛擬機器和其他計算資源需要代理收集監視資料，以測量其客體作業系統和工作負載的性能和可用性。 本文介紹了 Azure 監視器使用的代理，並説明您確定需要滿足特定環境要求。

> [!NOTE]
> Azure 監視器當前有多個代理，因為最近合併了 Azure 監視器和日誌分析。 雖然它們的功能可能有重疊，但每個功能都有獨特的功能。 根據您的要求，您可能需要虛擬機器上的一個或多個代理。 

您可能有一組特定的要求，這些要求不能由特定虛擬機器的單個代理完全滿足。 例如，您可能希望使用需要 Azure 診斷擴展的指標警報，但還希望利用 Azure 監視器的功能來執行需要日誌分析代理和依賴項代理的 VM 功能。 在這種情況下，您可以使用多個代理，對於需要每個代理的功能的客戶來說，這是一個常見方案。

## <a name="summary-of-agents"></a>代理摘要

下表提供了 Windows 和 Linux 的 Azure 監視器代理的快速比較。 有關每個部分的進一步詳細資訊，請參閱以下部分。 

### <a name="windows-agents"></a>Windows 代理程式

| | 診斷<br>擴展 （WAD） | Log Analytics<br>代理程式 | 相依性<br>代理程式 |
|:---|:---|:---|:---|
| 支援環境 | Azure | Azure<br>其他雲<br>內部部署 | Azure<br>其他雲<br>內部部署 | 
| 代理要求  | None | None | 需要日誌分析代理 |
| 收集的資料 | 事件記錄<br>ETW 事件<br>效能<br>基於檔的日誌<br>IIS 記錄<br>.NET 應用日誌<br>損毀傾印<br>代理診斷日誌 | 事件記錄<br>效能<IIS logs><br>基於檔的日誌<br>見解和解決方案<br>其他服務 | 過程詳細資訊和依賴項<br>網路連接指標 |
| 發送到的資料 | Azure 儲存體<br>Azure 監視器指標<br>事件中樞 | Azure 監視器記錄 | Azure 監視器記錄 |


### <a name="linux-agents"></a>Linux 代理程式

| | 診斷<br>擴展（LAD） | 泰拉夫<br>代理程式 | Log Analytics<br>代理程式 | 相依性<br>代理程式 |
|:---|:---|:---|:---|:---|
| 支援環境 | Azure | Azure<br>其他雲<br>內部部署 | Azure<br>其他雲<br>內部部署 | Azure<br>其他雲<br>內部部署 |
| 代理要求  | None | None | None | 需要日誌分析代理 |
| 收集的資料 | syslog<br>效能 | 效能 | syslog<br>效能| 過程詳細資訊和依賴項<br>網路連接指標 |
| 發送到的資料 | Azure 儲存體<br>事件中樞 | Azure 監視器指標 | Azure 監視器記錄 | Azure 監視器記錄 |

## <a name="log-analytics-agent"></a>Log Analytics 代理程式

[日誌分析代理](log-analytics-agent.md)從 Azure、其他雲供應商和本地虛擬機器的客體作業系統和工作負荷收集監視資料。 它將資料收集到日誌分析工作區中。 日誌分析代理是系統中心操作管理器使用的代理，您可以多家庭代理電腦與管理組和 Azure 監視器同時通信。 Azure 監視器中的某些見解和解決方案也需要此代理。


> [!NOTE]
> Windows 的日誌分析代理通常稱為 Microsoft 監視代理 （MMA）。 Linux 的日誌分析代理通常稱為 OMS 代理。



如果需要以下情況，請使用日誌分析代理：

* 從 Azure 外部的虛擬或物理電腦收集日誌和效能資料。 
* 將資料發送到日誌分析工作區，以利用[Azure 監視器日誌](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)支援的功能，如[日誌查詢](../log-query/log-query-overview.md)。
* 對 VM 使用[Azure 監視器](../insights/vminsights-overview.md)，它允許您大規模監視虛擬機器，並監視虛擬機器對其他資源和外部進程的依賴關係。  
* 使用[Azure 安全中心](../../security-center/security-center-intro.md)或[Azure 哨兵](../../sentinel/overview.md)管理虛擬機器的安全性。
* 使用[Azure 自動化更新管理](../../automation/automation-update-management.md)[、Azure 自動化狀態配置](../../automation/automation-dsc-overview.md)或[Azure 自動化更改跟蹤和清單](../../automation/change-tracking.md)來全面管理 Azure VM
* 使用不同的[解決方案](../monitor-reference.md#insights-and-core-solutions)來監視特定的服務或應用程式。

日誌分析代理的限制包括：

- 無法將資料發送到 Azure 監視器指標、Azure 存儲或 Azure 事件中心。

## <a name="azure-diagnostics-extension"></a>Azure 診斷延伸模組

[Azure 診斷擴展](diagnostics-extension-overview.md)從 Azure 虛擬機器和其他計算資源的客體作業系統和工作負荷收集監視資料。 它主要將資料收集到 Azure 存儲中，但也允許您定義資料接收器，以便將資料也發送到其他目標，如 Azure 監視器指標和 Azure 事件中心。

如果需要：使用 Azure 診斷擴展：

- 將資料發送到 Azure 存儲以進行存檔，或使用[Azure 存儲資源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)等工具對其進行分析。
- 將資料發送到[Azure 監視器指標](data-platform-metrics.md)，以便使用[指標資源管理器](metrics-getting-started.md)進行分析，並利用近即時[指標警報](../../azure-monitor/platform/alerts-metric-overview.md)和[自動縮放](autoscale-overview.md)（僅限 Windows）等功能。
- 使用[Azure 事件中心](diagnostics-extension-stream-event-hubs.md)將資料發送到協力廠商工具。
- 收集[啟動診斷](../../virtual-machines/troubleshooting/boot-diagnostics.md)以調查 VM 啟動問題。

Azure 診斷擴展的限制包括：

- 只能與 Azure 資源一起使用。
- 將資料發送到 Azure 監視器日誌的能力有限。

## <a name="telegraf-agent"></a>泰拉夫特工

[Influx 資料 Telegraf 代理](collect-custom-metrics-linux-telegraf.md)用於從 Linux 電腦到 Azure 監視器指標收集效能資料。

如果您需要：

* 將資料發送到[Azure 監視器指標](data-platform-metrics.md)，以便使用[指標資源管理器](metrics-getting-started.md)進行分析，並利用近即時[指標警報](../../azure-monitor/platform/alerts-metric-overview.md)和[自動縮放](autoscale-overview.md)（僅限 Linux）等功能。 



## <a name="dependency-agent"></a>相依性代理程式

依賴項代理收集有關在虛擬機器上運行的進程和外部進程依賴項的已發現資料。 

如果需要以下任務，請使用依賴項代理：

* 使用 VM 的"地圖"功能[Azure 監視器](../insights/vminsights-overview.md)[或服務映射](../insights/service-map.md)解決方案。

使用依賴項代理時請考慮以下事項：

- 依賴項代理要求在同一虛擬機器上安裝日誌分析代理。
- 在 Linux VM 上，日誌分析代理必須在 Azure 診斷擴展之前安裝。

## <a name="extensions-compared-to-agents"></a>與代理相比的擴展

[Windows](../../virtual-machines/extensions/oms-windows.md)和[Linux](../../virtual-machines/extensions/oms-linux.md)的日誌分析擴展在 Azure 虛擬機器上安裝日誌分析代理。 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md)和[Linux](../../virtual-machines/extensions/agent-dependency-linux.md)的 Azure 監視器依賴項擴展在 Azure 虛擬機器上安裝依賴項代理。 這些代理與上述代理相同，但允許您通過[虛擬機器擴展](../../virtual-machines/extensions/overview.md)來管理它們。 應盡可能使用擴展來安裝和管理代理。


## <a name="next-steps"></a>後續步驟

獲取有關每個代理的更多詳細資訊，請瞭解以下內容：

- [日誌分析代理概述](log-analytics-agent.md)
- [Azure 診斷擴展概述](diagnostics-extension-overview.md)
- [使用 InfluxData Telegraf 代理程式收集 Linux VM 的自訂計量](collect-custom-metrics-linux-telegraf.md)
