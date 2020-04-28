---
title: 包含檔案
description: 包含檔案
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: feea9696316723a2750be6fc1e13001224320324
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865611"
---
隨著 Azure 中託管的 VM 的顯著增長,確定影響其支援的應用程式和基礎結構服務的性能和運行狀況問題非常重要。 默認情況下,基本監視通過由主機虛擬機管理程式收集的 CPU 使用方式、磁碟利用率、記憶體利用率和網路流量的指標類型隨 Azure 一起提供。 可以使用[擴展](../articles/virtual-machines/windows/extensions-features.md)來收集其他指標和日誌數據,以配置來自來賓操作系統的 VM 上的診斷。

為了檢測和幫助診斷在 VM 內運行的來賓作業系統、基於 .NET 或 Java Web 應用程式元件的性能和運行狀況問題,Azure 監視器提供集中式監視,包括 Azure 監視器(VM 和應用程式見解)等功能。

## <a name="diagnostics-and-metrics"></a>診斷和計量 

您可以在 Azure 入口網站、Azure CLI、Azure PowerShell 和應用程式設計介面 (API) 中使用[計量](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md)，設定和監視[診斷資料](https://docs.microsoft.com/cli/azure/vm/diagnostics)的收集。 例如，您可以：

- **觀察 VM 的基本計量。** 在 Azure 入口網站的 [概觀] 畫面上，顯示的基本計量包括 CPU 使用量、網路使用量、磁碟位元組總計，以及每秒的磁碟作業。

- **啟用開機診斷的收集，並使用 Azure 入口網站進行檢視。** 將自己的映像送至 Azure 或甚至啟動其中一個平台映像時，VM 進入不可開機狀態的原因有很多。 在 [設定] 畫面的 [監視] 區段之下按一下 [啟用]**** 開機診斷，即可在建立 VM 時輕鬆地啟用開機診斷。

    當 VM 開機，開機診斷代理程式會擷取開機輸出，並儲存在 Azure 儲存體。 這項資料可以用於 VM 開機問題的疑難排解。 當您從命令列工具建立 VM 時，不會自動啟用開機診斷。 啟用開機診斷之前，需要建立儲存體帳戶用來儲存開機記錄。 如果您在 Azure 入口網站中啟用開機診斷，系統會自動為您建立儲存體帳戶。

    如果您未在建立 VM 時啟用開機診斷，您稍後一律可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic) 或 [Azure Resource Manager 範本](../articles/virtual-machines/windows/extensions-diagnostics-template.md)來啟用它。

- **啟用客體 OS 診斷資料的收集。** 當您建立 VM 時，您有機會在 [設定] 畫面上啟用客體 OS 診斷。 當您啟用診斷資料的收集時，[適用於 Linux 的 IaaSDiagnostics 擴充功能](../articles/virtual-machines/linux/diagnostic-extension.md)或[適用於 Windows 的 IaaSDiagnostics 擴充功能](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)會新增至 VM，以便您收集其他磁碟、CPU 和記憶體資料。

    使用所收集的診斷資料，即可設定 VM 的自動調整。 還可以配置[Azure 監視器日誌](../articles/azure-monitor/platform/data-platform-logs.md)以儲存數據並設置警報,以便知道性能何時不正確。

## <a name="alerts"></a>警示

您可以根據特定效能指標建立[警示](../articles/azure-monitor/platform/alerts-overview.md)。 舉例來說，當平均 CPU 使用量超過特定臨界值，或可用的磁碟空間低於特定數量時，您即可接獲問題的警示。 警示可以在[Azure 門戶](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal)中設定,使用[Azure 資源管理器樣本](../articles/azure-monitor/platform/alerts-metric-create-templates.md)或[Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli)。

## <a name="azure-service-health"></a>Azure 服務健康狀態

[Azure 服務健康情況](../articles/service-health/service-health-overview.md)提供個人化的指導方針並在 Azure 服務問題影響到您時提供支援，同時協助您準備即將進行的已規劃維護。 「Azure 服務健康情況」會使用有目標且有彈性的通知來警示您和您的小組。

## <a name="azure-resource-health"></a>Azure 資源健康狀態

