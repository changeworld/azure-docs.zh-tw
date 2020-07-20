---
title: 關於 Azure VPN 閘道設計
description: 瞭解您可以設計 VPN 閘道拓朴以連線到 Azure 虛擬網路的方式。
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/01/2020
ms.author: cherylmc
ms.openlocfilehash: d7b9077af50115e912415d784dc98ace081c0c88
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84302310"
---
# <a name="vpn-gateway-design"></a>VPN 閘道設計

請務必知道 VPN 閘道連線有不同的組態可用。 您必須決定哪個組態最符合您的需求。 在下列各節中，您可以查看有關下列 VPN 閘道連線的設計資訊和拓撲圖表。 使用圖形和描述來協助選取符合您需求的連線拓撲。 這些圖表顯示主要基準拓撲，但您可以使用圖表做為指導方針來建立更複雜的設定。

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>站對站和多網站（IPsec/IKE VPN 通道）

### <a name="site-to-site"></a><a name="S2S"></a>站對站

網站間 (S2S) VPN 閘道連線是透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道建立的連線。 S2S 連線可以用於跨單位與混合式組態。 S2S 連線需要位於內部部署的 VPN 裝置，其具有指派的公用 IP 位址。 如需選取 VPN 裝置的資訊，請參閱 [VPN 閘道常見問題集 - VPN 裝置](vpn-gateway-vpn-faq.md#s2s)。

![Azure VPN 閘道站對站連接範例](./media/design/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a><a name="Multi"></a>多網站

這類型的連線是站對站連線的變化。 您可以從虛擬網路閘道建立多個 VPN 連線，通常會連接至多個內部部署網站。 處理多重連線時，您必須使用路由式 VPN 類型 (也就是使用傳統 VNet 時的動態閘道)。 因為每個虛擬網路只能有一個 VPN 閘道，所以透過該閘道的所有連線會共用可用的頻寬。 這種組態通常稱為「多站台」連線。

![Azure VPN 閘道多網站連接範例](./media/design/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>站對站和多網站的部署模型和方法

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>點對站 VPN

點對站 (P2S) VPN 閘道連線可讓您建立從個別用戶端電腦到您的虛擬網路的安全連線。 P2S 連線的建立方式是從用戶端電腦開始。 此解決方案適合於想要從遠端位置 (例如從住家或會議) 連線到 Azure VNet 的遠距工作者。 當您只有少數用戶端必須連線至 VNet 時，P2S VPN 也是很實用的解決方案 (而不是 S2S VPN)。

如同 S2S 連線，P2S 連線不需要內部部署公眾對應 IP 位址或 VPN 裝置。 P2S 連線可與 S2S 連線透過相同的 VPN 閘道一起使用，前提是這兩個連線的所有設定需求都相容。 如需點對站連線的詳細資訊，請參閱[關於點對站 VPN](point-to-site-about.md)。

![Azure VPN 閘道點對站連接範例](./media/design/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>P2S 的部署模型和方法

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>VNet 對 VNet 連線 (IPsec/IKE VPN 通道)

將虛擬網路連接至另一個虛擬網路 (VNet 對 VNet)，類似於將 VNet 連接至內部部署網站位置。 這兩種連線類型都使用 VPN 閘道提供使用 IPsec/IKE 的安全通道。 您甚至可以將多網站連線組態與 VNet 對 VNet 通訊結合。 這可讓您建立結合了跨單位連線與內部虛擬網路連線的網路拓撲。

您所連接的 VNet 可以：

* 在相同或不同的區域中
* 在相同或不同的訂用帳戶中 
* 在相同或不同的部署模型中

![Azure VPN 閘道 VNet 對 VNet 連接範例](./media/design/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>部署模型之間的連線

Azure 目前有兩種部署模型：傳統和 Resource Manager。 如果您已使用 Azure 一段時間，則可能具有傳統 VNet 上執行的 Azure VM 和執行個體角色。 較新的 VM 和角色執行個體可能會在 Resource Manager 中建立的 VNet 中執行。 您可以建立 Vnet 間的連線，讓其中一個 VNet 中的資源直接與另一個 VNet 中的資源通訊。

### <a name="vnet-peering"></a>VNet 對等互連

只要您的虛擬網路符合特定需求，您就能夠使用 VNet 對等互連來建立連線。 VNet 對等互連不會使用虛擬網路閘道。 如需詳細資訊，請參閱 [VNet 對等互連](../virtual-network/virtual-network-peering-overview.md)。

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>VNet 對 VNet 的部署模型和方法

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute (私人連線)

ExpressRoute 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。 透過 ExpressRoute，您可以建立 Microsoft 雲端服務的連線，例如 Microsoft Azure、Office 365 和 CRM Online。 連線能力可以來自任意對任意（IP VPN）網路、點對點乙太網路，或是透過連線提供者在共置設備上進行的虛擬交叉連線。

ExpressRoute 連線不會經過公用網際網路。 相較於一般網際網路連線，這可讓 ExpressRoute 連線提供更可靠、更快速、延遲更短和更安全的連線。

ExpressRoute 連線會以虛擬網路閘道作為其必要組態的一部分。 在 ExpressRoute 連線中，虛擬網路閘道的閘道類型已設定為 'ExpressRoute' 而不是 'Vpn'。 雖然透過 ExpressRoute 線路傳輸的流量預設並未加密，但有可能建立一個解決方案，讓您透過 ExpressRoute 線路傳送已加密的流量。 如需 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)。

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>站對站及 ExpressRoute 並存連線

ExpressRoute 是從 WAN (不透過公用網際網路) 到 Microsoft 服務 (包括 Azure) 的私人連線。 站對站 VPN 流量會以加密方式透過公用網際網路進行傳輸。 能夠對相同的虛擬網路設定網站間 VPN 和 ExpressRoute 連線有諸多好處。

您可以將網站間 VPN 設定為 ExpressRoute 的安全容錯移轉路徑，或使用網站間 VPN 來連線至不在您網路中但透過 ExpressRoute 連接的網站。 請注意，對於相同的虛擬網路，此組態需要兩個虛擬網路閘道，一個使用 'Vpn' 閘道類型，而另一個使用 'ExpressRoute' 閘道類型。

![ExpressRoute 和 VPN 閘道並存連接範例](./media/design/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>S2S 和 ExpressRoute 的部署模型和方法並存

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="highly-available-connections"></a><a name="highly-available"></a>高可用性連接

如需高可用性連線的規劃和設計，請參閱[高可用性連接](vpn-gateway-highlyavailable.md)。

## <a name="next-steps"></a>後續步驟

* 如需詳細資訊，請參閱 [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md)。

* 深入瞭解[VPN 閘道的設定](vpn-gateway-about-vpn-gateway-settings.md)。

* 如需 VPN 閘道 BGP 考慮，請參閱[關於 bgp](vpn-gateway-bgp-overview.md)。

* 檢視[訂用帳戶與服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)。

* 了解 Azure 的一些其他重要[網路功能](../networking/networking-overview.md)。
