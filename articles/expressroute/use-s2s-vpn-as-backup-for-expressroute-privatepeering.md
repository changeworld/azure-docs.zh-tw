---
title: 使用 S2S VPN 作為 Azure ExpressRoute 私人對等互連的備份 |Microsoft Docs
description: 本頁面提供使用 S2S VPN 來備份 Azure ExpressRoute 私人對等互連的架構建議。
services: networking
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: duau
ms.openlocfilehash: 752edea8078cf55fc3965bdc7aa9e1b4269dee34
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207915"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>使用 S2S VPN 作為 ExpressRoute 私人對等互連的備份

在標題為 [使用 expressroute 私人對等互連來進行][DR-PP]嚴重損壞修復的文章中，我們討論了針對 expressroute 私用對等互連連線的備份連線解決方案需求，以及如何基於目的使用異地冗余 ExpressRoute 線路。 在本文中，讓我們考慮如何利用和維護站對站 (S2S) VPN 作為 ExpressRoute 私人對等互連的備份。 

不同于地理位置的 ExpressRoute 電路，您只能在主動-被動模式中使用 ExpressRoute VPN 嚴重損壞修復組合。 在被動模式中使用任何備份網路連線的主要挑戰是，被動連線通常會在主要連線失敗。 被動連接失敗的常見原因是缺乏主動維護。 因此，在本文中，我們將著重于如何驗證和主動維護備份 ExpressRoute 私用對等互連的 S2S VPN 連線能力。

>[!NOTE] 
>透過 ExpressRoute 和 VPN 通告指定的路由時，Azure 會偏好透過 ExpressRoute 進行路由。  
>

在本文中，我們將瞭解如何從 Azure 觀點和用戶端網路邊緣的觀點來確認連線能力。 無論您是否管理與 Microsoft 網路實體對等的用戶端網路裝置，都可以從任一端進行驗證的能力。 

## <a name="example-topology"></a>範例拓撲

在我們的設定中，我們已透過 ExpressRoute 線路和 S2S VPN 連線，將內部部署網路連線到 Azure 中樞 VNet。 Azure 中樞 VNet 接著會對等互連至輪輻 VNet，如下圖所示：

![1][1]

在安裝程式中，ExpressRoute 線路會在內部部署的一對「客戶 Edge」 (CE) 路由器上終止。 內部部署區域網路會透過一對以領導人執行者模式運作的防火牆來連線至 CE 路由器。 S2S VPN 直接在防火牆上終止。

下表列出拓撲的主要 IP 首碼：

| **實體** | **Prefix** |
| --- | --- |
| 內部部署區域網路 | 10.1.11.0/25 |
| Azure 中樞 VNet | 10.17.11.0/25 |
| Azure 輪輻 VNet | 10.17.11.128/26 |
| 內部部署測試伺服器 | 10.1.11.10 |
| 輪輻 VNet 測試 VM | 10.17.11.132 |
| ExpressRoute 主要連接 p2p 子網 | 192.168.11.16/30 |
| ExpressRoute 次要連接 p2p 子網 | 192.168.11.20/30 |
| VPN 閘道主要 BGP 對等 IP | 10.17.11.76 |
| VPN 閘道次要 BGP 對等 IP | 10.17.11.77 |
| 內部部署防火牆 VPN BGP 對等 IP | 192.168.11.88 |
| 主要 CE 路由器 i/f 朝向防火牆 IP | 192.168.11.0/31 |
| 針對主要 CE 路由器 IP 的防火牆 i/f | 192.168.11.1/31 |
| 針對防火牆 IP 的次要 CE 路由器 i/f | 192.168.11.2/31 |
| 針對次要 CE 路由器 IP 的防火牆 i/f | 192.168.11.3/31 |


下表列出拓撲的 Asn：

| **自主系統** | **加入** |
| --- | --- |
| 內部部署 | 65020 |
| Microsoft 企業版 Edge | 12076 |
| 虛擬網路 GW (ExR)  | 65515 |
| 虛擬網路 GW (VPN)  | 65515 |

## <a name="high-availability-without-asymmetricity"></a>沒有 asymmetricity 的高可用性

### <a name="configuring-for-high-availability"></a>設定高可用性

