---
title: Azure 網路服務概述
description: 瞭解 Azure 中的網路服務及其功能 - 連接服務、應用程式保護服務、應用程式交付服務和網路監視。
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 90eddea839d2f6ae5235ac6a391b40dd667ab401
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257830"
---
# <a name="azure-networking-services-overview"></a>Azure 網路服務概述

Azure 中的網路服務提供各種網路功能，這些功能可以一起使用或單獨使用。 按一下下列任一項重要功能，以深入了解相關資訊︰
- [**連接服務**](#connect)：使用 Azure - 虛擬網路 （VNet）、虛擬 WAN、ExpressRoute、VPN 閘道、虛擬網路 NAT 閘道、Azure DNS、對等服務和 Azure 堡壘中的這些網路服務的任何或組合連接 Azure 資源和本地資源。
- [**應用程式保護服務**](#protect)使用 Azure 中的這些網路服務的任何或組合來保護應用程式 - 專用連結、DDoS 保護、防火牆、網路安全性群組、Web 應用程式防火牆和虛擬網路終結點。
- [**應用程式交付服務**](#deliver)在 Azure - 內容交付網路 （CDN）、Azure 前門服務、流量管理器、應用程式閘道、Internet 分析器和負載等化器中使用這些網路服務的任何或組合在 Azure 網路中交付應用程式。
- [**網路監視**](#monitor)– 使用 Azure 中的任何或組合這些網路服務來監視網路資源 - 網路觀察程式、快速路由監視器、Azure 監視器或 VNet 終端存取點 （TAP）。

## <a name="connectivity-services"></a><a name="connect"></a>連線服務
 
本節介紹在 Azure 資源之間提供連接、從本地網路到 Azure 資源的連接以及 Azure - 虛擬網路 （VNet）、虛擬 WAN、ExpressRoute、VPN 閘道中的分支到分支連接的服務，虛擬網路 NAT 閘道、Azure DNS、Azure 對等服務和 Azure 堡壘。

|服務|為什麼要使用？|案例|
|---|---|---|
|[虛擬網路](#vnet)|使 Azure 資源能夠安全地相互通信、Internet 和本地網路。| <p>[篩選網路流量](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[路由網路流量](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[限制對資源的網路存取](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[連線虛擬網路](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[快速路線](#expressroute)|通過連接供應商推動的專用連線將本地網路擴展到 Microsoft 雲中。|<p>[創建和修改快速路由電路](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[建立和修改 ExpressRoute 線路的對等互連](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[將 VNet 連結到 ExpressRoute 線路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[配置和管理快速路由電路的路由濾波器](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN 閘道](#vpngateway)|通過公共 Internet 在 Azure 虛擬網路和本地位置之間發送加密流量。|<p>[網站到網站連接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[VNet 到 VNet 連接](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[點對點連接](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[虛擬 WAN](#virtualwan)|優化和自動化到 Azure 的分支連接，並通過 Azure 進行連接。 Azure 區域作為中樞，您可以選擇將您的分支連線到該中樞。|<p>[網站到網站連接](../virtual-wan/virtual-wan-site-to-site-portal.md)，[快速路由連接](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|託管通過使用 Microsoft Azure 基礎結構提供名稱解析的 DNS 域。|<p>[在 Azure DNS 中託管域](../dns/dns-delegate-domain-azure-dns.md)</p><p>[為 Web 應用創建 DNS 記錄](../dns/dns-web-sites-custom-domain.md)</p> <p>[為流量管理器創建別名記錄](../dns/tutorial-alias-tm.md)</p> <p>[為公共 IP 位址創建別名記錄](../dns/tutorial-alias-pip.md)</p> <p>[為區域資源記錄創建別名記錄](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|直接在 Azure 入口網站中，透過 SSL 設定與虛擬機器之間安全且順暢的 RDP/SSH 連線。 通過 Azure 堡壘進行連接時，虛擬機器不需要公共 IP 位址|<p>[創建 Azure 堡壘主機](../bastion/bastion-create-host-portal.md)</p><p>[使用 SSH 連接到 Linux VM](../bastion/bastion-connect-vm-ssh.md)</p><p>[使用 RDP 連接到 Windows VM](../bastion/bastion-connect-vm-rdp.md)</p>|
|[虛擬網路 NAT 閘道](#nat)|創建 NAT 閘道，為虛擬機器提供出站連接。|<p>[創建 NAT 閘道](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Azure 對等服務（預覽）](#azurepeeringservice)|與服務提供者協作，通過公共網路實現最佳、可靠的路由到 Microsoft 雲。|<p>[註冊 Azure 對等服務](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>虛擬網路

Azure 虛擬網路 (VNet) 是私人網路在 Azure 中的基本建置組塊。 您可以使用 VNet：
- **在 Azure 資源之間通信**：您可以將 VM 和幾種其他類型的 Azure 資源部署到虛擬網路，如 Azure 應用服務環境、Azure 庫伯奈斯服務 （AKS） 和 Azure 虛擬機器縮放集。 若要檢視可部署到虛擬網路中的 Azure 資源的完整清單，請參閱[虛擬網路服務整合](../virtual-network/virtual-network-for-azure-services.md)。
- **相互通信**：您可以將虛擬網路相互連接，使虛擬網路中的資源能夠使用虛擬網路對等互連相互通信。 您所連線的虛擬網路可位於相同或不同的 Azure 區域。 如需詳細資訊，請參閱[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)。
- **與互聯網通信**：預設情況下，VNet 中的所有資源都可以向外通信到互聯網。 您可以藉由指派公用 IP 位址或公用負載平衡器，對該項資源進行輸入通訊。 您還可以使用[公共 IP 位址](../virtual-network/virtual-network-public-ip-address.md)或公共[負載等化器](../load-balancer/load-balancer-overview.md)來管理出站連接。
- **與本地網路通信**：您可以使用[VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[ExpressRoute](../expressroute/expressroute-introduction.md)將本地電腦和網路連接到虛擬網路。

有關詳細資訊，請參閱什麼是[Azure 虛擬網路？](../virtual-network/virtual-networks-overview.md)

### <a name="expressroute"></a><a name="expressroute"></a>快速路線
ExpressRoute 使您能夠通過連接供應商推動的專用連線將本地網路擴展到 Microsoft 雲中。 此連線是私人連線。 流量不會經由網際網路傳送。 透過 ExpressRoute，您可以建立 Microsoft 雲端服務的連線，例如 Microsoft Azure、Office 365 和 Dynamics 365。  有關詳細資訊，請參閱[什麼是快速路由？](../expressroute/expressroute-introduction.md)

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN 閘道
VPN 閘道可説明您從本地位置創建到虛擬網路的加密跨界連接，或在 VNet 之間創建加密連接。 VPN 閘道連接有多種配置，例如網站到網站、點對點或 VNet 到 VNet。
下圖說明瞭與同一虛擬網路的多個網站到網站 VPN 連接。

![網站到網站 Azure VPN 閘道連接](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

有關不同類型的 VPN 連接的詳細資訊，請參閱[VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="virtual-wan"></a><a name="virtualwan"></a>虛擬 WAN
Azure 虛擬 WAN 是一種網路服務，可將最佳且自動化的分支連線提供給 Azure，或透過 Azure 提供最佳且自動化的分支連線。 Azure 區域作為中樞，您可以選擇將您的分支連線到該中樞。 您也可以利用 Azure 骨幹來連接分支，並享受分支對 VNet 的連線能力。 Azure 虛擬 WAN 將許多 Azure 雲連接服務（如網站到網站 VPN、ExpressRoute、點對點使用者 VPN）合併到單個操作介面中。 使用虛擬網路連線可建立與 Azure Vnet 的連線。 有關詳細資訊，請參閱什麼是[Azure 虛擬 WAN？](../virtual-wan/virtual-wan-about.md)

![虛擬 WAN 的圖表](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構來提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。 有關詳細資訊，請參閱什麼是[Azure DNS？](../dns/dns-overview.md)

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure Bastion 服務是您可在虛擬網路內佈建的新完全平台受控 PaaS 服務。 直接在 Azure 入口網站中，透過 SSL 提供與虛擬機器之間安全且順暢的 RDP/SSH 連線。 透過 Azure Bastion 連線時，虛擬機器不需要公用 IP 位址。 有關詳細資訊，請參閱什麼是[Azure 堡壘？](../bastion/bastion-overview.md)

![Azure 堡壘體系結構](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>虛擬網路 NAT 閘道
虛擬網路 NAT（網路位址轉譯）簡化了虛擬網路僅出站互聯網連接。 在子網上配置時，所有出站連接都使用指定的靜態公共 IP 位址。 無需直接連接到虛擬機器的負載等化器或公共 IP 位址，即可實現出站連接。 有關詳細資訊，請參閱[什麼是虛擬網路 NAT 閘道？](../virtual-network/nat-overview.md) 

![虛擬網路 NAT 閘道](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Azure 對等服務
Azure 對等服務增強了客戶與 Microsoft 雲服務的連接，例如 Office 365、Dynamics 365、軟體即服務 （SaaS） 服務、Azure 或可通過公共 Internet 訪問的任何 Microsoft 服務。 有關詳細資訊，請參閱什麼是[Azure 對等服務？](../peering-service/about.md)

## <a name="application-protection-services"></a><a name="protect"></a>應用程式保護服務

本節介紹 Azure 中有助於保護網路資源的網路服務 - 使用 Azure 中的任何或組合這些網路服務來保護應用程式 - 專用鏈路、DDoS 保護、防火牆、網路安全性群組、Web應用程式防火牆和虛擬網路終結點。

|服務|為什麼要使用？|狀況|
|---|---|---|
|[DDoS 保護](#ddosprotection) |應用程式的高可用性，可防止 IP 流量過高|[管理 Azure DDoS 保護](../virtual-network/manage-ddos-protection.md)|
|[Web 應用程式防火牆](#waf)|<p>[具有應用程式閘道的 Azure WAF](../web-application-firewall/ag/ag-overview.md)為公共和專用位址空間中的實體提供區域保護</p><p>[帶前門的 Azure WAF](../web-application-firewall/afds/afds-overview.md)可在網路邊緣為公共終結點提供保護。</p>|<p>[配置自動程式保護規則](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[設定自訂回應碼](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[配置 IP 限制規則](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[配置速率限制規則](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure 防火牆](#firewall)|Azure 防火牆是受控、雲端式網路安全性服務，可以保護您的 Azure 虛擬網路資源。 它是完全具狀態的防火牆即服務，具有內建的高可用性和不受限制的雲端延展性。|<p>[在 Vnet 中部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[- 在混合網路中部署 Azure 防火牆](../firewall/tutorial-hybrid-ps.md)</p> <p>[使用 Azure 防火牆 DNAT 篩選入站流量](../firewall/tutorial-firewall-dnat.md)</p>|
|[網路安全性群組](#nsg)|所有網路流量流量的 VM/子網的全粒分散式終端節點控制|[使用網路安全性群組來篩選網路流量](../virtual-network/tutorial-filter-network-traffic.md)|
|[虛擬網路服務終結點](#serviceendpoints)|使您能夠將對某些 Azure 服務資源的網路訪問限制為虛擬網路子網|[限制對 PaaS 資源的網路存取](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[私人連結](#privatelink)|使您能夠通過虛擬網路中的專用終結點訪問 Azure PaaS 服務（例如，Azure 存儲和 SQL 資料庫）和 Azure 託管的客戶擁有/合作夥伴服務。|<p>[創建專用終結點](../private-link/create-private-endpoint-portal.md)</p><p>[創建專用連結服務](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS 保護 
[Azure DDoS 保護](../virtual-network/manage-ddos-protection.md)針對最複雜的 DDoS 威脅提供了對策。 該服務為部署在虛擬網路中的應用程式和資源提供了增強的 DDoS 緩解功能。 此外，使用 Azure DDoS 保護的客戶可以訪問 DDoS 快速回應支援，在主動攻擊期間與 DDoS 專家接洽。

![DDoS保護](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Web 應用程式防火牆

[Azure Web 應用程式防火牆](../web-application-firewall/overview.md)（WAF） 可保護 Web 應用程式免受常見 Web 漏洞的漏洞（如 SQL 注入和跨網站腳本）的影響。 Azure WAF 通過託管規則提供非WASP 前 10 大漏洞的開箱即用保護。 此外，客戶還可以配置自訂規則，這些規則是客戶管理的規則，可基於源 IP 範圍提供其他保護，並請求屬性（如標頭、Cookie、表單資料欄位或查詢字串參數）。

客戶可以選擇[使用應用程式閘道部署 Azure WAF，該閘道](../application-gateway/waf-overview.md)為公共和專用位址空間中的實體提供區域保護。 客戶還可以選擇[使用前門部署 Azure WAF，](../frontdoor/waf-overview.md)在網路邊緣為公共終結點提供保護。

![Web 應用程式防火牆](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Azure 防火牆
Azure 防火牆是受控、雲端式網路安全性服務，可以保護您的 Azure 虛擬網路資源。 使用 Azure 防火牆，可以跨訂閱和虛擬網路集中創建、強制實施和記錄應用程式和網路連接策略。 Azure 防火牆會針對您的虛擬網路資源使用靜態公用 IP 位址，允許外部防火牆識別源自您虛擬網路的流量。 

有關 Azure 防火牆的詳細資訊，請參閱[Azure 防火牆文檔](../firewall/overview.md)。

![防火牆概觀](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>網路安全性群組
您可以透過網路安全性群組，篩選在 Azure 虛擬網路中進出 Azure 資源的網路流量。 如需詳細資訊，請參閱[安全性概觀](../virtual-network/security-overview.md)。

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>服務終結點
虛擬網路 (VNet) 服務端點可透過直接連線，將您的虛擬網路私人位址空間和 VNet 的身分識別延伸至 Azure 服務。 端點可讓您將重要的 Azure 服務資源只放到您的虛擬網路保護。 從您的 VNet 到 Azure 服務的流量一定會保留在 Microsoft Azure 骨幹網路上。 如需詳細資訊，請參閱[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。

![虛擬網路服務端點](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[Azure 專用連結](../private-link/private-link-overview.md)使您能夠通過虛擬網路中的專用終結點訪問 Azure PaaS 服務（例如，Azure 存儲和 SQL 資料庫）和 Azure 託管的客戶擁有/合作夥伴服務。
虛擬網路和服務之間的流量將傳輸 Microsoft 骨幹網路。 不再需要將您的服務公開到公共互聯網。 您可以在虛擬網路中創建自己的專用連結服務，並將其交付給客戶。

![私人端點概觀](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>應用程式交付服務

本節介紹 Azure 中説明交付應用程式的網路服務 - 網路觀察程式、快速路由監視器、Azure 監視器或 VNet 終端存取點 （TAP）。

|服務|為什麼要使用？|狀況|
|---|---|---|
|[內容交付網路](#cdn)|為使用者提供高頻寬內容。 CDN 將緩存的內容存儲在靠近最終使用者的位置的邊緣伺服器上，以儘量減少延遲|<p>[將 CDN 添加到 Web 應用](../cdn/cdn-add-to-web-app.md)</p> <p>[- 通過 HTTPS 使用 Azure CDN 自訂域訪問存儲 Blob](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[向 Azure CDN 終結點添加自訂域](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[在 Azure CDN 自訂網域上設定 HTTPS](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|通過優化最佳性能和即時全域容錯移轉，實現高可用性，使您能夠定義、管理和監視 Web 流量的全域路由。|<p>[向 Azure 前門服務添加自訂域](../frontdoor/front-door-custom-domain.md)</p> <p>[在 Front Door 自訂網域上設定 HTTPS](../frontdoor/front-door-custom-domain-https.md)</p><p>[設置地理篩選 Web 應用程式防火牆策略](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[流量管理器](#trafficmanager)|基於 DNS 將流量分發到跨全球 Azure 區域的服務，同時提供高可用性和回應性|<p> [低延遲的路由傳送流量](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[路由傳送流量至優先順序的端點](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [使用加權端點控制流量](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[基於端點地理位置的路由流量](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [根據使用者的子網路路由傳送流量](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[負載平衡器](#loadbalancer)|通過跨可用性區域路由流量並進入 VNet，提供區域負載平衡。 通過在資源之間和資源之間路由流量來構建區域應用程式，提供內部負載平衡。|<p> [VM 的負載平衡網際網路流量](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[虛擬網路中 VM 的負載平衡流量](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[將流量轉發到特定 VM 上的特定埠](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [配置負載平衡和出站規則](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[應用程式閘道](#applicationgateway)|Azure 應用程式閘道是網路流量負載平衡器，可讓您管理 Web 應用程式的流量。|<p>[使用 Azure 應用程式閘道引導網路流量](../application-gateway/quick-create-portal.md)</p><p>[設定具有 SSL 終止的應用程式閘道](../application-gateway/create-ssl-portal.md)</p><p>[建立包含 URL 路徑型重新導向的應用程式閘道](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>內容交付網路
Azure 內容傳遞網路 (CDN) 為開發人員提供一套全域解決方案，讓他們可藉由在策略性分布於全球的實體節點上快取內容，將高頻寬內容迅速傳遞給使用者。 有關 Azure CDN 的詳細資訊，請參閱[Azure 內容傳遞網路](../cdn/cdn-overview.md)

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure 前門服務
Azure Front Door Service 可讓您針對最佳效能和立即全域容錯移轉以獲得高可用性最佳化，定義、管理及監視網路流量的全域路由。 透過 Front Door，您可以將全球 (多區域) 消費者和企業應用程式轉換成健全、高效能的個人化新式應用程式、API，以及觸及 Azure 全球對象的內容。 有關詳細資訊，請參閱[Azure 前門](../frontdoor/front-door-overview.md)。


### <a name="traffic-manager"></a><a name="trafficmanager"></a>流量管理器

Azure 流量管理員是 DNS 型流量負載平衡器，可讓您跨全球的 Azure 區域將流量最佳分散至服務，同時提供高可用性和回應性。 流量管理器提供一系列流量路由方法來分發流量，如優先順序、加權、性能、地理、多值或子網。 有關流量路由方法的詳細資訊，請參閱[流量管理器路由方法](../traffic-manager/traffic-manager-routing-methods.md)。

下圖顯示了流量管理器基於終結點優先順序的路由：

![Azure 流量管理員「優先順序」流量路由方法](./media/networking-overview/priority.png)

有關流量管理器的詳細資訊，請參閱什麼是[Azure 流量管理器？](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>負載平衡器
Azure Load Balancer 針對所有 UDP 和 TCP 通訊協定提供高效能、低延遲的第 4 層負載平衡。 它會管理輸入及輸出連線。 您可以設定公用和內部負載平衡端點。 您可以使用 TCP 和 HTTP 健全狀況探查選項定義規則，將輸入連線對應至後端集區目的地，以管理服務可用性。 若要深入了解負載平衡器，請閱讀[負載平衡器概觀](../load-balancer/load-balancer-overview.md)一文。

下圖顯示同時使用外部和內部負載平衡器的網際網路對向多層應用程式︰

![Azure 負載等化器示例](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>應用程式閘道
Azure 應用程式閘道是網路流量負載平衡器，可讓您管理 Web 應用程式的流量。 它是應用程式交付控制器 （ADC） 作為服務，為您的應用程式提供各種第 7 層負載平衡功能。 有關詳細資訊，請參閱什麼是[Azure 應用程式閘道？](../application-gateway/overview.md)

下圖顯示了使用應用程式閘道的基於 URL 路徑的路由。

![應用程式閘道示例](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>網路監控服務
本節介紹 Azure 中説明監視網路資源的網路服務 - 網路觀察程式、快速路由監視器、Azure 監視器和虛擬網路 TAP。

|服務|為什麼要使用？|狀況|
|---|---|---|
|[網路監看員](#networkwatcher)|説明監視和排除連接問題，説明診斷 VPN、NSG 和路由問題，捕獲 VM 上的資料包，使用 Azure 函數和邏輯應用自動觸發診斷工具|<p>[診斷 VM 流量篩選問題](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[診斷 VM 路由問題](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[監控 VM 之間的通信](../network-watcher/connection-monitor.md)</p><p>[診斷網路間的通訊問題](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[記錄往返 VM 的網路流量](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[快速路由監視器](#expressroutemonitor)|提供網路性能、可用性和利用率的即時監控，説明自動探索網路拓撲，提供更快的故障隔離，檢測瞬態網路問題，説明分析歷史網路性能特點，支援多訂閱|<p>[設定 ExpressRoute 的網路效能監控](../expressroute/how-to-npm.md)</p><p>[ExpressRoute 監視、計量和警示](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure 監視器](#azuremonitor)|説明您瞭解應用程式的性能，並主動識別影響應用程式的問題及其所依賴的資源。|<p>[流量管理員的計量與警示](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[標準負載等化器的 Azure 監視器診斷](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[監視 Azure 防火牆記錄和計量](../firewall/tutorial-diagnostics.md)</p><p>[Azure Web 應用程式防火牆的監視和記錄](../frontdoor/waf-front-door-monitor.md)</p>|
|[虛擬網路 TAP](#vnettap)|提供虛擬機器網路流量到資料包收集器的連續流，支援網路和應用程式性能管理解決方案和安全分析工具|[創建 VNet TAP 資源](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>網路監看員
Azure 網路監看員提供了相關工具，可對 Azure 虛擬網路中的資源進行監視、診斷、檢視計量，以及啟用或停用記錄。 有關詳細資訊，請參閱[什麼是網路觀察程式？](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>快速路由監視器
要瞭解如何查看 ExpressRoute 電路指標、診斷日誌和警報，請參閱[ExpressRoute 監視、指標和警報](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)。
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure 監視器
Azure 監視器可藉由提供全方位的解決方案，以便收集、分析及處理來自雲端和內部部署環境的遙測資料，進而將應用程式的可用性和效能最大化。 它可協助您了解您的應用程式表現如何，並主動識別影響它們的問題以及它們所依賴的資源。 有關詳細資訊，請參閱[Azure 監視器概述](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)。
### <a name="virtual-network-tap"></a><a name="vnettap"></a>虛擬網路 TAP
Azure 虛擬網路 TAP (終端機存取點) 可讓您持續將虛擬機器網路流量串流到網路封包收集器或分析工具。 收集器或分析工具由[網路虛擬裝置](https://azure.microsoft.com/solutions/network-appliances/)合作夥伴提供。 

下圖顯示虛擬網路 TAP 的運作方式。 

![虛擬網路 TAP 的運作方式](./media/networking-overview/virtual-network-tap-architecture.png)

有關詳細資訊，請參閱[什麼是虛擬網路 TAP](../virtual-network/virtual-network-tap-overview.md)。

## <a name="next-steps"></a>後續步驟

- 完成[建立第一個虛擬網路](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)一文中的步驟，以建立第一個 VNet，並將一些 VM 連線至該 VNet。
- 通過完成["配置點對點連接"一文](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)中的步驟，將電腦連接到 VNet。
- 完成[建立網際網路對向負載平衡器](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)一文中的步驟，將網際網路流量負載平衡到公用伺服器。
 
 
   
