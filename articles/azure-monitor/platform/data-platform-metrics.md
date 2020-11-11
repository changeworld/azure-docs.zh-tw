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
ms.openlocfilehash: f64a91e3b285c265296c361366a10443eda18201
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489401"
---
# <a name="azure-monitor-metrics-overview"></a>Azure 監視器計量總覽
Azure 監視器計量是 Azure 監視器的功能，可從 [受監視的資源](../monitor-reference.md) 將數值資料收集到時間序列資料庫中。 計量是以固定間隔收集的數值，並描述系統在特定時間的某些層面。 Azure 監視器中的計量非常輕量，能夠支援近乎即時的案例，因此特別適合用來警示和快速偵測問題。 您可以使用計量瀏覽器以互動方式進行分析，並在值超出臨界值時主動通知警示，或在活頁簿或儀表板中將其視覺化。


> [!NOTE]
> Azure 監視器計量是支援 Azure 監視器的資料平臺的一半。 另一個是 [Azure 監視器記錄](data-platform-logs.md) 檔，它會收集和組織記錄檔和效能資料，並允許使用豐富的查詢語言進行分析。 計量比 Azure 監視器記錄中的資料更輕量，而且能夠支援近乎即時的案例，因此特別適用于警示和快速偵測問題。 不過，計量只能將數值資料儲存在特定結構中，而記錄可以儲存各種不同的資料類型，每個資料類型都有自己的結構。 您也可以使用無法用於分析計量資料的記錄查詢，對記錄資料執行複雜的分析。


## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Azure 監視器計量有何功用？
下表列出您可以在 Azure 監視器中使用計量的不同方式。

