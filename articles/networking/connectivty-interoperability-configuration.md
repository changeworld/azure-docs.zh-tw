---
title: Azure 後端連線功能的互通性：組態詳細資料 |Microsoft Docs
description: 本文說明您可用來分析互通性 (ExpressRoute、站對站 VPN 和在 Azure 中對等互連的虛擬網路之間) 之測試設定的組態詳細資料。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 9c4a57111566248d3537cab0d9d85c0c3be874a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68335938"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Azure 後端連線功能的互通性：測試組態詳細資料

本文將介紹[測試設定][Setup]的組態詳細資料。 測試設定可協助您分析 Azure 網路服務如何在控制平面層級和資料平面層級進行相互操作。

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>使用 VNet 對等互連的輪輻 VNet 連線

下圖顯示了輪輻虛擬網路 (VNet) 的 Azure 虛擬網路對等互連詳細資料。 若要了解如何設定兩個 Vnet 之間的對等互連，請參閱[管理 VNet 對等互連][VNet-Config]。 如要讓輪輻 VNet 使用連線至中樞 VNet 的閘道，請選取 [使用遠端閘道]****。

[![1]][1]

下圖顯示中樞 VNet 的 VNet 對等互連詳細資料。 如果希望中心 VNet 允許分支 VNet 使用集線器的閘道，請選擇"**允許閘道傳輸**"。

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>使用站對站 VPN 的分支 VNet 連線

透過使用 Azure VPN 閘道中的 VPN 閘道，設定中樞和分支 VNet 間的站對站 VPN 連線。 根據預設，VPN 閘道和 Azure ExpressRoute 閘道使用私用的自發系統編號 (ASN) 值 **65515**。 您可以變更 VPN 閘道的 ASN 值。 在測試設定中，分支 VNet VPN 閘道的 ASN 值變更為 **65516**，以支援中樞和分支 VNet 之間的 eBGP 路由。


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>使用 ExpressRoute 和站對站 VPN 的內部部署位置 1 連線

### <a name="expressroute-1-configuration-details"></a>ExpressRoute 1 組態詳細資料

下圖顯示 Azure 區域 1 ExpressRoute 線路至內部部署位置 1 客戶 Edge (CE) 路由器的設定：

[![4]][4]

下圖顯示 ExpressRoute1 線路和中樞 VNet 之間的連線設定：

[![5]][5]

下列清單顯示 ExpressRoute 私人對等互連連線的主要 CE 路由器設定。 （Cisco ASR1000 路由器在測試設定中用作 CE 路由器。當並行配置網站到網站 VPN 和 ExpressRoute 電路以將本地網路連接到 Azure 時，Azure 預設優先處理 ExpressRoute 電路。 若要避免不對稱的路由，內部部署網路也應優先考慮 ExpressRoute 連線而非站對站 VPN 連線。 下列組態會使用 BGP **local-preference** 屬性建立優先順序：

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

### <a name="site-to-site-vpn-configuration-details"></a>站對站 VPN 組態詳細資料

下列清單顯示站對站 VPN 連線的主要 CE 路由器組態：

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>使用 ExpressRoute 的內部部署位置 2 連線

第二個 ExpressRoute 線路更接近內部部署的位置 2，可將內部部署位置 2 連線至中樞 VNet。 下圖顯示第二個 ExpressRoute 組態：

[![6]][6]

下圖顯示第二個 ExpressRoute 線路和中樞 VNet 之間的連線設定：

[![7]][7]

ExpressRoute 1 會在不同的 Azure 區域將中樞 Vnet 和內部部署位置 1 連線至遠端 Vnet：

[![8]][8]

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

了解測試設定的[控制平面分析][Control-Analysis]以及拓撲中不同 VNet 或 VLAN 的檢視。

深入了解[資料平面分析][Data-Analysis]和 Azure 網路監視功能檢視。

請參閱 [ExpressRoute 常見問題集][ExR-FAQ]：
-   了解可以連線到 ExpressRoute 閘道的 ExpressRoute 線路數量。
-   了解可以連接到 ExpressRoute 線路的 ExpressRoute 閘道數量。
-   了解 ExpressRoute 的其他規模限制。


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "輪輻 VNet 的 VNet 對等互連"
[2]: ./media/backend-interoperability/HubVNet-peering.png "中樞 VNet 的 VNet 對等互連"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "分支 VNet 的 VPN 閘道組態"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute 1 組態"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "ExpressRoute 1 至中樞 VNet ExR 閘道的連線組態"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute 2 組態"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "ExpressRoute 2 至中樞 VNet ExR 閘道的連線組態"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "ExpressRoute 2 至遠端 VNet ExR 閘道的連線組態"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
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


