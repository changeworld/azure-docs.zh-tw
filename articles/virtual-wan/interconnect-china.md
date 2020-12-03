---
title: 使用 Azure 虛擬 WAN 和安全中樞與中國互相連線
description: 瞭解如何使用 Azure 虛擬 WAN 和安全中樞與中國互連。
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: sukishen
ms.openlocfilehash: 169477ffdf6fd593f1b6f01469c7303f8bd3a488
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531832"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>使用 Azure 虛擬 WAN 和安全中樞與中國互相連線

當您查看一般汽車、製造、物流產業或其他國家標準暨（例如 embassies）時，通常會有關于如何改進與中國互連的問題。 這些改進大部分都與使用雲端服務（例如 Microsoft 365、Azure 全域服務或中國內的互連分支）搭配客戶骨幹。

在大部分的情況下，客戶會面臨延遲高、頻寬低、連線不穩定，以及連線到中國之外 (例如歐洲或北美洲) 成本高等問題。

這些問題的原因來自「防火長城」(Great Firewall of China)，此防火牆會保護網際網路屬於中國的部分，並篩選進入中國的流量。 幾乎所有從中華人民共和國到中國境內執行的流量（像是香港特別行政區和澳門的特殊管理區域）都會傳遞絕佳的防火牆。 經過香港和澳門的流量不會完全到達防火長城，而是由防火長城的子集來處理的。

:::image type="content" source="./media/interconnect-china/provider.png" alt-text="圖表會顯示提供者互連。":::

客戶可以使用虛擬 WAN 建立更高效能且穩定的連線，來連線到 Microsoft 雲端服務及客戶企業網路，而不會違反中國的網路安全法。

## <a name="requirements-and-workflow"></a><a name="requirements"></a>需求與工作流程

如果您想要能持續符合中國網路安全法的規定，則必須符合一組特定條件。

首先，您必須與擁有中國 ICP (網際網路內容供應商) 授權的網路和 ISP 合作。 在大部分的情況下，您會與下列其中一個供應商合作：

* 中國電信國際有限公司
* 中國移動
* 中國聯通
* PCCW Global Ltd.
* 香港電訊

視供應商和您的需求而定，現在您必須購買下列其中一種網路連線服務，才能與您在中國的分公司互相連線。

* MPLS/IPVPN 網路 
* Software Defined WAN (SDWAN)
* 專用的網際網路存取

接下來，您必須同意供應商提供進入在香港 Microsoft 全球網路及其邊緣網路的突破點，而不是在北京或上海。 在此情況下，香港特別行政區非常重要，因為它的地理置與中國接壤。

雖然大部分的客戶都認為透過新加坡來互相連線是最佳的情況，因為新加坡在地圖上看起來與中國很近，但實則非然。 當您跟著網路光纖地圖瀏覽時，會看見幾乎所有的網路都會通過北京、上海和香港連線。 這讓香港在地理位置上成為與中國互相連線的更佳選擇。

視供應商而定，您可能會得到不同的服務供應項目。 下表顯示供應商及其所提供服務的範例，以本文撰寫時的資訊為準。

| 服務 | 供應商範例 |
| --- | --- |
| MPLS/IPVPN 網路 |PCCW、中國電信國際有限公司 |
|SDWAN| PCCW、中國電信國際有限公司|
| 專用的網際網路存取 | PCCW、香港電訊、中國移動|

透過您的供應商，您便可選擇使用下列兩種解決方案來連線 Microsoft 全球骨幹：

