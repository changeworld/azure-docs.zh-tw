---
title: 教學課程：針對 Microsoft 對等互連設定路由篩選 - Azure 入口網站
description: 本教學課程說明如何使用 Azure 入口網站針對 Microsoft 對等互連設定路由篩選。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: d142028d197f9e279b5f1e05757946dc40d2c153
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109131"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站針對 Microsoft 對等互連設定路由篩選

> [!div class="op_single_selector"]
> * [Azure 入口網站](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

路由篩選是透過 Microsoft 對等互連使用支援服務子集的方式。 這篇文章中的步驟可協助您設定和管理 ExpressRoute 線路的路由篩選。

Microsoft 365 服務 (例如 Exchange Online、SharePoint Online 和商務用 Skype) 可以透過 Microsoft 對等互連進行存取。 在 ExpressRoute 線路中設定 Microsoft 對等互連後，與這些服務相關的所有前置詞，都會透過建立的 BGP 工作階段進行公告。 BGP 社群值附加至每個前置詞，來識別透過前置詞提供的服務。 如需 BGP 社群值和它們對應之服務的清單，請參閱 [BGP 社群](expressroute-routing.md#bgp)。

與所有 Azure 和 Microsoft 365 服務的連線會導致大量的前置詞透過 BGP 進行公告。 大量前置詞會大幅增加網路內路由器維護的路由資料表大小。 如果您計劃僅使用透過 Microsoft 對等互連提供的服務子集，您可以用兩種方式減少路由資料表大小。 您可以：

* 在 BGP 社群上套用路由篩選，以篩選不想要的前置詞。 路由篩選是標準網路做法，在許多網路經常使用。

* 定義路由篩選，並將其套用到 ExpressRoute 線路。 路由篩選是新的資源，可讓您選取計劃透過 Microsoft 對等互連使用的服務清單。 ExpressRoute 路由器只會傳送屬於路由篩選中所識別之服務的前置詞清單。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> - 取得 BGP 社群值。
> - 建立路由篩選和篩選規則。
> - 將路由篩選關聯至 ExpressRoute 線路。

### <a name="about-route-filters"></a><a name="about"></a>關於路由篩選

在 ExpressRoute 線路上設定 Microsoft 對等互連後，Microsoft 邊緣路由器會透過您的連線提供者，建立一組 BGP 工作階段與邊緣路由器。 沒有路由會公告至您的網路。 若要讓路由公告至您的網路，您必須建立與路由篩選的關聯。

路由篩選可讓您識別想要透過 ExpressRoute 線路的 Microsoft 對等互連使用的服務。 這基本上是所有 BGP 社群值的允許清單。 一旦定義路由篩選資源，並且連結至 ExpressRoute 線路，對應到 BGP 社群值的所有前置詞都會公告至您的網路。

若要連結路由篩選與 Office 365 服務，您必須具有透過 ExpressRoute 取用 Microsoft 365 服務的授權。 如果您未獲授權透過 ExpressRoute 取用 Microsoft 365 服務，連結路由篩選的作業會失敗。 如需授權程序的詳細資訊，請參閱 [Azure ExpressRoute for Microsoft 365](/microsoft-365/enterprise/azure-expressroute)。

> [!IMPORTANT]
> 在 2017 年 8 月 1 日以前設定之 ExpressRoute 線路的 Microsoft 對等互連，會透過 Microsoft 對等互連公告所有服務首碼，即使未定義路由篩選也一樣。 在 2017 年 8 月 1 日當日或以後設定之 ExpressRoute 線路的 Microsoft 對等互連，不會公告任何首碼，直到路由篩選連結至線路為止。
> 

## <a name="prerequisites"></a>必要條件

- 開始設定之前，請檢閱[必要條件](expressroute-prerequisites.md)和[工作流程](expressroute-workflows.md)。

- 您必須具有已佈建 Microsoft 對等互連的使用中 ExpressRoute 線路。 您可以使用下列指示來完成這些工作：
  - 繼續之前，請[建立 ExpressRoute 線路](expressroute-howto-circuit-portal-resource-manager.md)，並由您的連線提供者來啟用該線路。 ExpressRoute 線路必須處於已佈建及已啟用的狀態。
  - [建立 Microsoft 對等互連](expressroute-howto-routing-portal-resource-manager.md)，如果您直接管理 BGP 工作階段。 或者，讓您的連線提供者為您的線路佈建 Microsoft 對等互連。

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>取得前置詞和 BGP 社群值的清單

### <a name="get-a-list-of-bgp-community-values"></a>取得 BGP 社群值的清單

與服務相關的 BGP 社群值可透過 [ExpressRoute 路由需求](expressroute-routing.md) 頁面中提供的 Microsoft 對等互連存取。

### <a name="make-a-list-of-the-values-that-you-want-to-use"></a>製作您想要使用的值清單

製作您想要在路由篩選中使用的 [BGP 社群值](expressroute-routing.md#bgp)清單。 

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>建立路由篩選和篩選規則

路由篩選只能有一個規則，且規則的類型必須是 'Allow'。 此規則可以具有與其相關聯的 BGP 社群值清單。

1. 選取 [建立資源]，然後搜尋「路由篩選」，如下圖所示：

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter.png" alt-text="顯示 [路由篩選] 頁面的螢幕擷取畫面":::

1. 在資源群組中放置路由篩選。 確定位置與 ExpressRoute 線路相同。 選取 [檢閱 + 建立]，然後選取 [建立]。

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter-basic.png" alt-text="顯示 [路由篩選] 頁面的螢幕擷取畫面":::

### <a name="create-a-filter-rule"></a>建立篩選規則

1. 若要新增及更新規則，請為您的路由篩選選取 [管理規則] 索引標籤。

    :::image type="content" source="./media/how-to-routefilter-portal/manage-route-filter.png" alt-text="顯示 [路由篩選] 頁面的螢幕擷取畫面":::

1. 從下拉式清單選取您想要連線的服務，並且在完成時儲存規則。

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="顯示 [路由篩選] 頁面的螢幕擷取畫面":::

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>將路由篩選連結至 ExpressRoute 線路

選取 [+ 新增線路] 按鈕，並從下拉式清單選取 ExpressRoute 線路，將路由篩選附加至線路。

:::image type="content" source="./media/how-to-routefilter-portal/add-circuit-to-route-filter.png" alt-text="顯示 [路由篩選] 頁面的螢幕擷取畫面":::

如果連線提供者為您的 ExpressRoute 線路設定對等互連，請先重新整理 ExpressRoute 線路頁面中的線路，再選取 [+ 新增線路] 按鈕。

:::image type="content" source="./media/how-to-routefilter-portal/refresh-express-route-circuit.png" alt-text="顯示 [路由篩選] 頁面的螢幕擷取畫面":::

## <a name="common-tasks"></a><a name="tasks"></a>常見工作

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>若要取得路由篩選的屬性

當您在入口網站中開啟資源時，可以檢視路由篩選的屬性。

:::image type="content" source="./media/how-to-routefilter-portal/view-route-filter.png" alt-text="顯示 [路由篩選] 頁面的螢幕擷取畫面":::

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>若要更新路由篩選的屬性

1. 可以藉由選取 [管理規則] 按鈕，更新附加至線路的 BGP 社群值清單。

    :::image type="content" source="./media/how-to-routefilter-portal/update-route-filter.png" alt-text="顯示 [路由篩選] 頁面的螢幕擷取畫面":::

1. 選取您想要的服務群，然後選取 [儲存]。

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="顯示 [路由篩選] 頁面的螢幕擷取畫面":::

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>若要從 ExpressRoute 線路取消連結路由篩選

若要從路由篩選將線路中斷連結，請以滑鼠右鍵按一下線路並按一下 [取消關聯]。

:::image type="content" source="./media/how-to-routefilter-portal/detach-route-filter.png" alt-text="顯示 [路由篩選] 頁面的螢幕擷取畫面":::


## <a name="clean-up-resources"></a>清除資源

可以選取 [刪除] 按鈕來刪除路由篩選。 執行此動作之前，請確定路由篩選未與任何線路產生關聯。

:::image type="content" source="./media/how-to-routefilter-portal/delete-route-filter.png" alt-text="顯示 [路由篩選] 頁面的螢幕擷取畫面":::

## <a name="next-steps"></a>後續步驟

如需路由器設定範例的相關資訊，請參閱：

> [!div class="nextstepaction"]
> [設定和管理路由的路由器組態範例](expressroute-config-samples-routing.md)
