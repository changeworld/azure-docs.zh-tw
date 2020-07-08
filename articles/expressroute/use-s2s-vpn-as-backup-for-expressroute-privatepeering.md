---
title: 使用 S2S VPN 做為 Azure ExpressRoute 私用對等互連的備份 |Microsoft Docs
description: 本頁面提供使用 S2S VPN 來備份 Azure ExpressRoute 私用對等互連的架構建議。
services: networking
author: rambk
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: cb6ebdcae837216efac5b9333789dee032219251
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84738085"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>使用 S2S VPN 做為 ExpressRoute 私用對等互連的備份

在標題為[使用 expressroute 私用對等互連進行嚴重損壞修復設計][DR-PP]的文章中，我們已討論過 expressroute 私用對等互連連線的備份連線解決方案需求，以及如何針對目的使用異地多餘的 ExpressRoute 線路。 在本文中，讓我們考慮如何利用和維護站對站（S2S） VPN 作為 ExpressRoute 私用對等互連的背面。 

不同于異地多餘的 ExpressRoute 線路，您只能在主動-被動模式中使用 ExpressRoute-VPN 嚴重損壞修復組合。 在被動模式中使用任何備份網路連線的主要挑戰是，被動連線通常會與主要連線一起失敗。 被動連線失敗的常見原因是缺少主動維護。 因此，在本文中，我們將焦點放在如何驗證和主動維護備份 ExpressRoute 私人對等互連的 S2S VPN 連線能力。

>[!NOTE] 
>當指定的路由透過 ExpressRoute 和 VPN 來公告時，Azure 會偏好透過 ExpressRoute 路由傳送。  
>

在本文中，我們將瞭解如何從 Azure 觀點和用戶端網路邊緣觀點來驗證連線能力。 無論您是否管理與 Microsoft 網路實體對等的用戶端網路裝置，都能從任一端進行驗證。 

## <a name="example-topology"></a>範例拓撲

在我們的設定中，我們有一個透過 ExpressRoute 線路和一個 S2S VPN 連線連接到 Azure 中樞 VNet 的內部部署網路。 Azure 中樞 VNet 接著會對等互連到輪輻 VNet，如下圖所示：

![1][1]

在安裝程式中，ExpressRoute 線路會在內部部署的一對「客戶邊緣」（CE）路由器上終止。 內部部署 LAN 是透過一組防火牆（以領導人執行者模式運作）連接到 CE 路由器。 S2S VPN 會直接在防火牆上終止。

下表列出拓撲的金鑰 IP 首碼：

| **實體** | **前置詞** |
| --- | --- |
| 內部部署 LAN | 10.1.11.0/25 |
| Azure 中樞 VNet | 10.17.11.0/25 |
| Azure 輪輻 VNet | 10.17.11.128/26 |
| 內部部署測試伺服器 | 10.1.11.10 |
| 輪輻 VNet 測試 VM | 10.17.11.132 |
| ExpressRoute 主要連接 p2p 子網 | 192.168.11.16/30 |
| ExpressRoute 次要連接 p2p 子網 | 192.168.11.20/30 |
| VPN 閘道主要 BGP 對等互連 IP | 10.17.11.76 |
| VPN 閘道次要 BGP 對等互連 IP | 10.17.11.77 |
| 內部部署防火牆 VPN BGP 對等互連 IP | 192.168.11.88 |
| 通往防火牆 IP 的主要 CE 路由器 i/f | 192.168.11.0/31 |
| 通往主要 CE 路由器 IP 的防火牆 i/f | 192.168.11.1/31 |
| 針對防火牆 IP 的次要 CE 路由器 i/f | 192.168.11.2/31 |
| 通往次要 CE 路由器 IP 的防火牆 i/f | 192.168.11.3/31 |


下表列出拓撲的 Asn：

| **自發系統** | **ASN** |
| --- | --- |
| 內部部署 | 65020 |
| Microsoft 企業邊緣 | 12076 |
| 虛擬網路 GW （ExR） | 65515 |
| 虛擬網路 GW （VPN） | 65515 |

## <a name="high-availability-without-asymmetricity"></a>沒有 asymmetricity 的高可用性

### <a name="configuring-for-high-availability"></a>設定高可用性

