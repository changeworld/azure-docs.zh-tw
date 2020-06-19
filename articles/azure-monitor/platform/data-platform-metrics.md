---
title: Azure 監視器中的計量 | Microsoft Docs
description: 描述 Azure 監視器中的計量，這是輕量監視資料，能夠支援近乎即時的案例。
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 23e4d104697b5b688330c6ab3a93beebf62f3c6a
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83799958"
---
# <a name="metrics-in-azure-monitor"></a>Azure 監視器中的計量

> [!NOTE]
> Azure 監視器資料平台的基礎是兩種基本的資料類型：計量和記錄。 本文描述的是計量。 如需記錄的詳細描述，請參閱 [Azure 監視器中的記錄](data-platform-logs.md)，如需兩者的比較，則請參閱 [Azure 監視器資料平台](data-platform.md)。

Azure 監視器中的計量非常輕量，能夠支援近乎即時的案例，因此特別適合用來警示和快速偵測問題。 本文會說明計量的結構化方式、其功用，以及如何識別將資料儲存在計量中的不同資料來源。

## <a name="what-are-metrics"></a>何謂度量？
計量為數值，描述系統在特定時間的某個方面。 計量會定期收集且適合用於警示，因為計量可頻繁地進行取樣，而且可使用相對較簡單的邏輯快速引發警示。

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Azure 監視器計量有何功用？
下表列出您可在 Azure 監視器中使用計量資料的各種方式。

