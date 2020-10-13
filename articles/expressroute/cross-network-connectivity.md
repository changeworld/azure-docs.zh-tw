---
title: Azure 跨網路連線能力
description: 此頁面描述以 Azure 網路功能為基礎的跨網路連線和解決方案的應用程式案例。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: duau
ms.openlocfilehash: f13e3c03c0cfd747c7819d95a5fb98560db861c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89398064"
---
# <a name="cross-network-connectivity"></a>跨網路連線能力

Fabrikam Inc. 在美國東部具有大型實體據點和 Azure 部署。 Fabrikam 可透過 ExpressRoute，在其內部部署與 Azure 部署之間進行後端連線。 同樣地，Contoso 公司也有美國西部的存在和 Azure 部署。 Contoso 可透過 ExpressRoute，在其內部部署與 Azure 部署之間進行後端連線。  

Fabrikam Inc. 取得 Contoso 公司。在合併之後，Fabrikam 想要將網路互連。 下圖說明這個案例：

![應用程式案例](./media/cross-network-connectivity/premergerscenario.png)

上圖中間的虛線箭號表示所要的網路互連。 具體來說，有三種類型的交叉連接： 1) Fabrikam 和 Contoso Vnet 交叉連線、2) 跨區域內部部署和 Vnet 交叉連接 (也就是說，將 Fabrikam 內部部署網路連線到 Contoso VNet，以及將 Contoso 內部部署網路連線至 Fabrikam VNet) ，以及 3) Fabrikam 和 Contoso 內部部署網路交叉連接。 

下表顯示在合併之前，Contoso 公司的 ExpressRoute 私人對等互連的路由表。

![合併之前的 Contoso ExpressRoute 路由表](./media/cross-network-connectivity/contosoexr-rt-premerger.png)

下表顯示在合併之前，Contoso 訂用帳戶中 VM 的有效路由。 根據每個資料表，VNet 上的 VM 會察覺 VNet 位址空間和 Contoso 內部部署網路（除了預設值之外）。

![合併之前的 Contoso VM 路由](./media/cross-network-connectivity/contosovm-routes-premerger.png)

下表顯示 Fabrikam Inc. 的 ExpressRoute 在合併之前的私人對等互連路由表。

![合併之前的 Fabrikam ExpressRoute 路由表](./media/cross-network-connectivity/fabrikamexr-rt-premerger.png)

下表顯示 Fabrikam 訂用帳戶中的 VM 在合併之前的有效路由。 根據每個資料表，VNet 上的 VM 會察覺 VNet 位址空間和 Fabrikam 內部部署網路（除了預設值之外）。

![合併前的 Fabrikam VM 路由](./media/cross-network-connectivity/fabrikamvm-routes-premerger.png)

在本文中，我們將逐步解說並討論如何使用下列 Azure 網路功能來達成所需的交叉連接：

