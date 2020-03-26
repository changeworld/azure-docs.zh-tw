---
title: Azure 虛擬網路 | Microsoft Docs
description: 了解 Azure 虛擬網路概念和功能。
services: virtual-network
documentationcenter: na
author: anavinahar
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: anavin
ms.openlocfilehash: 3b908406c8717d2fa8834bc4dff1bcd27ec4761f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79290196"
---
# <a name="what-is-azure-virtual-network"></a>什麼是 Azure 虛擬網路？

Azure 虛擬網路 (VNet) 是私人網路在 Azure 中的基本建置組塊。 VNet 可讓多種類型的 Azure 資源 (例如 Azure 虛擬機器 (VM)) 安全地彼此通訊，以及與網際網路和內部部署網路通訊。 VNet 與您自有資料中心內所運作的傳統網路類似，但會有更多 Azure 基礎結構的好處，例如調整規模、可用性和隔離。

## <a name="vnet-concepts"></a>VNet 概念

- **位址空間︰** 在建立 VNet 時，您必須使用公用和私人 (RFC 1918) 位址指定自訂私人 IP 位址空間。 Azure 會從您指派的位址空間，將私人 IP 位址指派給虛擬網路中的資源。 例如，如果您在位址空間為 10.0.0.0/16 的 VNet 中部署 VM，系統就會對 VM 指派像是 10.0.0.4 等的私人 IP。
- **子網路：** 子網路可讓您將虛擬網路分成一或多個次網路，並將虛擬網路位址空間的一部分配置給每個子網路。 然後，您便可以在特定子網路中部署 Azure 資源。 和傳統網路一樣，子網路也可讓您將 VNet 位址空間分成多個區段，以便適合組織的內部網路使用。 這也會改善位址的配置效率。 您可以使用網路安全性群組來保護子網路內的資源。 如需詳細資訊，請參閱[安全性群組](security-overview.md)。
- **區域**：VNet 的範圍為單一區域/位置；不過，您可以使用虛擬網路對等互連將不同區域的多個虛擬網路連在一起。
- **訂用帳戶︰** VNet 的範圍為訂用帳戶。 您可以在每個 Azure [訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和 Azure [區域](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region)內實作多個虛擬網路。

## <a name="best-practices"></a>最佳作法

當您 Azure 中建置網路時，請務必記住下列通用設計原則：

- 確保沒有重疊的位址空間。 確定 VNet 位址空間 (CIDR 區塊) 不會與組織的其他網路範圍重疊。
- 子網路不應涵蓋 VNet 的整個位址空間。 預先規劃並保留部分位址空間供日後使用。
- 建議您擁有少量大型 VNet 而非多個小型 VNet。 這可避免造成管理負荷。
- 將網路安全性群組 (NSG) 指派給其下的子網路，以保護您的 VNet。

## <a name="communicate-with-the-internet"></a>與網際網路通訊

依預設，VNet 中的所有資源都能夠進行對網際網路的輸出通訊。 您可以藉由指派公用 IP 位址或公用負載平衡器，對該項資源進行輸入通訊。 您也可以使用公用 IP 或公用負載平衡器來管理您的輸出連線。  若要深入了解 Azure 中的輸出連線，請參閱[輸出連線](../load-balancer/load-balancer-outbound-connections.md)、[公用 IP 位址](virtual-network-public-ip-address.md)和[負載平衡器](../load-balancer/load-balancer-overview.md)。

>[!NOTE]
>僅使用內部 [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) 時無法建立輸出連線，除非您定義[輸出連線](../load-balancer/load-balancer-outbound-connections.md)要如何與執行個體層級的公用 IP 或公用負載平衡器搭配運作。

## <a name="communicate-between-azure-resources"></a>Azure 資源之間的通訊

Azure 資源可透過下列其中一種方式安全地相互通訊：

- **透過虛擬網路**：您可以將虛擬機器和數種其他類型的 Azure 資源部署到虛擬網路，例如 Azure App Service Environment、Azure Kubernetes Service (AKS) 和 Azure 虛擬機器擴展集。 若要檢視可部署到虛擬網路中的 Azure 資源的完整清單，請參閱[虛擬網路服務整合](virtual-network-for-azure-services.md)。
- **透過虛擬網路服務端點**：透過直接連線，將您的虛擬網路私人位址空間與虛擬網路的身分識別延伸至 Azure 服務資源，例如 Azure 儲存體帳戶與 Azure SQL 資料庫。 服務端點可讓您將重要的 Azure 服務資源限用於虛擬網路，而加以保護。 若要深入了解，請參閱[虛擬網路服務端點概觀](virtual-network-service-endpoints-overview.md)。
- **透過 VNet 對等互連**：您可以讓虛擬網路彼此連線，以便虛擬網路中的資源能夠使用虛擬網路對等互連彼此通訊。 您所連線的虛擬網路可位於相同或不同的 Azure 區域。 若要深入了解，請參閱[虛擬網路對等互連](virtual-network-peering-overview.md)。

## <a name="communicate-with-on-premises-resources"></a>與內部部署資源通訊

您可以使用下列選項的任意組合，將內部部署電腦和網路連線至虛擬網路︰

- **點對站虛擬私人網路 (VPN)：** 建立於虛擬網路與您網路中的單一電腦之間。 每部想要與虛擬網路建立連線的電腦，都必須設定其連線。 如果您剛開始使用 Azure，此連線類型就很適合您，也適用於開發人員，因為它幾乎不需要變更您現有的網路。 您的電腦與虛擬網路之間的通訊，會在網際網路間透過加密通道傳送。 若要深入了解，請參閱[點對站 VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S)。
- **站對站 VPN：** 建立於您的內部部署 VPN 裝置與虛擬網路中部署的 Azure VPN 閘道之間。 此連線類型可讓您授權的任何內部部署資源存取虛擬網路。 您的內部部署 VPN 裝置與 Azure VPN 閘道之間的通訊，會在網際網路間透過加密通道傳送。 若要深入了解，請參閱[站對站 VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti)。
- **Azure ExpressRoute：** 透過 ExpressRoute 合作夥伴，建立於您的網路與 Azure 之間。 此連線是私人連線。 流量不會經由網際網路傳送。 若要深入了解，請參閱 [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute)。

## <a name="filter-network-traffic"></a>篩選網路流量

您可以使用下列一個或兩個選項，篩選子網路之間的網路流量︰

- **安全性群組：** 網路安全性群組可包含多個輸入和輸出安全性規則，讓您依照來源和目的地 IP 位址、連接埠和通訊協定篩選資源收送的流量。 若要深入了解，請參閱[網路安全性群組](security-overview.md#network-security-groups)或[應用程式安全性群組](security-overview.md#application-security-groups)。
- **網路虛擬設備：** 網路虛擬設備是執行網路功能的 VM，例如防火牆、WAN 最佳化或其他網路功能。 若要檢視可在虛擬網路中部署的網路虛擬設備，請參閱 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)。

## <a name="route-network-traffic"></a>路由網路流量

Azure 依預設會路由子網路、連線的虛擬網路、內部部署網路和網際網路之間的流量。 您可以實作下列一個或兩個選項，覆寫 Azure 所建立的預設路由︰

- **路由資料表︰** 你可以自訂路由表，設定傳送路線用來控制傳送到每個子網路的流量 深入了解[路由表](virtual-networks-udr-overview.md#user-defined)。
- **邊界閘道協定 (BGP) 路由：** 如果您使用 Azure VPN 閘道或 ExpressRoute 連線將虛擬網路連線至內部部署網路，則可將您的內部部署 BGP 路由傳播至虛擬網路。 深入了解如何透過 [Azure VPN 閘道](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和 [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange) 使用 BGP。

## <a name="azure-vnet-limits"></a>Azure VNet 的限制

您可以部署的 Azure 資源數量有一些限制。 大部分的 Azure 網路限制均在最大值。 不過，您可以[增加某些網路限制](../azure-portal/supportability/networking-quota-requests.md)，如 [VNet 限制頁面](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)所指定。 

## <a name="pricing"></a>定價

Azure VNet 沒有使用費，成本為零。 但資源會有標準費用，例如虛擬機器 (VM) 和其他產品。 若要深入了解，請參閱 [VNet 定價](https://azure.microsoft.com/pricing/details/virtual-network/)和 Azure[定價計算機](https://azure.microsoft.com/pricing/calculator/)。

## <a name="next-steps"></a>後續步驟

 若要開始使用虛擬網路，請建立一個虛擬網路、對其部署一些 VM，然後進行 VM 之間的通訊。 若要深入了解，請參閱[建立虛擬網路](quick-create-portal.md)快速入門。
