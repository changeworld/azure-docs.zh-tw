---
title: Azure 網路服務概欟
description: 瞭解 Azure 中的網路服務，包括連線、應用程式保護、應用程式傳遞，以及網路監視服務。
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: kumud
ms.openlocfilehash: f49a340a004a4aef37bcae9e3ae1c2b02ae030b9
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913073"
---
# <a name="azure-networking-services-overview"></a>Azure 網路服務概欟

Azure 中的網路服務提供各種不同的網路功能，可一起使用或分開使用。 按一下下列任一項重要功能，以深入了解相關資訊︰
- 連線 [**服務**](#connect)：在 azure 中使用這些網路服務的任何或組合來連接 azure 資源和內部部署資源-虛擬網路 (VNet) 、虛擬 WAN、EXPRESSROUTE、VPN 閘道、虛擬網路 NAT 閘道、Azure DNS、對等互連服務和 Azure 防禦。
- [**應用程式保護服務**](#protect)：在 Azure 中使用這些網路服務的任何或組合來保護您的應用程式-Private Link、DDoS 保護、防火牆、網路安全性群組、Web 應用程式防火牆和虛擬網路端點。
- [**應用程式傳遞服務**](#deliver)：在 azure 網路中使用任何或結合這些網路服務的方式，在 azure 中提供應用程式-內容傳遞網路 (CDN) 、Azure Front Door Service、流量管理員、應用程式閘道、Internet Analyzer 和 Load Balancer。
- [**網路監視**](#monitor)：在 Azure 中使用這些網路服務的任何或組合來監視您的網路資源-網路監看員、ExpressRoute 監視、Azure 監視器或 VNet 終端機存取點 (點擊) 。

## <a name="connectivity-services"></a><a name="connect"></a>連線服務
 
本節說明的服務可提供 Azure 資源之間的連線能力、從內部部署網路到 Azure 資源的連線，以及 Azure 虛擬網路中的分支對分支連線能力 (VNet) 、ExpressRoute、VPN 閘道、虛擬 WAN、虛擬網路 NAT 閘道、Azure DNS、Azure 對等互連服務和 Azure 防禦。


### <a name="virtual-network"></a><a name="vnet"></a>虛擬網路

Azure 虛擬網路 (VNet) 是私人網路在 Azure 中的基本建置組塊。 您可以使用 Vnet 來：
- 在 **Azure 資源之間進行通訊** ：您可以將 vm 和數種其他類型的 azure 資源部署到虛擬網路，例如 Azure App Service 環境、AZURE KUBERNETES SERVICE (AKS) 和 Azure 虛擬機器擴展集。 若要檢視可部署到虛擬網路中的 Azure 資源的完整清單，請參閱[虛擬網路服務整合](../virtual-network/virtual-network-for-azure-services.md)。
- 彼此 **通訊** ：您可以使用虛擬網路對等互連，將虛擬網路彼此連線，讓任一虛擬網路中的資源彼此通訊。 您所連線的虛擬網路可位於相同或不同的 Azure 區域。 如需詳細資訊，請參閱[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)。
- 與 **網際網路通訊** ： VNet 中的所有資源預設都可以輸出至網際網路。 您可以藉由指派公用 IP 位址或公用負載平衡器，對該項資源進行輸入通訊。 您也可以使用 [公用 IP 位址](../virtual-network/virtual-network-public-ip-address.md) 或公用 [Load Balancer](../load-balancer/load-balancer-overview.md) 來管理您的輸出連接。
- **與內部部署網路通訊** ：您可以使用 [VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute](../expressroute/expressroute-introduction.md)，將您的內部部署電腦和網路連線到虛擬網路。

如需詳細資訊，請參閱 [什麼是 Azure 虛擬網路？](../virtual-network/virtual-networks-overview.md)。

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
ExpressRoute 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。 此連線是私人連線。 流量不會經由網際網路傳送。 使用 ExpressRoute，即可和 Microsoft 雲端服務建立連線，例如 Microsoft Azure、Microsoft 365 和 Dynamics 365。  如需詳細資訊，請參閱 [什麼是 ExpressRoute？](../expressroute/expressroute-introduction.md)。

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN 閘道
VPN 閘道可協助您建立從內部部署位置到虛擬網路的加密跨單位連線，或在 Vnet 之間建立加密連線。 VPN 閘道連線有不同的設定可供使用，例如站對站、點對站或 VNet 對 VNet。
下圖說明相同虛擬網路的多個站對站 VPN 連線。

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="Azure ExpressRoute":::

如需不同 VPN 連線類型的詳細資訊，請參閱 [Vpn 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtual WAN
Azure 虛擬 WAN 是一種網路服務，可將最佳且自動化的分支連線提供給 Azure，或透過 Azure 提供最佳且自動化的分支連線。 Azure 區域作為中樞，您可以選擇將您的分支連線到該中樞。 您也可以利用 Azure 骨幹來連接分支，並享受分支對 VNet 的連線能力。 Azure 虛擬 WAN 將許多 Azure 雲端聯機服務（例如站對站 VPN、ExpressRoute、點對站使用者 VPN）彙集在單一操作介面中。 使用虛擬網路連線可建立與 Azure Vnet 的連線。 如需詳細資訊，請參閱 [什麼是 Azure 虛擬 WAN？](../virtual-wan/virtual-wan-about.md)。

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Azure ExpressRoute":::

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構來提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。 如需詳細資訊，請參閱 [什麼是 Azure DNS？](../dns/dns-overview.md)。

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure Bastion 服務是您可在虛擬網路內佈建的新完全平台受控 PaaS 服務。 直接在 Azure 入口網站中，透過 TLS 提供與虛擬機器之間安全且順暢的 RDP/SSH 連線。 透過 Azure Bastion 連線時，虛擬機器不需要公用 IP 位址。 如需詳細資訊，請參閱 [什麼是 Azure 防禦？](../bastion/bastion-overview.md)。

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Azure ExpressRoute":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>虛擬網路 NAT 閘道
虛擬網路 NAT (網路位址轉譯) 可簡化虛擬網路的僅限輸出網際網路連線。 在子網路上設定時，所有輸出連線都會使用您指定的靜態公用 IP 位址。 在沒有負載平衡器或直接連結至虛擬機器的公用 IP 位址的情況下，可能有輸出連線能力。 如需詳細資訊，請參閱 [何謂虛擬網路 NAT 閘道？](../virtual-network/nat-overview.md)。

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="Azure ExpressRoute":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Azure 對等互連服務
Azure 對等互連服務可增強客戶與 Microsoft 雲端服務的連線，例如 Microsoft 365、Dynamics 365、軟體即服務 (SaaS) services、Azure，或任何可透過公用網際網路存取的 Microsoft 服務。 如需詳細資訊，請參閱 [什麼是 Azure 對等互連服務？](../peering-service/about.md)。

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zones

Azure Edge 區域是 Microsoft Azure 的一系列產品，可讓使用者接近使用者的資料處理。 您可以將 Vm、容器和其他選取的 Azure 服務部署到邊緣區域，以解決應用程式的低延遲和高輸送量需求。 如需詳細資訊，請參閱 [什麼是 Azure Edge 區域？](edge-zones-overview.md)。

### <a name="azure-orbital"></a><a name="orbital"></a>Azure Orbital

Azure Orbital 是完全受控的雲端式地面站即服務，可讓您與太空船或衛星星座 (satellite constellation) 通訊、進行下行和上行的資料傳輸、在雲端中處理您的資料、在獨特的案例中將服務與 Azure 服務鏈結，以及為您的客戶產生產品。 此系統的建置基礎是 Azure 全球基礎結構和低延遲的全球光纖網路。 如需詳細資訊，請參閱 [什麼是 Azure Orbital？](azure-orbital-overview.md)。

:::image type="content" source="./media/azure-orbital-overview/orbital-communications-use-flow.png" alt-text="Azure ExpressRoute":::

## <a name="application-protection-services"></a><a name="protect"></a>應用程式保護服務

本節說明 Azure 中的網路服務，可協助保護您的網路資源-在 Azure 中使用這些網路服務的任何或組合來保護您的應用程式-DDoS 保護、Private Link、防火牆、Web 應用程式防火牆、網路安全性群組，以及虛擬網路服務端點。

### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS 保護 
[Azure DDoS 保護](../virtual-network/manage-ddos-protection.md) 針對最複雜的 DDoS 威脅提供對策。 此服務為您的應用程式和部署在虛擬網路中的資源，提供增強的 DDoS 風險降低功能。 此外，使用 Azure DDoS 保護的客戶可以存取 DDoS 快速回應支援，以在主動攻擊期間吸引 DDoS 專家。

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="Azure ExpressRoute":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[Azure Private Link](../private-link/private-link-overview.md) 可讓您存取 Azure PaaS 服務 (例如 Azure 儲存體和 SQL Database) ，以及 azure 裝載客戶擁有/合作夥伴服務的虛擬網路中的私人端點。
您的虛擬網路與服務之間的流量會經由 Microsoft 骨幹網路傳輸。 您的服務不再需要向公用網際網路公開。 您可以在虛擬網路中建立自己的 Private Link 服務，並提供給客戶。

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="Azure ExpressRoute":::

### <a name="azure-firewall"></a><a name="firewall"></a>Azure 防火牆
Azure 防火牆是受控、雲端式網路安全性服務，可以保護您的 Azure 虛擬網路資源。 使用 Azure 防火牆，您可以跨訂用帳戶和虛擬網路集中建立、強制執行及記錄應用程式和網路連線原則。 Azure 防火牆會針對您的虛擬網路資源使用靜態公用 IP 位址，允許外部防火牆識別源自您虛擬網路的流量。 

如需 Azure 防火牆的詳細資訊，請參閱 [Azure 防火牆檔](../firewall/overview.md)。

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="Azure ExpressRoute":::

### <a name="web-application-firewall"></a><a name="waf"></a>Web 應用程式防火牆
[Azure Web 應用程式防火牆](../web-application-firewall/overview.md) (WAF) 可保護您的 web 應用程式免于常見的 web 惡意探索和弱點，例如 SQL 插入式攻擊和跨網站腳本。 Azure WAF 提供現成的保護，可透過受控規則 OWASP 前10大弱點。 此外，客戶也可以設定自訂規則，也就是客戶管理的規則，以根據來源 IP 範圍提供額外的保護，並要求屬性，例如標頭、cookie、表單資料欄位或查詢字串參數。

客戶可以選擇部署 [AZURE WAF 與應用程式閘道](../application-gateway/waf-overview.md) ，以針對公用和私人位址空間中的實體提供區域保護。 客戶也可以選擇 [使用 Front Door 部署 AZURE WAF](../frontdoor/waf-overview.md) ，以提供網路邊緣的保護給公用端點。

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="Azure ExpressRoute":::

### <a name="network-security-groups"></a><a name="nsg"></a>網路安全性群組
您可以透過網路安全性群組，篩選在 Azure 虛擬網路中進出 Azure 資源的網路流量。 如需詳細資訊，請參閱[網路安全性群組](../virtual-network/network-security-groups-overview.md)。

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>服務端點
虛擬網路 (VNet) 服務端點可透過直接連線，將您的虛擬網路私人位址空間和 VNet 的身分識別延伸至 Azure 服務。 端點可讓您將重要的 Azure 服務資源只放到您的虛擬網路保護。 從您的 VNet 到 Azure 服務的流量一定會保留在 Microsoft Azure 骨幹網路上。 如需詳細資訊，請參閱[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="Azure ExpressRoute":::

## <a name="application-delivery-services"></a><a name="deliver"></a>應用程式傳遞服務

本節說明 Azure 中的網路服務，可協助提供應用程式-內容傳遞網路、Azure Front Door 服務、流量管理員、Load Balancer 和應用程式閘道。

### <a name="content-delivery-network"></a><a name="cdn"></a>內容傳遞網路
Azure 內容傳遞網路 (CDN) 為開發人員提供一套全域解決方案，讓他們可藉由在策略性分布於全球的實體節點上快取內容，將高頻寬內容迅速傳遞給使用者。 如需 Azure CDN 的詳細資訊，請參閱 [Azure 內容傳遞網路](../cdn/cdn-overview.md)。

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="Azure ExpressRoute":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front Door Service
Azure Front Door Service 可讓您針對最佳效能和立即全域容錯移轉以獲得高可用性最佳化，定義、管理及監視網路流量的全域路由。 透過 Front Door，您可以將全球 (多區域) 消費者和企業應用程式轉換成健全、高效能的個人化新式應用程式、API，以及觸及 Azure 全球對象的內容。 如需詳細資訊，請參閱 [Azure Front Door](../frontdoor/front-door-overview.md)。

:::image type="content" source="./media/networking-overview/front-door-visual-diagram.png" alt-text="Azure ExpressRoute":::

### <a name="traffic-manager"></a><a name="trafficmanager"></a>流量管理員

Azure 流量管理員是 DNS 型流量負載平衡器，可讓您跨全球的 Azure 區域將流量最佳分散至服務，同時提供高可用性和回應性。 流量管理員會提供一系列流量路由方法來分配流量，例如優先順序、加權、效能、地理、多重值或子網。 如需流量路由方法的詳細資訊，請參閱 [流量管理員路由方法](../traffic-manager/traffic-manager-routing-methods.md)。

下圖顯示以流量管理員為基礎的端點優先順序型路由：

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Azure ExpressRoute":::

如需流量管理員的詳細資訊，請參閱 [什麼是 Azure 流量管理員？](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>負載平衡器
Azure Load Balancer 針對所有 UDP 和 TCP 通訊協定提供高效能、低延遲的第 4 層負載平衡。 它會管理輸入及輸出連線。 您可以設定公用和內部負載平衡端點。 您可以使用 TCP 和 HTTP 健全狀況探查選項定義規則，將輸入連線對應至後端集區目的地，以管理服務可用性。 若要深入了解負載平衡器，請閱讀[負載平衡器概觀](../load-balancer/load-balancer-overview.md)一文。

下圖顯示同時使用外部和內部負載平衡器的網際網路對向多層應用程式︰

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Azure ExpressRoute":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>應用程式閘道
Azure 應用程式閘道是網路流量負載平衡器，可讓您管理 Web 應用程式的流量。 它是應用程式傳遞控制器 (ADC) 即服務，為您的應用程式提供各種第7層負載平衡功能。 如需詳細資訊，請參閱 [什麼是 Azure 應用程式閘道？](../application-gateway/overview.md)。

下圖顯示以 url 路徑為基礎的應用程式閘道路由。

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Azure ExpressRoute":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>網路監視服務
本節說明 Azure 中的網路服務，可協助監視您的網路資源-網路監看員、網路 Azure 監視器、ExpressRoute 監視器、Azure 監視器和虛擬網路。

### <a name="network-watcher"></a><a name="networkwatcher"></a>網路監看員
Azure 網路監看員提供了相關工具，可對 Azure 虛擬網路中的資源進行監視、診斷、檢視計量，以及啟用或停用記錄。 如需詳細資訊，請參閱 [什麼是網路](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)監看員？。

### <a name="azure-monitor-for-networks-preview"></a>適用于網路預覽的 Azure 監視器
適用于網路的 Azure 監視器可針對所有已部署的網路資源全面查看健康情況和計量，而不需要任何設定。 它也可讓您存取網路監視功能，例如連線 [監視器](../network-watcher/connection-monitor-preview.md)、 [網路安全性群組的流量記錄](../network-watcher/network-watcher-nsg-flow-logging-overview.md)，以及使用 [分析](../network-watcher/traffic-analytics.md)。 如需詳細資訊，請參閱 [Azure 監視器網路預覽](../azure-monitor/insights/network-insights-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)。

### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute 監視
若要瞭解如何查看 ExpressRoute 線路計量、資源記錄和警示，請參閱 [expressroute 監視、計量和警示](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)。
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure 監視器
Azure 監視器可藉由提供全方位的解決方案，以便收集、分析及處理來自雲端和內部部署環境的遙測資料，進而將應用程式的可用性和效能最大化。 它可協助您了解您的應用程式表現如何，並主動識別影響它們的問題以及它們所依賴的資源。 如需詳細資訊，請參閱 [Azure 監視器總覽](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)。
### <a name="virtual-network-tap"></a><a name="vnettap"></a>虛擬網路點擊
Azure 虛擬網路 TAP (終端機存取點) 可讓您持續將虛擬機器網路流量串流到網路封包收集器或分析工具。 收集器或分析工具是由 [網路虛擬裝置](https://azure.microsoft.com/solutions/network-appliances/) 合作夥伴提供。

下圖顯示虛擬網路分流的運作方式：

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="Azure ExpressRoute":::

如需詳細資訊，請參閱 [什麼是虛擬網路的點擊](../virtual-network/virtual-network-tap-overview.md)。

## <a name="next-steps"></a>下一步

- 完成「 [建立您的第一個虛擬網路](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) 」一文中的步驟，建立您的第一個虛擬網路，並將幾個 vm 連接到該網路。
- 完成 [設定點對站連線文章](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)中的步驟，以將您的電腦連線到虛擬網路。
- 完成[建立網際網路對向負載平衡器](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)一文中的步驟，將網際網路流量負載平衡到公用伺服器。
