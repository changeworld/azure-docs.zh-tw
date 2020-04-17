---
title: Azure 監視器中的指標 |微軟文件
description: 描述 Azure 監視器中的指標,這些指標是能夠支援近即時方案的輕量級監視數據。
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: a02b3df02d455db8a7dfd21f63d659f75a66e446
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457309"
---
# <a name="metrics-in-azure-monitor"></a>Azure 監視器中的計量

> [!NOTE]
> Azure 監視器數據平臺基於兩種基本數據類型:指標和日誌。 本文介紹了指標。 有關日誌的詳細說明,請參閱[Azure 監視器中的日誌](data-platform-logs.md),請參閱[Azure 監視器數據平臺](data-platform.md),以便比較這兩個。

Azure 監視器中的指標重量輕,能夠支援接近即時的方案,因此它們對於警報和快速檢測問題特別有用。 本文介紹了指標的結構、可以如何處理指標以及標識在指標中存儲數據的不同數據源。

## <a name="what-are-metrics"></a>何謂度量？
計量為數值，描述系統在特定時間的某個方面。 指標是定期收集的,並且可用於警報,因為它們可以經常採樣,並且警報可以通過相對簡單的邏輯快速觸發。

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>使用 Azure 監視器指標可以執行哪些操作?
下表列出了在 Azure 監視器中使用指標數據的不同方法。

