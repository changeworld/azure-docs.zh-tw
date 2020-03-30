---
title: Azure 快速路由：為災害復原設計
description: 此頁提供使用 Azure ExpressRoute 時的災害復原的體系結構建議。
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076695"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>使用 ExpressRoute 專用對等互連設計災害復原

ExpressRoute 專為高可用性而設計，可向 Microsoft 資源提供運營商級專用網路連接。 換句話說，在 Microsoft 網路中的 ExpressRoute 路徑中沒有單點故障。 有關最大化 ExpressRoute 電路可用性的設計注意事項，請參閱使用[ExpressRoute 設計高可用性][HA]。

然而，考慮到Murphy的流行格言——*如果有什麼可能出錯的話，它將會*——在本文中，讓我們專注于那些超越故障的解決方案，這些解決方案可以通過單個ExpressRoute電路來解決。 換句話說，在本文中，讓我們介紹使用異地冗余 ExpressRoute 電路構建強大的後端網路連接以進行災害復原的網路架構注意事項。

## <a name="need-for-redundant-connectivity-solution"></a>需要冗余連接解決方案

存在整個區域服務（無論是 Microsoft、網路服務提供者、客戶或其他雲服務提供者）降級的可能性和實例。 造成這種區域範圍服務影響的根本原因包括自然災害。 因此，對於業務連續性和任務關鍵型應用程式，規劃災害復原非常重要。   

無論您在 Azure 區域、本地或其他地方運行任務關鍵型應用程式，都可以使用另一個 Azure 區域作為容錯移轉網站。 以下文章從應用程式和前端訪問角度介紹災害復原：

- [企業級災害復原][Enterprise DR]
- [使用 Azure Site Recovery 的 SMB 災害復原][SMB DR]

如果您依賴本地網路和 Microsoft 之間的 ExpressRoute 連接進行要徑任務操作，則災害復原計畫還應包括異地冗余網路連接。 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>使用多個 ExpressRoute 電路的挑戰

當您使用多個連接互連同一組網路時，在網路之間引入並行路徑。 並行路徑（如果未正確構建）可能會導致非對稱路由。 如果路徑中具有有狀態實體（例如 NAT、防火牆），則非對稱路由可能會阻止流量流。  通常，在 ExpressRoute 專用對等路徑上，您不會遇到有狀態實體（如 NAT 或防火牆）。 因此，通過 ExpressRoute 專用對等互連的不對稱路由不一定阻止流量流。
 
但是，如果跨異地冗余並行路徑載入平衡流量，無論您是否具有有狀態實體，您都會遇到不一致的網路性能。 在本文中，讓我們討論如何應對這些挑戰。

## <a name="small-to-medium-on-premises-network-considerations"></a>中小型本地網路注意事項

讓我們考慮下圖中所示的示例網路。 在此示例中，在 Contoso 的本地位置和 Azure 區域中的 Contoso VNet 之間建立異地冗余 ExpressRoute 連接。 在圖中，實心綠線表示首選路徑（通過 ExpressRoute 1），虛線表示備用路徑（通過 ExpressRoute 2）。

[![1]][1]

在為災害復原設計 ExpressRoute 連接時，需要考慮：

- 使用異地冗余快速路由電路
- 對不同的 ExpressRoute 電路使用不同的服務提供者網路
- 設計每個快速路由電路，以便[高可用性][HA]
- 在客戶網路的不同位置終止不同的 ExpressRoute 電路

預設情況下，如果在所有 ExpressRoute 路徑上以相同的方式通告路由，Azure 將使用等價多路徑 （ECMP） 路由在所有 ExpressRoute 路徑上載入本地綁定流量。

但是，對於異地冗余 ExpressRoute 電路，我們需要考慮不同網路路徑的不同網路性能（尤其是網路延遲）。 為了在正常操作期間獲得更一致的網路性能，您可能需要首選提供最小延遲的 ExpressRoute 電路。

您可以使用以下技術之一（按有效性順序列出）影響 Azure 首選一個 ExpressRoute 電路而不是另一個 ExpressRoute 電路：

- 與其他 ExpressRoute 電路相比，在首選的 ExpressRoute 電路上通告更具體的路由
- 在將虛擬網路連接到首選的 ExpressRoute 電路的連接上配置更高的連接重量
- 在不太首選的 ExpressRoute 電路上通告路由，具有較長的 AS 路徑（AS 路徑預置）

### <a name="more-specific-route"></a>更具體的路線

下圖說明瞭使用更具體的路由播發影響快速路由直接選取。 在圖示示例中，Contoso 本地 /24 IP 範圍通過首選路徑 （ExpressRoute 1） 通告為兩個 /25 位址範圍，並通過備用路徑 （ExpressRoute 2） 作為 /24。

[![2]][2]

由於 /25 更具體，與 /24 相比，Azure 將在正常狀態下通過 ExpressRoute 1 將註定的流量發送到 10.1.11.0/24。 如果 ExpressRoute 1 的兩個連接都斷開，則 VNet 將僅通過 ExpressRoute 2 看到 10.1.11.0/24 路由通告;因此，備用電路用於此故障狀態。

### <a name="connection-weight"></a>連接重量

以下螢幕截圖演示了通過 Azure 門戶配置 ExpressRoute 連接的重量。

[![3]][3]

