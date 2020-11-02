---
title: Azure ExpressRoute：設計嚴重損壞修復
description: 本頁面提供使用 Azure ExpressRoute 時的嚴重損壞修復架構建議。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: duau
ms.openlocfilehash: 2a5730cd75ccb76d25897e9109555113f7355c2f
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "92202408"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>使用 ExpressRoute 私用對等互連設計災難復原

ExpressRoute 的設計目的是為了提供高可用性，以提供對 Microsoft 資源的電訊公司私人網路連線能力。 換句話說，在 Microsoft 網路內的 ExpressRoute 路徑中不會有單一失敗點。 如需將 ExpressRoute 線路的可用性最大化的設計考慮，請參閱 [使用 Expressroute 設計高可用性][HA]。

不過，Murphy 的熱門」古訓-- *如果有任何錯誤，就會* 在本文中考慮到，在本文中，我們將焦點放在可使用單一 ExpressRoute 線路來解決失敗的解決方案之外。 換句話說，在本文中，我們將探討網路架構考慮，以使用異地冗余 ExpressRoute 線路來建立強大的後端網路連線，以進行嚴重損壞修復。

>[!NOTE]
>本文所述的概念同樣適用于在虛擬 WAN 或其外部建立 ExpressRoute 線路時。
>

## <a name="need-for-redundant-connectivity-solution"></a>需要有多餘的連接解決方案

有一些可能和實例，其中的整個區域服務 (Microsoft、網路服務提供者、客戶或其他雲端服務提供者) 變差。 這類區域性服務影響的根本原因包括自然遭遇災難。 因此，針對商務持續性和任務關鍵性應用程式，規劃嚴重損壞修復相當重要。   

無論您是在 Azure 區域中或內部部署或任何地方執行任務關鍵性應用程式，都可以使用另一個 Azure 區域做為容錯移轉網站。 下列文章說明從應用程式和前端存取角度進行的嚴重損壞修復：

- [企業級災害復原][Enterprise DR]
- [使用 Azure Site Recovery 的 SMB 災害復原][SMB DR]

如果您需要內部部署網路與 Microsoft 之間的 ExpressRoute 連線能力，以進行任務關鍵性作業，您的嚴重損壞修復計畫也應該包含異地冗余的網路連線。 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>使用多個 ExpressRoute 線路的挑戰

當您使用一個以上的連線來互連同一組網路時，您會在網路之間引進平行路徑。 平行路徑若未正確架構，可能會導致非對稱路由。 如果您有具狀態實體 (例如，NAT、在路徑中) 防火牆，非對稱路由可能會封鎖流量。  一般而言，透過 ExpressRoute 私用對等互連路徑，您將不會跨越可設定狀態的實體（例如 NAT 或防火牆）。 因此，透過 ExpressRoute 私用對等互連的非對稱路由不一定會封鎖流量。
 
但是，如果您將流量負載平衡到異地多餘的平行路徑，無論您是否有具狀態實體，您都會遇到不一致的網路效能。 在本文中，我們將討論如何解決這些挑戰。

## <a name="small-to-medium-on-premises-network-considerations"></a>中小型內部部署網路考慮

