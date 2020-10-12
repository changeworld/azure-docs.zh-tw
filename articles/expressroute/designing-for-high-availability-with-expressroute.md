---
title: Azure ExpressRoute：設計高可用性
description: 本頁面提供使用 Azure ExpressRoute 時的高可用性架構建議。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: duau
ms.openlocfilehash: d6e63ad5781b0b8d6f46aee1cb88373721bb9dfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397894"
---
# <a name="designing-for-high-availability-with-expressroute"></a>使用 ExpressRoute 設計高可用性

ExpressRoute 的設計目的是為了提供高可用性，以提供對 Microsoft 資源的電訊公司私人網路連線能力。 換句話說，在 Microsoft 網路內的 ExpressRoute 路徑中不會有單一失敗點。 若要將可用性最大化，您的 ExpressRoute 線路客戶和服務提供者區段也應設計為高可用性。 在本文中，我們先來看看使用 ExpressRoute 建立強大的網路連線能力的網路架構考慮，然後讓我們來看看微調功能，以協助您改善 ExpressRoute 線路的高可用性。

>[!NOTE]
>本文所述的概念同樣適用于在虛擬 WAN 或其外部建立 ExpressRoute 線路時。
>

## <a name="architecture-considerations"></a>架構考慮

下圖說明使用 ExpressRoute 線路進行連線以將 ExpressRoute 線路的可用性最大化的建議方式。

 [![1]][1]

為了達到高可用性，在整個端對端網路中維持 ExpressRoute 電路的冗余是不可或缺的。 換句話說，您必須在內部部署網路內維持冗余，而且不應該危及服務提供者網路內的冗余。 盡可能保持冗余，表示避免單一網路失敗點。 具有網路裝置冗余的電源和冷卻能力，可進一步改善高可用性。

### <a name="first-mile-physical-layer-design-considerations"></a>第一英里的實體層設計考慮

 如果您在相同的客戶部署設備上終止 ExpressRoute 線路的主要和次要連線 (CPE) ，您將會危害內部部署網路內的高可用性。 此外，如果您透過 (的相同埠來設定主要和次要連線，請在不同的子介面上終止兩個連線，或藉由合併夥伴網路) 內的兩個連接，來強制夥伴在其網路區段上入侵高可用性。 下圖說明此漏洞。

[![2]][2]

相反地，如果您在不同的地理位置終止 ExpressRoute 線路的主要和次要連線，則可能會危及連線能力的網路效能。 如果流量在不同地理位置上的主要和次要連線之間主動負載平衡，則這兩個路徑之間的網路延遲可能會有顯著的差異，而導致網路效能不佳。 

針對地理位置多餘的設計考慮，請參閱 [使用 ExpressRoute][DR]進行嚴重損壞修復的設計。

### <a name="active-active-connections"></a>主動-主動連接

Microsoft network 設定為以主動-主動模式操作 ExpressRoute 線路的主要和次要連線。 不過，您可以透過您的路由通告，強制 ExpressRoute 線路的多餘連接以主動-被動模式運作。 將更明確的路由和 BGP 公告為路徑前置，是常見的技術，用來讓一個路徑優先于另一個路徑。

若要改善高可用性，建議您以主動-主動模式操作 ExpressRoute 線路的連接。 如果您讓連線在主動-主動模式下運作，Microsoft network 會對每個流量的連線負載平衡流量。

在主動-被動模式中執行 ExpressRoute 線路的主要和次要連線，會面臨兩個連線在使用中路徑失敗後失敗的風險。 切換時失敗的常見原因是缺乏主動的被動連線管理，以及用來通告過時路由的被動連接。

或者，在主動-主動模式中執行 ExpressRoute 線路的主要和次要連線，只會在 ExpressRoute 連線失敗後產生大約一半的流程失敗並重新路由傳送。 因此，主動-主動模式將大幅協助改善復原 (MTTR) 的平均時間。

### <a name="nat-for-microsoft-peering"></a>適用于 Microsoft 對等互連的 NAT 

