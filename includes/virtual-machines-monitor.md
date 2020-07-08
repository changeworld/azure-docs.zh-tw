---
title: 包含檔案
description: 包含檔案
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: feea9696316723a2750be6fc1e13001224320324
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81865611"
---
隨著裝載于 Azure 中的 Vm 大幅成長，請務必找出影響應用程式和支援的基礎結構服務的效能和健康情況問題。 根據預設，基本監視會根據計量類型 [CPU 使用量]、[磁片使用量]、[記憶體使用率] 和主機程式管理器所收集的網路流量，以提供 Azure。 您可以使用[擴充](../articles/virtual-machines/windows/extensions-features.md)功能來收集其他計量和記錄檔資料，以在您的 vm 上設定來自客體作業系統的診斷。

若要偵測並協助診斷在 VM 內執行的客體作業系統、.NET 型或 JAVA web 應用程式元件的效能和健全狀況問題，Azure 監視器透過適用於 VM 的 Azure 監視器和 Application Insights 等完整功能來提供集中式監視。

## <a name="diagnostics-and-metrics"></a>診斷和計量 

您可以在 Azure 入口網站、Azure CLI、Azure PowerShell 和應用程式設計介面 (API) 中使用[計量](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md)，設定和監視[診斷資料](https://docs.microsoft.com/cli/azure/vm/diagnostics)的收集。 例如，您可以：

- **觀察 VM 的基本計量。** 在 Azure 入口網站的 [概觀] 畫面上，顯示的基本計量包括 CPU 使用量、網路使用量、磁碟位元組總計，以及每秒的磁碟作業。

- **啟用開機診斷的收集，並使用 Azure 入口網站進行檢視。** 將自己的映像送至 Azure 或甚至啟動其中一個平台映像時，VM 進入不可開機狀態的原因有很多。 在 [設定] 畫面的 [監視] 區段之下按一下 [啟用]**** 開機診斷，即可在建立 VM 時輕鬆地啟用開機診斷。

    當 VM 開機，開機診斷代理程式會擷取開機輸出，並儲存在 Azure 儲存體。 這項資料可以用於 VM 開機問題的疑難排解。 當您從命令列工具建立 VM 時，不會自動啟用開機診斷。 啟用開機診斷之前，需要建立儲存體帳戶用來儲存開機記錄。 如果您在 Azure 入口網站中啟用開機診斷，系統會自動為您建立儲存體帳戶。

    如果您未在建立 VM 時啟用開機診斷，您稍後一律可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic) 或 [Azure Resource Manager 範本](../articles/virtual-machines/windows/extensions-diagnostics-template.md)來啟用它。

- **啟用客體 OS 診斷資料的收集。** 當您建立 VM 時，您有機會在 [設定] 畫面上啟用客體 OS 診斷。 當您啟用診斷資料的收集時，[適用於 Linux 的 IaaSDiagnostics 擴充功能](../articles/virtual-machines/linux/diagnostic-extension.md)或[適用於 Windows 的 IaaSDiagnostics 擴充功能](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)會新增至 VM，以便您收集其他磁碟、CPU 和記憶體資料。

    使用所收集的診斷資料，即可設定 VM 的自動調整。 您也可以設定[Azure 監視器記錄](../articles/azure-monitor/platform/data-platform-logs.md)來儲存資料，並設定警示，讓您知道效能不是正確的時機。

## <a name="alerts"></a>警示

您可以根據特定效能計量來建立[警示](../articles/azure-monitor/platform/alerts-overview.md)。 舉例來說，當平均 CPU 使用量超過特定臨界值，或可用的磁碟空間低於特定數量時，您即可接獲問題的警示。 您可以使用[Azure Resource Manager 範本](../articles/azure-monitor/platform/alerts-metric-create-templates.md)或[Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli)，在[Azure 入口網站](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal)中設定警示。

## <a name="azure-service-health"></a>Azure 服務健康狀態

[Azure 服務健康情況](../articles/service-health/service-health-overview.md)提供個人化的指導方針並在 Azure 服務問題影響到您時提供支援，同時協助您準備即將進行的已規劃維護。 「Azure 服務健康情況」會使用有目標且有彈性的通知來警示您和您的小組。

