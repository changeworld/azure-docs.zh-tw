---
title: Azure ExpressRoute：針對嚴重損壞修復進行設計
description: 此頁面提供使用 Azure ExpressRoute 時的嚴重損壞修復架構建議。
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74076695"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>使用 ExpressRoute 私用對等互連設計災難復原

ExpressRoute 是專為高可用性所設計，可提供與 Microsoft 資源的電訊公司私人網路連線能力。 換句話說，在 Microsoft network 內的 ExpressRoute 路徑中，沒有單一失敗點。 如需將 ExpressRoute 線路的可用性最大化的設計考慮，請參閱[使用 ExpressRoute 設計高可用性][HA]。

不過，採取 Murphy 的熱門」古訓--*如果發生任何問題，請將其*納入考慮，在本文中，我們將著重于超越無法使用單一 ExpressRoute 線路解決之失敗的解決方案。 換句話說，在本文中，我們將探討使用異地多餘的 ExpressRoute 線路，針對嚴重損壞修復建立穩固的後端網路連線能力的網路架構考慮。

## <a name="need-for-redundant-connectivity-solution"></a>需要重複的連接解決方案

在某些情況下，整個區域服務（Microsoft、網路服務提供者、客戶或其他雲端服務提供者）都會變得降低。 這類地區性服務影響的根本原因包括天然遭遇災難。 因此，針對商務持續性和任務關鍵性應用程式，請務必規劃嚴重損壞修復。   

不論您是在 Azure 區域或內部部署或其他任何地方執行任務關鍵性應用程式，您都可以使用另一個 Azure 區域作為容錯移轉網站。 下列文章說明從應用程式和前端存取角度的嚴重損壞修復：

- [企業級災害復原][Enterprise DR]
- [使用 Azure Site Recovery 的 SMB 災害復原][SMB DR]

如果您依賴內部部署網路與 Microsoft 之間的 ExpressRoute 連線來進行任務關鍵性作業，則您的嚴重損壞修復計畫也應該包含異地多餘的網路連線能力。 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>使用多個 ExpressRoute 線路的挑戰

當您使用一個以上的連線來互連相同的一組網路時，您會在網路之間引進平行路徑。 平行路徑若未正確架構，可能會導致非對稱路由。 如果您的路徑中有具狀態實體（例如，NAT、防火牆），非對稱路由可能會封鎖流量。  一般而言，透過 ExpressRoute 私人對等互連路徑，您將不會遇到可設定狀態的實體，例如 NAT 或防火牆。 因此，透過 ExpressRoute 私用對等互連的非對稱式路由不一定會封鎖流量。
 
不過，如果您將流量負載平衡到異地多餘的平行路徑，不論您是否有具狀態實體，都可能會遇到不一致的網路效能。 在本文中，我們將討論如何解決這些挑戰。

## <a name="small-to-medium-on-premises-network-considerations"></a>中小型內部部署網路考慮

讓我們來看下圖所示的範例網路。 在此範例中，Contoso 的內部部署位置與 Contoso 在 Azure 區域中的 VNet 之間會建立異地冗余 ExpressRoute 連線能力。 在圖表中，實心綠色線條表示慣用的路徑（透過 ExpressRoute 1），而虛線則代表待命路徑（透過 ExpressRoute 2）。

[![1]][1]

當您針對嚴重損壞修復設計 ExpressRoute 連線能力時，您需要考慮：

- 使用異地多餘的 ExpressRoute 線路
- 針對不同的 ExpressRoute 線路使用多樣化的服務提供者網路
- 設計每個 ExpressRoute 線路以提供[高可用性][HA]
- 在客戶網路上的不同位置終止不同的 ExpressRoute 線路

根據預設，如果您通告所有 ExpressRoute 路徑的路由都相同，Azure 會使用相同的多重路徑（ECMP）路由，對所有 ExpressRoute 路徑的內部部署系結流量進行負載平衡。

不過，使用異地多餘的 ExpressRoute 線路時，我們必須考慮不同網路路徑的不同網路效能（特別是針對網路延遲）。 若要在正常作業期間取得更一致的網路效能，您可能會想要使用提供最低延遲的 ExpressRoute 線路。

您可以使用下列其中一種技術來影響 Azure，使其優先于另一個 ExpressRoute 線路（以有效的順序列出）：

- 與其他 ExpressRoute 線路相比，在慣用的 ExpressRoute 線路上通告更具體的路由
- 在連接上設定更高的連線權數，以將虛擬網路連結至慣用的 ExpressRoute 線路
- 以較不常用的 ExpressRoute 線路，以較長的路徑（如路徑前置）來公告路由

### <a name="more-specific-route"></a>更特定的路由

下圖說明使用更明確的路由通告來影響 ExpressRoute 路徑選取專案。 在說明的範例中，Contoso 內部部署/24 IP 範圍會透過慣用路徑（ExpressRoute 1）和 as/24 透過待命路徑（ExpressRoute 2）公告為兩個/25 位址範圍。

[![2]][2]

相較于/24，因為/25 更具體，所以 Azure 會透過 ExpressRoute 1，將目的地為 10.1.11.0/24 的流量傳送至正常狀態。 如果 ExpressRoute 1 的兩個連線都已關閉，則 VNet 只會透過 ExpressRoute 2 查看 10.1.11.0/24 路由通告;因此，待命電路會在此失敗狀態下使用。

### <a name="connection-weight"></a>連接權數

下列螢幕擷取畫面說明如何透過 Azure 入口網站來設定 ExpressRoute 連接的權數。

[![3]][3]

下圖說明使用連接權數影響 ExpressRoute 路徑選取範圍。 預設連接權數為0。 在下列範例中，ExpressRoute 1 的連線權數設定為100。 當 VNet 收到透過多個 ExpressRoute 線路所公告的路由前置詞時，VNet 會偏好具有最高權數的連接。

