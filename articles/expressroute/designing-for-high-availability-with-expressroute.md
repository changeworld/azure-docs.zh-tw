---
title: Azure 快速路由：為高可用性設計
description: 此頁提供使用 Azure ExpressRoute 時高可用性的體系結構建議。
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4c3c6ae5fbdd91e6e44438be7fef2a3a91564a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076687"
---
# <a name="designing-for-high-availability-with-expressroute"></a>使用 ExpressRoute 設計高可用性

ExpressRoute 專為高可用性而設計，可向 Microsoft 資源提供運營商級專用網路連接。 換句話說，在 Microsoft 網路中的 ExpressRoute 路徑中沒有單點故障。 為了最大化可用性，還應為高可用性設計快速路由電路的客戶和服務提供者段。 在本文中，首先讓我們介紹使用 ExpressRoute 構建強大網路連接的網路架構注意事項，然後介紹可説明您提高 ExpressRoute 電路高可用性的微調功能。


## <a name="architecture-considerations"></a>體系結構注意事項

下圖說明瞭使用 ExpressRoute 電路連接以最大化 ExpressRoute 電路可用性的建議方式。

 [![1]][1]

為了獲得高可用性，在整個端到端網路中維護 ExpressRoute 電路的冗余至關重要。 換句話說，您需要在本地網路中保持冗余，並且不應危及服務提供者網路中的冗余。 保持最小冗余意味著避免單點網路故障。 為網路設備提供冗余電源和冷卻將進一步提高高可用性。

### <a name="first-mile-physical-layer-design-considerations"></a>第一英里實體層設計注意事項

 如果在同一客戶場所設備 （CPE） 上終止 ExpressRoute 電路的主連接和輔助連接，則會損害本地網路的高可用性。 此外，如果通過 CPE 的同一端口配置主連接和輔助連接（通過終止不同子介面下的兩個連接或在合作夥伴網路中合併兩個連接），則強制合作夥伴也會損害其網段的高可用性。 下圖說明瞭這種折衷。

[![2]][2]

另一方面，如果您在不同地理位置終止 ExpressRoute 電路的主連接和輔助連接，則可能會損害連接的網路性能。 如果跨在不同地理位置上終止的主連接和輔助連接的流量在主動負載平衡，則兩個路徑之間的網路延遲的潛在巨大差異將導致網路不理想性能。 

有關異地冗余設計注意事項，請參閱[使用 ExpressRoute 進行災害復原設計][DR]。

### <a name="active-active-connections"></a>主動-主動連接

Microsoft 網路設定為在主動-主動模式下運行 ExpressRoute 電路的主和輔助連接。 但是，通過路由通告，您可以強制 ExpressRoute 電路的冗余連接在主動-被動模式下運行。 播發更具體的路徑和 BGP AS 路徑預處理是用於使一個路徑優先于另一條路徑的常見技術。

為了提高高可用性，建議在主動-主動模式下操作 ExpressRoute 電路的兩個連接。 如果允許連接在主動-活動模式下運行，Microsoft 網路將載入按流平衡跨連接的流量。

在主動-被動模式下運行 ExpressRoute 電路的主連接和輔助連接時，在有源路徑發生故障後，兩個連接都失敗的風險。 切換失敗的常見原因是被動連接缺乏主動管理，以及被動連接廣告陳舊路由。

或者，在主動-主動模式下運行 ExpressRoute 電路的主連接和輔助連接，在 ExpressRoute 連接發生故障後，只會導致大約一半的流失敗並重新路由。 因此，主動-主動模式將大大有助於提高平均恢復時間 （MTTR）。

### <a name="nat-for-microsoft-peering"></a>用於微軟對等的 NAT 

微軟對等互連設計用於公共端點之間的通信。 通常，本地專用終結點是在通過 Microsoft 對等互連進行通信之前，在客戶或合作夥伴網路上使用公共 IP 翻譯 （NATed） 的網路位址轉譯 （NATed）。 假設您在主動-主動模式下同時使用主連接和輔助連接，則 NAT 在其中一個 ExpressRoute 連接發生故障後恢復的速度和方式都會受到影響。 下圖說明瞭兩個不同的 NAT 選項：

[![3]][3]

在選項 1 中，NAT 在拆分 ExpressRoute 的主連接和輔助連接之間的流量後應用。 為了滿足 NAT 的有狀態要求，在主設備和輔助設備之間使用獨立的 NAT 池，以便返回流量到達流通過的同一邊緣設備。

在選項 2 中，在拆分 ExpressRoute 的主連接和輔助連接之間的流量之前，將使用公共 NAT 池。 在拆分流量之前，必須區分公共 NAT 池並不意味著引入單點故障，從而影響高可用性。

在選項 1 中，在 ExpressRoute 連接失敗後，訪問相應 NAT 池的能力將中斷。 因此，所有斷開的流都必須在相應的視窗超時後由 TCP 或應用程式層重新建立。 如果其中一個 NAT 池用於前端任何本機伺服器，並且相應的連接失敗，則在修復連接之前，無法從 Azure 訪問本機伺服器。

而使用選項 2，即使在主連接或輔助連接故障後，NAT 也可到達。 因此，網路層本身可以重新路由資料包，並説明在發生故障後更快地恢復。 

> [!NOTE]
> 如果使用 NAT 選項 1（用於主和輔助 ExpressRoute 連接的獨立 NAT 池），並將 IP 位址的埠從其中一個 NAT 池映射到本機伺服器，則當相應的連接失敗。
> 

## <a name="fine-tuning-features-for-private-peering"></a>用於專用對等互連的微調功能

在本節中，讓我們查看可選功能（取決於 Azure 部署以及您對 MTTR 的敏感程度），這些功能有助於提高 ExpressRoute 電路的高可用性。 具體來說，讓我們回顧一下 ExpressRoute 虛擬網路閘道和雙向轉發檢測 （BFD） 的區域感知部署。

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>可用性區域感知快速路由虛擬網路閘道

Azure 區域中的可用性區域是由容錯網域和更新網域組成。 如果選擇區域冗余 Azure IaaS 部署，您可能還需要配置終止 ExpressRoute 專用對等互連的區域冗余虛擬網路閘道。 要進一步瞭解，請參閱[有關 Azure 可用性區域中的區域冗余虛擬網路閘道][zone redundant vgw]。 要配置區域冗余虛擬網路閘道，請參閱[在 Azure 可用性區域中創建區域冗余虛擬網路閘道][conf zone redundant vgw]。

### <a name="improving-failure-detection-time"></a>縮短故障檢測時間

ExpressRoute 支援 BFD 而不是專用對等互連。 BFD 將 Microsoft 企業邊緣 （MsEEs） 與其本地端的 BGP 鄰居之間的第 2 層網路故障檢測時間從大約 3 分鐘（預設）縮短到不到一秒。 快速故障檢測時間有助於加快故障恢復。 要進一步瞭解，請參閱[通過快速路由配置 BFD][BFD]。

## <a name="next-steps"></a>後續步驟

在本文中，我們討論了如何為快速路由電路連接的高可用性進行設計。 ExpressRoute 電路對等點固定到地理位置，因此可能會受到影響整個位置的災難性故障的影響。 

有關構建與 Microsoft 骨幹網的異地冗余網路連接以承受災難性故障（影響整個區域）的設計注意事項，請參閱使用[ExpressRoute 專用對等互連進行災害復原設計][DR]。

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "使用 ExpressRoute 進行連接的推薦方式"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "次優到最後一英里的連接"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "個 NAT 選項"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




