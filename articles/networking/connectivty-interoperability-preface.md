---
title: Azure 後端連線功能的互通性：測試設定 | Microsoft Docs
description: 本文說明的測試設定可供您分析 Azure 中 ExpressRoute、站對站 VPN 及虛擬網路對等互連之間的互通性。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 0cbd4b620a03ed26e95679cf7cb1abef277a9471
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873790"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Azure 後端連線功能的互通性：測試設定

本文說明的測試設定，可供您分析 Azure 網路服務如何在控制平面層級和資料平面層級進行相互操作。 讓我們簡短探討 Azure 網路元件：

-   **Azure ExpressRoute**：使用 Azure ExpressRoute 中的私人對等互連，直接將內部部署網路中的私人 IP 空間連線至 Azure 虛擬網路部署。 ExpressRoute 可協助您獲得更高頻寬和私人連線。 許多 ExpressRoute 環保合作夥伴可透過 SLA 提供 ExpressRoute 連線。 若要深入了解 ExpressRoute 以及了解如何設定 ExpressRoute，請參閱 [ExpressRoute 簡介][ExpressRoute]。
-   **站對站 VPN**：您可以使用 Azure VPN 閘道作為站對站 VPN，以透過網際網路或利用 ExpressRoute 將內部部署網路安全地連線到 Azure。 若要深入了解如何設定站對站 VPN 以連線到 Azure，請參閱[設定 VPN 閘道][VPN]。
-   **VNet 對等互連**：使用虛擬網路 (VNet) 對等互連來建立 Azure 虛擬網路中 VNet 之間的連線。 若要深入了解 VNet 對等互連，請參閱 [VNet 對等互連教學課程][VNet]。

## <a name="test-setup"></a>測試設定

下圖說明測試設定：

![1][1]

測試設定的核心部分是 Azure 區域 1 的中樞 VNet。 中樞 VNet 會以下列方式連線到不同的網路：

-   中樞 Vnet 會使用 VNet 對等互連來連線到輪輻 VNet。 輪輻 VNet 可在中樞 VNet 中遠端存取兩種閘道。
-   中樞 VNet 會使用站對站 VPN 來連線到分支 VNet。 連線使用 eBGP 來交換路由。
-   中樞 VNet 會使用 ExpressRoute 私人對等互連作為主要路徑，連線到內部部署位置 1 網路。 它會使用站對站 VPN 連線作為備份路徑。 在本文的其餘部分，我們會將此 ExpressRoute 線路稱為 ExpressRoute 1。 根據預設，ExpressRoute 線路會提供備援連線以獲得高可用性。 在 ExpressRoute 1 上，面對次要 Microsoft Enterprise Edge (MSEE) 路由器的次要客戶邊緣 (CE) 路由器的子介面已停用。 上圖中雙線箭號上的紅線表示已停用的 CE 路由器子介面。
-   中樞 VNet 會使用另一個 ExpressRoute 私人對等互連，連線到內部部署位置 2 網路。 在本文的其餘部分，我們會將這第二個 ExpressRoute 線路稱為 ExpressRoute 2。
-   ExpressRoute 1 也會將中樞 VNet 和內部部署位置 1 網路連線至 在 Azure 區域 2 中的遠端 VNet。

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

深入了解測試拓撲的[設定詳細資料][Configuration]。

了解測試設定的[控制平面分析][Control-Analysis]以及拓撲中不同 VNet 或 VLAN 的檢視。

深入了解[資料平面分析][Data-Analysis]和 Azure 網路監視功能檢視。

請參閱 [ExpressRoute 常見問題集][ExR-FAQ]：
-   了解可以連線到 ExpressRoute 閘道的 ExpressRoute 線路數量。
-   了解可以連接到 ExpressRoute 線路的 ExpressRoute 閘道數量。
-   了解 ExpressRoute 的其他規模限制。


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "測試拓撲圖"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha


