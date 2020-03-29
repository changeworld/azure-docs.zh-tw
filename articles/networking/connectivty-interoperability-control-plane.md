---
title: Azure 後端連線功能的互通性：控制平面分析 | Microsoft Docs
description: 本文提供測試設定的控制平面分析，您可用來分析 Azure 中 ExpressRoute、站對站 VPN 及虛擬網路對等互連之間的互通性。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4921e4c4fc0da95250a0171c66d6a69093b10687
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873840"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Azure 後端連線功能的互通性：控制平面分析

本文說明[測試設定][Setup]的控制平面分析。 您也可以檢閱測試設定的[測試設定組態][Configuration]和[資料平面分析][Data-Analysis]。

控制平面分析基本上會在拓撲中檢查網路間交換的路由。 控制平面分析可協助您了解拓撲中不同網路檢視的差異。

## <a name="hub-and-spoke-vnet-perspective"></a>中樞和輪輻視角

下圖從中樞虛擬網路 (VNet) 和輪輻 VNet (藍色醒目顯示) 的視角說明網路。 此圖也顯示不同網路的自治系統號碼 (ASN)，以及在不同網路之間交換的路由： 

![1][1]

VNet 的 Azure ExpressRoute 閘道 ASN 與 Microsoft Enterprise Edge Router (MSEE) 的 ASN 不同。 ExpressRoute 閘道使用私人 ASN (值為 **65515**)，MSEE 則使用全球公用 ASN (值為 **12076**)。 配置 ExpressRoute 對等互連時，由於 MSEE 是對等體，因此使用**12076**作為對等 ASN。 在 Azure 端，MSEE 透過 ExpressRoute 閘道建立 eBGP 對等互連。 MSEE 為個別 ExpressRoute 對等互連建立的雙重 eBGP 對等互連於控制平面層級中為公開透明狀態。 因此，當您檢視 ExpressRoute 路由表時，您會看到 VNet 的首碼為 VNet 的 ExpressRoute 閘道 ASN。 

以下顯示 ExpressRoute 路由表範例： 

![5][5]

在 Azure 中，ASN 只有在對等互連視角中才屬重要資訊。 根據預設，Azure VPN 閘道中 ExpressRoute 閘道和 VPN 閘道的 ASN 均為 **65515**。

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>透過 ExpressRoute 1 的內部部署位置 1 和遠端 VNet 視角

內部部署位置 1 和遠端 VNet 都會透過 ExpressRoute 1 連線到中樞 VNet。 它們會共用相同的拓撲視角，如下圖所示︰

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>透過站對站 VPN 的內部部署位置 1 和分支 VNet 視角

內部部署位置 1 和分支 VNet 都會透過站對站 VPN 連線來連線到中樞 VNet 的 VPN 閘道。 它們會共用相同的拓撲視角，如下圖所示︰

![3][3]

## <a name="on-premises-location-2-perspective"></a>內部部署位置 2 視角

內部部署位置 2 會透過 ExpressRoute 2 的私人對等互連連線至中樞 VNet： 

![4][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute 和站對站 VPN 連線串聯

###  <a name="site-to-site-vpn-over-expressroute"></a>透過 ExpressRoute 設定站對站 VPN

您可透過 ExpressRoute Microsoft 對等互連設定站對站 VPN，私下交換您內部部署網路和 Azure VNet 之間的資料。 使用此組態時，您可以交換具有機密性、真實性和完整性的資料。 資料交換也會禁止重新播放。 如需如何使用 ExpressRoute Microsoft 對等互連，來設定通道模式站對站 IPSec VPN 的詳細資訊，請參閱[透過 ExpressRoute Microsoft 對等互連設定站對站 VPN][S2S-Over-ExR]。 

設定使用 Microsoft 對等互連的站對站 VPN 的主要限制是輸送量。 IPSec 通道的輸送量受限於 VPN 閘道容量。 VPN 閘道輸送量低於 ExpressRoute 輸送量。 在此情況下，針對高安全流量使用 IPSec 通道，並針對所有其他流量使用私人對等互連，可協助將 ExpressRoute 頻寬使用量最佳化。

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>站對站 VPN 作為 ExpressRoute 的安全容錯移轉路徑

ExpressRoute 作為備援線路組可確保高可用性。 您可以在不同的 Azure 區域中設定異地備援 ExpressRoute 連線。 如果您想在 Azure 區域中為 ExpressRoute 連線建立容錯移轉路徑，可以透過與測試設定中示範的相同方法使用站對站 VPN。 當 ExpressRoute 和站對站 VPN 顯示相同的首碼時，Azure 將優先選擇 ExpressRoute。 為了避免 ExpressRoute 和站對站 VPN 擁有不對稱的路由，內部部署網路組態也應該在使用站對站 VPN 連線之前使用 ExpressRoute 連線進行回應。

如需如何設定 ExpressRoute 和站對站 VPN 共存連線的詳細資訊，請參閱 [ExpressRoute 和站對站共存][ExR-S2S-CoEx]。

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>將後端連線擴充至輪輻 VNet 和分支位置

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>使用 VNet 對等互連的輪輻 VNet 連線

中樞和輪輻 VNet 架構為普遍採用的選項。 中樞是 Azure 中的 VNet，可在您的輪輻 VNet 和內部部署網站之間作為連線中心點。 輪輻是與中樞對等互連的 VNet，可用於隔離工作負載。 流量會透過 ExpressRoute 或 VPN 連線，在內部部署資料中心和中樞之間流動。 如需架構的詳細資訊，請參閱[在 Azure 中實作中樞輪輻網路拓撲][Hub-n-Spoke]。

區域中的 VNet 對等互連可讓輪輻 VNet 使用中樞 VNet 閘道 (VPN 和 ExpressRoute 閘道) 與遠端網路通訊。

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>使用站對站 VPN 的分支 VNet 連線

您可能希望分支 VNet (位於不同區域) 和內部部署網路透過中樞 VNet 與彼此通訊。 此設定的原生 Azure 解決方案是使用 VPN 的站對站 VPN 連線。 替代方式是使用網路虛擬設備 (NVA) 在中樞中進行路由。

如需詳細資訊，請參閱[什麼是 VPN 閘道？][VPN]和[部署高可用性 NVA][Deploy-NVA]。

## <a name="next-steps"></a>後續步驟

深入了解[資料平面分析][Data-Analysis]和 Azure 網路監視功能檢視。

請參閱 [ExpressRoute 常見問題集][ExR-FAQ]：
-   了解可以連線到 ExpressRoute 閘道的 ExpressRoute 線路數量。
-   了解可以連接到 ExpressRoute 線路的 ExpressRoute 閘道數量。
-   了解 ExpressRoute 的其他規模限制。


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "拓撲的集線器和分支 VNet 透視"
[2]: ./media/backend-interoperability/Loc1ExRView.png "通過 ExpressRoute 1 對拓撲的位置 1 和遠端 VNet 透視"
[3]: ./media/backend-interoperability/Loc1VPNView.png "通過網站到網站 VPN 對拓撲的位置 1 和分支 VNet 透視"
[4]: ./media/backend-interoperability/Loc2View.png "拓撲位置 2 透視"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "快速路由 1 路由表"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