[設定 ExpressRoute 和站對站][Conf-CoExist]並存連線討論如何設定並存的 ExpressRoute 線路和 S2S VPN 連接。 如我們在[使用 Expressroute 設計高可用性][HA]中所討論，為了改善 expressroute 高可用性，我們的安裝程式會維護網路的冗余（避免單一失敗點），直到端點為止。 此外，ExpressRoute 線路的主要和次要連線都會設定為以主動-主動模式運作，方法是透過這兩個連線的相同方式來公告內部部署首碼。 

透過 ExpressRoute 線路主要連線的主要 CE 路由器的內部部署路由通告如下所示（Junos 命令）：

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

透過 ExpressRoute 線路的次要連線來進行次要 CE 路由器的內部部署路由通告，如下所示（Junos 命令）：

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

為了改善備份連接的高可用性，也會以主動-主動模式設定 S2S VPN。 Azure VPN 閘道設定如下所示。 注意：作為 VPN 設定 VPN 的一部分，也會列出閘道的 BGP 對等互連 IP 位址--10.17.11.76 和10.17.11.77。

![2][2]

內部部署路由會由防火牆公告至 VPN 閘道的主要和次要 BGP 對等互連。 路由公告如下所示（Junos）：

    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

    {primary:node0}
    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

>[!NOTE] 
>設定主動-主動模式的 S2S VPN 不僅會提供嚴重損壞修復備份網路連線的高可用性，還能為備份連線提供較高的輸送量。 換句話說，建議您在主動-主動模式中設定 S2S VPN，因為它會強制建立多個基礎通道。
>

### <a name="configuring-for-symmetric-traffic-flow"></a>設定對稱流量流程

我們注意到，當指定的內部部署路由透過 ExpressRoute 和 S2S VPN 來公告時，Azure 會偏好使用 ExpressRoute 路徑。 若要強制 Azure 在並存的 ExpressRoute 上使用 S2S VPN 路徑，您需要透過 VPN 連線來公告更特定的路由（較大的子網路遮罩的前置詞）。 我們的目標是使用 VPN 連線作為「僅備份」。 因此，Azure 的預設路徑選取行為會隨著我們的目標而行。 

我們負責確保從內部部署目的地到 Azure 的流量也偏好透過 S2S VPN 的 ExpressRoute 路徑。 在我們的內部部署設定中，CE 路由器和防火牆的預設本機喜好設定為100。 因此，藉由設定透過 ExpressRoute 私用100對等互連（如150）所接收之路由的本機喜好設定，我們可以讓目的地為 Azure 的流量偏好處于穩定狀態的 ExpressRoute 線路。

主要 CE 路由器的 BGP 設定會終止 ExpressRoute 線路的主要連線，如下所示。 請注意，透過 iBGP 會話公告之路由的本機喜好設定值，會設為150。 同樣地，我們必須確定第二部 CE 路由器的本機喜好設定終止 ExpressRoute 線路的次要連線也已設定為150。

    user@SEA-MX03-01> show configuration routing-instances Cust11 
    description "Customer 11 VRF";
    instance-type virtual-router;
    interface xe-0/0/0:0.110;
    interface ae0.11;
    protocols {
      bgp {
        group ibgp {
            type internal;
            local-preference 150;
            neighbor 192.168.11.1;
        }
        group ebgp {
            peer-as 12076;
            bfd-liveness-detection {
                minimum-interval 300;
                multiplier 3;
            }
            neighbor 192.168.11.18;
        }
      }
    }

內部部署防火牆的路由表會確認（如下所示），針對以 Azure 為目標的內部部署流量，慣用的路徑是在穩定狀態的 ExpressRoute 上。

    user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
    + = Active Route, - = Last Active, * = Both

    10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119
    10.17.11.76/32     *[Static/5] 2d 21:12:16
                        > via st0.118
    10.17.11.77/32     *[Static/5] 2d 00:41:56
                        > via st0.119
    10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119

在上述路由表中，針對中樞和輪輻 VNet 路由--10.17.11.0/25 和 10.17.11.128/26--我們看到 ExpressRoute 線路偏好高於 VPN 連線。 192.168.11.0 和192.168.11.2 是防火牆介面上的 Ip，可朝向 CE 路由器。

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>通過 S2S VPN 的路由交換驗證

