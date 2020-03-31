---
title: 網路 Azure 監視器（預覽）
description: 網路 Azure 監視器的快速概述，它為所有部署的網路資源提供運行狀況和指標的全面視圖，而無需任何配置。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654863"
---
# <a name="azure-monitor-for-networks-preview"></a>網路 Azure 監視器（預覽）
Azure 網路監視器為所有部署的網路資源提供運行狀況和指標的全面視圖，而無需任何配置。 高級搜索功能可説明識別資源依賴項，從而啟用諸如通過搜索託管網站名稱來標識託管網站的資源等方案。

"Azure 網路**監視器概述**"頁提供了一種輕鬆的方式來視覺化網路資源的清單以及資源運行狀況和警報。 它分為四個關鍵功能領域：

- 搜索和篩選
- 資源運行狀況和指標
- 警示 
- 依賴項視圖

![概觀分頁](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>搜索和篩選
可以使用**訂閱**、**資源組**和資源**類型**等篩選器自訂資源運行狀況和警示檢視。 搜索框提供搜索資源屬性的功能。

搜索框可用於搜索資源和關聯資源。 例如，公共 IP 與應用程式閘道相關聯。 搜索公共 IP DNS 名稱將同時標識公共 IP 和相關的應用程式閘道。

![搜尋](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>資源運行狀況和指標
每個磁貼表示一種資源類型，在所有訂閱中部署的實例數以及資源運行狀況狀態一起選擇。 在下面的示例中，部署了 105 個 ER 和 VPN 連接，103 個正常連接，2 個不可用。

![資源健康情況](media/network-insights-overview/resource-health.png)

按一下兩個不可用的 ER 和 VPN 連接，將啟動指標視圖。 

![計量檢視](media/network-insights-overview/metric-view.png)

您可以按一下網格視圖中的每個元素。 按一下"運行狀況"圖示以重定向到該連接的資源運行狀況。 按一下"警報"可分別重定向到該連接的警報和指標頁。 

## <a name="alerts"></a>警示
右側的 **"警報"** 網格提供所有訂閱中所選資源生成的所有警報的視圖。 按一下警報計數以導航到詳細警報頁面。

## <a name="dependency-view"></a>依賴項視圖
**依賴項**視圖有助於視覺化資源的配置方式。 當前依賴項視圖僅支援應用程式閘道。 可以通過按一下指標網格視圖中的應用程式閘道資源名稱來訪問依賴項視圖。

![應用程式閘道視圖](media/network-insights-overview/application-gateway.png)

應用程式閘道**的依賴項**視圖提供了前端 IP 如何連接到攔截器、規則和後端池的簡化視圖。 連接邊採用顏色編碼，並根據後端池運行狀況提供其他詳細資訊。 該視圖還提供了所有相關後端池（如 VMSS 和 VM 實例）的應用程式閘道指標和指標的詳細視圖。

![依賴項視圖](media/network-insights-overview/dependency-view.png)

依賴項圖允許輕鬆導航到配置設置。 按右鍵後端池以訪問其他功能。 例如，如果後端池是 VM，則可以直接存取 VM 見解和網路觀察程式連接疑難排解，以確定連接問題。

![依賴項視圖功能表](media/network-insights-overview/dependency-view-menu.png)

依賴項視圖上的搜索和篩選欄提供了一種輕鬆的方法來搜索圖形。 例如，在下面的示例中搜索*AppGWTestRule*會將圖形視圖縮小到通過*AppGWTestRule*連接的所有節點。 

![搜尋範例](media/network-insights-overview/search-example.png)

不同的篩選器有助於縮小到特定的路徑和狀態。 例如，從"**運行狀況狀態**"下拉清單中僅選擇 *"不正常"* 以顯示狀態為 *"不正常"* 的所有邊。

按一下 **"詳細指標視圖**"可啟動預配置的活頁簿，其中包含應用程式閘道、所有後端池資源和前端 IP 的詳細指標。 

## <a name="next-steps"></a>後續步驟 

- 在什麼是[Azure 網路觀察程式上](/azure/network-watcher/network-watcher-monitoring-overview)瞭解有關網路監視的更多資訊？
