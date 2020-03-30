---
title: Azure 跨網路連接
description: 本頁介紹基於 Azure 網路功能的跨網路連接和解決方案的應用程式方案。
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644251"
---
# <a name="cross-network-connectivity"></a>跨網路連線能力

Fabrikam Inc. 在美國東部具有大型實體據點和 Azure 部署。 Fabrikam 可透過 ExpressRoute，在其內部部署與 Azure 部署之間進行後端連線。 同樣，Contoso Ltd. 在美國西部也有存在和 Azure 部署。 Contoso 可透過 ExpressRoute，在其內部部署與 Azure 部署之間進行後端連線。  

法布裡卡姆公司收購康托索有限公司。合併後，法布裡卡姆希望互連網路。 下圖說明這個案例：

 [![1]][1]

上圖中間的虛線箭頭指示所需的網路互連。 具體來說，需要三種類型的交叉連接：1） 法布裡卡姆和 Contoso VNets 交叉連接，2） 交叉區域本地和 VNets 交叉連接（即將 Fabrikam 本地網路連接到 Contoso VNet 並連接 Contoso本地網路到法布裡卡姆 VNet），3） 法布裡卡姆和康托索本地網路交叉連接。 

下表顯示了合併前 Contoso Ltd. ExpressRoute 私人對等互連的路由表。

[![2]][2]

下表顯示了合併前 Contoso 訂閱中 VM 的有效路由。 根據表，VNet 上的 VM 知道 VNet 位址空間和 Contoso 本地網路，除了預設網路之外。 

[![4]][4]

下表顯示了合併前 Fabrikam Inc. 的 Express Route 私人對等互連的路由表。

[![3]][3]

下表顯示了合併前 Fabrikam 訂閱中 VM 的有效路由。 根據表，VNet 上的 VM 知道 VNet 位址空間和 Fabrikam 本地網路，除了預設網路外。

[![5]][5]

在本文中，讓我們逐步討論如何使用以下 Azure 網路功能實現所需的交叉連接：

