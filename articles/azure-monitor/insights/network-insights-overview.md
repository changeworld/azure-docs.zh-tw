---
title: 適用于網路 (預覽) 的 Azure 監視器
description: 快速流覽網路的 Azure 監視器，可針對所有已部署的網路資源全面查看健康情況和計量，而不需要任何設定。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 080952752dc5abfd7cf61ae1c5b00b539af58b99
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646572"
---
# <a name="azure-monitor-for-networks-preview"></a>適用于網路 (預覽) 的 Azure 監視器
針對所有已部署的網路資源，Azure 監視器 for Network 提供完整的健全狀況和計量觀點，而不需要任何設定。 「先進搜尋」功能可協助識別資源相依性，以藉由搜尋託管網站名稱來識別裝載您網站的資源等案例。

[網路的 Azure 監視器 **總覽** ] 頁面可讓您輕鬆地將網路資源的清查視覺化，以及資源健康狀態和警示。 它分成四個主要功能區域：

- 搜尋和篩選
- 資源健康狀態和計量
- 警示 
- 相依性視圖

![概觀分頁](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>搜尋和篩選
您可以使用 **訂**用帳戶、 **資源群組** 和 **資源類型**等篩選器來自訂資源健康狀態和警示的觀點。 搜尋方塊提供搜尋資源屬性的功能。

搜尋方塊可以用來搜尋資源和相關聯的資源。 例如，公用 IP 會與應用程式閘道相關聯。 搜尋公用 IP DNS 名稱將會識別公用 IP 和相關聯的應用程式閘道。

![搜尋](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>資源健康狀態和度量
每個磚都代表一種資源類型，其中包含在所有選取的訂用帳戶中部署的實例數目，以及資源健康狀態。 在下列範例中，已部署105個 ER 和 VPN 連線、103的狀況良好，以及2個無法使用。

![資源健康情況](media/network-insights-overview/resource-health.png)

按一下兩個無法使用的 ER 和 VPN 連線，即會啟動度量視圖。 

![計量檢視](media/network-insights-overview/metric-view.png)

您可以按一下格線視圖中的每個元素。 按一下健全狀況圖示，以重新導向至該連線的資源健康狀態。 按一下 [警示]，分別針對該連線重新導向至 [警示和計量] 頁面。 

## <a name="alerts"></a>警示
右側的 [ **警示** ] 方格會顯示所有訂用帳戶中針對所選資源所產生的所有警示。 按一下警示計數，以流覽至 [詳細警示] 頁面。

## <a name="dependency-view"></a>相依性視圖
相依 **性視圖可** 協助視覺化資源的設定方式。 目前，應用程式閘道、虛擬 WAN 和 Load Balancer 現在支援相依性視圖。 例如，在應用程式閘道的案例中，您可以從計量方格視圖中按一下應用程式閘道資源名稱來存取相依性視圖。 這也適用于虛擬 WAN 和 Load Balancer。 

![應用程式閘道視圖](media/network-insights-overview/application-gateway.png)

應用程式閘道的相依 **性視圖可** 簡化前端 ip 連接至接聽程式、規則和後端集區的方式。 連接邊緣會以色彩標示，並根據後端集區健康情況提供其他詳細資料。 此視圖也會針對所有相關的後端集區（例如虛擬機器擴展集與虛擬機器實例），提供應用程式閘道計量和計量的詳細觀點。

![相依性視圖](media/network-insights-overview/dependency-view.png)

相依性圖形可讓您輕鬆流覽至設定。 以滑鼠右鍵按一下後端集區，以存取其他功能。 例如，如果後端集區是虛擬機器，您可以直接存取虛擬機器見解和網路監看員連線，以針對連線問題進行疑難排解及找出問題。

![相依性視圖功能表](media/network-insights-overview/dependency-view-menu.png)

相依性視圖上的搜尋和篩選列可讓您輕鬆地在圖形中搜尋。 例如，在下列範例中搜尋 *AppGWTestRule* ，會將圖形化視圖縮小至透過 *AppGWTestRule*連線的所有節點。 

![搜尋範例](media/network-insights-overview/search-example.png)

不同的篩選器可提供協助縮小至特定路徑和狀態。 例如，從 [**健全狀況狀態**] 下拉式清單中選取 [僅狀況*不良*]，以顯示狀態為*狀況不良*的所有邊緣。

按一下詳細的計量 **視圖** ，以啟動預先設定的活頁簿，其中包含應用程式閘道、所有後端集區資源和前端 ip 的詳細計量。 

## <a name="next-steps"></a>接下來的步驟 

- 深入瞭解什麼是 [Azure 網路](../../network-watcher/network-watcher-monitoring-overview.md)監看員的網路監視？。
