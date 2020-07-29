---
title: Azure 網路服務總覽
description: 瞭解 Azure 中的網路服務，包括聯機服務、應用程式保護服務、應用程式傳遞服務，以及網路監視。
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 5b32f6b368af62bad13a505472a9ddd4709c032c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282155"
---
# <a name="azure-networking-services-overview"></a>Azure 網路服務總覽

Azure 中的網路服務提供各種不同的網路功能，可一起或分開使用。 按一下下列任一項重要功能，以深入了解相關資訊︰
- 連線[**服務**](#connect)：使用 azure 虛擬網路（VNet）、虛擬 WAN、ExpressRoute、VPN 閘道、虛擬網路 NAT 閘道、Azure DNS、對等互連服務和 Azure 防禦中的任何或結合這些網路服務，來連接 Azure 資源和內部部署資源。
- [**應用程式保護服務**](#protect)：在 Azure 中使用這些網路服務的任何或組合來保護您的應用程式-私人連結、DDoS 保護、防火牆、網路安全性群組、Web 應用程式防火牆，以及虛擬網路端點。
- [**應用程式傳遞服務**](#deliver)：在 azure 中使用這些網路服務的任何或組合，在 azure 中傳遞應用程式（內容傳遞網路（CDN）、Azure Front 門服務、流量管理員、應用程式閘道、Internet Analyzer 和 Load Balancer。
- [**網路監視**](#monitor)：在 Azure 中使用這些網路服務的任何或組合來監視網路資源-網路監看員、ExpressRoute 監視器、Azure 監視器或 VNet 終端機存取點（點一下）。

## <a name="connectivity-services"></a><a name="connect"></a>連線服務
 
本節說明可提供 Azure 資源之間連線的服務、從內部部署網路到 Azure 資源的連線，以及分支到 Azure 中的分支連線能力（虛擬網路（VNet）、虛擬 WAN、ExpressRoute、VPN 閘道、虛擬網路 NAT 閘道、Azure DNS、Azure 對等服務和 Azure 防禦。

|服務|為何要使用？|案例|
|---|---|---|
|[虛擬網路](#vnet)|可讓 Azure 資源安全地與彼此、網際網路和內部部署網路通訊。| <p>[篩選網路流量](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[路由網路流量](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[限制對資源的網路存取](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[連線虛擬網路](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|透過連線提供者所提供的私人連線，將您的內部部署網路延伸至 Microsoft 雲端。|<p>[建立和修改 ExpressRoute 線路](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[建立和修改 ExpressRoute 線路的對等互連](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[將 VNet 連結到 ExpressRoute 線路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[設定和管理 ExpressRoute 線路的路由篩選](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN 閘道](#vpngateway)|透過公用網際網路在 Azure 虛擬網路與內部部署位置之間傳送加密的流量。|<p>[站對站連線](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[VNet 對 VNet 連線](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[點對站連線](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[虛擬 WAN](#virtualwan)|將分支連線優化並自動執行至 Azure。 Azure 區域作為中樞，您可以選擇將您的分支連線到該中樞。|<p>[站對站](../virtual-wan/virtual-wan-site-to-site-portal.md)連線、 [ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)連線</p>|
|[Azure DNS](#dns)|裝載使用 Microsoft Azure 基礎結構來提供名稱解析的 DNS 網域。|<p>[在 Azure DNS 上裝載您的網域](../dns/dns-delegate-domain-azure-dns.md)</p><p>[建立 web 應用程式的 DNS 記錄](../dns/dns-web-sites-custom-domain.md)</p> <p>[建立流量管理員的別名記錄](../dns/tutorial-alias-tm.md)</p> <p>[建立公用 IP 位址的別名記錄](../dns/tutorial-alias-pip.md)</p> <p>[建立區域資源記錄的別名記錄](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|透過 TLS 直接在 Azure 入口網站中，為您的虛擬機器設定安全且順暢的 RDP/SSH 連線能力。 當您透過 Azure 防禦進行連接時，您的虛擬機器不需要公用 IP 位址|<p>[建立 Azure Bastion 主機](../bastion/bastion-create-host-portal.md)</p><p>[使用 SSH 連接至 Linux VM](../bastion/bastion-connect-vm-ssh.md)</p><p>[使用 RDP 連接到 Windows VM](../bastion/bastion-connect-vm-rdp.md)</p>|
|[虛擬網路 NAT 閘道](#nat)|建立 NAT 閘道，為虛擬機器提供輸出連線能力。|<p>[建立 NAT 閘道](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Azure 對等服務（預覽）](#azurepeeringservice)|透過公用網路，與服務提供者共同作業，以獲得最佳且可靠的路由傳送至 Microsoft cloud。|<p>[註冊 Azure 對等服務](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>虛擬網路

Azure 虛擬網路 (VNet) 是私人網路在 Azure 中的基本建置組塊。 您可以使用 Vnet 來執行下列動作：
- **Azure 資源之間的通訊**：您可以將 vm 和數種其他類型的 azure 資源部署到虛擬網路，例如 Azure App Service 環境、AZURE KUBERNETES SERVICE （AKS）和 Azure 虛擬機器擴展集。 若要檢視可部署到虛擬網路中的 Azure 資源的完整清單，請參閱[虛擬網路服務整合](../virtual-network/virtual-network-for-azure-services.md)。
- 彼此**通訊**：您可以將虛擬網路彼此連線，讓任一虛擬網路中的資源能夠使用虛擬網路對等互連彼此通訊。 您所連線的虛擬網路可位於相同或不同的 Azure 區域。 如需詳細資訊，請參閱[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)。
- 與**網際網路通訊**：根據預設，VNet 中的所有資源都可以向網際網路進行輸出通訊。 您可以藉由指派公用 IP 位址或公用負載平衡器，對該項資源進行輸入通訊。 您也可以使用[公用 IP 位址](../virtual-network/virtual-network-public-ip-address.md)或公用[Load Balancer](../load-balancer/load-balancer-overview.md)來管理您的輸出連線。
- **與內部部署網路通訊**：您可以使用[VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[ExpressRoute](../expressroute/expressroute-introduction.md)，將內部部署電腦和網路連線到虛擬網路。

如需詳細資訊，請參閱[什麼是 Azure 虛擬網路？](../virtual-network/virtual-networks-overview.md)。

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
ExpressRoute 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。 此連線是私人連線。 流量不會經由網際網路傳送。 透過 ExpressRoute，您可以建立 Microsoft 與雲端服務 (例如 Microsoft Azure、Office 365 和 Dynamics 365) 的連線。  如需詳細資訊，請參閱[什麼是 ExpressRoute？](../expressroute/expressroute-introduction.md)。

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN 閘道
VPN 閘道可協助您從內部部署位置建立虛擬網路的加密跨單位連線，或建立 Vnet 之間的加密連接。 有不同的設定可用於 VPN 閘道連線，例如站對站、點對站或 vnet 對 VNet。
下圖說明相同虛擬網路的多個站對站 VPN 連線。

![站對站 Azure VPN 閘道連線](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

如需不同 VPN 連線類型的詳細資訊，請參閱[VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="virtual-wan"></a><a name="virtualwan"></a>虛擬 WAN
Azure 虛擬 WAN 是一種網路服務，可將最佳且自動化的分支連線提供給 Azure，或透過 Azure 提供最佳且自動化的分支連線。 Azure 區域作為中樞，您可以選擇將您的分支連線到該中樞。 您也可以利用 Azure 骨幹來連接分支，並享受分支對 VNet 的連線能力。 Azure 虛擬 WAN 將許多 Azure 雲端聯機服務（例如站對站 VPN、ExpressRoute、點對站使用者 VPN）整合成單一操作介面。 使用虛擬網路連線可建立與 Azure Vnet 的連線。 如需詳細資訊，請參閱[什麼是 Azure 虛擬 WAN？](../virtual-wan/virtual-wan-about.md)。

![虛擬 WAN 的圖表](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS 是 DNS 網域的主機服務，可使用 Microsoft Azure 基礎結構來提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。 如需詳細資訊，請參閱[什麼是 Azure DNS？](../dns/dns-overview.md)。

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure Bastion 服務是您可在虛擬網路內佈建的新完全平台受控 PaaS 服務。 直接在 Azure 入口網站中，透過 TLS 提供與虛擬機器之間安全且順暢的 RDP/SSH 連線。 透過 Azure Bastion 連線時，虛擬機器不需要公用 IP 位址。 如需詳細資訊，請參閱[什麼是 Azure 防禦？](../bastion/bastion-overview.md)。

![Azure 防禦架構](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>虛擬網路 NAT 閘道
虛擬網路 NAT (網路位址轉譯) 可簡化虛擬網路的僅限輸出網際網路連線。 在子網路上設定時，所有輸出連線都會使用您指定的靜態公用 IP 位址。 在沒有負載平衡器或直接連結至虛擬機器的公用 IP 位址的情況下，可能有輸出連線能力。 如需詳細資訊，請參閱[什麼是虛擬網路 NAT 閘道？](../virtual-network/nat-overview.md) 

![虛擬網路 NAT 閘道](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Azure 對等服務
Azure 對等服務可加強客戶對 Microsoft 雲端服務的連線，例如 Office 365、Dynamics 365、軟體即服務（SaaS）服務、Azure，或透過公用網際網路存取的任何 Microsoft 服務。 如需詳細資訊，請參閱[什麼是 Azure 對等服務？](../peering-service/about.md)。

## <a name="application-protection-services"></a><a name="protect"></a>應用程式保護服務

本節說明 Azure 中的網路服務，可協助保護您的網路資源-在 Azure 中使用這些網路服務的任何或組合來保護您的應用程式-私人連結、DDoS 保護、防火牆、網路安全性群組、Web 應用程式防火牆，以及虛擬網路端點。

|服務|為何要使用？|狀況|
|---|---|---|
|[DDoS 保護](#ddosprotection) |您的應用程式的高可用性，並具有超過 IP 流量費用的保護|[管理 Azure DDoS 保護](../virtual-network/manage-ddos-protection.md)|
|[Web 應用程式防火牆](#waf)|<p>[具有應用程式閘道的 AZURE WAF](../web-application-firewall/ag/ag-overview.md)可為公用和私人位址空間中的實體提供區域保護</p><p>[具有 Front 門板的 AZURE WAF](../web-application-firewall/afds/afds-overview.md)可提供網路邊緣到公用端點的保護。</p>|<p>[設定 bot 保護規則](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[設定自訂回應碼](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[設定 IP 限制規則](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[設定速率限制規則](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure 防火牆](#firewall)|Azure 防火牆是受控、雲端式網路安全性服務，可以保護您的 Azure 虛擬網路資源。 它是完全具狀態的防火牆即服務，具有內建的高可用性和不受限制的雲端延展性。|<p>[在 Vnet 中部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[-在混合式網路中部署 Azure 防火牆](../firewall/tutorial-hybrid-ps.md)</p> <p>[使用 Azure 防火牆 DNAT 篩選輸入流量](../firewall/tutorial-firewall-dnat.md)</p>|
|[網路安全性群組](#nsg)|VM/子網中所有網路流量流程的完整細微分散式結束節點控制|[使用網路安全性群組來篩選網路流量](../virtual-network/tutorial-filter-network-traffic.md)|
|[虛擬網路服務端點](#serviceendpoints)|可讓您限制某些 Azure 服務資源對虛擬網路子網的網路存取|[限制對 PaaS 資源的網路存取](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[私人連結](#privatelink)|可讓您透過虛擬網路中的私人端點，存取 Azure PaaS 服務（例如，Azure 儲存體和 SQL Database）和 Azure 託管的客戶所擁有/合作夥伴服務。|<p>[建立私人端點](../private-link/create-private-endpoint-portal.md)</p><p>[建立私用連結服務](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS 保護 
[Azure DDoS 保護](../virtual-network/manage-ddos-protection.md)針對最複雜的 DDoS 威脅提供對策。 此服務為您的應用程式和虛擬網路中部署的資源，提供增強的 DDoS 風險降低功能。 此外，使用 Azure DDoS 保護的客戶可以存取 DDoS 快速回應支援，以在主動攻擊期間與 DDoS 專家接洽。

![DDoS 保護](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Web 應用程式防火牆

[Azure Web 應用程式防火牆](../web-application-firewall/overview.md)（WAF）可保護 web 應用程式免于遭受常見的 web 入侵和弱點，例如 SQL 插入式攻擊和跨網站腳本。 Azure WAF 透過受控規則提供現成的保護，避免 OWASP 前10大弱點。 此外，客戶也可以設定自訂規則，這是客戶管理的規則，可根據來源 IP 範圍提供額外的保護，並要求標頭、cookie、表單資料欄位或查詢字串參數等屬性。

客戶可以選擇[使用應用程式閘道來部署 AZURE WAF](../application-gateway/waf-overview.md) ，以針對公用和私人位址空間中的實體提供區域保護。 客戶也可以選擇部署[具有 Front 的 AZURE WAF](../frontdoor/waf-overview.md) ，將網路邊緣的保護提供給公用端點。

![Web 應用程式防火牆](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Azure 防火牆
Azure 防火牆是受控、雲端式網路安全性服務，可以保護您的 Azure 虛擬網路資源。 使用 Azure 防火牆，您可以跨訂用帳戶和虛擬網路集中建立、強制執行及記錄應用程式和網路連線原則。 Azure 防火牆會針對您的虛擬網路資源使用靜態公用 IP 位址，允許外部防火牆識別源自您虛擬網路的流量。 

如需 Azure 防火牆的詳細資訊，請參閱[Azure 防火牆檔](../firewall/overview.md)。

![防火牆概觀](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>網路安全性群組
您可以透過網路安全性群組，篩選在 Azure 虛擬網路中進出 Azure 資源的網路流量。 如需詳細資訊，請參閱[安全性概觀](../virtual-network/security-overview.md)。

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>服務端點
虛擬網路 (VNet) 服務端點可透過直接連線，將您的虛擬網路私人位址空間和 VNet 的身分識別延伸至 Azure 服務。 端點可讓您將重要的 Azure 服務資源只放到您的虛擬網路保護。 從您的 VNet 到 Azure 服務的流量一定會保留在 Microsoft Azure 骨幹網路上。 如需詳細資訊，請參閱[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。

![虛擬網路服務端點](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[Azure 私人連結](../private-link/private-link-overview.md)可讓您透過虛擬網路中的私人端點，存取 Azure PaaS 服務（例如，Azure 儲存體和 SQL Database）和 azure 託管的客戶所擁有/合作夥伴服務。
您的虛擬網路與服務之間的流量會經由 Microsoft 骨幹網路傳輸。 您的服務不再需要向公用網際網路公開。 您可以在虛擬網路中建立自己的 Private Link 服務，並提供給客戶。

![私人端點概觀](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>應用程式傳遞服務

本節說明 Azure 中的網路服務，可協助提供應用程式-內容傳遞網路、Azure Front 門板服務、流量管理員、Load Balancer 和應用程式閘道。

|服務|為何要使用？|狀況|
|---|---|---|
|[內容傳遞網路](#cdn)|將高頻寬內容傳遞給使用者。 Cdn 會將快取的內容儲存在邊緣伺服器上的存在點（POP）位置，以接近使用者，以將延遲降至最低|<p>[將 CDN 新增至 web 應用程式](../cdn/cdn-add-to-web-app.md)</p> <p>[-使用 Azure CDN 自訂網域透過 HTTPS 存取儲存體 blob](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[將自訂網域新增至 Azure CDN 端點](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[在 Azure CDN 自訂網域上設定 HTTPS](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|可讓您針對高可用性的最佳效能和即時全域容錯移轉進行優化，以定義、管理及監視 web 流量的全域路由。|<p>[將自訂網域新增至您的 Azure Front Door 服務](../frontdoor/front-door-custom-domain.md)</p> <p>[在 Front Door 自訂網域上設定 HTTPS](../frontdoor/front-door-custom-domain-https.md)</p><p>[設定地區篩選 Web 應用程式防火牆原則](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[流量管理員](#trafficmanager)|將以 DNS 為基礎的流量分散到全球 Azure 區域的服務，同時提供高可用性和回應性|<p> [低延遲的路由傳送流量](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[路由傳送流量至優先順序的端點](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [使用加權端點控制流量](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[根據端點的地理位置來路由傳送流量](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [根據使用者的子網路路由傳送流量](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[負載平衡器](#loadbalancer)|藉由將流量路由傳送到可用性區域及 Vnet，提供區域負載平衡。 提供內部負載平衡，其方式是在您的資源之間和之間路由傳送流量，以建立您的區域應用程式。|<p> [VM 的負載平衡網際網路流量](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[對虛擬網路內 Vm 之間的流量進行負載平衡](../load-balancer/tutorial-load-balancer-standard-internal-portal.md)<p>[埠將流量轉送至特定 Vm 上的特定埠](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [設定負載平衡和輸出規則](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[應用程式閘道](#applicationgateway)|Azure 應用程式閘道是網路流量負載平衡器，可讓您管理 Web 應用程式的流量。|<p>[使用 Azure 應用程式閘道引導網路流量](../application-gateway/quick-create-portal.md)</p><p>[教學課程：使用 Azure 入口網站設定包含 TLS 終止的應用程式閘道](../application-gateway/create-ssl-portal.md)</p><p>[建立包含 URL 路徑型重新導向的應用程式閘道](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>內容傳遞網路
Azure 內容傳遞網路 (CDN) 為開發人員提供一套全域解決方案，讓他們可藉由在策略性分布於全球的實體節點上快取內容，將高頻寬內容迅速傳遞給使用者。 如需 Azure CDN 的詳細資訊，請參閱[azure 內容傳遞網路](../cdn/cdn-overview.md)

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front 門板服務
Azure Front Door Service 可讓您針對最佳效能和立即全域容錯移轉以獲得高可用性最佳化，定義、管理及監視網路流量的全域路由。 透過 Front Door，您可以將全球 (多區域) 消費者和企業應用程式轉換成健全、高效能的個人化新式應用程式、API，以及觸及 Azure 全球對象的內容。 如需詳細資訊，請參閱[Azure Front 門](../frontdoor/front-door-overview.md)。


### <a name="traffic-manager"></a><a name="trafficmanager"></a>流量管理員

Azure 流量管理員是 DNS 型流量負載平衡器，可讓您跨全球的 Azure 區域將流量最佳分散至服務，同時提供高可用性和回應性。 流量管理員提供各種流量路由方法來散發流量，例如優先順序、加權、效能、地理、多重值或子網。 如需流量路由方法的詳細資訊，請參閱[流量管理員路由方法](../traffic-manager/traffic-manager-routing-methods.md)。

下圖顯示以端點優先順序為基礎的路由與流量管理員：

![Azure 流量管理員「優先順序」流量路由方法](./media/networking-overview/priority.png)

如需流量管理員的詳細資訊，請參閱[什麼是 Azure 流量管理員？](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>負載平衡器
Azure Load Balancer 針對所有 UDP 和 TCP 通訊協定提供高效能、低延遲的第 4 層負載平衡。 它會管理輸入及輸出連線。 您可以設定公用和內部負載平衡端點。 您可以使用 TCP 和 HTTP 健全狀況探查選項定義規則，將輸入連線對應至後端集區目的地，以管理服務可用性。 若要深入了解負載平衡器，請閱讀[負載平衡器概觀](../load-balancer/load-balancer-overview.md)一文。

下圖顯示同時使用外部和內部負載平衡器的網際網路對向多層應用程式︰

![Azure Load Balancer 範例](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>應用程式閘道
Azure 應用程式閘道是網路流量負載平衡器，可讓您管理 Web 應用程式的流量。 它是應用程式傳遞控制器（ADC）即服務，為您的應用程式提供各種第7層負載平衡功能。 如需詳細資訊，請參閱[什麼是 Azure 應用程式閘道？](../application-gateway/overview.md)。

下圖顯示以 url 路徑為基礎的路由與應用程式閘道。

![應用程式閘道範例](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>網路監視服務
本節說明 Azure 中的網路服務，可協助監視您的網路資源，網路監看員、ExpressRoute 監視器、Azure 監視器和虛擬網路點按。

|服務|為何要使用？|狀況|
|---|---|---|
|[網路監看員](#networkwatcher)|協助監視和疑難排解連線問題、協助診斷 VPN、NSG 和路由問題、在 VM 上捕捉封包、使用 Azure Functions 和 Logic Apps 自動化觸發診斷工具|<p>[診斷 VM 流量篩選問題](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[診斷 VM 路由問題](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[監視 Vm 之間的通訊](../network-watcher/connection-monitor.md)</p><p>[診斷網路間的通訊問題](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[記錄往返 VM 的網路流量](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute 監視](#expressroutemonitor)|提供網路效能、可用性和使用率的即時監視，協助自動探索網路拓撲、提供更快的錯誤隔離、偵測暫時性的網路問題、協助分析歷程記錄的網路效能特性、支援多個訂用帳戶|<p>[設定 ExpressRoute 的網路效能監控](../expressroute/how-to-npm.md)</p><p>[ExpressRoute 監視、計量和警示](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure 監視器](#azuremonitor)|協助您瞭解應用程式的執行方式，並主動識別影響它們的問題以及它們所依賴的資源。|<p>[流量管理員的計量與警示](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[適用于 Standard Load Balancer 的 Azure 監視器診斷](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[監視 Azure 防火牆記錄和計量](../firewall/tutorial-diagnostics.md)</p><p>[Azure Web 應用程式防火牆的監視和記錄](../frontdoor/waf-front-door-monitor.md)</p>|
|[虛擬網路點](#vnettap)|為封包收集器提供虛擬機器網路流量的連續串流，可啟用網路和應用程式效能管理解決方案和安全性分析工具|[建立 VNet 點分資源](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>網路監看員
Azure 網路監看員提供了相關工具，可對 Azure 虛擬網路中的資源進行監視、診斷、檢視計量，以及啟用或停用記錄。 如需詳細資訊，請參閱[什麼是網路監看員？](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)。
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute 監視
若要瞭解如何觀看 ExpressRoute 電路計量、資源記錄和警示，請參閱[expressroute 監視、計量和警示](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)。
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure 監視器
Azure 監視器可藉由提供全方位的解決方案，以便收集、分析及處理來自雲端和內部部署環境的遙測資料，進而將應用程式的可用性和效能最大化。 它可協助您了解您的應用程式表現如何，並主動識別影響它們的問題以及它們所依賴的資源。 如需詳細資訊，請參閱[Azure 監視器總覽](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)。
### <a name="virtual-network-tap"></a><a name="vnettap"></a>虛擬網路點
Azure 虛擬網路 TAP (終端機存取點) 可讓您持續將虛擬機器網路流量串流到網路封包收集器或分析工具。 收集器或分析工具是由[網路虛擬裝置](https://azure.microsoft.com/solutions/network-appliances/)合作夥伴所提供。 

下圖顯示虛擬網路 TAP 的運作方式。 

![虛擬網路 TAP 的運作方式](./media/networking-overview/virtual-network-tap-architecture.png)

如需詳細資訊，請參閱[什麼是虛擬網路](../virtual-network/virtual-network-tap-overview.md)點。

## <a name="next-steps"></a>後續步驟

- 完成[建立第一個虛擬網路](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)一文中的步驟，以建立第一個 VNet，並將一些 VM 連線至該 VNet。
- 完成[設定點對站](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)連線一文中的步驟，將您的電腦連線到 VNet。
- 完成[建立網際網路對向負載平衡器](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)一文中的步驟，將網際網路流量負載平衡到公用伺服器。
 
 
   
