---
title: Azure ExpressRoute：高可用性的設計
description: 此頁面提供使用 Azure ExpressRoute 時的高可用性架構建議。
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4c3c6ae5fbdd91e6e44438be7fef2a3a91564a34
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74076687"
---
# <a name="designing-for-high-availability-with-expressroute"></a>使用 ExpressRoute 設計高可用性

ExpressRoute 是專為高可用性所設計，可提供與 Microsoft 資源的電訊公司私人網路連線能力。 換句話說，在 Microsoft network 內的 ExpressRoute 路徑中，沒有單一失敗點。 若要將可用性最大化，您的 ExpressRoute 線路的客戶和服務提供者區段也應為高可用性架構。 在本文中，我們將探討使用 ExpressRoute 建立健全的網路連線的網路架構考慮，然後讓我們深入探討可協助您改善 ExpressRoute 線路高可用性的微調功能。


## <a name="architecture-considerations"></a>架構考慮

下圖說明使用 ExpressRoute 線路進行連線以將 ExpressRoute 線路的可用性最大化的建議方式。

 [![1]][1]

為了達到高可用性，必須維護整個端對端網路的 ExpressRoute 線路的冗余。 換句話說，您需要維護內部部署網路內的冗余，而不應危害服務提供者網路內的冗余。 維持最小的冗余，意味著避免單一網路失敗點。 具有網路裝置的重複電源和冷卻能力，可進一步提升高可用性。

### <a name="first-mile-physical-layer-design-considerations"></a>第一英里實體層設計考慮

 如果您在相同的客戶部署設備（CPE）上同時終止 ExpressRoute 線路的主要和次要連線，您就會危害內部部署網路內的高可用性。 此外，如果您透過相同的 CPE 埠（藉由終止不同子介面的兩個連線，或合併夥伴網路內的兩個連線）來設定主要和次要連線，您也會強制夥伴危害其網路區段的高可用性。 下圖說明這項入侵。

[![2]][2]

另一方面，如果您在不同的地理位置終止 ExpressRoute 線路的主要和次要連線，您可能會危害連線的網路效能。 如果流量在主要和不同地理位置上終止的次要連線之間主動達到負載平衡，這兩個路徑之間的網路延遲可能會有顯著的差異，而導致網路效能不佳。 

如需異地多餘的設計考慮，請參閱[使用 ExpressRoute 進行][DR]嚴重損壞修復的設計。

### <a name="active-active-connections"></a>主動-主動連線

Microsoft network 已設定為以主動-主動模式操作 ExpressRoute 線路的主要和次要連線。 不過，透過您的路由公告，您可以強制 ExpressRoute 線路的重複連線以主動-被動模式運作。 將更特定的路由和 BGP 公告為路徑前置，是用來在另一個路徑慣用的常用技術。

若要改善高可用性，建議您在主動-主動模式中，同時操作 ExpressRoute 線路的連接。 如果您讓連線以主動-主動模式運作，Microsoft network 會針對每個流程的連線，對流量進行負載平衡。

在主動-被動模式中執行 ExpressRoute 線路的主要和次要連線，會面臨在使用中路徑失敗之後，這兩個連線失敗的風險。 切換失敗的常見原因是缺少被動連線的主動管理，以及會通告過時路由的被動連線。

或者，在主動-主動模式中執行 ExpressRoute 線路的主要和次要連線，會導致在 ExpressRoute 連線失敗之後，只有一半的流程失敗並取得重新路由。 因此，主動-主動模式會大幅協助改善復原的平均時間（MTTR）。

### <a name="nat-for-microsoft-peering"></a>適用于 Microsoft 對等互連的 NAT 

Microsoft 對等互連是針對公開端點之間的通訊而設計的。 一般來說，內部部署私人端點是在客戶或合作夥伴網路上使用公用 IP 進行網路位址轉譯（NATed），然後再透過 Microsoft 對等互連進行通訊。 假設您同時使用主動-主動模式中的主要和次要連線，而 NAT 會對您在其中一個 ExpressRoute 連線發生失敗的復原速度造成影響。 下圖說明兩個不同的 NAT 選項：

[![3]][3]

在選項1中，在分割 ExpressRoute 的主要和次要連線之間的流量之後，會套用 NAT。 為了符合 NAT 的具狀態需求，會在主要和次要裝置之間使用獨立的 NAT 集區，以便讓傳回流量抵達流量輸出的相同邊緣裝置。

在選項2中，會先使用一般的 NAT 集區，再分割 ExpressRoute 的主要和次要連線之間的流量。 請務必區分一般 NAT 集區在分割流量之前，不表示會導致單一失敗點，因而危及高可用性。

使用選項1時，在 ExpressRoute 連線失敗之後，連線到對應 NAT 集區的功能就會中斷。 因此，必須在對應的視窗超時後，由 TCP 或應用層重新建立所有中斷的流程。 如果其中一個 NAT 集區是用來前端任何內部部署伺服器，而且對應的連線失敗，則必須等到連線解決後，才能從 Azure 連線到內部部署伺服器。

但使用選項2時，即使在主要或次要連線失敗之後，也可以觸達 NAT。 因此，網路層本身可以重新路由封包，並在失敗之後協助復原更快。 

> [!NOTE]
> 如果您使用 NAT 選項1（主要和次要 ExpressRoute 連線的獨立 NAT 集區），並將 IP 位址的埠從其中一個 NAT 集區對應到內部部署伺服器，則當對應的連線失敗時，將無法透過 ExpressRoute 線路連線到該伺服器。
> 

## <a name="fine-tuning-features-for-private-peering"></a>私用對等互連的微調功能

在本節中，我們會根據您的 Azure 部署和您對 MTTR 的敏感性，讓我們瞭解如何協助改善 ExpressRoute 線路的高可用性。 具體來說，讓我們來看看 ExpressRoute 虛擬網路閘道的區域感知部署，以及雙向轉送偵測（BFD）。

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>可用性區域感知 ExpressRoute 虛擬網路閘道

Azure 區域中的可用性區域是由容錯網域和更新網域組成。 如果您選擇進列區域多餘的 Azure IaaS 部署，您可能也會想要設定可終止 ExpressRoute 私用對等互連的區域冗余虛擬網路閘道。 若要進一步瞭解，請參閱[關於 Azure 可用性區域中的區域冗余虛擬網路閘道][zone redundant vgw]。 若要設定區域冗余虛擬網路閘道，請參閱[在 Azure 可用性區域中建立區域冗余虛擬網路閘道][conf zone redundant vgw]。

### <a name="improving-failure-detection-time"></a>改善失敗偵測時間

ExpressRoute 支援透過私用對等互連的 BFD。 BFD 會將 Microsoft Enterprise Edge （Msee）及其 BGP 鄰近專案（在內部部署端）之間的失敗偵測時間，從約3分鐘（預設值）減少為小於一秒。 快速失敗偵測時間有助於 hastening 損毀修復。 若要進一步瞭解，請參閱透過[ExpressRoute 設定 BFD][BFD]。

## <a name="next-steps"></a>後續步驟

在本文中，我們討論了如何針對 ExpressRoute 線路連線能力的高可用性進行設計。 ExpressRoute 線路對等互連點會釘選到地理位置，因此可能會受到影響整個位置的嚴重失敗影響。 

如需設計考慮，以建立與 Microsoft 骨幹的異地冗余網路連線，以承受會影響整個區域的重大失敗，請參閱[使用 ExpressRoute 私用對等互連設計][DR]嚴重損壞修復。

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "使用 ExpressRoute 連接的建議方式"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "次佳的最後英里連線能力"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT 選項"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