[Azure 資源健康情況](../articles/service-health/resource-health-overview.md)可協助您進行診斷，並在 Azure 問題影響您的資源時取得支援。 它會通知您資源的目前及過去的健康狀態，並協助您解決問題。 資源健康狀態會在您需要解決 Azure 服務問題時提供技術支援。

## <a name="azure-activity-log"></a>Azure 活動記錄檔

[Azure 活動記錄](../articles/azure-monitor/platform/platform-logs-overview.md)是訂用帳戶記錄，可讓您深入探索 Azure 中發生的訂用帳戶層級事件。 此記錄包含的資料範圍，從 Azure Resource Manager 作業資料到「服務健康情況」事件的更新。 您可以在 Azure 入口網站中按一下活動記錄，以檢視 VM 的記錄。

您可以利用活動記錄進行的事項包括：

- 在[作用紀錄事件上建立警報](../articles/azure-monitor/platform/platform-logs-overview.md)。
- [將其流式傳輸到事件中心](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md),以便由第三方服務或自定義分析解決方案(如 Power BI)引入。
- 使用[Power BI 內容包](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)在 Power BI 中分析它。
- [將其保存到存儲帳戶以](../articles/azure-monitor/platform/archive-activity-log.md)進行存檔或手動檢查。 您可以使用記錄設定檔來指定保留時間 (以天為單位)。

您也可以使用 [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/)、[Azure CLI](https://docs.microsoft.com/cli/azure/monitor) 或[監視器 REST API](https://docs.microsoft.com/rest/api/monitor/)，存取活動記錄資料。

[Azure 資源日誌](../articles/azure-monitor/platform/platform-logs-overview.md)是 VM 發出的日誌,提供有關其操作的豐富、頻繁的數據。 資源日誌與活動日誌不同,提供有關在 VM 中執行的操作的見解。

您可以利用診斷記錄進行的事項包括：

- [將它們保存到存儲帳戶以](../articles/azure-monitor/platform/archive-diagnostic-logs.md)進行審核或手動檢查。 您可以使用 [資源診斷設定] 來指定保留時間 (以天為單位)。
- [將它們流式傳輸到事件中心](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md),以便由第三方服務或自定義分析解決方案(如 Power BI)引入。
- 使用[日誌分析](../articles/log-analytics/log-analytics-azure-storage.md)分析分析它們。

## <a name="advanced-monitoring"></a>進階監視

有關 Azure VM 和虛擬機器規模集支援的應用程式或服務的可見性,請辨識 VM 中執行的來賓作業系統或工作負載的問題,以瞭解其是否影響應用程式的可用性或效能,或者應用程式有問題,請同時啟用[Azure 監視器 VM](../articles/azure-monitor/insights/vminsights-overview.md)和[應用程式見解](../articles/azure-monitor/app/app-insights-overview.md)。

VM 的 Azure 監視器透過分析 Windows 和 Linux VM 的效能和執行狀況(包括它發現的其他資源和外部進程的不同進程和互連依賴關係)來大規模監視 Azure 虛擬機 (VM)。 它包括幾個趨勢性能圖表,用於在調查問題和評估 VM 的能力時提供説明。 依賴項映射顯示受監視和未受監視的計算機、進程和這些計算機之間的失敗和活動網路連接,並顯示具有標準網路連接指標的趨勢圖表。 結合應用程式見解,您可以監視應用程式並捕獲遙測數據(如 HTTP 請求、異常等),以便您可以關聯 VM 和應用程式之間的問題。 設定[Azure 監視器警報](../articles/azure-monitor/platform/alerts-overview.md),以提醒您從 Azure 監視器為 VM 收集的監視數據偵測到的重要條件。

## <a name="next-steps"></a>後續步驟

- 逐步執行[使用 Azure PowerShell 監視 Windows 虛擬機器](../articles/virtual-machines/windows/tutorial-monitoring.md)或[使用 Azure CLI 監視 Linux 虛擬機器](../articles/virtual-machines/linux/tutorial-monitoring.md)中的步驟。

- 深入了解[監視和診斷](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)的最佳做法。
