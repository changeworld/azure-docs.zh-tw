---
title: 網路的 Azure 監視器（預覽）
description: Azure 監視器網路的快速總覽，針對所有已部署的網路資源提供健全狀況和計量的全面觀點，而不需要任何設定。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: ccc5578944dc6eea9a62360045272896d78e2fac
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128670"
---
# <a name="azure-monitor-for-networks-preview"></a>網路的 Azure 監視器（預覽）
網路的 Azure 監視器針對所有已部署的網路資源提供健全狀況和計量的全面觀點，而不需要任何設定。 先進的搜尋功能有助於識別資源相依性，並藉由直接搜尋主控的網站名稱來識別裝載您網站的資源等案例。

網路的 Azure 監視器 **[總覽**] 頁面可讓您輕鬆地以視覺化方式呈現網路資源的清查，以及資源健康狀態和警示。 它分為四個主要功能區域：

- 搜尋和篩選
- 資源健康狀態和計量
- 警示 
- 相依性視圖

![概觀分頁](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>搜尋和篩選
您可以使用**訂**用帳戶、**資源群組**和**資源類型**之類的篩選準則來自訂 [資源健康狀態] 和 [警示] 視圖。 [搜尋] 方塊提供搜尋資源屬性的功能。

[搜尋] 方塊可用來搜尋資源和相關聯的資源。 例如，公用 IP 會與應用程式閘道相關聯。 搜尋 [公用 ip] DNS 名稱會識別公用 IP 和相關聯的應用程式閘道。

![搜尋](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>資源健康狀態和度量
每個圖格都代表一種資源類型，其中包含在所有選取的訂用帳戶之間部署的實例數目，以及資源健康狀態。 在下列範例中，已部署 105 ER 和 VPN 連線、103狀況良好，且2無法使用。

![資源健康情況](media/network-insights-overview/resource-health.png)

按一下兩個無法使用的 ER 和 VPN 連線，就會啟動計量視圖。 

![計量檢視](media/network-insights-overview/metric-view.png)

您可以按一下方格視圖中的每個元素。 按一下健全狀況圖示，以重新導向至該連線的資源健康狀態。 按一下 [警示]，分別重新導向至該連線的 [警示和計量] 頁面。 

## <a name="alerts"></a>警示
右側的 [**警示**] 方格提供所有訂用帳戶中所選資源所產生的所有警示的視圖。 按一下 [警示計數] 以流覽至 [詳細警示] 頁面。

## <a name="dependency-view"></a>相依性視圖
[相依性 **] 視圖有助於**視覺化資源的設定方式。 目前應用程式閘道、虛擬 WAN 和 Load Balancer 都支援相依性視圖。 例如，在應用程式閘道的情況下，您可以按一下 [計量] 方格視圖中的 [應用程式閘道資源名稱來存取相依性視圖]。 這也適用于虛擬 WAN 和 Load Balancer。 

![應用程式閘道視圖](media/network-insights-overview/application-gateway.png)

應用程式閘道的相依性視圖提供前端 Ip 如何連線至接聽程式、規則和後端集區的**簡單觀點。** 連接邊緣會以色彩標示，並根據後端集區健康情況提供其他詳細資料。 此視圖也會針對所有相關的後端集區（例如 VMSS 和 VM 實例），提供應用程式閘道計量和計量的詳細觀點。

![相依性視圖](media/network-insights-overview/dependency-view.png)

相依性圖形可讓您輕鬆流覽至設定。 以滑鼠右鍵按一下後端集區，以存取其他功能。 例如，如果後端集區是 VM，則您可以直接存取 VM 深入解析，並網路監看員連線疑難排解來識別連線問題。

![相依性視圖功能表](media/network-insights-overview/dependency-view-menu.png)

[相依性] 視圖上的 [搜尋] 和 [篩選] 列提供一種輕鬆搜尋圖形的方式。 例如，在下列範例中搜尋*AppGWTestRule*會將圖形化視圖縮小至透過*AppGWTestRule*連線的所有節點。 

![搜尋範例](media/network-insights-overview/search-example.png)

不同的篩選器會提供有助於縮小至特定路徑和狀態的說明。 例如，從 [**健全狀況狀態**] 下拉式選選取 [僅健康情況*不良*]，以顯示狀態*狀況不良*的所有邊緣。

按一下 [**詳細**的計量視圖] 以啟動預先設定的活頁簿，其中包含應用程式閘道、所有後端集區資源和前端 ip 的詳細度量。 

## <a name="next-steps"></a>後續步驟 

- 若要深入瞭解網路監視功能，請閱讀什麼是[Azure 網路監看員？](../../network-watcher/network-watcher-monitoring-overview.md)。