## <a name="azure-resource-health"></a>Azure 資源健康狀態

[Azure 資源健康情況](../articles/service-health/resource-health-overview.md)可協助您進行診斷，並在 Azure 問題影響您的資源時取得支援。 它會通知您資源的目前及過去的健康狀態，並協助您解決問題。 資源健康狀態會在您需要解決 Azure 服務問題時提供技術支援。

## <a name="azure-activity-log"></a>Azure 活動記錄檔

[Azure 活動記錄](../articles/azure-monitor/platform/platform-logs-overview.md)是訂用帳戶記錄，可讓您深入探索 Azure 中發生的訂用帳戶層級事件。 此記錄包含的資料範圍，從 Azure Resource Manager 作業資料到「服務健康情況」事件的更新。 您可以在 Azure 入口網站中按一下活動記錄，以檢視 VM 的記錄。

您可以利用活動記錄進行的事項包括：

- 建立[活動記錄事件的警示](../articles/azure-monitor/platform/platform-logs-overview.md)。
- 將[它串流至事件中樞](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md)，以供協力廠商服務或自訂分析解決方案（例如 Power BI）進行內嵌。
- 使用[Power BI 內容套件](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)，在 Power BI 中分析它。
- [將它儲存到儲存體帳戶](../articles/azure-monitor/platform/archive-activity-log.md)以供封存或手動檢查。 您可以使用記錄設定檔來指定保留時間 (以天為單位)。

您也可以使用 [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/)、[Azure CLI](https://docs.microsoft.com/cli/azure/monitor) 或[監視器 REST API](https://docs.microsoft.com/rest/api/monitor/)，存取活動記錄資料。

[Azure 資源記錄](../articles/azure-monitor/platform/platform-logs-overview.md)是您的 VM 所發出的記錄，可提供有關其作業的豐富、經常性資料。 資源記錄與活動記錄的差異，在於提供在 VM 內執行之作業的深入解析。

您可以利用診斷記錄進行的事項包括：

- [將它們儲存到儲存體帳戶](../articles/azure-monitor/platform/archive-diagnostic-logs.md)，以進行審核或手動檢查。 您可以使用 [資源診斷設定] 來指定保留時間 (以天為單位)。
- [將它們串流至事件中樞](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md)，以供協力廠商服務或自訂分析解決方案（例如 Power BI）進行內嵌。
- 使用[Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md)進行分析。

## <a name="advanced-monitoring"></a>進階監視

如需 Azure VM 和虛擬機器擴展集所支援的應用程式或服務的可見度，請識別 VM 中執行的虛擬作業系統或工作負載問題，以瞭解其是否會影響應用程式的可用性或效能，或是應用程式的問題，同時啟用[適用於 VM 的 Azure 監視器](../articles/azure-monitor/insights/vminsights-overview.md)和[Application Insights](../articles/azure-monitor/app/app-insights-overview.md)。

適用於 VM 的 Azure 監視器藉由分析 Windows 和 Linux Vm 的效能和健康情況來大規模監視您的 Azure 虛擬機器（VM），包括不同的進程，以及它探索到的其他資源和外部進程的相互關聯相依性。 其中包含數個趨勢效能圖表，可協助調查問題及評估 Vm 的容量。 相依性對應會顯示監視和未受監控的電腦、進程與這些電腦之間的失敗和作用中網路連線，並顯示具有標準網路連線計量的趨勢圖表。 與 Application Insights 結合，您可以監視應用程式並捕捉遙測資料，例如 HTTP 要求、例外狀況等等，讓您可以將 Vm 與應用程式之間的問題相互關聯。 設定[Azure 監視器警示](../articles/azure-monitor/platform/alerts-overview.md)，以在從適用於 VM 的 Azure 監視器所收集的監視資料中偵測到的重要條件發出警示。

## <a name="next-steps"></a>下一步

- 逐步執行[使用 Azure PowerShell 監視 Windows 虛擬機器](../articles/virtual-machines/windows/tutorial-monitoring.md)或[使用 Azure CLI 監視 Linux 虛擬機器](../articles/virtual-machines/linux/tutorial-monitoring.md)中的步驟。

- 深入了解[監視和診斷](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)的最佳做法。
