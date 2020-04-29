---
title: 使用 Azure 虛擬 WAN 和安全中樞與中國互連
description: 了解虛擬 WAN 自動化可調整的分支對分支連線、可用的區域和夥伴。
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d086484ece6faf95dccffb1e29da8a0e906b1da4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985621"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>使用 Azure 虛擬 WAN 和安全中樞與中國互連

在查看一般汽車、製造、物流產業或其他國家標準暨（例如 embassies）時，通常會有關于如何改善與中國的互連的問題。 這些改良功能大多適用于使用 Office 365、Azure 全球服務或中國內部的互連分支與客戶骨幹等雲端服務。

在大部分的情況下，客戶會面臨高延遲、低頻寬、不穩定的連線，以及連接到中國外部（例如歐洲或美國）的高成本。

這些掙扎的原因是「中國的絕佳防火牆」，可保護網際網路的中文部分，並篩選到中國的流量。 幾乎所有從中國到中國以外執行的流量（如香港和澳門之類的特殊系統管理區域除外）都會通過絕佳的防火牆。 透過香港和澳門執行的流量不會在完全強制下達到絕佳的防火牆，而是由絕佳防火牆的子集來處理。

![提供者互連](./media/interconnect-china/provider.png)

客戶可以使用虛擬 WAN，建立更高效能且穩定的連線來連線到 Microsoft Cloud 服務和商業網路的連線，而不會中斷中文網路安全性法則。

## <a name="requirements-and-workflow"></a><a name="requirements"></a>需求和工作流程

如果您想要保持與中文網路安全性法則相容，則必須符合一組特定條件。

首先，您必須與擁有適用于中國之 ICP （網際網路內容提供者）授權的網路和 ISP 一起合作。 在大部分的情況下，您會得到下列其中一個提供者：

* 中國電信全球有限公司。
* 中國 Mobile 公司。
* 中國 Unicom 有限公司。
* PCCW GLOBAL LIMITED Global 有限公司。
* 香港的電信公司。

視提供者和您的需求而定，您現在必須購買下列其中一種網路聯機服務，才能在中國與您的分支互連。

* MPLS/IPVPN 網路 
* 軟體定義的 WAN （SDWAN）
* 專用的網際網路存取

接下來，您必須同意該提供者，讓 Microsoft 全球網路及其邊緣網路輸入香港，而不是北京或上海。 在此情況下，香港特別行政區非常重要，因為它的實體連接和中國的位置。

雖然大部分的客戶都認為使用新加坡來進行互連是最佳情況，因為在查看地圖時，中國看起來很近，這不是真正的情況。 當您遵循網路光纖對應時，幾乎所有網路連線都會通過北京、上海和香港。 這讓香港的位置更適合與中國互連。

視提供者而定，您可能會取得不同的服務供應專案。 下表根據撰寫本文時的資訊，顯示提供者的範例及其提供的服務。

| Service | 提供者範例 |
| --- | --- |
| MPLS/IPVPN 網路 |PCCW GLOBAL LIMITED，中國電信全球 |
|SDWAN| PCCW GLOBAL LIMITED，中國電信全球|
| 專用的網際網路存取 | PCCW GLOBAL LIMITED，香港電信，中國 Mobile|

有了您的提供者，您就可以同意下列哪一種解決方案用來觸及 Microsoft 全球骨幹：

