---
title: 適用于網路 (預覽) 的 Azure 監視器
description: 快速流覽網路 Azure 監視器，可針對所有已部署的網路資源全面查看健康情況和計量，而不需要任何設定。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/24/2020
ms.openlocfilehash: 5f076f477c36f96d1807ce7071720225a6df8e03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803801"
---
# <a name="azure-monitor-for-networks-preview"></a>適用于網路 (預覽) 的 Azure 監視器
針對所有已部署的網路資源，Azure 監視器 for Network 提供完整的 [健全狀況](https://docs.microsoft.com/azure/service-health/resource-health-checks-resource-types) 和計量觀點，而不需要任何 [設定](../platform/metrics-supported.md) 。  它也可讓您存取所有網路監視功能，例如連線 [監視器](../../network-watcher/connection-monitor-preview.md)、 [網路安全性群組的流量記錄 (nsg) ](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)、使用 [分析](../../network-watcher/traffic-analytics.md)，以及其他網路 [診斷](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) 功能。

網路的 Azure 監視器是以監視的下列主要元件為結構：
- [網路健康情況和計量](#networkhealth)
- [連線能力](#connectivity)
- [交通流量](#traffic)
- [診斷工具組](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>網路健康情況和計量

[網路的 Azure 監視器 **總覽** ] 頁面可讓您輕鬆地將網路資源的清查視覺化，以及資源健康狀態和警示。 它分成四個主要功能區域-搜尋和篩選、資源健康狀態和計量、警示。 和相依性視圖

![概觀分頁](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>搜尋和篩選
您可以使用 **訂**用帳戶、 **資源群組** 和 **資源類型**等篩選器來自訂資源健康狀態和警示的觀點。 搜尋方塊提供搜尋資源屬性的功能。

搜尋方塊可以用來搜尋資源和相關聯的資源。 例如，公用 IP 會與應用程式閘道相關聯。 搜尋公用 IP DNS 名稱將會識別公用 IP 和相關聯的應用程式閘道。

![適用于網路的 Azure 監視器-搜尋](media/network-insights-overview/search.png)


### <a name="resource-health-and-metric"></a>資源健康狀態和度量
每個磚都代表一種資源類型，其中包含在所有選取的訂用帳戶中部署的實例數目，以及資源健康狀態。 在下列範例中，已部署45個 ER 和 VPN 連線、44的狀況良好，且1個無法使用。

![網路資源健康狀態的 Azure 監視器](media/network-insights-overview/resource-health.png)

按一下無法使用的 ER 和 VPN 連線，即會啟動度量視圖。 

![網路的 Azure 監視器-度量視圖](media/network-insights-overview/metric-view.png)

您可以按一下格線視圖中的每個元素。 按一下健全狀況圖示，以重新導向至該連線的資源健康狀態。 按一下 [警示]，分別針對該連線重新導向至 [警示和計量] 頁面。 

### <a name="alerts"></a>警示
右側的 [ **警示** ] 方格會顯示所有訂用帳戶中針對所選資源所產生的所有警示。 按一下警示計數，以流覽至 [詳細警示] 頁面。

### <a name="dependency-view"></a>相依性視圖
相依 **性視圖可** 協助視覺化資源的設定方式。 目前，應用程式閘道、虛擬 WAN 和 Load Balancer 現在支援相依性視圖。 例如，在應用程式閘道的案例中，您可以從計量方格視圖中按一下應用程式閘道資源名稱來存取相依性視圖。 這也適用于虛擬 WAN 和 Load Balancer。

![網路的 Azure 監視器-應用程式閘道視圖](media/network-insights-overview/application-gateway.png)

應用程式閘道的相依 **性視圖可** 簡化前端 ip 連接至接聽程式、規則和後端集區的方式。 連接邊緣會以色彩標示，並根據後端集區健康情況提供其他詳細資料。 此視圖也會針對所有相關的後端集區（例如虛擬機器擴展集和 VM 實例），提供應用程式閘道計量和計量的詳細觀點。

![網路的 Azure 監視器-相依性視圖](media/network-insights-overview/dependency-view.png)

相依性圖形可讓您輕鬆流覽至設定。 以滑鼠右鍵按一下後端集區，以存取其他功能。 例如，如果後端集區是 VM，您可以直接存取 VM 深入解析和網路監看員連線疑難排解，以找出連線問題。

![網路的 Azure 監視器-相依性視圖功能表](media/network-insights-overview/dependency-view-menu.png)

相依性視圖上的搜尋和篩選列可讓您輕鬆地在圖形中搜尋。 例如，在下列範例中搜尋 *AppGWTestRule* ，會將圖形化視圖縮小至透過 *AppGWTestRule*連線的所有節點。

![網路的 Azure 監視器-搜尋範例](media/network-insights-overview/search-example.png)

不同的篩選器可提供協助縮小至特定路徑和狀態。 例如，從 [**健全狀況狀態**] 下拉式清單中選取 [僅狀況*不良*]，以顯示狀態為*狀況不良*的所有邊緣。

按一下詳細的計量 **視圖** ，以啟動預先設定的活頁簿，其中包含應用程式閘道、所有後端集區資源和前端 ip 的詳細計量。 

## <a name="connectivity"></a><a name="connectivity"></a>連線能力

[連線 **能力** ] 索引標籤可讓您輕鬆地將所有使用 [連線監視器] 和 [連線監視器] 設定的測試視覺化， [ (預覽版) ](../../network-watcher/connection-monitor-preview.md) 選取的訂用帳戶。

![網路 Azure 監視器中的 [連線能力] 索引標籤](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

測試依來源和目的地磚分組，並顯示每個測試的可連線性狀態。 可連線的設定可讓您根據檢查失敗 (% ) 和 RTT (ms) ，輕鬆地設定您的可存取性準則。 設定值之後，就會根據選取準則更新每項測試的狀態。

![網路 Azure 監視器中的連線能力測試](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

按一下任何來源或目的地磚，即會啟動度量視圖。

![網路 Azure 監視器的連接計量](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


您可以按一下格線視圖中的每個元素。 按一下 [可連線 **] 圖示，** 以重新導向至連線 **監視器** 入口網站頁面，以依躍點拓撲和已識別的連線影響問題來查看躍點。 按一下 [ **警示** ] 以重新導向至警示，並 **檢查失敗的百分比/來回行程時間** ，以重新導向至所選連線監視器的 [計量] 頁面。

右側的 [ **警示**] 方格會顯示所有在所有訂用帳戶   上設定的連線測試所產生的所有警示。 按一下警示計數，以流覽至 [詳細警示] 頁面。

## <a name="traffic"></a><a name="traffic"></a>交通流量
[流量] 索引標籤可讓您存取所選訂用帳戶集的 [NSG 流量記錄](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) 和使用 [分析](../../network-watcher/traffic-analytics.md) ，以及依位置分組的所有 nsg。 此索引標籤上提供的搜尋功能可讓您識別針對搜尋的 IP 位址所設定的 Nsg。 您可以在環境中搜尋任何 IP 位址，而並排顯示的區域視圖會顯示所有 Nsg 以及 NSG 流量記錄和流量分析設定狀態。

![網路中 Azure 監視器的流量查看](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

按一下任何區域磚會啟動方格視圖，讓您輕鬆地查看及設定 NSG 流程記錄和流量分析。  

![網路 Azure 監視器中的流量區域查看](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

您可以按一下格線視圖中的每個元素。 按一下 [設定狀態] 以編輯 NSG 流量記錄和流量分析設定。 按一下 [警示]，以重新導向至針對選取的 NSG 所設定的流量警示。 同樣地，您可以按一下工作區以流覽至 [流量分析] 視圖。  

右側的 [ **警示**]   方格可讓您查看所有訂用帳戶的所有以分析工作區為基礎的警示。 按一下警示計數，以流覽至 [詳細警示] 頁面。

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> 診斷工具組
診斷工具組可讓您存取所有可用來對網路進行疑難排解的診斷功能。 從這個下拉式清單中，您可以存取封 [包捕獲](../../network-watcher/network-watcher-packet-capture-overview.md)、 [VPN 疑難排解](../../network-watcher/network-watcher-troubleshoot-overview.md)、連線 [疑難排解](../../network-watcher/network-watcher-connectivity-overview.md)、 [下一個躍點](../../network-watcher/network-watcher-next-hop-overview.md) 和 [IP 流量驗證](../../network-watcher/network-watcher-ip-flow-verify-overview.md)等功能。

![診斷工具組索引標籤](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>疑難排解 

如需一般疑難排解指引，請參閱專用的以活頁簿為基礎的深入解析 [疑難排解文章](troubleshoot-workbooks.md)。

本節將協助您進行診斷，並針對您使用網路 Azure 監視器時可能遇到的一些常見問題進行疑難排解。 請使用下列清單，找到與您的特定問題相關的資訊。

### <a name="resolving-performance-issues-or-failures"></a>解決效能問題或失敗

若要協助針對您使用網路 Azure 監視器識別的任何網路相關問題進行疑難排解，請參閱故障資源的疑難排解檔。 以下列出適用于高用途服務的疑難排解連結。
* 虛擬網路 (VNET) 
* 應用程式閘道
* VPN 閘道
* ExpressRoute 
* 負載平衡器 

### <a name="why-dont-i-see-the-resources-from-all-the-subscriptions-i-have-selected"></a>為什麼我看不到我選取的所有訂用帳戶中的資源

網路深入解析一次只能顯示5個訂用帳戶中的資源。 

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-network-insights-how-do-i-do-so"></a>我想要進行變更，或將其他視覺效果新增至網路深入解析，我該怎麼做

若要進行變更，請選取 [編輯模式] 以修改活頁簿，然後，您可以將工作儲存為新的活頁簿，並繫結至指定的訂用帳戶和資源群組。

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>釘選活頁簿的任何部分之後的時間細微性為何

我們採用「自動」時間精細度，這具體上取決於選取的時間範圍。

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>當活頁簿的任何部分釘選時的時間範圍

時間範圍將取決於儀表板設定。

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-network-insights"></a>如果我想要查看其他資料或建立自己的視覺效果，該怎麼做？ 如何對 Network Insights 進行變更

您可以透過使用編輯模式，編輯在任何側邊面板和詳細計量視圖中看到的活頁簿，然後將您的工作儲存為新的活頁簿，以進行所有新的變更。


## <a name="next-steps"></a>後續步驟

- 深入瞭解 [什麼是 Azure 網路](../../network-watcher/network-watcher-monitoring-overview.md)監看員的網路監視？。
- 檢閱[使用 Azure 監視器活頁簿建立互動式報表](../platform/workbooks-overview.md)，以了解設計活頁簿以提供支援的案例、如何撰寫新報表和自訂現有報表，以及其他資訊。
