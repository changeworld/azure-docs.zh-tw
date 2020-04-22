---
title: 使用 S2S VPN 作為 Azure 快速路由專用對等互連的備份 |微軟文件
description: 此頁提供使用 S2S VPN 備份 Azure ExpressRoute 專用對等互連的體系結構建議。
services: networking
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: a6a22b667bc66d6ee69bfbd7ad1db88f72d8df0e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687852"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>使用 S2S VPN 作為 ExpressRoute 專用對等互連的備份

在題為「[使用 ExpressRoute 專用對等互連進行災難恢復設計][DR-PP]」的文章中,我們討論了為 ExpressRoute 專用對等互連連接提供備份連接解決方案的必要性,以及如何為此使用異地冗餘 ExpressRoute 電路。 在本文中,讓我們考慮如何利用和維護網站到網站 (S2S) VPN 作為 ExpressRoute 專用對等互連的背面。 

與異地冗餘 ExpressRoute 電路不同,您只能在主動-被動模式下使用 ExpressRoute-VPN 災難恢復組合。 在被動模式下使用任何備份網路連接的一個主要挑戰是,被動連接通常與主連接同時失敗。 被動連接故障的常見原因是缺少主動維護。 因此,在本文中,讓我們重點介紹如何驗證並主動維護正在備份 ExpressRoute 專用對等互連的 S2S VPN 連接。

>[!NOTE] 
>當通過 ExpressRoute 和 VPN 通告給定路由時,Azure 更喜歡路由而不是快速路由。  
>

在本文中,讓我們看看如何從 Azure 角度和用戶端網路邊緣角度驗證連接。 無論是否管理與 Microsoft 網路實體對等的用戶端網路設備,從任一端進行驗證都會有所説明。 

## <a name="example-topology"></a>範例拓撲

在我們的設置中,我們透過 ExpressRoute 電路和 S2S VPN 連接將本地網路連接到 Azure 集線器 VNet。 Azure 中心 VNet 依次與分支 VNet 對等互連,如下圖所示:

![1][1]

在設置中,ExpressRoute 電路在本地的一對「客戶邊緣」(CE) 路由器上終止。 本地 LAN 透過一對在領導者跟隨模式下運行的防火牆連接到 CE 路由器。 S2S VPN 直接終止在防火牆上。

下表列出了拓撲的關鍵 IP 首碼:

| **實體** | **前置詞** |
| --- | --- |
| 本地區域網 | 10.1.11.0/25 |
| Azure 中心 VNet | 10.17.11.0/25 |
| Azure 分支 VNet | 10.17.11.128/26 |
| 本機測試伺服器 | 10.1.11.10 |
| 分支 VNet 測試 VM | 10.17.11.132 |
| 快速路由主連線 p2p 子網路 | 192.168.11.16/30 |
| 快速路由輔助連線 p2p 子網路 | 192.168.11.20/30 |
| VPN 閘道主 BGP 對等 IP | 10.17.11.76 |
| VPN 閘道輔助 BGP 對等 IP | 10.17.11.77 |
| 本地防火牆 VPN BGP 對等 IP | 192.168.11.88 |
| 主 CE 路由器 i/f 面向防火牆 IP | 192.168.11.0/31 |
| 面向主 CE 路由器 IP 的防火牆 i/f | 192.168.11.1/31 |
| 輔助 CE 路由器 i/f 面向防火牆 IP | 192.168.11.2/31 |
| 適合 CE 路由器 IP 的防火牆 i/f | 192.168.11.3/31 |


下表列出了拓撲的 ASN:

| **自主系統** | **ASN** |
| --- | --- |
| 內部部署 | 65020 |
| 微軟企業邊緣 | 12076 |
| 虛擬網路 GW (ExR) | 65515 |
| 虛擬網路 GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>高可用性,無不對稱性

### <a name="configuring-for-high-availability"></a>設定,提供高可用性

[配置 ExpressRoute 和網站到網站共存的連接][Conf-CoExist]討論如何配置共存的 ExpressRoute 電路和 S2S VPN 連接。 正如我們[在使用 ExpressRoute 設計高可用性][HA]時所討論的,為了提高 ExpressRoute 的高可用性,我們的設置一直保持網路冗餘(避免單點故障),一直保持到端點。 此外,ExpressRoute 電路的主連接和輔助連接配置為在主動-主動模式下運行,通過兩個連接以相同的方式通告本地前綴。 

透過 ExpressRoute 電路的主連線的主 CE 路由器的本地路由通告如下所示(Junos):

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

透過 ExpressRoute 電路的連結,輔助 CE 路由器的本地路由通告如下所示(Junos):

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

為了提高備份連接的高可用性,S2S VPN 也配置為主動-主動模式。 Azure VPN 閘道配置如下所示。 請注意,作為 VPN 配置 VPN 的一部分,還列出了閘道的 BGP 對等 IP 位址 -- 10.17.11.76 和 10.17.11.77。

![2][2]

防火牆通告到 VPN 閘道的主資料庫和輔助 BGP 對等方的本地路由。 路由播發如下所示(Junos):

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
>在主動-主動模式下配置 S2S VPN 不僅為您的災難恢復備份網路連接提供高可用性,而且還為備份連接提供了更高的輸送量。 換句話說,建議在主動-主動模式下配置 S2S VPN,因為它強制創建多個基礎隧道。
>

### <a name="configuring-for-symmetric-traffic-flow"></a>設定對稱流量串流