[設定 expressroute 和站對站][Conf-CoExist] 並存連線，討論如何設定並存的 ExpressRoute 線路和 S2S VPN 連線。 如我們在 [使用 ExpressRoute 設計高可用性][HA]時所討論，為了改善 expressroute 的高可用性，我們的安裝程式會維護網路冗余 (避免單一失敗點) 一直到端點為止。 此外，ExpressRoute 線路的主要和次要連線都會設定為以主動-主動模式運作，方法是以相同方式透過兩個連線來通告內部部署首碼。 

主要 CE 路由器的內部部署路由公告透過 ExpressRoute 線路的主要連線，如下所示 (Junos 命令) ：

```console
user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

次要 CE 路由器的內部部署路由公告透過 ExpressRoute 線路的次要連線，如下所示 (Junos 命令) ：

```console
user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

為了改善備份連接的高可用性，S2S VPN 也會以主動-主動模式進行設定。 Azure VPN 閘道設定如下所示。 請注意，作為 VPN 設定 VPN 的一部分，也會列出閘道的 BGP 對等 IP 位址（10.17.11.76 和10.17.11.77）。

![2][2]

防火牆會將內部部署路由公告至 VPN 閘道的主要和次要 BGP 對等。 路由公告如下所示 (Junos) ：

```console
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I

{primary:node0}
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

>[!NOTE] 
>在主動-主動模式中設定 S2S VPN，不僅能為您的嚴重損壞修復備份網路連線提供高可用性，還可提供更高的輸送量給備份連線能力。 換句話說，建議在主動-主動模式中設定 S2S VPN，因為它會強制建立多個基礎通道。
>

### <a name="configuring-for-symmetric-traffic-flow"></a>設定對稱流量流程

我們注意到，當指定的內部部署路由透過 ExpressRoute 和 S2S VPN 公告時，Azure 會偏好使用 ExpressRoute 路徑。 若要強制 Azure 優先使用並存 ExpressRoute 的 S2S VPN 路徑，您需要透過 VPN 連線，以較大的子網) 遮罩 (更長的前置詞來通告更特定的路由。 我們的目標是要使用僅限備份的 VPN 連接。 因此，Azure 的預設直接選取行為會與我們的目標在線上。 

我們的責任是確保從內部部署流向 Azure 的流量也會優先于 S2S VPN 的 ExpressRoute 路徑。 在內部部署設定中，CE 路由器和防火牆的預設本機喜好設定為100。 因此，藉由設定透過 ExpressRoute 私用對等互連（大於100）所接收的路由本機喜好設定 (例如 150) ，我們可以讓目的地為 Azure 的流量偏好處于穩定狀態的 ExpressRoute 線路。

終止 ExpressRoute 線路主要連線的主要 CE 路由器 BGP 設定如下所示。 請注意，透過 iBGP 會話通告之路由的本機喜好設定值設定為150。 同樣地，我們需要確保第二個可終止 ExpressRoute 線路次要連線的次要 CE 路由器本機喜好設定，也會設定為150。

```console
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
```

內部部署防火牆的路由表會確認 (如下所示) 針對目的地為 Azure 的內部部署流量，慣用的路徑是透過 ExpressRoute 處於穩定狀態。

```console
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
```

在上述路由表中，針對中樞和輪輻 VNet 路由--10.17.11.0/25 和 10.17.11.128/26--我們會看到 ExpressRoute 線路優先于 VPN 連接。 192.168.11.0 和192.168.11.2 是防火牆介面上的 Ip，可朝向 CE 路由器。

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>S2S VPN 的路由交換驗證

稍早在本文中，我們已向 VPN 閘道的主要和次要 BGP 對等驗證防火牆的內部部署路由通告。 此外，讓我們確認防火牆從 VPN 閘道的主要和次要 BGP 對等端收到的 Azure 路由。

```console
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
```

同樣地，讓我們確認 Azure VPN 閘道收到的內部部署網路路由首碼。 

```powershell
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
```

如上所示，VPN 閘道具有 VPN 閘道的主要和次要 BGP 對等端所接收的路由。 它也可以看到透過主要和次要 ExpressRoute 連線所收到的路由， (在 12076) 前面加上路徑的路由。 若要確認透過 VPN 連線所收到的路由，我們必須知道連線的內部部署 BGP 對等互連 IP。 在我們考慮的設定中，它是192.168.11.88 的，我們會看到從它收到的路由。

接下來，讓我們先確認 Azure VPN 閘道所通告的路由， (192.168.11.88) 的內部部署防火牆 BGP 對等互連。

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

Network         NextHop     AsPath Weight
-------         -------     ------ ------
10.17.11.0/25   10.17.11.76 65515       0
10.17.11.128/26 10.17.11.76 65515       0
10.17.11.0/25   10.17.11.77 65515       0
10.17.11.128/26 10.17.11.77 65515       0
```