* 使用終止於香港的 Microsoft Azure ExpressRoute。 這是目前使用 MPLS/IPVPN 的情況。 目前香港唯一提供 ExpressRoute 的 ICP 授權供應商是中國電信國際有限公司。 不過，如果客戶與 Megaport 或 InterCloud 等雲端交換供應商合作，也可以與其他供應商討論。 如需詳細資訊，請參閱 [ExpressRoute Global Reach](../expressroute/expressroute-locations-providers.md#partners)。

* 直接在以下其中一個網際網路交換點使用「專用的網際網路存取」，或使用私人網路互相連線。

下列清單顯示香港的網際網路交換中心：

* 香港 AMS-IX
* 香港 BBIX
* 香港 Equinix
* HKIX

使用此連線時，您對 Microsoft 服務的下一個 BGP 躍點必須是 Microsoft 自發系統編號 (AS#) 8075。 如果您使用單一位置或 SDWAN 解決方案，則這會是連線的選擇。

關於中國與香港特別行政區之間的互連目前的變更，這些網路提供者大多會在中國和香港特別行政區之間建立 MPLS 橋樑。

您可以看到，中國內的站對站 VPN 連線是允許的，而且大多是穩定的。 這同樣適用于世界各地其他分支之間的站對站連線。 提供者現在會在兩端建立 VPN/SDWAN 匯總，並透過 MPLS 在兩者之間進行橋接。

:::image type="content" source="./media/interconnect-china/china-mpls-bridge.png" alt-text="顯示中國 MPLS 橋接器的圖表。":::

無論何種方式，我們仍然建議您將第二個一般網際網路分類為中國。 這是為了將企業流量與雲端服務（例如 Microsoft 365 和 Azure）之間的流量分割，以及法律規範的網際網路流量。

以下為符合中國網路架構的範例：

:::image type="content" source="./media/interconnect-china/multi-branch.png" alt-text="圖表會顯示多個分支。":::

在此範例中，為了與香港的 Microsoft 全球網路互相連線，現在您可以開始使用 [Azure 虛擬 WAN 全球傳輸架構](virtual-wan-global-transit-network-architecture.md)及如 Azure 安全虛擬 WAN 中樞等其他服務，以取用服務並與在中國境外的分公司和資料中心互相連線。

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>中樞對中樞通訊

在本節中，我們會使用虛擬 WAN 中樞對中樞通訊互相連線。 在此案例中，您會建立新的虛擬 WAN 中樞資源，以連線在香港的虛擬 WAN 中樞、其他您偏好的區域、您已經有 Azure 資源的區域，或想要連線的位置。

範例架構如下列範例所示：

:::image type="content" source="./media/interconnect-china/sample.png" alt-text="圖顯示範例 WAN。":::

在此範例中，中國分公司會使用 VPN 或 MPLS 連線與中國 Azure 雲端及彼此連線。 需要連線到全球服務的分公司會使用直接連線到香港的 MPLS 或以網際網路為基礎的服務。 如果您想要在香港及另一個區域使用 ExpressRoute，您必須設定 [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) 使這兩個 ExpressRoute 線路互相連線。

ExpressRoute Global Reach 在某些區域無法使用。 例如，如果您需要與巴西或印度互相連線，您必須利用[雲端交換供應商](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers)來提供路由服務。

下圖針對此狀況顯示這兩種範例。

:::image type="content" source="./media/interconnect-china/global.png" alt-text="顯示全球接觸的圖表。":::

## <a name="secure-internet-breakout-for-microsoft-365"></a><a name="secure"></a>Microsoft 365 的網際網路分組安全

另一個考慮是網路安全性，與記錄中國與虛擬 WAN 所建立骨幹元件之間的進入點及客戶骨幹。 在大部分情況下，需要有對香港網際網路的突破點，以直接觸及 Microsoft Edge 網路，並藉此觸及用於 Microsoft 365 服務的 Azure 前端伺服器。

針對虛擬 WAN 的這兩種使用案例，您可以利用 [Azure 虛擬 WAN 保護的中樞](../firewall-manager/secured-virtual-hub.md)。 您可以使用 Azure 防火牆管理員，將一般的虛擬 WAN 中樞變更為受保護中樞，然後在該中樞內部署和管理 Azure 防火牆。

下圖顯示此情況的範例：

:::image type="content" source="./media/interconnect-china/internet.png" alt-text="圖顯示網路和 Microsoft 服務流量的網際網路分組。":::

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>架構與流量流程

根據您對於香港選擇的連線而定，整體架構可能會稍微改變。 本節顯示三種適用於 VPN 或 SDWAN 和/或 ExpressRoute 不同組合的可用架構。

所有這些選項都會使用 Azure 虛擬 WAN 安全中樞，在香港中進行直接 Microsoft 365 連線。 這些架構也支援 [Microsoft 365 多地理](/microsoft-365/enterprise/microsoft-365-multi-geo) 位置的合規性需求，並讓該流量保持在下一個 Azure Front Door 位置附近。 因此，這也能夠改善中國境外的 Microsoft 365 使用狀況。

將 Azure 虛擬 WAN 與網際網路連線搭配使用時，每個連線都可以受益於其他服務，例如 [Microsoft Azure 對等互連服務 (MAPS)](../peering-service/about.md)。 建立 MAPS 的目的在於將協力網際網路服務供應廠商至 Microsoft 全球網路的流量最佳化。

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>選項 1：SDWAN 或 VPN

本節討論使用 SDWAN 或 VPN 至香港和其他分公司的設計。 此選項會顯示當在虛擬 WAN 骨幹的兩個網站上使用純網際網路連線時的使用狀況和流量流程。 在此情況下，會使用專用的網際網路存取或 ICP 供應商 SDWAN 解決方案，將連線帶入香港。 其他分公司也會使用純網際網路或 SDWAN 解決方案。

:::image type="content" source="./media/interconnect-china/china-traffic.png" alt-text="圖顯示中國至香港的流量。":::

在此架構中，每個網站都會使用 VPN 和 Azure 虛擬 WAN 與 Microsoft 全球網路連線。 網站與香港之間的流量會透過 Microsoft 網路傳輸，且只會在最後一英里使用一般的網際網路連線。

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>選項 2：ExpressRoute 和 SDWAN 或 VPN

本節討論在香港及其他採用 VPN/SDWAN 的分公司使用 ExpressRoute 的設計。 此選項顯示使用中止於香港的 ExpressRoute 及其他透過 SDWAN 或 VPN 連線的分公司。 在香港的 ExpressRoute 目前僅限於 [Express Route 合作夥伴](../expressroute/expressroute-locations-providers.md#global-commercial-azure)清單中所提供一份簡短供應商清單中的供應商。

:::image type="content" source="./media/interconnect-china/expressroute.png" alt-text="圖顯示中國至香港的流量-ExpressRoute。":::

也有一些選項可終止來自中國的 ExpressRoute，例如，在韓國或日本。 但是基於合規性、法規和延遲性的考量，目前香港是最佳選擇。

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>選項 3：僅 ExpressRoute

本節討論在香港及其他分公司使用 ExpressRoute 的設計。 此選項會顯示在兩端使用 ExpressRoute 互相連線。 在此您的流量流程與他人不同。 Microsoft 365 流量會流至 Azure 虛擬 WAN 保護的中樞，並從該處流向 Microsoft Edge 網路和網際網路。

前往已互相連線分公司的流量，或從分公司流到中國某些地點的流量，會遵循該架構中的不同方法。 目前虛擬 WAN 不支援 ExpressRoute 對 ExpressRoute 傳輸。 流量會利用 ExpressRoute Global Reach 或協力廠商互相連線，而不會通過虛擬 WAN 中樞。 它會從某個 Microsoft Enterprise Edge (MSEE) 直接流向另一個。

:::image type="content" source="./media/interconnect-china/expressroute-virtual.png" alt-text="圖表顯示 ExpressRoute 的全球觸達範圍。":::

目前，ExpressRoute Global Reach 並未適用於每個國家/地區，但您可以使用 Azure 虛擬 WAN 來設定解決方案。

例如，您可以使用 Microsoft 對等互連來設定 ExpressRoute，並透過該對等互連將 VPN 通道連線至 Azure 虛擬 WAN。 現在您已再次啟用 VPN 和 ExpressRoute 之間的傳輸，且不需要 Global Reach 和協力廠商供應商與服務，例如 Megaport Cloud。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

* [使用 Azure 虛擬 WAN 的全球傳輸網路架構](virtual-wan-global-transit-network-architecture.md)

* [建立虛擬 WAN 中樞](virtual-wan-site-to-site-portal.md)

* [設定虛擬 WAN 保護的中樞](../firewall-manager/secure-cloud-network.md)

* [Azure 對等互連服務預覽概觀](../peering-service/about.md)