讓我們考慮下圖所示的範例網路。 在此範例中，Contoso 的內部部署位置和 Contoso 在 Azure 區域中的 VNet 之間建立了地理位置冗余 ExpressRoute 連線能力。 在此圖中，綠色實線表示透過 ExpressRoute 1)  (慣用路徑，而點狀線則代表透過 ExpressRoute 2)  (的路徑。

[![1]][1]

當您針對嚴重損壞修復設計 ExpressRoute 連線時，您必須考慮：

- 使用異地冗余 ExpressRoute 線路
- 針對不同的 ExpressRoute 線路使用不同的服務提供者網路 (s) 
- 設計每個 ExpressRoute 線路以獲得 [高可用性][HA]
- 在客戶網路上的不同位置終止不同的 ExpressRoute 線路

根據預設，如果您在所有 ExpressRoute 路徑上公告相同的路由，Azure 就會使用相等的多路徑 (ECMP) 路由，將內部部署系結流量負載平衡到所有 ExpressRoute 路徑。

不過，有了地理位置的 ExpressRoute 電路，我們必須考慮不同網路路徑的不同網路效能 (特別是) 的網路延遲。 若要在正常操作期間獲得更一致的網路效能，您可能會想要使用可提供最短延遲的 ExpressRoute 線路。

您可以使用下列其中一種方法，以使用下列其中一種方法，來影響 Azure 對另一個 ExpressRoute 線路， (以) 的有效性順序列出：

- 相較于其他 ExpressRoute 線路 (，在慣用 ExpressRoute 線路上通告更明確的路由) 
- 在連線上設定更高的連線權數，以將虛擬網路連結至慣用的 ExpressRoute 線路
- 以較不慣用的 ExpressRoute 線路（以路徑為開頭的路徑 (）來通告路由) 

### <a name="more-specific-route"></a>更特定的路由

下圖說明如何使用更特定的路由通告來影響 ExpressRoute 路徑選取專案。 在說明的範例中，Contoso 內部部署/24 IP 範圍會透過慣用的路徑 (ExpressRoute 1) 和 as/24 透過 (ExpressRoute 2) 的待命路徑，公告為兩個/25 個位址範圍。

[![2]][2]

由於/25 更明確，相較于/24，Azure 會透過 ExpressRoute 1 以正常狀態傳送目的地為 10.1.11.0/24 的流量。 如果 ExpressRoute 1 的兩個連線都停止運作，則 VNet 只會透過 ExpressRoute 2 查看 10.1.11.0/24 路由公告;因此，此失敗狀態會使用待命電路。

### <a name="connection-weight"></a>連接權數

下列螢幕擷取畫面說明如何透過 Azure 入口網站設定 ExpressRoute 連接的加權。

[![3]][3]

下圖說明如何使用連接權數來影響 ExpressRoute 路徑選取專案。 預設連接權數為0。 在下列範例中，ExpressRoute 1 的連線權數設定為100。 當 VNet 收到透過多個 ExpressRoute 線路所通告的路由首碼時，VNet 會偏好最高權數的連接。

[![億]][4]

如果 ExpressRoute 1 的兩個連線都停止運作，則 VNet 只會透過 ExpressRoute 2 查看 10.1.11.0/24 路由公告;因此，此失敗狀態會使用待命電路。

### <a name="as-path-prepend"></a>作為路徑前置

下圖說明使用作為路徑開頭的方式來影響 ExpressRoute 路徑選取專案。 在圖表中，透過 ExpressRoute 1 的路由通告表示 eBGP 的預設行為。 在透過 ExpressRoute 2 的路由通告上，內部部署網路的 ASN 會在路由的 AS 路徑上額外加上。 當透過多個 ExpressRoute 線路收到相同的路由時，根據 eBGP 路由選取進程，VNet 會偏好使用最短路徑的路由。 

[![5]][5]

如果兩個 ExpressRoute 1 的連線都停止運作，則 VNet 只會看到透過 ExpressRoute 2 的 10.1.11.0/24 路由通告。 因此，較長的路徑會變成不相關。 因此，待命電路會在此失敗狀態中使用。

使用任何一項技術時，如果您影響 Azure 來優先使用您的其中一個 ExpressRoute，您也必須確定內部部署網路也偏好使用相同的 ExpressRoute 路徑來進行 Azure 系結的流量，以避免非對稱流量。 一般而言，本機喜好設定值會用來影響內部部署網路，以優先使用一個 ExpressRoute 線路。 本機喜好設定是內部 BGP (iBGP) 度量。 偏好最高的本機喜好設定值的 BGP 路由。

> [!IMPORTANT]
> 當您使用特定 ExpressRoute 線路作為待命時，您需要主動管理它們，並定期測試容錯移轉作業。 
> 

## <a name="large-distributed-enterprise-network"></a>大型分散式商業網路

當您有大型的分散式商業網路時，您可能會有多個 ExpressRoute 線路。 在本節中，我們將瞭解如何使用主動-主動 ExpressRoute 線路來設計嚴重損壞修復，而不需要額外的線路。 

讓我們來看看下圖中所說明的範例。 在此範例中，Contoso 在兩個不同的對等互連位置中透過 ExpressRoute 線路，在兩個不同的 Azure 區域中，連接到兩個 Contoso IaaS 部署的兩個內部部署位置。 

[![6]][6]

我們設計嚴重損壞修復的方式，會影響跨區域間的跨位置 (>region1/region2 到 location2/>location1) 流量路由傳送。 讓我們假設有兩個不同的災難架構會以不同方式路由傳送跨區域位置的流量。

### <a name="scenario-1"></a>實例 1

在第一個案例中，讓我們設計嚴重損壞修復，讓 Azure 區域與內部部署網路之間的所有流量，都會流經處於穩定狀態的本機 ExpressRoute 線路。 如果本機 ExpressRoute 線路失敗，則會將遠端 ExpressRoute 線路用於 Azure 和內部部署網路之間的所有流量。

下圖說明案例1。 在圖表中，綠色線表示 VNet1 和內部部署網路之間流量的路徑。 藍線表示 VNet2 和內部部署網路之間流量的路徑。 實線表示在穩定狀態中所需的路徑，而虛線表示具有穩定狀態流量的對應 ExpressRoute 線路失敗時的流量路徑。 

[![7]][7]

您可以使用連線權數來設計案例，以對內部部署網路系結流量的本機對等互連位置 ExpressRoute 進行 Vnet 影響。 若要完成此解決方案，您必須確保對稱的反向流量。 您可以在 BGP 路由器之間的 iBGP 會話使用本機喜好設定 (在內部部署端) 的 ExpressRoute 線路，以優先使用 ExpressRoute 線路。 下圖說明此解決方案。 

[![八角]][8]

### <a name="scenario-2"></a>案例 2

下圖說明案例2。 在圖表中，綠色線表示 VNet1 和內部部署網路之間流量的路徑。 藍線表示 VNet2 和內部部署網路之間流量的路徑。 在圖表) 的穩定狀態 (實線中，Vnet 和內部部署位置之間的所有流量都會透過 Microsoft 骨幹流動，而且只會流經內部部署位置之間的連線，而只會在失敗 (狀態下進行內部部署位置之間的互相連線，以) ExpressRoute 的圖表。

[![9]][9]

下圖說明此解決方案。 如圖所示，您可以使用更特定的路由來設計案例， (選項 1) 或路徑前面加上 (選項 2) ，以影響 VNet 路徑選取專案。 若要對 Azure 系結流量影響內部部署網路路由選取專案，您需要設定內部部署位置之間的互相連線。 羅豪您視需要設定互連連結，取決於內部部署網路內使用的路由通訊協定。 您可以搭配使用 iBGP 或計量與 IGP (OSPF 或) 來使用本機喜好設定。

[![10]][10]


## <a name="next-steps"></a>後續步驟

在本文中，我們討論了如何設計 ExpressRoute 線路私人對等互連連線的災難復原。 下列文章說明從應用程式和前端存取角度進行的嚴重損壞修復：

- [企業級災害復原][Enterprise DR]
- [使用 Azure Site Recovery 的 SMB 災害復原][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png個 "小型到中型的內部部署網路考慮"
[ ]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "使用更明確的路由選取2個影響路徑"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png透過 "Azure 入口網站設定連接權數"
使用連接權數的[4 個]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "影響路徑選取專案"
[ ]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "使用 AS 路徑前置的5個影響路徑選取專案"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "大型分散式內部部署網路考慮"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "案例 1"
[8 個]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "主動-主動 ExpressRoute 線路解決方案 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "案例 2"
[10 個]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "主動-主動 ExpressRoute 線路解決方案 2"

<!--Link References-->
[HA]: ./designing-for-high-availability-with-expressroute.md
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: ./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: ./expressroute-optimize-routing.md#solution-use-as-path-prepending