Microsoft 對等互連是針對公用端點之間的通訊而設計的。 一般情況下，內部部署私人端點是 (NATed) 在客戶或合作夥伴網路上使用公用 IP 進行轉譯的網路位址，然後才能透過 Microsoft 對等互連進行通訊。 假設您使用主動-主動模式中的主要和次要連線，則 NAT 的位置和方式會影響您在其中一個 ExpressRoute 連線中失敗後的復原速度。 下圖說明兩種不同的 NAT 選項：

[![3]][3]

在選項1中，會在分割 ExpressRoute 的主要和次要連線之間的流量之後套用 NAT。 為了符合 NAT 的可設定狀態需求，主要與次要裝置之間會使用獨立的 NAT 集區，讓傳回流量抵達流量輸出的相同 edge 裝置。

在選項2中，在分割 ExpressRoute 的主要和次要連線之間的流量之前，會使用常見的 NAT 集區。 請務必區分一般 NAT 集區，在分割流量之前，不表示引進單一失敗點，因而危及高可用性。

使用選項1，在 ExpressRoute 連線失敗後，能夠連線到對應的 NAT 集區就會中斷。 因此，所有中斷的流程都必須由 TCP 或應用層在對應的視窗 timeout 之後重新建立。 如果有任一個 NAT 集區用來前端任何內部部署伺服器，而且對應的連線失敗，則在連線能力修正之前，無法從 Azure 連線到內部部署伺服器。

但使用選項2，即使在主要或次要連接失敗之後，也可以連線到 NAT。 因此，網路層本身可以重新路由傳送封包，並在失敗後加快復原速度。 

> [!NOTE]
> 如果您對主要和次要 ExpressRoute 連線使用 NAT 選項 1 (獨立的 NAT 集區) 並將 IP 位址的埠對應到內部部署伺服器，則在對應的連線失敗時，將無法透過 ExpressRoute 線路連線到伺服器。
> 

## <a name="fine-tuning-features-for-private-peering"></a>私用對等互連的微調功能

在本節中，我們會根據您的 Azure 部署，讓我們查看選擇性的 (，以及您對於可協助改善 ExpressRoute 電路高可用性的 MTTR) 功能有何敏感性。 具體而言，我們將探討 ExpressRoute 虛擬網路閘道的區域感知部署，以及雙向轉送偵測 (BFD) 。

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>可用性區域感知 ExpressRoute 虛擬網路閘道

Azure 區域中的可用性區域是由容錯網域和更新網域組成。 如果您選擇區域多餘的 Azure IaaS 部署，您可能也會想要設定可終止 ExpressRoute 私用對等互連的區域冗余虛擬網路閘道。 若要進一步瞭解，請參閱 [Azure 可用性區域中的區域冗余虛擬網路閘道][zone redundant vgw]。 若要設定區域冗余虛擬網路閘道，請參閱 [Azure 可用性區域中的建立區域冗余虛擬網路閘道][conf zone redundant vgw]。

### <a name="improving-failure-detection-time"></a>改善失敗偵測時間

ExpressRoute 支援 BFD over 私用對等互連。 BFD 可減少在內部部署端，Microsoft Enterprise Edge (Msee) 與其 BGP 鄰近專案之間的第2層網路失敗偵測時間（從大約3分鐘 (預設) 到小於一秒）。 快速失敗偵測時間有助於 hastening 失敗復原。 若要進一步瞭解，請參閱透過 [ExpressRoute 設定 BFD][BFD]。

## <a name="next-steps"></a>後續步驟

在本文中，我們討論了如何設計 ExpressRoute 線路連線能力的高可用性。 ExpressRoute 線路對等互連點會釘選到地理位置，因此可能會受到影響整個位置的嚴重失敗影響。 

若要針對設計考慮，建立與 Microsoft 骨幹的異地冗余網路連線能力，以承受會影響整個區域的嚴重失敗，請參閱 [使用 ExpressRoute 私用對等互連設計災難][DR]復原。

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "使用 ExpressRoute 連線的建議方式"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "次最差的最後英里連接"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png個 "NAT 選項"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