* [虛擬網路對等互連][Virtual network peering] 
* [虛擬網路 ExpressRoute 連接][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>交叉連接 Vnet

虛擬網路對等互連 (VNet 對等互連) 可在連接兩個虛擬網路時提供最理想且最適合的網路效能。 VNet 對等互連支援相同 Azure 區域內的兩個 Vnet， (通常稱為 VNet 對等互連) 和兩個不同的 Azure 區域 (通常稱為全域 VNet 對等互連) 。 

讓我們設定 Contoso 和 Fabrikam Azure 訂用帳戶中 Vnet 之間的全域 VNet 對等互連。 如需如何建立兩個虛擬網路之間的虛擬網路對等互連，請參閱 [建立虛擬網路對等互連][Configure VNet peering] 一文。

下圖顯示設定全域 VNet 對等互連之後的網路架構。

![VNet 對等互連之後的架構](./media/cross-network-connectivity/vnet-peering.png )

下表顯示 Contoso 訂用帳戶 VM 已知的路由。 留意資料表的最後一個專案。 此專案是交叉連接虛擬網路的結果。

![VNet 對等互連之後的 Contoso VM 路由](./media/cross-network-connectivity/contosovm-routes-peering.png)

下表顯示 Fabrikam 訂用帳戶 VM 已知的路由。 留意資料表的最後一個專案。 此專案是交叉連接虛擬網路的結果。

![VNet 對等互連後的 Fabrikam VM 路由](./media/cross-network-connectivity/fabrikamvm-routes-peering.png)

VNet 對等互連會直接連結兩個虛擬網路 (查看上述兩個數據表中沒有下一個躍點來 *>vnetglobalpeering* 專案) 

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>跨連接 Vnet 至內部部署網路

我們可以將 ExpressRoute 線路連線至多個虛擬網路。 針對可連接至 ExpressRoute 線路的虛擬網路數目上限，請參閱訂用帳戶 [和服務限制][Subscription limits] 。 

讓我們將 Fabrikam ExpressRoute 線路連接到 Contoso 訂用帳戶 VNet，並將類似的 Contoso ExpressRoute 電路連接至 Fabrikam 訂用帳戶 VNet，以啟用虛擬網路與內部部署網路之間的交叉連線。 若要將虛擬網路連線至不同訂用帳戶中的 ExpressRoute 線路，我們需要建立並使用授權。  請參閱文章： [將虛擬網路連線到 ExpressRoute 線路][Connect-ER-VNet]。

下圖顯示在設定 ExpressRoute 跨虛擬網路連線之後的網路架構。

![Expressroute 交叉連接之後的架構](./media/cross-network-connectivity/exr-x-connect.png)

下表顯示在透過 ExpressRoute 將虛擬網路連線到內部部署網路之後，Contoso 公司的 ExpressRoute 私人對等互連的路由表。 查看路由表是否有屬於這兩個虛擬網路的路由。

![跨連接 ExR 和 Vnet 之後的 Contoso ExpressRoute 路由表](./media/cross-network-connectivity/contosoexr-rt-xconnect.png)

下表顯示在透過 ExpressRoute 將虛擬網路連線到內部部署網路之後，Fabrikam Inc. 之 ExpressRoute 的私人對等互連的路由表。 查看路由表是否有屬於這兩個虛擬網路的路由。

![跨連接 ExR 和 Vnet 之後的 Fabrikam ExpressRoute 路由表](./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png)

下表顯示 Contoso 訂用帳戶 VM 已知的路由。 留意資料表的 *虛擬網路閘道* 專案。 VM 會看到內部部署網路的路由。

![跨連接 ExR 和 Vnet 之後的 Contoso VM 路由](./media/cross-network-connectivity/contosovm-routes-xconnect.png)

下表顯示 Fabrikam 訂用帳戶 VM 已知的路由。 留意資料表的 *虛擬網路閘道* 專案。 VM 會看到內部部署網路的路由。

![跨連接 ExR 和 Vnet 之後的 Fabrikam VM 路由](./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png)

>[!NOTE]
>在 Fabrikam 和/或 Contoso 訂用帳戶中，您也可以將輪輻 Vnet 至各自的中樞 VNet (中樞和輪輻設計不會在本文的架構圖表中說明) 。 中樞 VNet 閘道與 ExpressRoute 之間的交叉連線，也會允許在東部與西部中樞和輪輻之間進行通訊。
>

## <a name="cross-connecting-on-premises-networks"></a>交叉連接內部部署網路

ExpressRoute Global 觸及提供連線到不同 ExpressRoute 線路的內部部署網路之間的連線能力。 讓我們設定 Contoso 和 Fabrikam ExpressRoute 線路之間的全球接觸。 因為 ExpressRoute 線路位於不同的訂用帳戶中，所以我們需要建立並使用授權。 如需逐步指引，請參閱 [設定 ExpressRoute 全球存取範圍][Configure Global Reach] 文章。

下圖顯示設定全球存取範圍之後的網路架構。

![設定全球範圍之後的架構](./media/cross-network-connectivity/globalreach.png)

下表顯示 Contoso 公司 ExpressRoute 的私人對等互連的路由表（在設定全球存取範圍之後）。 查看路由表是否有屬於內部部署網路的路由。 

![全球接觸之後的 Contoso ExpressRoute 路由表](./media/cross-network-connectivity/contosoexr-rt-gr.png)

下表顯示 Fabrikam Inc. 之 ExpressRoute 的私人對等互連的路由表，並在設定全球存取範圍之後。 查看路由表是否有屬於內部部署網路的路由。

![全球接觸之後的 Fabrikam ExpressRoute 路由表]( ./media/cross-network-connectivity/fabrikamexr-rt-gr.png )

## <a name="next-steps"></a>後續步驟

如需 VNet 和 VNet 對等互連的任何進一步問題，請參閱 [虛擬網路常見問題][VNet-FAQ]。 如需 ExpressRoute 和虛擬網路連線能力的任何進一步問題，請參閱 [EXPRESSROUTE 常見問題][ER-FAQ] 。

全球接觸會依國家/地區在國家/地區推出。 若要查看您想要的國家/地區是否有全球接觸範圍，請參閱 [ExpressRoute 全球接觸][Global Reach]。

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq