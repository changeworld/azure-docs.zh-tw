---
title: Azure 監視器中的計量 |Microsoft Docs
description: 描述 Azure 監視器中的計量，這是輕量監視資料，能夠支援近乎即時的案例。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 80bbf83da17d833c4f8bb1abac9610d70e9a23cb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274823"
---
# <a name="metrics-in-azure-monitor"></a>Azure 監視器中的計量

> [!NOTE]
> Azure 監視器資料平臺是以兩種基本資料類型為基礎：計量和記錄。 本文描述計量。 請參閱[Azure 監視器中的記錄](data-platform-logs.md)，以取得記錄的詳細描述，以及[Azure 監視器資料平臺](data-platform.md)以進行這兩項比較。

Azure 監視器中的計量非常輕量，而且能夠支援近乎即時的案例，讓它們特別適用于警示和快速偵測問題。 本文說明計量的結構、您可以如何利用它們，以及識別將資料儲存在計量中的不同資料來源。

## <a name="what-are-metrics"></a>何謂度量？
計量為數值，描述系統在特定時間的某個方面。 計量會定期收集，適用于警示，因為它們可以頻繁地進行取樣，而且可以使用相對簡單的邏輯快速地引發警示。

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Azure 監視器計量可以做些什麼？
下表列出您可以在 Azure 監視器中使用度量資料的不同方式。

