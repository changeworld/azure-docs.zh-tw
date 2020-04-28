---
title: Azure 跨網路連線能力
description: 此頁面描述以 Azure 網路功能為基礎的跨網路連線和解決方案的應用程式案例。
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75644251"
---
# <a name="cross-network-connectivity"></a>跨網路連線能力

Fabrikam Inc. 在美國東部具有大型實體據點和 Azure 部署。 Fabrikam 可透過 ExpressRoute，在其內部部署與 Azure 部署之間進行後端連線。 同樣地，Contoso 公司在美國西部有提供和 Azure 部署。 Contoso 可透過 ExpressRoute，在其內部部署與 Azure 部署之間進行後端連線。  

Fabrikam Inc. 取得 Contoso 公司。在合併之後，Fabrikam 想要互連網路。 下圖說明這個案例：

 [![1]][1]

上圖中間的虛線箭號表示所需的網路互連。 具體而言，需要的交叉連線類型有三種：1） Fabrikam 和 Contoso Vnet cross connect、2）跨地區內部部署和 Vnet 交叉連線（也就是將 Fabrikam 內部部署網路連接到 Contoso VNet，並將 Contoso 內部部署網路連接到 Fabrikam VNet），以及3） Fabrikam 和 Contoso 內部部署網路交叉連接。 

下表顯示在合併之前，Contoso 公司 ExpressRoute 的私用對等互連路由表。

[![2]][2]

下表顯示在合併之前，Contoso 訂用帳戶中 VM 的有效路由。 根據資料表，VNet 上的 VM 會感知 VNet 位址空間和 Contoso 內部部署網路（與預設值分開）。 

[![4]][4]

下表顯示在合併之前，Fabrikam Inc. 的 ExpressRoute 之私用對等互連的路由表。

[![3]][3]

下表顯示 Fabrikam 訂用帳戶中的 VM 在合併之前的有效路由。 根據資料表，VNet 上的 VM 會感知 VNet 位址空間和 Fabrikam 內部部署網路（與預設值分開）。

[![5]][5]

在本文中，我們將逐步解說，並討論如何使用下列 Azure 網路功能來達成所需的交叉連接：

