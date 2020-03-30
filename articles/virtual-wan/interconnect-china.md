---
title: 使用 Azure 虛擬 WAN 和安全集線器與中國互連
description: 了解虛擬 WAN 自動化可調整的分支對分支連線、可用的區域和夥伴。
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: e05aa6e8f656001eb9eb2a3717dde6c16f030e33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337139"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>使用 Azure 虛擬 WAN 和安全集線器與中國互連

當觀察常見的汽車、製造業、物流業或其他機構，如大使館時，經常有如何改善與中國的互聯的問題。 這些改進主要與使用雲服務（如 Office 365、Azure 全球服務）或將中國境內的分支機搆與客戶骨幹網連接相關。

在大多數情況下，客戶都在為連接中國境外的高延遲、低頻寬、不穩定的連接和高昂的成本而苦苦掙扎（例如，歐洲或美國）。

這些鬥爭的一個原因是"中國長城防火牆"，它保護了中國互聯網的一部分，並過濾到中國的流量。 除了香港和澳門等特別行政區外，幾乎所有從中國大陸到境外的交通都通過長城。 貫穿香港和澳門的流量沒有完全撞上長城防火牆，由長城防火牆的一部分處理。

![提供程式互連](./media/interconnect-china/provider.png)

使用虛擬 WAN，客戶可以建立更性能、更穩定的連接到 Microsoft 雲服務，並在不違反中國網路安全法的情況下建立與其商業網路的連接。

## <a name="requirements-and-workflow"></a><a name="requirements"></a>要求和工作流

如果您想遵守中國網路安全法，您需要滿足一系列特定條件。

首先，您需要與擁有中國 ICP（互聯網內容提供者）許可證的網路和 ISP 合作。 在大多數情況下，您將最終擁有以下提供程式之一：

* 中國電信全球有限公司
* 中國移動有限公司
* 中國聯通有限公司
* 電訊盈科全球有限公司
* 香港電訊有限公司

根據供應商和您的需求，您現在需要購買以下網路連接服務之一，以互連您在中國境內的分支機搆。

* MPLS/IPVPN網路 
* 軟體定義的 WAN （SDWAN）
* 專用互聯網接入

接下來，您需要與該供應商達成一致，以便突破微軟全球網路及其在香港的邊緣網路，而不是在北京或上海。 在這種情況下，香港非常重要，因為它與中國有物理聯繫和位置。

雖然大多數客戶認為使用新加坡進行互連是最好的例子，因為它在地圖上看起來離中國更近，但事實並非如此。 當您按照網路光纖地圖操作時，幾乎所有的網路連接都經過北京、上海和香港。 這使得香港成為與中國互聯的更好位置選擇。

根據供應商的不同，您可能會獲得不同的服務產品。 下表根據撰寫本文時的資訊，顯示了提供程式及其提供的服務的示例。

| 服務 | 提供程式示例 |
| --- | --- |
| MPLS/IPVPN網路 |電訊盈科，中國電信全球 |
|SDWAN| 電訊盈科，中國電信全球|
| 專用互聯網接入 | 電訊盈科、香港電訊、中國美孚、電訊盈科 |

通過您的供應商，您可以商定以下兩種解決方案中的哪一種可用於訪問 Microsoft 全球主幹：