若看不到路由交換，則表示連接失敗。 請參閱 [疑難排解： Azure 站對站 VPN 連線無法連線並停止運作][VPN Troubleshoot] ，以取得針對 VPN 連線進行疑難排解的協助。

## <a name="testing-failover"></a>測試容錯移轉

既然我們已確認透過 VPN 連線的成功路由交換 (控制平面) ，我們會將流量從 ExpressRoute 連線) 至 VPN 連線能力，以將流量切換 (資料平面。 

>[!NOTE] 
>在生產環境中，必須在排程的網路維護工作期間完成容錯移轉測試，因為這可能會造成服務干擾。
>

在進行流量切換之前，讓我們追蹤將設定中的目前路徑從內部部署測試伺服器路由傳送至輪輻 VNet 中的測試 VM。

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2    <1 ms    <1 ms    11 ms  192.168.11.0
  3    <1 ms    <1 ms    <1 ms  192.168.11.18
  4     *        *        *     Request timed out.
  5     6 ms     6 ms     5 ms  10.17.11.132

Trace complete.
```

我們的設定的主要和次要 ExpressRoute 點對點連線子網分別是 192.168.11.16/30 和 192.168.11.20/30。 在上述的追蹤路由中，我們會在步驟3中看到我們碰到192.168.11.18，也就是主要 MSEE 的介面 IP。 存在 MSEE 介面時，會確認目前的路徑是透過 ExpressRoute。

在 [重設 expressroute 線路對等互連][RST]中回報，讓我們使用下列 powershell 命令來停用 ExpressRoute 線路的主要和次要對等互連。

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Disabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

容錯移轉切換時間取決於 BGP 聚合時間。 在我們的設定中，容錯移轉切換需要幾秒鐘的時間 (小於 10) 。 切換之後，重複追蹤路由會顯示下列路徑：

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2     *        *        *     Request timed out.
  3     6 ms     7 ms     9 ms  10.17.11.132

Trace complete.
```

追蹤路由結果會確認透過 S2S VPN 的備份連線為使用中狀態，而且如果主要和次要 ExpressRoute 連線都失敗，則可以提供服務持續性。 若要完成容錯移轉測試，讓我們使用下列命令組來啟用 ExpressRoute 連線，並將流量標準化。

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Enabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

若要確認流量已切換回 ExpressRoute，請重複追蹤路由，並確定它是透過 ExpressRoute 私用對等互連。

## <a name="next-steps"></a>後續步驟

ExpressRoute 是針對高可用性而設計的，在 Microsoft 網路內不會有單一失敗點。 您仍然可以將 ExpressRoute 線路限制在單一地理區域和服務提供者。 S2S VPN 可以是 ExpressRoute 線路的良好嚴重損壞修復被動備份解決方案。 針對可靠的被動備份連線解決方案，被動設定和定期驗證的定期維護是很重要的。 請勿讓 VPN 設定變得過時，且每季定期 (，) 在維護期間重複本文中所述的驗證和容錯移轉測試步驟。

若要啟用以 VPN 閘道計量為基礎的監視和警示，請參閱 [設定 Vpn 閘道計量的警示][VPN-alerts]。

若要在 ExpressRoute 失敗之後加速 BGP 聚合，請透過 [Expressroute 設定 BFD][BFD]。

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "考慮的拓撲"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN GW 設定"

<!--Link References-->
[DR-PP]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
[Conf-CoExist]: ./expressroute-howto-coexist-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[VPN Troubleshoot]: ../vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect.md
[VPN-alerts]: ../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md
[BFD]: ./expressroute-bfd.md
[RST]: ./expressroute-howto-reset-peering.md