|  |  |
|:---|:---|
| 分析 | 使用[指標資源管理器](metrics-charts.md)分析圖表上收集的指標,並比較來自不同資源的指標。 |
| 視覺化 | 將圖表從指標資源管理器固定到[Azure 儀表板](../learn/tutorial-app-dashboards.md)。<br>創建[工作簿](../app/usage-workbooks.md)以在互動式報表中與多組數據組合。將查詢結果匯出到[Grafana](grafana-plugin.md)以利用其儀表板並與其他數據源結合使用。 |
| 警示 | 設定[指標警示規則](alerts-metric.md),該規則在指標值超過閾值時傳送通知或執行[自動操作](action-groups.md)。 |
| 自動化 |  使用[自動縮放](autoscale-overview.md)根據跨越閾值的指標值增加或減少資源。 |
| 匯出 | [將指標路由到日誌](resource-logs-collect-storage.md)以分析 Azure 監視器指標中的數據以及 Azure 監視器日誌中的數據,並存儲指標值超過 93 天。<br>將指標流式傳輸到[事件中心](stream-monitoring-data-event-hubs.md),以將它們路由到外部系統。 |
| 擷取 | 使用[PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights)從命令列存取指標值<br>使用[REST API](rest-api-walkthrough.md)從自訂應用程式存取指標值。<br>使用[CLI](/cli/azure/monitor/metrics)從命令行存取指標值。 |
| 封存 | [封存](..//learn/tutorial-archive-data.md)資源的效能或健全狀況歷程記錄，以用於相容性、稽核或離線報告。 |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Azure 監視器指標中的數據是如何構造的?
Azure 監視器指標收集的數據存儲在時間序列資料庫中,該資料庫經過優化,可用於分析時間戳數據。 每組指標值都是具有以下屬性的時間序列:

* 收集值的時間
* 值關聯的資源
* 類似於指標的類別的命名空間
* 指標名稱
* 值本身
* 某些指標可能具有多個維度,如[多維指標](#multi-dimensional-metrics)中所述。 自訂計量最多可以有 10 個維度。

## <a name="multi-dimensional-metrics"></a>多維度計量
指標數據面臨的挑戰之一是,它通常資訊有限,無法為收集的數據提供上下文。 Azure 監視器使用多維指標解決了這一難題。 計量的維度是成對的名稱和數值，其中含有額外的資料來說明計量值。 例如,指標_可用磁碟空間_可以具有一個稱為_驅動器_的維度,其值為_C:_, _D:_, 允許查看所有驅動器或每個驅動器的可用磁碟空間。

下列範例說明適用於名為「網路輸送量」__ 之假設性計量的兩個資料集。 第一個資料集沒有維度。 第二個資料集會顯示具有兩個維度 (_IP 位址_與_方向_) 的值：

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
使用[指標資源管理器](metrics-charts.md)以交互方式分析指標資料庫中的數據,並繪製多個指標隨時間的變化值。 您可以將圖表固定到儀錶板,以便與其他可視化效果一起查看圖表。 您也可以使用 [Azure 監視 REST API](rest-api-walkthrough.md) 來擷取計量。

![計量瀏覽器](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Azure 監視器指標的來源
Azure 監視器所收集的計量有三個基本來源。 在 Azure 監視器指標資料庫中收集這些指標後,可以同時計算它們,而不管它們的來源如何。

**平台計量**是由 Azure 資源所建立，讓您能夠掌握其健康情況和效能。 每個類型的資源都會建立[一組不同的計量](metrics-supported.md)，而不需進行任何必要的設定。 平台指標以一分鐘的頻率從 Azure 資源中收集,除非指標的定義另有說明。 

**來賓 OS 指標**是從虛擬機器的來賓操作系統收集的。 為具有[Windows 診斷擴展 (WAD)](../platform/diagnostics-extension-overview.md)的 Windows 虛擬機器啟用來賓 OS 指標,並為具有[「湧入資料遠端廣播代理](https://www.influxdata.com/time-series-platform/telegraf/)」的 Linux 虛擬機器啟用來賓 OS 指標。

**應用程式計量**是由 Application Insights 為您受監視的應用程式所建立，可協助您偵測效能問題，以及追蹤應用程式的使用趨勢。 這包括像是「伺服器回應時間」__ 和「瀏覽器例外狀況」__ 之類的值。

**自訂指標**是除了自動可用的標準指標之外,您定義的指標。 您可以在[應用程式中定義](../app/api-custom-events-metrics.md)由應用程式見解監視的自訂指標,或使用[自訂指標 API](metrics-store-custom-rest-api.md)為 Azure 服務創建自訂指標。

## <a name="retention-of-metrics"></a>保留指標
對於 Azure 中的大多數資源,指標將存儲 93 天。 有一些例外情況:

**來賓作業系統指標**
-   **傳統來賓作業系統指標**。 這些是[Windows 診斷擴展 (WAD)](../platform/diagnostics-extension-overview.md)或[Linux 診斷擴展 (LAD)](../../virtual-machines/extensions/diagnostics-linux.md)收集並路由到 Azure 儲存帳戶的性能計數器。 這些指標的保留期為 14 天。
-   **傳送到 Azure 監視器指標的來賓 OS 指標**。 這些是[Windows 診斷擴展 (WAD)](diagnostics-extension-overview.md)收集並發送到[Azure 監視器資料接收器](diagnostics-extension-overview.md#data-destinations)或透過 Linux 電腦上[的「湧入資料遠端格拉夫代理」](https://www.influxdata.com/time-series-platform/telegraf/)收集的性能計數器。 這些指標的保留期為 93 天。
-   **紀錄分析代理收集的來賓 OS 指標**。 這些是日誌分析代理收集併發送到日誌分析工作區的性能計數器。 這些指標的保留期為 31 天,可延長至 2 年。

**應用程式見解基於紀錄的指標**。 
- 在場景後面,[基於日誌的指標](../app/pre-aggregated-metrics-log-metrics.md)將轉換為日誌查詢。 它們的保留與基礎日誌中事件的保留匹配。 對於應用程式見解資源,日誌將存儲 90 天。


> [!NOTE]
> 您可以將[Azure 監視器資源的平台指標傳送到日誌分析工作區,](resource-logs-collect-storage.md)以進行長期趨勢分析。





## <a name="next-steps"></a>後續步驟

- 瞭解有關 Azure[監視器數據平臺](data-platform.md)的更多資訊。
- 在[Azure 監視器 中瞭解紀錄資料](data-platform-logs.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。