|  |  |
|:---|:---|
| 分析 | 使用[計量瀏覽器](metrics-charts.md)在圖表上分析所收集的計量，並比較來自不同資源的計量。 |
| 視覺化 | 將圖表從計量瀏覽器釘選到 [Azure 儀表板](../learn/tutorial-app-dashboards.md)。<br>建立[活頁簿](../platform/workbooks-overview.md)，以在互動式報告中與多組資料結合。將查詢的結果匯出至 [Grafana](grafana-plugin.md) 以利用其儀表板功能，並與其他資料來源結合。 |
| 警示 | 設定[計量警示規則](alerts-metric.md)，在計量值超出閾值時，傳送通知或採取[自動化動作](action-groups.md)。 |
| 自動化 |  使用[自動調整規模](autoscale-overview.md)，根據超出閾值的計量值來增加或減少資源。 |
| 匯出 | [將計量路由傳送到記錄](resource-logs-collect-storage.md)，來分析 Azure 監視器計量中的資料與 Azure 監視器記錄中的資料，以及儲存計量值超過 93 天。<br>將計量串流至[事件中樞](stream-monitoring-data-event-hubs.md)以將計量路由傳送至外部系統。 |
| 擷取 | 使用 [PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights) 從命令列存取計量值<br>使用 [REST API](rest-api-walkthrough.md) 從自訂應用程式存取計量值。<br>使用 [CLI](/cli/azure/monitor/metrics) 從命令列存取計量值。 |
| 封存 | [封存](..//learn/tutorial-archive-data.md)資源的效能或健全狀況歷程記錄，以用於相容性、稽核或離線報告。 |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>資料如何在 Azure 監視器計量中結構化？
Azure 監視器計量所收集的資料會儲存在時間序列資料庫中，其已進行最佳化而能分析有時間戳記的資料。 每組計量值都是具有下列屬性的時間序列：

* 收集到值的時間
* 與值相關聯的資源
* 作用類似計量類別的命名空間
* 計量名稱
* 值本身
* 某些計量可能具有多個維度，如[多維度計量](#multi-dimensional-metrics)所述。 自訂計量最多可以有 10 個維度。

## <a name="multi-dimensional-metrics"></a>多維度計量
計量資料的其中一項挑戰是，其可用來為所收集到的值提供內容的資訊往往很有限。 Azure 監視器利用多維度計量來解決這項挑戰。 計量的維度是成對的名稱和數值，其中含有額外的資料來說明計量值。 例如，「可用磁碟空間」計量可以有名為「磁碟機」且值為 _C:_ 、_D:_ 的維度，讓您能夠檢視所有磁碟機或每個磁碟機的可用磁碟空間。

下列範例說明適用於名為「網路輸送量」之假設性計量的兩個資料集。 第一個資料集沒有維度。 第二個資料集會顯示具有兩個維度 (_IP 位址_與_方向_) 的值：

### <a name="network-throughput"></a>網路輸送量

| 時間戳記     | 計量值 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kbps |
| 8/9/2017 8:15 | 1,141.4 Kbps |
| 8/9/2017 8:16 | 1,110.2 Kbps |

這個無維度計量只能回答像是「我的網路輸送量在指定時間為何」這種基本問題。

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>網路輸送量 + 兩個維度 (「IP」與「方向」)

| 時間戳記     | 維度「IP」   | 維度「方向」 | 計量值|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | 方向="傳送"    | 646.5 Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | 方向="接收" | 420.1 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 方向="傳送"    | 150.0 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 方向="接收" | 115.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 方向="傳送"    | 515.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 方向="接收" | 371.1 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 方向="傳送"    | 155.0 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 方向="接收" | 100.1 Kbps |

此計量可以回答像是「每個 IP 位址的網路輸送量為何」與「資料的傳送與接收比為何」這種問題。 相較於無維度計量，多維度計量具有分析與診斷值。

## <a name="interacting-with-azure-monitor-metrics"></a>與 Azure 監視器計量互動
使用[計量瀏覽器](metrics-charts.md)，以互動方式分析計量資料庫中的資料，並隨著時間繪製多個計量值的圖表。 您可以將圖表釘選到儀表板，以使用其他視覺效果來檢視圖表。 您也可以使用 [Azure 監視 REST API](rest-api-walkthrough.md) 來擷取計量。

![計量瀏覽器](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Azure 監視器計量的來源
Azure 監視器所收集的計量有三個基本來源。 在 Azure 監視器計量資料庫中收集這些計量之後，不論其來源為何，您都可以將其放在一起進行評估。

**平台計量**是由 Azure 資源所建立，讓您能夠掌握其健康情況和效能。 每個類型的資源都會建立[一組不同的計量](metrics-supported.md)，而不需進行任何必要的設定。 除非計量定義中另有指定，否則會以一分鐘的頻率從 Azure 資源收集平台計量。 

系統會從虛擬機器的客體作業系統收集**客體 OS 計量**。 請為具有 [Windows 診斷擴充功能 (WAD)](../platform/diagnostics-extension-overview.md) 的 Windows 虛擬機器和具有 [InfluxData Telegraf 代理程式](https://www.influxdata.com/time-series-platform/telegraf/)的 Linux 虛擬機器，啟用客體 OS 計量。

**應用程式計量**是由 Application Insights 為您受監視的應用程式所建立，可協助您偵測效能問題，以及追蹤應用程式的使用趨勢。 這包括像是「伺服器回應時間」和「瀏覽器例外狀況」之類的值。

**自訂計量**是除了自動可供使用的標準計量之外您所定義的計量。 您可以[在應用程式中定義自訂計量](../app/api-custom-events-metrics.md) (由 Application Insights 加以監視)，或使用[自訂計量 API](metrics-store-custom-rest-api.md) 為 Azure 服務建立自訂計量。

## <a name="retention-of-metrics"></a>計量的保留期
針對 Azure 中的大部分資源，計量會儲存 93 天。 但有一些例外狀況：

**客體 OS 計量**
-   **傳統的客體 OS 計量**。 這些是由 [Windows 診斷擴充功能 (WAD)](../platform/diagnostics-extension-overview.md) 或 [Linux 診斷擴充功能 (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) 所收集並路由傳送至 Azure 儲存體帳戶的效能計數器。 這些計量的保留期為 14 天。
-   **傳送至 Azure 監視器計量的客體 OS 計量**。 這些是由 [Windows 診斷擴充功能 (WAD)](diagnostics-extension-overview.md) 所收集並傳送至 [Azure 監視器資料接收器](diagnostics-extension-overview.md#data-destinations)的效能計數器，而在 Linux 機器上，則是透過 [InfluxData Telegraf 代理程式](https://www.influxdata.com/time-series-platform/telegraf/)。 這些計量的保留期為 93 天。
-   **Log Analytics 代理程式所收集的客體 OS 計量**。 這些是 Log Analytics 代理程式所收集並傳送至 Log Analytics 工作區的效能計數器。 這些計量的保留期為 31 天，最多可延長為 2 年。

**Application Insights 記錄型的計量**。 
- 在幕後，[記錄型計量](../app/pre-aggregated-metrics-log-metrics.md)會轉譯為記錄查詢。 其保留期會符合基礎記錄中的事件保留期。 若為 Application Insights 資源，記錄會儲存 90 天。


> [!NOTE]
> 您可以[將 Azure 監視器資源的平台計量傳送至 Log Analytics 工作區](resource-logs-collect-storage.md)以進行長期趨勢分析。





## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器資料平台](data-platform.md)。
- 了解 [Azure 監視器中的記錄資料](data-platform-logs.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。