* [虛擬網路對等互連][Virtual network peering] 
* [虛擬網路快速路由連接][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>交叉連接 VNet

虛擬網路對等互連（VNet 對等互連）在連接兩個虛擬網路時提供最優和最佳的網路性能。 VNet 對等互連支援在同一 Azure 區域（通常稱為 VNet 對等互連）和兩個不同的 Azure 區域（通常稱為全域 VNet 對等互連）中對等兩個 VNet。 

讓我們在 Contoso 中的 VNet 和 Fabrikam Azure 訂閱之間配置全域 VNet 對等互連。 有關如何在兩個虛擬網路之間創建虛擬網路對等互連，請參閱[創建虛擬網路對等式][Configure VNet peering]文章。

下圖顯示了配置全域 VNet 對等互連後的網路架構。

[![6]][6]

下表顯示了 Contoso 訂閱 VM 已知的路由。 注意表的最後一個條目。 此條目是交叉連接虛擬網路的結果。

[![7]][7]

下表顯示了 Fabrikam 訂閱 VM 已知的路由。 注意表的最後一個條目。 此條目是交叉連接虛擬網路的結果。

[![8]][8]

VNet 對等互連直接連結兩個虛擬網路（請參閱上述兩個表中沒有*VNetGlobal 對等互連*條目的下一個躍點）

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>將 VNet 與本地網路交叉連接

我們可以將 ExpressRoute 電路連接到多個虛擬網路。 有關可連接到 ExpressRoute 電路的最大虛擬網路數，請參閱[訂閱和服務限制][Subscription limits]。 

讓我們將 Fabrikam ExpressRoute 電路連接到 Contoso 訂閱 VNet，將同樣的 Contoso ExpressRoute 電路連接到 Fabrikam 訂閱 VNet，以實現虛擬網路和本地網路之間的交叉連接。 要將虛擬網路連接到其他訂閱中的 ExpressRoute 電路，我們需要創建和使用授權。  請參閱文章：[將虛擬網路連接到 ExpressRoute 電路][Connect-ER-VNet]。

下圖顯示了將 ExpressRoute 與虛擬網路交叉連接配置後的網路架構。

[![9]][9]

下表顯示了 Contoso Ltd 的 ExpressRoute 通過 ExpressRoute 將虛擬網路交叉連接到本地網路後，其專用對等互連的路由表。 查看路由表具有屬於兩個虛擬網路的路由。

[![10]][10]

下表顯示了 Fabrikam Inc. 的 Express Route 的專用對等互連的路由表，這些路由表通過 ExpressRoute 將虛擬網路交叉連接到本地網路。 查看路由表具有屬於兩個虛擬網路的路由。

[![11]][11]

下表顯示了 Contoso 訂閱 VM 已知的路由。 注意表的*虛擬網路閘道*條目。 VM 看到兩個本地網路的路由。

[![12]][12]

下表顯示了 Fabrikam 訂閱 VM 已知的路由。 注意表的*虛擬網路閘道*條目。 VM 看到兩個本地網路的路由。

[![13]][13]

>[!NOTE]
>在 Fabrikam 和/或 Contoso 訂閱中，您還可以將 VNet 與相應的中心 VNet 分支（本文中的體系結構圖中未說明中心和分支設計）。 樞紐 VNet 閘道與 ExpressRoute 之間的交叉連接還允許東西方樞紐和輻條之間的通信。
>

## <a name="cross-connecting-on-premises-networks"></a>交叉連接本地網路

ExpressRoute 全球覆蓋提供連接到不同 ExpressRoute 電路的本地網路之間的連接。 讓我們配置 Contoso 和 Fabrikam 快速路由電路之間的全域覆蓋範圍。 由於 ExpressRoute 電路位於不同的訂閱中，因此我們需要創建和使用授權。 有關逐步指導，請參閱[配置快速路由全域覆蓋][Configure Global Reach]文章。

下圖顯示了配置全域覆蓋後的網路架構。

[![14]][14]

下表顯示了配置全域覆蓋後 Contoso 有限公司快速路由的專用對等互連的路由表。 查看路由表具有屬於兩個本地網路的路由。 

[![15]][15]

下表顯示了在配置全域覆蓋後 Fabrikam Inc. 的 Express Route 的專用對等互連的路由表。 查看路由表具有屬於兩個本地網路的路由。

[![16]][16]

## <a name="next-steps"></a>後續步驟

有關 VNet 和 VNet 對等互連的任何其他問題，請參閱[虛擬網路常見問題解答][VNet-FAQ]。 有關快速路由和虛擬網路連接的任何問題，請參閱[ExpressRoute 常見問題解答][ER-FAQ]。

"全球覆蓋"按國家/地區按國家/地區推出。 要查看"全球覆蓋"是否在所需的國家/地區可用，請參閱[ExpressRoute 全球覆蓋。][Global Reach]

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "應用程式案例"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "合併前康托索快速路由表"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "合併前法布裡卡姆快速路線路線表"
[合併]: ./media/cross-network-connectivity/contosovm-routes-premerger.png前的 4 "個 Contoso VM 路由"
[合併]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png前的 5 "法布裡卡姆 VM 路線"
[6]: ./media/cross-network-connectivity/vnet-peering.png "VNet 對等互連後的體系結構"
[ ]: ./media/cross-network-connectivity/contosovm-routes-peering.png  "7 VNet 對等互連後的 Contoso VM 路由"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "VNet 對等互連後 Fabrikam VM 路由"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "快速路由交叉連接後的體系結構"
交叉連接 ExR 和 VNet 後[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "康托索快速路由表"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "交叉連接 ExR 和 VNet 後 Fabrikam 快速路由表"
 "交叉連接 ExR 和 VNet 後" [12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png個 Contoso VM 路由
 "交叉連接 ExR 和 VNet 後，13 法布裡卡姆 VM 路由"[ ]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png
[14]: ./media/cross-network-connectivity/globalreach.png "配置全域覆蓋後的體系結構"
全球覆蓋後的[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "個 Contoso 快速路由路由表"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "全球覆蓋後法布裡卡姆快速路線路線表"

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