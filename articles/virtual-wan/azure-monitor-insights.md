---
title: 使用 Azure 監視器 Insights 監視虛擬 WAN
description: 在本文中，您將瞭解如何使用 Azure 監視器 Insights 來監視 Azure 虛擬 WAN。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: d14ae86d41caec3c1bd897c2c81bee748dcc312c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050879"
---
# <a name="azure-monitor-insights-for-virtual-wan"></a>虛擬 WAN 的 Azure 監視器見解

適用于 Azure 虛擬 WAN 的[Azure 監視器見解](../azure-monitor/insights/network-insights-overview.md)可讓使用者和操作員能夠查看虛擬 WAN 的狀態和狀態（透過 autodiscovered 拓撲對應來呈現）。 地圖上的資源狀態和狀態重迭可讓您查看虛擬 WAN 的整體健全狀況。 您可以透過單鍵存取虛擬 WAN 入口網站的資源設定頁面，來流覽地圖上的資源。

虛擬 WAN 資源層級計量是透過預先封裝的虛擬 WAN 計量活頁簿收集和呈現。 活頁簿會顯示虛擬 WAN、中樞、閘道和連接層級的計量。 本文將逐步引導您瞭解如何使用虛擬 WAN Azure 監視器見解，在單一位置中查看您的虛擬 WAN 拓撲和計量。

> [!NOTE]
> [ **見解** ] 功能表選項位於 [ **監視**] 底下的虛擬 WAN 入口網站中。 您也可以使用網路 Azure 監視器來存取虛擬 WAN 拓撲和計量活頁簿。 如需詳細資訊，請參閱 [網路的 Azure 監視器](../azure-monitor/insights/network-insights-overview.md)。 
>

## <a name="before-you-begin"></a>開始之前

若要完成本文中的步驟，您需要有一個或多個中樞的虛擬 WAN。 若要建立虛擬 WAN 和中樞，請依照下列文章中的步驟進行：

* [建立虛擬 WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [建立中樞](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>View VWAN 拓撲

移至 **Azure 入口網站**  >  **虛擬 WAN**。 在左窗格的 [ **監視** ] 功能表中，選取 [ **Insights (預覽])**。 [ **見解** ] 視圖隨即出現。 它會顯示虛擬 WAN 相依性對應和高階 **計量** 迷你活頁簿。

**圖1：監視器 > 見解功能表**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="顯示見解 (預覽) 視圖的螢幕擷取畫面。" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

在 **深入** 解析視圖中，您可以查看 AUTODISCOVERED 虛擬 WAN 資源。 這些資源包括中樞、閘道、防火牆、連接和輪輻虛擬網路、協力廠商 Nva，以及端對端虛擬 WAN 中的分支。 如需範例，請參閱 **圖 2**。

資源狀態和狀態會以色彩標示，並在地圖中的資源圖示上重迭。 高層級的虛擬 WAN 計量（像是中樞容量和閘道使用率）會出現在視窗右側的迷你活頁簿中。

**圖2：見解視圖**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="顯示見解視圖的螢幕擷取畫面。" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>相依性視圖

虛擬 WAN 的相依性視圖可協助您將廣泛組織的所有虛擬 WAN 資源的 **互連觀點視覺化** ，以作為中樞和輪輻架構。

**圖3： VWAN 相依性視圖**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="顯示相依性視圖的螢幕擷取畫面。" lightbox="./media/azure-monitor-insights/dependency-map.png":::

[相依 **性視圖] 地圖會將下列** 資源顯示為連接的圖形：

* 跨不同 Azure 區域的虛擬 WAN 中樞。
* 直接連線至中樞的輪輻虛擬網路。
* 透過其個別的 ExpressRoute、S2S 和 P2S 連線，以及虛擬網路閘道，連線到每個中樞的 VPN 和 Azure ExpressRoute 分支網站和 P2S 使用者。
* Azure 防火牆 (包括) 部署在中樞 (安全中樞) 中的協力廠商防火牆 proxy。
* 協力廠商 Nva (部署在輪輻虛擬網路中的網路虛擬裝置) 。

相依性對應也會顯示間接連接的虛擬網路， (使用虛擬 WAN 輪輻虛擬網路) 對等互連的虛擬網路。

相依性對應可讓您輕鬆地流覽每個資源的設定。 例如，您可以將滑鼠停留在中樞資源上方以查看基本資源設定，例如中樞區域和中樞首碼。 按一下滑鼠右鍵以存取中樞資源的 [Azure 入口網站] 頁面。

**圖4：流覽至資源特定資訊**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="顯示如何流覽至資源特定資訊的螢幕擷取畫面。":::

相依 **性視圖中的搜尋** 和篩選列可提供簡單的方式來搜尋圖形。 各種篩選器可讓您將搜尋範圍縮小到特定的路徑和狀態。

**圖5：搜尋和篩選**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="顯示搜尋和篩選列的螢幕擷取畫面。" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>詳細計量

您可以選取 [ **View 詳細計量** ] 以存取 [詳細 **計量** ] 頁面。 [ **計量** ] 頁面是以個別索引標籤預先設定的儀表板。 這些索引標籤可讓您深入瞭解虛擬 wan 層級和中樞層級的虛擬 WAN 資源容量、效能和使用率，以及個別連接的層級。

**圖6：詳細計量儀表板**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="顯示詳細計量儀表板的螢幕擷取畫面。" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>下一步

* 若要深入瞭解，請參閱 [Azure 監視器中的計量](../azure-monitor/platform/data-platform-metrics.md)。
* 如需所有虛擬 WAN 計量的完整說明，請參閱 [虛擬 wan 記錄和計量](logs-metrics.md)。