我們注意到,當通過 ExpressRoute 和 S2S VPN 通告給定的本地路由時,Azure 更喜歡 ExpressRoute 路徑。 要強制 Azure 首選 S2S VPN 路徑而不是共存的 ExpressRoute,您需要透過 VPN 連接通告更具體的路由(具有較大子網掩碼的較長首碼)。 我們在這裡的目標是只使用 VPN 連接作為返回。 因此,Azure 的預設路徑選擇行為符合我們的目標。 

我們有責任確保從本地發送到 Azure 的流量也更喜歡快速路由路徑,而不是 S2S VPN。 本地設定中 CE 路由器和防火牆的預設本階設定為 100。 因此,通過配置通過 ExpressRoute 專用對等互連接收的路由的本地首選項大於 100(例如 150),我們可以使發送到 Azure 的流量選擇處於穩定狀態的 ExpressRoute 電路。

終止 ExpressRoute 電路主連接的主 CE 路由器的 BGP 配置如下所示。 請注意,在 iBGP 會話上通告的路由的本地首選項的值配置為 150。 同樣,我們需要確保終止 ExpressRoute 電路的輔助連接的輔助 CE 路由器的本地首選項也配置為 150。

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

本地防火牆的路由表確認(如下所示),對於發往 Azure 的本地流量,首選路徑在穩定狀態下超過 ExpressRoute。

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

在上述路由表中,對於集線器和分支 VNet 路由 -10.17.11.0/25 和 10.17.11.128/26 -- 192.168.11.0 和 192.168.11.2 是面向 CE 路由器的防火牆介面上的 IP。

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>透過 S2S VPN 驗證路由交換

在本文前面,我們驗證了防火牆的本地路由播發到 VPN 閘道的主資料庫和輔助 BGP 對等體。 此外,讓我們確認防火牆從 VPN 閘道的主資料庫和輔助 BGP 對等體接收的 Azure 路由。

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

同樣,讓我們驗證 Azure VPN 閘道收到的本地網路路由前綴。 

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

如上所述,VPN 閘道具有 VPN 閘道的主資料庫和輔助 BGP 對等方接收的路由。 它還可查看通過主和輔助 ExpressRoute 連接接收的路由(AS 路徑以 12076 預示的路線)。 要確認透過 VPN 連接接收的路由,我們需要瞭解連接的本地 BGP 對等 IP。 在所考慮的設置中,它是192.168.11.88,我們確實看到了從它接收的路線。

接下來,讓我們驗證 Azure VPN 閘道通告到本地防火牆 BGP 對等體 (192.168.11.88) 通告的路由。

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


看不到路由交換指示連接失敗。 請參閱[故障排除:Azure 網站到網站 VPN 連接無法連接並停止工作][VPN Troubleshoot]以尋求對 VPN 連接進行故障排除的説明。

## <a name="testing-failover"></a>測試容錯移轉

現在,我們已經確認通過 VPN 連接(控制平面)成功路由交換,我們設置為將流量(數據平面)從 ExpressRoute 連接切換到 VPN 連接。 

>[!NOTE] 
>在生產環境中,故障轉移測試必須在計劃網路維護工作視窗期間完成,因為它可能會造成服務中斷。
>

在進行流量切換之前,讓我們追蹤設置中的當前路徑從本地測試伺服器到分支 VNet 中的測試 VM。

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

我們設置的主和輔助 ExpressRoute 點對點連接子網分別為 192.168.11.16/30 和 192.168.11.20/30。 在上面的跟蹤路線中,在步驟 3 中,我們看到我們達到 192.168.11.18,這是主 MSEE 的介面 IP。 MSEE 介面的存在證實,正如預期的那樣,我們當前的路徑位於 ExpressRoute 上。

如[重置 ExpressRoute 電路對等互連中][RST]所報告的那樣,讓我們使用以下電源殼命令來禁用 ExpressRoute 電路的主對等互連。

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

故障轉移開關時間取決於 BGP 收斂時間。 在我們的設置中,故障轉移開關需要幾秒鐘(少於 10)。 切換後,追蹤路徑的重複顯示以下路徑:

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

跟蹤路由結果確認通過 S2S VPN 的備份連接處於活動狀態,並且如果主和輔助 ExpressRoute 連接都發生故障,則可以提供服務連續性。 要完成故障轉移測試,讓我們使用以下命令集重新啟用 ExpressRoute 連接並規範化流量流。

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

要確認流量已切換回 ExpressRoute,請重複追蹤路由並確保它通過 ExpressRoute 專用對等互連。

## <a name="next-steps"></a>後續步驟

ExpressRoute 專為高可用性而設計,在 Microsoft 網路中沒有單點故障。 ExpressRoute 電路仍局限於單個地理區域和服務提供者。 S2S VPN 是快速路由電路的良好災難恢復被動備份解決方案。 對於可靠的被動備份連接解決方案,定期維護被動配置和定期驗證連接非常重要。 至關重要的是,不要讓 VPN 配置過時,並且定期(例如每個季度)在維護時段期間重複本文中描述的驗證和故障轉移測試步驟。

要基於 VPN 閘道指標啟用監視和警報,請參閱[在 VPN 閘道指標上設定警報][VPN-alerts]。

您可以快速路由故障後加快 BGP 收斂,[請透過 ExpressRoute 設定 BFD][BFD]。

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "正在考慮的拓撲"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN GW 設定"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



