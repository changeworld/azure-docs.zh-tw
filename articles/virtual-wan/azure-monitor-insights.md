---
title: 使用 Azure 監視器 Insights 監視 Azure 虛擬 WAN
description: 瞭解如何使用 Azure 監視器 Insights 監視虛擬 WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: cherylmc
ms.openlocfilehash: 8553a809173d955a21e6730de35c70de5b69e81b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136149"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>適用于虛擬 WAN 的 Azure 監視器深入解析（預覽）

虛擬 WAN 的[Azure 監視器深入](../azure-monitor/insights/network-insights-overview.md)解析可讓使用者和操作員能夠查看虛擬 WAN 的狀態和狀態，並透過 autodiscovered 拓撲對應來呈現。 資源狀態和狀態會在地圖上重迭，以提供您虛擬 WAN 整體健全狀況的快照集。 透過單鍵存取虛擬 WAN 入口網站的 [資源設定] 頁面，即可在地圖上啟用資源流覽。

虛擬 WAN 資源層級計量是透過預先封裝的虛擬 WAN 計量活頁簿來收集和呈現，其中顯示虛擬 WAN、中樞、閘道和連線層級的計量。 這篇文章會逐步引導您使用虛擬 WAN Azure 監視器深入解析，以單一位置來查看您的虛擬 WAN 拓朴和計量。

> [!NOTE]
> [深入解析] 功能表正在推出此預覽階段。
>

## <a name="before-you-begin"></a>開始之前

此文章中的步驟假設您已經部署具有一或多個中樞的虛擬 WAN。 若要建立新的虛擬 WAN 與新的中樞，請使用下列文章中的步驟：

* [建立虛擬 WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [建立中樞](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>View VWAN 拓撲

在**Azure 入口網站 >的虛擬 WAN**中，從左側的 [**監視**] 功能表中，選取 [ **Insights （預覽）**]。 這會開啟 [**深入**解析] 視圖，其中顯示虛擬 WAN 相依性對應和高階計量迷你活頁簿。

**圖1：監視器-深入解析功能表**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="圖表" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

在 [**深入**解析] 視圖中，您可以在端對端虛擬 wan 中查看 AUTODISCOVERED 虛擬 wan 資源（例如中樞、閘道、防火牆、連線和輪輻 vnet、協力廠商 nva 和分支），如 [**圖 2**] 所示。

**資源狀態**和**狀態**會以色彩標示，並會在地圖的資源圖示上重迭。 虛擬 WAN 的高階計量，例如集線器容量和閘道使用率，會透過迷你活頁簿顯示在右側。

**圖2：深入解析視圖**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="圖表" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>相依性視圖

虛擬 WAN 的相依性視圖可讓您以視覺化方式呈現所有虛擬 WAN 資源的**互連觀點，** 並廣泛地組織成中樞和輪輻架構。

**圖3： VWAN 相依性視圖**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="相依性對應" lightbox="./media/azure-monitor-insights/dependency-map.png":::

相依性視圖對應會將下列資源顯示為已連接的圖形：

* 跨不同 Azure 區域的虛擬 WAN 中樞。
* 直接連線到中樞的輪輻 Vnet。
* VPN 和 ExpressRoute 分支網站，以及透過其各自的 ExpressRoute、S2S 和 P2S 連線，以及虛擬網路閘道連接到每個中樞的 P2S 使用者。
* 部署在中樞（受保護的中樞）中的 Azure 防火牆（包括協力廠商防火牆 proxy）。
* 部署在輪輻 Vnet 中的協力廠商 NVA （網路虛擬裝置）。

相依性對應也會顯示間接連線的 Vnet （VNet 是以虛擬 WAN 輪輻 Vnet 對等互連）。

相依性對應可讓您輕鬆流覽到每個資源的設定。 例如，您可以將滑鼠停留在中樞資源上，以查看基本資源設定，例如中樞區域和中樞首碼。 以滑鼠右鍵按一下以存取中樞資源的 [Azure 入口網站] 頁面。

**圖4：流覽至資源特定的資訊**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="資源資訊":::

[相依性] 視圖上的 [搜尋] 和 [篩選] 列提供一種輕鬆搜尋圖形的方式。 不同的篩選器會提供協助，將您的搜尋範圍縮小至特定的路徑和狀態。

**圖5：搜尋和篩選**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="搜尋和篩選列" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>詳細計量

您可以選取 [**查看詳細計量**] 來存取詳細的**計量**頁面。 [計量] 頁面是以個別索引標籤預先設定的儀表板，可讓您深入瞭解虛擬 wan 層級、中樞層級和個別連線的虛擬 WAN 資源容量、效能和使用率。

**圖6：詳細計量儀表板**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="詳細計量" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解 Azure 監視器中的計量，請參閱[Azure 監視器中的計量](../azure-monitor/platform/data-platform-metrics.md)。
* 如需所有虛擬 WAN 計量的完整說明，請參閱[虛擬 wan 記錄和計量](logs-metrics.md)。