稍早在本文中，我們已將防火牆的內部部署路由通告驗證至 VPN 閘道的主要和次要 BGP 對等互連。 此外，我們將確認防火牆從 VPN 閘道的主要和次要 BGP 對等互連收到的 Azure 路由。

    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.76                             65515 I
      10.17.11.128/26         10.17.11.76                             65515 I

    {primary:node0}
    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.77                             65515 I
      10.17.11.128/26         10.17.11.77                             65515 I

同樣地，讓我們來驗證 Azure VPN 閘道所收到的內部部署網路路由首碼。 

    PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

    Network      NextHop       AsPath      Weight
    -------      -------       ------      ------
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.76   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.77   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769

如上所示，VPN 閘道具有 VPN 閘道的主要和次要 BGP 對等端所接收的路由。 它也可以看到透過主要和次要 ExpressRoute 連線接收到的路由（在12076前面加上 AS 路徑）。 若要確認透過 VPN 連線接收的路由，我們需要知道連線的內部部署 BGP 對等互連 IP。 在我們的安裝程式中，它是192.168.11.88，而我們會看到從它接收的路由。

接下來，讓我們來驗證 Azure VPN 閘道向內部部署防火牆 BGP 對等（192.168.11.88）通告的路由。

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


若無法查看路由交換，即表示連接失敗。 請參閱[疑難排解： Azure 站對站 VPN 連線無法連線並停止運作][VPN Troubleshoot]，以取得疑難排解 VPN 連接的協助。

## <a name="testing-failover"></a>測試容錯移轉

既然我們已透過 VPN 連線（控制平面）確認成功的路由交換，我們就會將流量（資料平面）從 ExpressRoute 連線切換到 VPN 連線能力。 

>[!NOTE] 
>在生產環境中，容錯移轉測試必須在排定的網路維護工作時間範圍內完成，因為這可能會造成服務中斷。
>

在執行流量切換之前，讓我們追蹤將設定中的目前路徑從內部部署測試伺服器路由至輪輻 VNet 中的測試 VM。

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

我們的設定的主要和次要 ExpressRoute 點對點連線子網分別是 192.168.11.16/30 和 192.168.11.20/30。 在上述追蹤路由的步驟3中，我們看到我們遇到192.168.11.18，也就是主要 MSEE 的介面 IP。 出現 MSEE 介面時，確認我們目前的路徑是透過 ExpressRoute。

如[重設 expressroute 線路對等互連][RST]中所報告，讓我們使用下列 powershell 命令來停用 ExpressRoute 線路的主要和次要對等互連。

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

容錯移轉切換時間取決於 BGP 聚合時間。 在我們的設定中，容錯移轉參數需要幾秒鐘的時間（小於10）。 在切換之後，重複的追蹤路由會顯示下列路徑：

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

追蹤路由結果會確認透過 S2S VPN 的備份連線為使用中狀態，而且如果主要和次要 ExpressRoute 連線失敗，則可以提供服務持續性。 若要完成容錯移轉測試，讓我們使用下列命令集來啟用 ExpressRoute 連線，並將流量標準化。

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

若要確認流量已切換回 ExpressRoute，請重複追蹤路由，並確定它會通過 ExpressRoute 私用對等互連。

## <a name="next-steps"></a>後續步驟

ExpressRoute 是針對高可用性而設計的，在 Microsoft 網路內不會有單一失敗點。 仍然，ExpressRoute 線路僅限於單一地理區域和服務提供者。 S2S VPN 可能是 ExpressRoute 線路的良好嚴重損壞修復被動備份解決方案。 針對可靠的被動備份連線解決方案，定期維護被動設定和定期驗證連線非常重要。 請務必讓 VPN 設定變得過時，並定期（例如每季）在維護期間重複本文中所述的驗證和容錯移轉測試步驟。

若要啟用以 VPN 閘道計量為基礎的監視和警示，請參閱[設定 VPN 閘道計量的警示][VPN-alerts]。

若要加速 ExpressRoute 失敗後的 BGP 聚合，請透過[Expressroute 設定 BFD][BFD]。

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "考慮的拓撲"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN GW 設定"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