下圖說明瞭使用連接權重影響快速路由直接選取。 預設連接權重為 0。 在下面的示例中，ExpressRoute 1 的連接重量配置為 100。 當 VNet 接收通過多個 ExpressRoute 電路通告的路由首碼時，VNet 將更喜歡具有最大權重的連接。

[![4]][4]

如果 ExpressRoute 1 的兩個連接都斷開，則 VNet 將僅通過 ExpressRoute 2 看到 10.1.11.0/24 路由通告;因此，備用電路用於此故障狀態。

### <a name="as-path-prepend"></a>AS 路徑預置

下圖說明瞭使用 AS 路徑預置影響快速路由選擇。 在圖中，ExpressRoute 1 上的路由播發指示 eBGP 的預設行為。 在通過 ExpressRoute 2 的路由通告中，本地網路的 ASN 在路由的 AS 路徑上額外預告。 當通過多個 ExpressRoute 電路接收同一路由時，根據 eBGP 路由選擇過程，VNet 更喜歡具有最短 AS 路徑的路由。 

[![5]][5]

如果 ExpressRoute 1 的兩個連接都斷開，則 VNet 將僅通過 ExpressRoute 2 看到 10.1.11.0/24 路由通告。 因此，較長的 AS 路徑將變得無關緊要。 因此，備用電路將在此故障狀態下使用。

使用任何技術，如果影響 Azure 首選 ExpressRoute 之一，還需要確保本地網路也更喜歡 Azure 綁定流量的相同 ExpressRoute 路徑，以避免非對稱流。 通常，本地首選項值用於影響本地網路，以首選一個 ExpressRoute 電路而不是其他電路。 本地首選項是內部 BGP （iBGP） 指標。 首選具有最高本地首選項值的 BGP 路由。

> [!IMPORTANT]
> 當您使用某些 ExpressRoute 電路作為備用電路時，您需要主動管理這些電路並定期測試容錯移轉操作。 
> 

## <a name="large-distributed-enterprise-network"></a>大型分散式商業網路

當您擁有大型分散式商業網路時，可能會有多個 ExpressRoute 電路。 在本節中，讓我們看看如何使用主動-主動的 ExpressRoute 電路設計災害復原，而無需額外的備用電路。 

讓我們考慮下圖中所示的示例。 在此示例中，Contoso 有兩個本地位置通過兩個不同的對等位置的 ExpressRoute 電路連接到兩個不同的 Azure 區域中的兩個 Contoso IaaS 部署。 

[![6]][6]

我們如何構建災害復原對跨區域到跨位置（區域1/區域2到位置2/位置1）流量的路由方式產生影響。 讓我們考慮兩個不同的災難體系結構，這些體系結構以不同的方式路由跨區域位置流量。

### <a name="scenario-1"></a>實例 1

在第一個方案中，讓我們設計災害復原，以便 Azure 區域和本地網路之間的所有流量以穩定狀態通過本地 ExpressRoute 電路。 如果本地 ExpressRoute 電路發生故障，則遠端 ExpressRoute 電路將用於 Azure 和本地網路之間的所有流量流。

方案 1 如下圖所示。 在圖中，綠色線指示 VNet1 和本地網路之間的流量流路徑。 藍線表示 VNet2 和本地網路之間的流量路徑。 實線指示穩態中的所需路徑，虛線表示承載穩態流量的相應 ExpressRoute 電路故障中的流量路徑。 

[![7]][7]

您可以使用連接權重來構建方案，以影響 VNets 首選與本地對等位置 ExpressRoute 的連接，以便進行本地網路綁定流量。 要完成解決方案，您需要確保對稱反向交通流量。 您可以在 BGP 路由器之間的 iBGP 會話上使用本地首選項（其中 ExpressRoute 電路在本地端終止），以首選 ExpressRoute 電路。 下圖說明瞭該解決方案。 

[![8]][8]

### <a name="scenario-2"></a>案例 2

方案 2 如下圖所示。 在圖中，綠色線指示 VNet1 和本地網路之間的流量流路徑。 藍線表示 VNet2 和本地網路之間的流量路徑。 在穩態（圖中的實線）中，VNet 和本地位置之間的所有流量大部分都通過 Microsoft 骨幹流，並且僅在故障狀態下通過本地位置之間的互連（在快速路由的圖表）。

[![9]][9]

下圖說明瞭該解決方案。 如圖所示，您可以使用更具體的路由（選項 1）或 AS 路徑前置（選項 2）來構建方案，以影響 VNet 直接選取。 要影響 Azure 綁定流量的本地網路路由選擇，需要將本地位置之間的互連配置為不太可取。 配置互連鏈路的可取方式取決於本地網路中使用的路由式通訊協定。 您可以將本地首選項與 iBGP 或指標與 IGP（OSPF 或 IS-IS）一起使用。

[![10]][10]


## <a name="next-steps"></a>後續步驟

在本文中，我們討論了如何設計用於快速路由電路專用對等互連連接的災害復原。 以下文章從應用程式和前端訪問角度介紹災害復原：

- [企業級災害復原][Enterprise DR]
- [使用 Azure Site Recovery 的 SMB 災害復原][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "個中小型本地網路注意事項"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "使用更具體的路徑影響直接選取"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "通過 Azure 門戶配置連接權重"
[使用]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "連接權重影響直接選取"4
[使用]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "AS 路徑預置影響直接選取 5"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "個大型分散式本地網路注意事項"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "方案 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "個主動-主動快速路由電路解決方案 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "方案 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "個主動-主動快速路由電路解決方案 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