* 取得 Microsoft Azure ExpressRoute 在香港特別行政區結束。 這就是使用 MPLS/IPVPN 的情況。 目前只有唯一具有「ExpressRoute」的 ICP 授權提供者，才是「中國電信全球」。 不過，如果使用者利用 Megaport 或 InterCloud 等雲端交換提供者，也可以與其他提供者溝通。 如需詳細資訊，請參閱[ExpressRoute 連線提供者](../expressroute/expressroute-locations-providers.md#partners)。

* 直接在下列其中一個網際網路交換點上使用專用的網際網路存取，或使用私人網路互連。

下列清單顯示香港中可能的網際網路交換：

* AMS-IX 香港特別行政區
* BBIX 香港特別行政區
* Equinix 香港特別行政區
* HKIX

使用此連接時，您的下一個 Microsoft 服務 BGP 躍點必須是 Microsoft 自發系統編號（AS #）8075。 如果您使用單一位置或 SDWAN 解決方案，則會選擇連接。

無論如何，我們還是建議您將第二個和一般的網際網路分類為中文大陸。 這是為了將企業流量之間的流量分割為 Microsoft 365 和 Azure 這類雲端服務，以及受法律管制的網際網路流量。

中國內符合規範的網路架構可能會如下列範例所示：

![多個分支](./media/interconnect-china/multi-branch.png)

在此範例中，與 Microsoft Global 網路輸入香港）互連，您現在可以開始利用[Azure 虛擬 Wan 全域傳輸架構](virtual-wan-global-transit-network-architecture.md)和其他服務（例如 Azure 安全虛擬 wan 中樞），以在中國以外的分支和資料中心使用服務和互連。

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>中樞對中樞通訊

在本節中，我們會使用虛擬 WAN 中樞對中樞通訊來進行互連。 在此案例中，您會建立新的虛擬 WAN 中樞資源，以連線至香港中的虛擬 WAN 中樞、您偏好的其他區域、您已經有 Azure 資源的區域，或想要連接的位置。

範例架構看起來如下列範例所示：

![範例 WAN](./media/interconnect-china/sample.png)

在此範例中，中國分支會使用 VPN 或 MPLS 連線，連接到 Azure 雲端中國和彼此。 需要連接到全域服務的分支，會使用直接連線到香港的 MPLS 或以網際網路為基礎的服務。 如果您想要在香港和另一個區域中使用 ExpressRoute，您必須設定[Expressroute Global 觸及](../expressroute/expressroute-global-reach.md)以同時互連這兩個 ExpressRoute 線路。

在某些區域中無法使用 ExpressRoute 全球範圍。 例如，如果您需要與巴西或印度互連，您必須利用[雲端 Exchange 提供者](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers)來提供路由服務。

下圖顯示此案例的兩個範例。

![Global Reach](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Office 365 的安全網際網路分類

另一個考慮是網路安全性，以及記錄中國與虛擬 WAN 建立的骨幹元件之間的進入點，以及客戶骨幹。 在大部分情況下，需要以香港的方式對網際網路進行分類，以直接觸達 Microsoft Edge 網路，並將用於 Microsoft 365 服務的 Azure 前端伺服器。

針對這兩種使用虛擬 WAN 的案例，您會利用[Azure 虛擬 wan 保護的中樞](../firewall-manager/secured-virtual-hub.md)。 使用 Azure 防火牆管理員，您可以將一般虛擬 WAN 中樞變更為受保護的中樞，然後在該中樞內部署和管理 Azure 防火牆。

下圖顯示此案例的範例：

![Web 和 Microsoft 服務流量的網際網路分類](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>架構和流量流程

根據您對於香港連線的選擇而定，整體架構可能會稍微變更。 本節顯示三種適用于 VPN 或 SDWAN 和/或 ExpressRoute 之不同組合的可用架構。

所有這些選項都是使用 Azure 虛擬 WAN 保護的中樞，以香港的方式進行直接 M365 連線。 這些架構也支援[Office 365 多地理](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo)位置的合規性需求，並將該流量保留在下一個辦公室365的 Front 大門附近。 因此，這也是在中國使用 Microsoft 365 的改善。

將 Azure 虛擬 WAN 與網際網路連線搭配使用時，每個連接都可以受益于其他服務，例如[Microsoft Azure 對等互連服務（MAPS）](https://docs.microsoft.com/azure/peering-service/about)。 已建立對應，以將來自協力廠商網際網路服務提供者的流量優化至 Microsoft 全球網路。

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>選項1： SDWAN 或 VPN

本節討論使用 SDWAN 或 VPN 至香港和其他分支的設計。 此選項會顯示在虛擬 WAN 骨幹的兩個網站上使用純網際網路連線時的使用和流量。 在此情況下，會使用專用的網際網路存取或 ICP 提供者 SDWAN 解決方案，將連線帶入香港。 其他分支也會使用純網際網路或 SDWAN 解決方案。

![中國到香港的流量](./media/interconnect-china/china-traffic.png)

在此架構中，每個網站都會使用 VPN 和 Azure 虛擬 WAN 連線至 Microsoft 全球網路。 網站與香港特別行政區之間的流量會透過數目 Microsoft 網路傳輸，而且只會在最後一英里使用一般的網際網路連線。

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>選項2： ExpressRoute 和 SDWAN 或 VPN

本節討論使用香港和其他分支中的 ExpressRoute 搭配 VPN/SDWAN 分支的設計。 此選項會顯示在香港特別行政區終止和 ExpressRoute 的使用，以及透過 SDWAN 或 VPN 連線的其他分支。 在香港的 ExpressRoute 中，目前僅限於簡短的提供者清單，您可以在[Express Route 合作夥伴](../expressroute/expressroute-locations-providers.md#global-commercial-azure)清單中找到。

![中國到香港特別行政區流量 ExpressRoute](./media/interconnect-china/expressroute.png)

也有一些選項可終止來自中國的 ExpressRoute，例如，在韓國或日本。 但是，由於合規性、法規和延遲，香港特別行政區是最佳選擇。

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>選項3：僅限 ExpressRoute

本節討論將 ExpressRoute 用於香港和其他分支的設計。 此選項會顯示在兩端使用 ExpressRoute 的互連。 這裡有不同的流量流程。 Microsoft 365 流量會傳送至 Azure 虛擬 WAN 保護的中樞，並從該處流向 Microsoft Edge 網路和網際網路。

前往已互連分支或從其到中國位置的流量，會遵循該架構中的不同方法。 目前虛擬 WAN 不支援 ExpressRoute 傳輸。 流量會利用 ExpressRoute 全球範圍或協力廠商互連，而不會傳遞虛擬 WAN 中樞。 它會直接從一個 Microsoft 企業邊緣（MSEE）流向另一個。

![ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

目前，每個國家/地區都無法使用 ExpressRoute 全球範圍，但您可以使用 Azure 虛擬 WAN 來設定解決方案。

例如，您可以使用 Microsoft 對等互連來設定 ExpressRoute，並透過該對等互連將 VPN 通道連線至 Azure 虛擬 WAN。 現在您已再次啟用 VPN 和 ExpressRoute 之間的傳輸，而不需要全球觸達和協力廠商提供者和服務，例如 Megaport Cloud。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

* [Azure 虛擬 WAN 的全球傳輸網路架構](virtual-wan-global-transit-network-architecture.md)

* [建立虛擬 WAN 中樞](virtual-wan-site-to-site-portal.md)

* [設定虛擬 WAN 保護的中樞](../firewall-manager/secure-cloud-network.md)

* [Azure 對等服務預覽總覽](https://docs.microsoft.com/azure/peering-service/about)
