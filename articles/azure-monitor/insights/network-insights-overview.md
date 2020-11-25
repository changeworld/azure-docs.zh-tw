---
title: 適用於網路的 Azure 監視器
description: 網路的 Azure 監視器總覽，可針對所有已部署的網路資源全面查看健康情況和計量，而不需要任何設定。
ms.subservice: ''
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: 52ca879d5a680d0e62cc469e768236eac11f3719
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030148"
---
# <a name="azure-monitor-for-networks"></a>適用於網路的 Azure 監視器
適用于網路的 Azure 監視器可針對所有已部署的網路資源全面查看 [健康](../../service-health/resource-health-checks-resource-types.md) 情況和計量，而不需要任何 [設定](../platform/metrics-supported.md) 。 它也可讓您存取網路監視功能，例如連線 [監視器](../../network-watcher/connection-monitor-preview.md)、 [網路安全性群組的流量記錄 (nsg) ](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)和使用 [分析](../../network-watcher/traffic-analytics.md)。 它還提供其他網路 [診斷](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) 功能。

網路的 Azure 監視器是以監視的這些主要元件為結構：
- [網路健康情況和計量](#networkhealth)
- [連線能力](#connectivity)
- [交通流量](#traffic)
- [診斷工具組](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>網路健康情況和計量

[網路的 Azure 監視器 **總覽** ] 頁面提供簡單的方式來視覺化網路資源的清查，以及資源健康狀態和警示。 它分成四個主要功能區域：搜尋和篩選、資源健康狀態和度量、警示和相依性視圖。

[ ![ 顯示 [總覽] 頁面 #lightbox) 的螢幕擷取畫面](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>搜尋和篩選
您可以使用 **訂** 用帳戶、 **資源群組** 和 **類型** 等篩選準則來自訂資源健康狀態和警示的觀點。

您可以使用 [搜尋] 方塊來搜尋資源和其相關聯的資源。 例如，公用 IP 會與應用程式閘道相關聯。 搜尋公用 IP 的 DNS 名稱將會傳回公用 IP 和相關聯的應用程式閘道：

[![顯示網路搜尋結果 Azure 監視器的螢幕擷取畫面。](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>資源健康狀態和計量
在下列範例中，每個磚都代表一種資源類型。 此磚會顯示在所有選取的訂用帳戶中部署之資源類型的實例數目。 它也會顯示資源的健康情況狀態。 在此範例中，已部署 105 ER 和 VPN 連線。 103的狀況良好，且有2個無法使用。

![顯示網路中 Azure 監視器資源健康狀態和計量的螢幕擷取畫面。](media/network-insights-overview/resource-health.png)

如果您選取無法使用的 ER 和 VPN 連線，您會看到計量視圖： 

![顯示 Azure 監視器中的 [網路] 計量視圖的螢幕擷取畫面。](media/network-insights-overview/metric-view.png)

您可以在格線視圖中選取任何專案。 選取 [ **健康** 情況] 資料行中的圖示，以取得該連接的資源健康狀態。 選取 [ **警示** ] 資料行中的值，以移至連線的 [警示和計量] 頁面。 

### <a name="alerts"></a>警示
頁面右側的 **警示** 方塊提供針對所有訂用帳戶中所選資源所產生的所有警示的查看。 選取警示計數以移至詳細警示頁面。

### <a name="dependency-view"></a>相依性視圖
相依性視圖可協助您視覺化資源的設定方式。 相依性視圖目前適用于 Azure 應用程式閘道、Azure 虛擬 WAN 和 Azure Load Balancer。 例如，針對應用程式閘道，您可以在計量方格視圖中選取應用程式閘道資源名稱，以存取相依性視圖。 您可以對虛擬 WAN 和 Load Balancer 進行相同的動作。

![爭取，顯示網路 Azure 監視器中的應用程式閘道視圖。](media/network-insights-overview/application-gateway.png)

應用程式閘道的相依性視圖可簡化前端 Ip 連接至接聽程式、規則和後端集區的方式。 連接線會以色彩標示，並根據後端集區健康情況提供其他詳細資料。 此視圖也會提供應用程式閘道計量的詳細觀點，以及所有相關後端集區的度量，例如虛擬機器擴展集和 VM 實例。

[![顯示網路 Azure 監視器中相依性觀點的螢幕擷取畫面。](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

相依性圖形可讓您輕鬆地導覽至設定。 以滑鼠右鍵按一下後端集區，以存取其他資訊。 例如，如果後端集區是 VM，您可以直接存取 VM 深入解析和 Azure 網路監看員連線疑難排解，以找出連線問題：

![顯示 Azure 監視器中的 [相依性視圖] 功能表的螢幕擷取畫面。](media/network-insights-overview/dependency-view-menu.png)

相依性視圖上的搜尋和篩選列可提供簡單的方式來搜尋圖形。 例如，如果您在上一個範例中搜尋 **AppGWTestRule** ，此視圖將會縮小至透過 AppGWTestRule 連線的所有節點：

![顯示 Azure 監視器網路搜尋範例的螢幕擷取畫面。](media/network-insights-overview/search-example.png)

各種篩選器可協助您縮小為特定的路徑和狀態。 例如，從 [**健全狀況狀態**] 清單中選取 [僅狀況 **不良**]，以顯示狀態為狀況不良的所有邊緣。

選取 [ **View 詳細計量** ] 來開啟預先設定的活頁簿，以提供應用程式閘道、所有後端集區資源和前端 ip 的詳細計量。 

## <a name="connectivity"></a><a name="connectivity"></a>連線能力

[連線 **能力** ] 索引標籤提供簡單的方式，讓您透過連線 [監視器](../../network-watcher/connection-monitor-overview.md) 和連線監視器將設定的所有測試視覺化， (選取的訂用帳戶集的傳統) 。

![顯示 Azure 監視器中網路的 [連線能力] 索引標籤的螢幕擷取畫面。](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

測試依 **來源** 和 **目的地** 磚分組，並顯示每個測試的可連線性狀態。 可連線的設定可讓您根據檢查失敗 (% ) 和 RTT (ms) ，輕鬆存取可存取性準則的設定。 設定值之後，每個測試的狀態都會根據選取準則來更新。

[![顯示 Azure 監視器中網路連線能力測試的螢幕擷取畫面。](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

您可以選取任何來源或目的地磚來開啟度量視圖：

[![顯示網路中 Azure 監視器連接計量的螢幕擷取畫面。](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


您可以在格線視圖中選取任何專案。 選取 [可連線] **資料行中** 的圖示，以移至 [連線監視器] 入口網站頁面，並查看會影響所識別問題的逐一躍點拓撲和連線能力。 選取 [ **警示** ] 資料行中的值，以移至 [警示]。 選取 [ **檢查失敗百分比** ] 和 [ **來回行程時間] (ms)** 資料行中的圖表，以移至所選連線監視器的 [計量] 頁面。

頁面右側的 **警示** 方塊提供針對所有訂用帳戶上設定的連線測試所產生的所有警示的查看。 選取警示計數以移至詳細警示頁面。

## <a name="traffic"></a><a name="traffic"></a>交通流量
[ **流量** ] 索引標籤可讓您存取所選訂用帳戶集合（依位置分組）的 [NSG 流量記錄](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) 和使用 [分析](../../network-watcher/traffic-analytics.md) 所設定的所有 nsg。 此索引標籤上提供的搜尋功能可讓您識別針對搜尋的 IP 位址所設定的 Nsg。 您可以在您的環境中搜尋任何 IP 位址。 並排顯示的區域視圖會顯示所有 Nsg，以及 NSG 流量記錄和流量分析設定狀態。

[![顯示 Azure 監視器中網路的 [流量] 索引標籤的螢幕擷取畫面。](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

如果您選取任何區域圖格，則會出現格線視圖。 此方格可讓您輕鬆閱讀並設定 NSG 流程記錄和流量分析的觀點：  

[![螢幕擷取畫面，顯示網路中 Azure 監視器的流量區域查看。](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

您可以在格線視圖中選取任何專案。 選取 [ **Flowlog 設定狀態** ] 資料行中的圖示，以編輯 NSG 流量記錄和流量分析設定。 選取 [ **警示** ] 資料行中的值，以移至針對所選取 NSG 所設定的流量警示。 同樣地，您可以藉由選取 [使用 **分析] 工作區** 來移至 [流量分析] 的觀點。  

頁面右側的 **警示** 方塊可讓您查看所有訂用帳戶的所有流量分析以工作區為基礎的警示。 選取警示計數以移至詳細警示頁面。

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> 診斷工具組
診斷工具組可讓您存取所有可用來對網路進行疑難排解的診斷功能。 您可以使用此下拉式清單來存取功能，例如封 [包捕獲](../../network-watcher/network-watcher-packet-capture-overview.md)、 [VPN 疑難排解](../../network-watcher/network-watcher-troubleshoot-overview.md)、連線 [疑難排解](../../network-watcher/network-watcher-connectivity-overview.md)、 [下一個躍點](../../network-watcher/network-watcher-next-hop-overview.md)和 [IP 流量驗證](../../network-watcher/network-watcher-ip-flow-verify-overview.md)：

![顯示 [診斷工具組] 索引標籤的螢幕擷取畫面。](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>疑難排解 

如需一般疑難排解指引，請參閱專用的以活頁簿為基礎的深入解析 [疑難排解文章](troubleshoot-workbooks.md)。

本節將協助您診斷和疑難排解當您使用網路的 Azure 監視器時可能遇到的一些常見問題。 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>如何? 解決效能問題或失敗？

若要瞭解如何針對您使用網路 Azure 監視器識別的任何網路相關問題進行疑難排解，請參閱故障資源的疑難排解檔。 

以下是常用服務的疑難排解文章連結。 如需有關這些服務的詳細疑難排解文章，請參閱服務之目錄的疑難排解一節中的其他文章。
* [Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-peering-issues)
* [Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/create-gateway-internal-load-balancer-app-service-environment)
* [Azure VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot)
* [Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-expressroute-overview) 
* [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-troubleshoot) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>為什麼我看不到我選取的所有訂用帳戶的資源？

網路深入解析一次只能顯示五個訂用帳戶的資源。 

### <a name="how-do-i-make-changes-or-add-visualizations-to-network-insights"></a>如何? 進行變更或將視覺效果新增至網路見解？

若要進行變更，請選取 [ **編輯模式]** 來修改活頁簿。 然後，您可以將變更儲存為與指定的訂用帳戶和資源群組系結的新活頁簿。

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>釘選活頁簿的任何部分之後的時間細微性為何？

網路深入解析會使用 **自動** 時間範圍，因此時間細微性是根據選取的時間範圍。

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>當活頁簿的任何部分釘選時的時間範圍為何？

時間範圍取決於儀表板設定。

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-network-insights"></a>如果我想要查看其他資料或建立自己的視覺效果，該怎麼做？ 如何變更網路見解？

您可以使用編輯模式，編輯您在任何側邊面板中看到的活頁簿或詳細的度量視圖。 然後，您可以將變更儲存為新的活頁簿。

## <a name="next-steps"></a>後續步驟

- 深入瞭解網路監視： [什麼是 Azure 網路監看員？](../../network-watcher/network-watcher-monitoring-overview.md)
- 瞭解活頁簿設計來支援的案例、如何建立報表及自訂現有的報表，以及其他： [使用 Azure 監視器活頁簿建立互動式報表](../platform/workbooks-overview.md)