|  |  |
|:---|:---|
| **分析** | 使用[計量瀏覽器](metrics-charts.md)在圖表上分析所收集的計量，並比較來自不同資源的計量。 |
| **警示** | 設定[計量警示規則](alerts-metric.md)，在計量值超出閾值時，傳送通知或採取[自動化動作](action-groups.md)。 |
| **視覺化** | 將圖表從計量瀏覽器釘選到 [Azure 儀表板](../learn/tutorial-app-dashboards.md)。<br>建立[活頁簿](./workbooks-overview.md)，以在互動式報告中與多組資料結合。將查詢的結果匯出至 [Grafana](grafana-plugin.md) 以利用其儀表板功能，並與其他資料來源結合。 |
| **自動化** |  使用[自動調整規模](autoscale-overview.md)，根據超出閾值的計量值來增加或減少資源。 |
| **擷取** | 使用 [PowerShell Cmdlet](/powershell/module/az.applicationinsights) 從命令列存取計量值<br>使用 [REST API](rest-api-walkthrough.md) 從自訂應用程式存取計量值。<br>使用 [CLI](/cli/azure/monitor/metrics) 從命令列存取計量值。 |
| **匯出** | [將計量路由傳送到記錄](./resource-logs.md#send-to-azure-storage)，來分析 Azure 監視器計量中的資料與 Azure 監視器記錄中的資料，以及儲存計量值超過 93 天。<br>將計量串流至[事件中樞](stream-monitoring-data-event-hubs.md)以將計量路由傳送至外部系統。 |
| **封存** | [封存](./platform-logs-overview.md)資源的效能或健全狀況歷程記錄，以用於相容性、稽核或離線報告。 |

![計量概觀](media/data-platform-metrics/metrics-overview.png)


## <a name="data-collection"></a>資料集合
Azure 監視器所收集的計量有三個基本來源。 在 Azure 監視器計量資料庫中收集這些計量之後，不論其來源為何，您都可以將其放在一起進行評估。

**Azure 資源** 。 平台計量是由 Azure 資源所建立，讓您能夠掌握其健康情況和效能。 每個類型的資源都會建立[一組不同的計量](metrics-supported.md)，而不需進行任何必要的設定。 除非計量定義中另有指定，否則會以一分鐘的頻率從 Azure 資源收集平台計量。 

[應用程式]。 計量是由受監視應用程式的 Application Insights 所建立，可協助您偵測效能問題，並追蹤應用程式使用方式的趨勢。 這包括像是「伺服器回應時間」和「瀏覽器例外狀況」之類的值。

**虛擬機器代理** 程式。 系統會從虛擬機器的客體作業系統收集計量。 請為具有 [Windows 診斷擴充功能 (WAD)](./diagnostics-extension-overview.md) 的 Windows 虛擬機器和具有 [InfluxData Telegraf 代理程式](https://www.influxdata.com/time-series-platform/telegraf/)的 Linux 虛擬機器，啟用客體 OS 計量。

**自訂計量** 。 除了自動提供的標準計量之外，您還可以定義度量。 您可以[在應用程式中定義自訂計量](../app/api-custom-events-metrics.md) (由 Application Insights 加以監視)，或使用[自訂計量 API](metrics-store-custom-rest-api.md) 為 Azure 服務建立自訂計量。

- 如需可將資料傳送至 Azure 監視器計量的完整資料來源清單，請參閱 [Azure 監視器所監視的內容](../monitor-reference.md) 。

## <a name="metrics-explorer"></a>計量瀏覽器
使用[計量瀏覽器](metrics-charts.md)，以互動方式分析計量資料庫中的資料，並隨著時間繪製多個計量值的圖表。 您可以將圖表釘選到儀表板，以使用其他視覺效果來檢視圖表。 您也可以使用 [Azure 監視 REST API](rest-api-walkthrough.md) 來擷取計量。

![計量瀏覽器](media/data-platform/metrics-explorer.png)

- 若要開始使用計量瀏覽器，請參閱 [Azure 監視器計量瀏覽器](metrics-getting-started.md) 使用者入門。

## <a name="data-structure"></a>資料結構
Azure 監視器計量所收集的資料會儲存在時間序列資料庫中，其已進行最佳化而能分析有時間戳記的資料。 每組計量值都是具有下列屬性的時間序列：

* 收集到值的時間
* 與值相關聯的資源
* 作用類似計量類別的命名空間
* 計量名稱
* 值本身
* 某些計量可能具有多個維度，如[多維度計量](#multi-dimensional-metrics)所述。 自訂計量最多可以有 10 個維度。

## <a name="multi-dimensional-metrics"></a>多維度計量
計量資料的其中一項挑戰是，其可用來為所收集到的值提供內容的資訊往往很有限。 Azure 監視器利用多維度計量來解決這項挑戰。 計量的維度是成對的名稱和數值，其中含有額外的資料來說明計量值。 例如，「可用磁碟空間」計量可以有名為「磁碟機」且值為 _C:_ 、 _D:_ 的維度，讓您能夠檢視所有磁碟機或每個磁碟機的可用磁碟空間。

下列範例說明適用於名為「網路輸送量」之假設性計量的兩個資料集。 第一個資料集沒有維度。 第二個資料集會顯示具有兩個維度 ( _IP 位址_ 與 _方向_ ) 的值：

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

## <a name="retention-of-metrics"></a>計量的保留期
針對 Azure 中的大部分資源，計量會儲存 93 天。 但有一些例外狀況：

**客體 OS 計量**
-   **傳統的客體 OS 計量** 。 這些是由 [Windows 診斷擴充功能 (WAD)](./diagnostics-extension-overview.md) 或 [Linux 診斷擴充功能 (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) 所收集並路由傳送至 Azure 儲存體帳戶的效能計數器。 這些計量的保留期為 14 天。
-   **傳送至 Azure 監視器計量的客體 OS 計量** 。 這些是由 [Windows 診斷擴充功能 (WAD)](diagnostics-extension-overview.md) 所收集並傳送至 [Azure 監視器資料接收器](diagnostics-extension-overview.md#data-destinations)的效能計數器，而在 Linux 機器上，則是透過 [InfluxData Telegraf 代理程式](https://www.influxdata.com/time-series-platform/telegraf/)。 這些計量的保留期為 93 天。
-   **Log Analytics 代理程式所收集的客體 OS 計量** 。 這些是 Log Analytics 代理程式所收集並傳送至 Log Analytics 工作區的效能計數器。 這些計量的保留期為 31 天，最多可延長為 2 年。

**Application Insights 記錄型的計量** 。 
- 在幕後，[記錄型計量](../app/pre-aggregated-metrics-log-metrics.md)會轉譯為記錄查詢。 其保留期會符合基礎記錄中的事件保留期。 若為 Application Insights 資源，記錄會儲存 90 天。


> [!NOTE]
> 您可以[將 Azure 監視器資源的平台計量傳送至 Log Analytics 工作區](./resource-logs.md#send-to-azure-storage)以進行長期趨勢分析。





## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器資料平台](data-platform.md)。
- 了解 [Azure 監視器中的記錄資料](data-platform-logs.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。