* [虛擬網路對等互連][Virtual network peering] 
* [虛擬網路 ExpressRoute 連線][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>交叉連接 Vnet

連線兩個虛擬網路時，虛擬網路對等互連（VNet 對等互連）可提供最高的最佳網路效能。 VNet 對等互連支援在同一個 Azure 區域（通常稱為 VNet 對等互連）和兩個不同的 Azure 區域（通常稱為全域 VNet 對等互連）中，對兩個 Vnet 的對等互連。 

讓我們在 Contoso 和 Fabrikam Azure 訂用帳戶中的 Vnet 之間設定全域 VNet 對等互連。 如需如何在兩個虛擬網路之間建立虛擬網路對等互連，請參閱[建立虛擬網路對等][Configure VNet peering]互連一文。

下圖顯示設定全域 VNet 對等互連之後的網路架構。

[![6]][6]

下表顯示 Contoso 訂用帳戶 VM 已知的路由。 請注意資料表的最後一個專案。 此專案是跨連接虛擬網路的結果。

[![7]][7]

下表顯示 Fabrikam 訂用帳戶 VM 已知的路由。 請注意資料表的最後一個專案。 此專案是跨連接虛擬網路的結果。

[![8]][8]

VNet 對等互連直接連結兩個虛擬網路（請參閱上述兩個表格中沒有*vnetglobalpeering 路由*專案的下一個躍點）

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>跨連接 Vnet 與內部部署網路

我們可以將 ExpressRoute 線路連線到多個虛擬網路。 請參閱訂用帳戶[和服務限制][Subscription limits]，以取得可連線至 ExpressRoute 線路的虛擬網路數目上限。 

讓我們將 Fabrikam ExpressRoute 線路連線至 Contoso 訂用帳戶 VNet，並將類似的 Contoso ExpressRoute 線路連接到 Fabrikam 訂用帳戶 VNet，以啟用虛擬網路與內部部署網路之間的交叉連線。 若要將虛擬網路連線至不同訂用帳戶中的 ExpressRoute 線路，我們需要建立並使用授權。  請參閱將[虛擬網路連線到 ExpressRoute 線路一][Connect-ER-VNet]文。

下圖顯示設定虛擬網路的 ExpressRoute 交叉連線之後的網路架構。

[![9]][9]

下表顯示 Contoso 公司 ExpressRoute 的私用對等互連路由表（在透過 ExpressRoute 將虛擬網路與內部部署網路交叉連接之後）。 查看路由表有屬於這兩個虛擬網路的路由。

[![10]][10]

下表顯示在透過 ExpressRoute 將虛擬網路與內部部署網路交叉連線之後，Fabrikam Inc. 的 ExpressRoute 之私用對等互連的路由表。 查看路由表有屬於這兩個虛擬網路的路由。

[![英寸]][11]

下表顯示 Contoso 訂用帳戶 VM 已知的路由。 請注意資料表的*虛擬網路閘道*專案。 VM 會看到兩個內部部署網路的路由。

[![12]][12]

下表顯示 Fabrikam 訂用帳戶 VM 已知的路由。 請注意資料表的*虛擬網路閘道*專案。 VM 會看到兩個內部部署網路的路由。

[![十三]][13]

>[!NOTE]
>在 Fabrikam 和/或 Contoso 訂用帳戶中，您也可以將輪輻 Vnet 至個別的中樞 VNet （本文的架構圖表中不會說明中樞和輪輻設計）。 中樞 VNet 閘道與 ExpressRoute 之間的交叉連線也會允許「東部」和「西部」中樞與輪輻之間的通訊。
>

## <a name="cross-connecting-on-premises-networks"></a>交叉連接內部部署網路

ExpressRoute Global 觸及會在連線到不同 ExpressRoute 線路的內部部署網路之間提供連接。 讓我們設定 Contoso 與 Fabrikam ExpressRoute 線路之間的全球範圍。 因為 ExpressRoute 線路位於不同的訂用帳戶中，所以我們需要建立並使用授權。 如需逐步指導方針，請參閱[設定 ExpressRoute 全球][Configure Global Reach]觸達文章。

下圖顯示設定全球範圍後的網路架構。

[![14]][14]

下表顯示 Contoso 公司 ExpressRoute 的私用對等互連路由表（在設定全球範圍之後）。 查看路由表有屬於內部部署網路的路由。 

[![15]][15]

下表顯示在設定全球範圍之後，Fabrikam Inc. 的 ExpressRoute 私人對等互連的路由表。 查看路由表有屬於內部部署網路的路由。

[![1600]][16]

## <a name="next-steps"></a>後續步驟

如需 VNet 和 VNet 對等互連的任何進一步問題，請參閱[虛擬網路常見問題][VNet-FAQ]。 如需 ExpressRoute 和虛擬網路連線能力的任何進一步問題，請參閱[EXPRESSROUTE 常見問題][ER-FAQ]。

全球範圍會依國家/地區在國家/地區推出。 若要查看全球範圍是否適用于您想要的國家/地區，請參閱[ExpressRoute 全球][Global Reach]觸達。

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "應用程式案例"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "合併前的 Contoso ExpressRoute 路由表"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "合併前的 Fabrikam ExpressRoute 路由表"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "合併前的 Contoso VM 路由"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "在合併前的 Fabrikam VM 路由"
[6]: ./media/cross-network-connectivity/vnet-peering.png "VNet 對等互連之後的架構"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "在 VNet 對等互連之後的 Contoso VM 路由"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "在 VNet 對等互連之後的 Fabrikam VM 路由"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "ExpressRoutes 交叉連線之後的架構"
[ ]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "跨連接 ExR 和 vnet 後的10個 Contoso ExpressRoute 路由表"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "交叉連接 ExR 和 vnet 後的 Fabrikam ExpressRoute 路由表"
[ ]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "在交叉連線 ExR 和 vnet 之後的12個 Contoso VM 路由"
[ ]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "交叉連接 ExR 和 vnet 後的13個 Fabrikam VM 路由"
設定 "全球範圍之後的" [14]: ./media/cross-network-connectivity/globalreach.png個架構
全球[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png位的 "Contoso ExpressRoute 路由表"
全球達到[16 個]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Fabrikam ExpressRoute 路由表"

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