|  |  |
|:---|:---|
| 分析 | 使用[計量瀏覽器](metrics-charts.md)來分析圖表上所收集的度量，並比較不同資源的計量。 |
| 視覺化 | 將圖表從計量瀏覽器釘選到[Azure 儀表板](../learn/tutorial-app-dashboards.md)。<br>建立活頁[簿](../app/usage-workbooks.md)，以結合互動式報表中的多個資料集。將查詢的結果匯出至[Grafana](grafana-plugin.md) ，以利用其儀表板管理並與其他資料來源結合。 |
| 警示 | 設定計量[警示規則](alerts-metric.md)，以在計量值超過閾值時傳送通知或採取[自動化動作](action-groups.md)。 |
| 自動化 |  使用 [[自動](autoscale-overview.md)調整]，根據超出閾值的度量值來增加或減少資源。 |
| 匯出 | 將[計量路由傳送至記錄](resource-logs-collect-storage.md)，以分析 Azure 監視器計量中的資料以及 Azure 監視器記錄中的資料，並儲存超過93天的度量值。<br>將計量串流至[事件中樞](stream-monitoring-data-event-hubs.md)，以將其路由至外部系統。 |
| 擷取 | 使用[PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights)從命令列存取計量值<br>使用[REST API](rest-api-walkthrough.md)從自訂應用程式存取計量值。<br>使用[CLI](/cli/azure/monitor/metrics)從命令列存取計量值。 |
| 封存 | [封存](..//learn/tutorial-archive-data.md)資源的效能或健全狀況歷程記錄，以用於相容性、稽核或離線報告。 |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Azure 監視器計量中的資料如何結構化？
Azure 監視器計量所收集的資料會儲存在時間序列資料庫中，並已針對分析時間戳記的資料進行優化。 每組度量值都是具有下列屬性的時間序列：

* 收集值的時間
* 與值相關聯的資源
* 命名空間，其作用類似于度量的分類
* 度量名稱
* 值本身
* 某些計量可能會有多個維度，如[多維度計量](#multi-dimensional-metrics)中所述。 自訂計量最多可以有 10 個維度。

## <a name="multi-dimensional-metrics"></a>多維度計量
度量資料的其中一項挑戰是，它通常會有有限的資訊來提供所收集值的內容。 Azure 監視器利用多維度計量來解決這項挑戰。 計量的維度是成對的名稱和數值，其中含有額外的資料來說明計量值。 例如，_可用磁碟空間_的計量可能有一個稱為_磁片磁碟機_的維度，其值為_C：_ 、 _D：_ ，這可讓您跨所有磁片磁碟機或每個磁片磁碟機分別查看可用的磁碟空間。

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
使用[計量瀏覽器](metrics-charts.md)，以互動方式分析計量資料庫中的資料，並在一段時間後繪製多個度量的值。 您可以將圖表釘選到儀表板，以查看其他視覺效果。 您也可以使用 [Azure 監視 REST API](rest-api-walkthrough.md) 來擷取計量。

![計量瀏覽器](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Azure 監視器計量的來源
Azure 監視器所收集的計量有三個基本來源。 在 Azure 監視器計量資料庫中收集這些度量之後，不論來源為何，都可以一起進行評估。

**平台計量**是由 Azure 資源所建立，讓您能夠掌握其健康情況和效能。 每個類型的資源都會建立[一組不同的計量](metrics-supported.md)，而不需進行任何必要的設定。 除非在計量定義中另有指定，否則會以一分鐘頻率從 Azure 資源收集平臺計量。 

系統會從虛擬機器的客體作業系統收集**來賓 OS 計量**。 針對具有[Windows 診斷擴充功能（WAD）](../platform/diagnostics-extension-overview.md)的 windows 虛擬機器，以及使用[InfluxData Telegraf 代理程式](https://www.influxdata.com/time-series-platform/telegraf/)的 Linux 虛擬機器，啟用來賓 OS 計量。

**應用程式計量**是由 Application Insights 為您受監視的應用程式所建立，可協助您偵測效能問題，以及追蹤應用程式的使用趨勢。 這包括像是「伺服器回應時間」和「瀏覽器例外狀況」之類的值。

**自訂計量**是指您定義的計量，以及可自動使用的標準計量。 您可以在 Application Insights 監視的[應用程式中定義自訂計量](../app/api-custom-events-metrics.md)，或使用[自訂計量 API](metrics-store-custom-rest-api.md)建立 Azure 服務的自訂計量。

## <a name="retention-of-metrics"></a>計量的保留期
針對 Azure 中的大部分資源，計量會儲存93天。 有一些例外狀況：

**客體作業系統計量**
-   **傳統的來賓 OS 計量**。 這些是由[Windows 診斷擴充功能（WAD）](../platform/diagnostics-extension-overview.md)或[Linux 診斷擴充功能（LAD）](../../virtual-machines/extensions/diagnostics-linux.md)所收集並路由傳送至 Azure 儲存體帳戶的效能計數器。 這些計量的保留時間為14天。
-   **傳送至 Azure 監視器計量的客體作業系統計量**。 這些是由[Windows 診斷擴充功能（WAD）](diagnostics-extension-overview.md)所收集並傳送至[Azure 監視器資料接收器](diagnostics-extension-overview.md#data-destinations)，或透過 Linux 電腦上的[InfluxData Telegraf 代理程式](https://www.influxdata.com/time-series-platform/telegraf/)所收集的效能計數器。 這些計量的保留期為93天。
-   **Log Analytics 代理程式所收集的來賓 OS 計量**。 這些是 Log Analytics 代理程式所收集並傳送到 Log Analytics 工作區的效能計數器。 這些計量的保留期為31天，最多可延長2年。

**Application Insights 以記錄為基礎的計量**。 
- 在幕後，以[記錄為基礎的計量](../app/pre-aggregated-metrics-log-metrics.md)會轉譯成記錄查詢。 其保留會符合基礎記錄中事件的保留期。 針對 Application Insights 資源，記錄會儲存90天。


> [!NOTE]
> 您可以將[Azure 監視器資源的平臺計量傳送到 Log Analytics 工作區](resource-logs-collect-storage.md)，以進行長期趨勢分析。





## <a name="next-steps"></a>後續步驟

- 深入瞭解[Azure 監視器資料平臺](data-platform.md)。
- 深入瞭解[Azure 監視器中的記錄資料](data-platform-logs.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。