[![4]][4]

如果 ExpressRoute 1 的兩個連線都已關閉，則 VNet 只會透過 ExpressRoute 2 查看 10.1.11.0/24 路由通告;因此，待命電路會在此失敗狀態下使用。

### <a name="as-path-prepend"></a>做為路徑的前置

下圖說明使用做為路徑前面的來影響 ExpressRoute 路徑選取專案。 在圖表中，透過 ExpressRoute 1 的路由通告會指出 eBGP 的預設行為。 在透過 ExpressRoute 2 的路由通告上，內部部署網路的 ASN 會在路由的 AS 路徑前面加上。 透過多個 ExpressRoute 線路接收相同的路由時，根據 eBGP 路由選取程式，VNet 會偏好具有最短路徑的路由。 

[![5]][5]

如果 ExpressRoute 1 的兩個連線都已關閉，則 VNet 只會透過 ExpressRoute 2 查看 10.1.11.0/24 路由通告。 Consequentially，path 較長的路徑會變得不相關。 因此，待命線路會在此失敗狀態下使用。

使用任何一種技術時，如果您將 Azure 影響到其他人的其中一個 ExpressRoute，您也必須確保內部部署網路也偏好 Azure 系結流量的相同 ExpressRoute 路徑，以避免非對稱流量。 一般而言，本機喜好設定值是用來影響內部部署網路，以優先使用一個 ExpressRoute 線路。 本機喜好設定是內部 BGP （iBGP）度量。 較高的本機喜好設定值是慣用的 BGP 路由。

> [!IMPORTANT]
> 當您使用特定 ExpressRoute 線路作為待命時，您需要主動管理它們，並定期測試容錯移轉作業。 
> 

## <a name="large-distributed-enterprise-network"></a>大型分散式商業網路

當您有大型的分散式商業網路時，可能會有多個 ExpressRoute 線路。 在本節中，我們將瞭解如何使用主動-主動 ExpressRoute 線路設計嚴重損壞修復，而不需要額外的待命線路。 

讓我們來看看下圖所說明的範例。 在此範例中，Contoso 有兩個內部部署位置連線到兩個不同 Azure 區域中的兩個 Contoso IaaS 部署，透過 ExpressRoute 線路位於兩個不同的對等互連位置。 

[![6]][6]

我們架構嚴重損壞修復的方式，會影響跨區域（region1/region2] 至 location2/location1）如何路由傳送流量。 讓我們假設有兩個不同的嚴重損壞架構會以不同的方式路由跨區域位置的流量。

### <a name="scenario-1"></a>實例 1

在第一個案例中，我們將設計嚴重損壞修復，讓 Azure 區域與內部部署網路之間的所有流量，透過本機 ExpressRoute 線路在穩定狀態下流動。 如果本機 ExpressRoute 線路失敗，則遠端 ExpressRoute 線路會用於 Azure 與內部部署網路之間的所有流量。

下圖說明案例1。 在圖表中，綠色線條表示 VNet1 與內部部署網路之間流量的路徑。 藍色線表示 VNet2 與內部部署網路之間流量的路徑。 實線表示在穩定狀態中所需的路徑，而虛線表示在具有穩定狀態流量流量的對應 ExpressRoute 電路失敗時的流量路徑。 

[![7]][7]

您可以使用連線權數來架構案例，以影響 Vnet，以將內部部署網路系結流量連線到本機對等互連位置 ExpressRoute。 若要完成此解決方案，您必須確定對稱的反向流量流程。 您可以將本機喜好設定用於 BGP 路由器（在內部部署端上已終止 ExpressRoute 線路）上的 iBGP 會話，以偏好使用 ExpressRoute 線路。 下圖說明此解決方案。 

[![8]][8]

### <a name="scenario-2"></a>案例 2

下圖說明案例2。 在圖表中，綠色線條表示 VNet1 與內部部署網路之間流量的路徑。 藍色線表示 VNet2 與內部部署網路之間流量的路徑。 在穩定狀態（圖中的實線）中，Vnet 與內部部署位置之間的所有流量都是透過 Microsoft 骨幹流動，而且只會流經內部部署位置之間的互連，而不會通過 ExpressRoute 的失敗狀態（在圖表中的虛線線）。

[![9]][9]

下圖說明此解決方案。 如圖所示，您可以使用更明確的路由（選項1）或路徑前置（選項2）來設計案例，以影響 VNet 路徑選取。 若要影響 Azure 系結流量的內部部署網路路由選取，您需要設定內部部署位置之間的相互連線，這不是較理想的方式。 羅豪您視需要設定互連連結，視內部部署網路內使用的路由通訊協定而定。 您可以搭配使用本機喜好設定與 iBGP 或公制搭配 IGP （OSPF 或 IS-IS）。

[![10]][10]


## <a name="next-steps"></a>後續步驟

在本文中，我們討論了如何針對 ExpressRoute 線路私用對等互連連線的嚴重損壞修復進行設計。 下列文章說明從應用程式和前端存取角度的嚴重損壞修復：

- [企業級災害復原][Enterprise DR]
- [使用 Azure Site Recovery 的 SMB 災害復原][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "個小型到中型的內部部署網路考慮"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "使用更特定的路由來影響路徑選取"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png透過 "Azure 入口網站設定連接權數"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "使用連接權數影響路徑選取"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "使用做為路徑前置的方式來影響路徑選取"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "大型分散式內部部署網路考慮"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "案例 1"
[8 個]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "主動-主動 ExpressRoute 線路解決方案 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "案例 2"
[10 個]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "主動-主動 ExpressRoute 線路解決方案 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