* 在香港終止 Microsoft Azure ExpressRoute。 使用MPLS/IPVPN就是這種情況。 目前，只有只有ICP牌照供應商的快遞路由香港是中國電信全球。 但是，如果他們利用雲交換供應商（如兆埠或 InterCloud），他們也可以與其他供應商交談。 有關詳細資訊，請參閱[ExpressRoute 連接提供程式](../expressroute/expressroute-locations-providers.md#partners)。

* 直接在以下互聯網交換點之一使用專用 Internet 訪問，或使用私人網路絡互連。

以下清單顯示香港可能進行互聯網交易所：

* AMS-IX 香港
* BBIX 香港
* Equinix 香港
* 香港IX

使用此連接時，Microsoft 服務的下一個 BGP 躍點必須是 Microsoft 自治系統編號 （AS#） 8075。 如果使用單個位置或 SDWAN 解決方案，則是連接的選擇。

無論哪種方式，我們仍然建議你有第二個和週期性互聯網突破進入中國大陸。 這是為了將企業流量拆分為雲服務（如 Microsoft 365 和 Azure）以及受法律監管的 Internet 流量之間的流量。

中國境內合規的網路架構可能類似于以下示例：

![多個分支](./media/interconnect-china/multi-branch.png)

在此示例中，與香港的 Microsoft 全球網路進行互連，現在可以開始利用[Azure 虛擬廣域網路全球傳輸體系結構](virtual-wan-global-transit-network-architecture.md)和其他服務（如 Azure 安全虛擬廣域網路中心）來使用服務並互連中國境外的分支機搆和資料中心。

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>樞紐到集線器通信

在本節中，我們使用虛擬 WAN 集線器到集線器通信進行互連。 在這種情況下，您將創建新的虛擬廣域網路中心資源以連接到香港的虛擬廣域網路中心、您喜歡的其他地區、已具有 Azure 資源的區域或要連接的區域。

示例體系結構可能類似于以下示例：

![示例 WAN](./media/interconnect-china/sample.png)

在此示例中，中國分支通過使用 VPN 或 MPLS 連接連接到 Azure 雲中國並相互連接。 需要連接到全球服務的分支機搆使用直接連接到香港的 MPLS 或基於互聯網的服務。 如果要在香港和其他區域使用 ExpressRoute，則需要配置[ExpressRoute 全球覆蓋，](../expressroute/expressroute-global-reach.md)以互連兩個 ExpressRoute 電路。

某些地區不提供 ExpressRoute 全球覆蓋。 例如，如果您需要與巴西或印度進行互連，則需要利用[雲交換供應商](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers)提供路由服務。

下圖顯示了此方案的兩個示例。

![Global Reach](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Office 365 的安全互聯網突破

另一個考慮因素是網路安全以及登錄中國和虛擬廣域網路建立骨幹元件與客戶骨幹網之間的進入點。 在大多數情況下，需要突破香港互聯網，直接到達微軟邊緣網路，並因此，Azure 前門伺服器用於微軟365服務。

對於具有虛擬 WAN 的兩種方案，您將利用[Azure 虛擬 WAN 安全集線器](../firewall-manager/secured-virtual-hub.md)。 使用 Azure 防火牆管理器，可以將常規虛擬 WAN 中心更改為安全中心，然後在該中心內部署和管理 Azure 防火牆。

下圖顯示了此方案的示例：

![網路和微軟服務流量的互聯網突破](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>體系結構和流量流

根據您對與香港連接的選擇，整體架構可能會稍有改變。 本節顯示了三個可用的體系結構，與 VPN 或 SDWAN 和/或 ExpressRoute 不同組合。

所有這些選項都利用 Azure 虛擬 WAN 安全集線器進行香港直接 M365 連接。 這些體系結構還支援[Office 365 多地理位置](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo)的合規性要求，並將流量保留在下一個 Office 365 前門位置附近。 因此，這也是微軟365在中國使用的改進。

將 Azure 虛擬 WAN 與 Internet 連接結合使用時，每個連接都可以受益于其他服務，如[Microsoft Azure 對等服務 （MAPS）。](https://docs.microsoft.com/azure/peering-service/about) MAPS 旨在優化來自協力廠商互聯網服務提供者的 Microsoft 全球網路流量。

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>選項 1：SDWAN 或 VPN

本節討論一種設計，該設計使用 SDWAN 或 VPN 到香港和其他分支機搆。 此選項顯示在虛擬 WAN 主幹的兩個網站上使用純 Internet 連接時的使用和流量流。 在這種情況下，連接通過專用互聯網接入或 ICP 供應商 SDWAN 解決方案帶到香港。 其他分支機搆也使用純互聯網或SDWAN解決方案。

![中國到香港的交通](./media/interconnect-china/china-traffic.png)

在此體系結構中，每個網站都使用 VPN 和 Azure 虛擬 WAN 連接到 Microsoft 全球網路。 網站與香港之間的流量在微軟網路傳輸，最後一英里只使用常規的互聯網連接。

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>選項 2：快速路由和 SDWAN 或 VPN

本節討論在香港使用 ExpressRoute 和其他具有 VPN/SDWAN 分支機搆的分支機搆的設計。 此選項顯示在香港終止的和 ExpressRoute 的使用以及通過 SDWAN 或 VPN 連接的其他分支機搆。 香港的 ExpressRoute 目前僅限於供應商的簡短清單，您可以在[快速路線合作夥伴](../expressroute/expressroute-locations-providers.md#global-commercial-azure)清單中找到該清單。

![中國至香港交通快線](./media/interconnect-china/expressroute.png)

也有選擇終止從中國的ExpressRoute，例如在韓國或日本。 但是，鑒於合規、監管和延遲，香港目前是最佳選擇。

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>選項 3：僅限快速路由

本節討論用於香港和其他分支機搆的 ExpressRoute 的設計。 此選項在兩端使用 ExpressRoute 顯示互連。 在這裡，您有一個不同于其他流量的流量。 Microsoft 365 流量將流向 Azure 虛擬 WAN 安全集線器，並從那裡流向 Microsoft 邊緣網路和 Internet。

到互聯分支機搆或從它們到中國位置的流量將遵循該體系結構中的不同方法。 目前虛擬廣域網路不支援快速路由到快速路由傳輸。 流量將利用 ExpressRoute 全球覆蓋或協力廠商互連，而無需通過虛擬廣域網路中心。 它將直接從一個 Microsoft 企業邊緣 （MSEE） 流向另一個。

![ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

目前，ExpressRoute 全球覆蓋/地區並非在每個國家/地區都可用，但您可以使用 Azure 虛擬 WAN 配置解決方案。

例如，您可以使用 Microsoft 對等互連配置 ExpressRoute，並通過該對等互連連接到 Azure 虛擬 WAN 的 VPN 隧道。 現在，您再次啟用了 VPN 和 ExpressRoute 之間的傳輸，沒有全球覆蓋和協力廠商供應商和服務（如兆埠雲）。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

* [使用 Azure 虛擬 WAN 的全球傳輸網路架構](virtual-wan-global-transit-network-architecture.md)

* [創建虛擬廣域網路中心](virtual-wan-site-to-site-portal.md)

* [配置虛擬廣域網路安全集線器](../firewall-manager/secure-cloud-network.md)

* [Azure 對等服務預覽概述](https://docs.microsoft.com/azure/peering-service/about)
