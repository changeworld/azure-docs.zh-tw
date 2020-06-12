---
title: Azure 虛擬網路的 IPv6 概觀
titlesuffix: Azure Virtual Network
description: IPv6 端點的 IPv6 描述和 Azure 虛擬網路中的資料路徑。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 5c175a1575a4efbdc2294412e3743e201d8c4bb1
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653293"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>什麼是 Azure 虛擬網路的 IPv6？

Azure 虛擬網路 (VNet) 的 IPv6 可讓您在 Azure 中裝載應用程式，並在虛擬網路內以及網際網路之間建立 IPv6 和 IPv4 連線。 由於公用 IPv4 位址耗盡，行動裝置和物聯網 (IoT) 的新網路通常會建置在 IPv6 上。 即使是成立很久的 ISP 和行動網路也都正在轉換為 IPv6。 僅適用於 IPv4 的服務會發現其在現有市場和新興市場都處於真正的劣勢。 雙重堆疊的 IPv4/IPv6 連線能力讓 Azure 裝載的服務能夠利用全球可用的雙重堆疊服務跨越這項技術缺口，輕鬆地與現有的 IPv4 和這些新的 IPv6 裝置與網路連線。

Azure 的原始 IPv6 連線能力可讓您輕鬆地為裝載於 Azure 中的應用程式提供雙重堆疊 (IPv4/IPv6) 網際網路連線能力。 其可讓您使用已負載平衡的 IPv6 連線，針對輸入和輸出起始的連線簡單地部署 VM。 這項功能仍然可供使用，[這裡](../load-balancer/load-balancer-ipv6-overview.md)有詳細資訊。
Azure 虛擬網路的 IPv6 有更完整的功能，可讓您在 Azure 中部署完整的 IPv6 解決方案架構。


下圖說明 Azure 中的簡單雙重堆疊 (IPv4/IPv6) 部署：

![IPv6 網路部署圖表](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>優點

Azure VNET 的 IPv6 優點：

- 協助擴展 Azure 裝載的應用程式並觸及到不斷成長的行動裝置和物聯網市場。
- 雙重堆疊的 IPv4/IPv6 VM 可提供最大的服務部署彈性。 單一服務執行個體可以與支援 IPv4 和 IPv6 的網際網路用戶端連線。
- 建置於成立很久且穩定的 Azure VM 對網際網路 IPv6 連線能力之上。
- 預設是安全的，因為只有在部署中明確要求時，才會建立網際網路的 IPv6 連線能力。

## <a name="capabilities"></a>功能

適用於 Azure VNet 的 IPv6 包含下列功能：

- Azure 客戶可以定義自己的 IPv6 虛擬網路位址空間，以符合其應用程式、客戶或緊密整合到其內部部署 IP 空間的需求。
- 具有雙重堆疊子網路的雙重堆疊 (IPv4 和 IPv6) 虛擬網路，可讓應用程式與其虛擬網路或網際網路中的 IPv4 和 IPv6 資源連線。
    > [!IMPORTANT]
    > IPv6 子網路的大小必須正好是 /64。  這可確保萬一日後您決定啟用子網路到內部部署網路的路由時，能夠保有相容性 (因為某些路由器只能接受 /64 IPv6 路由)。  
- 使用網路安全性群組的 IPv6 規則來保護您的資源。
    - 而且 Azure 平台的分散式阻斷服務 (DDoS) 保護會延伸到網際網路對向的公用 IP
- 使用使用者定義的路由來自訂虛擬網路中的 IPv6 流量路由 (特別是在利用網路虛擬設備來擴充您的應用程式時)。
- Linux 和 Windows 虛擬機器都可以使用 Azure VNET 的 IPv6
- [標準 IPv6 公用 Load Balancer](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 支援，可建立具有復原性、可擴縮的應用程式，其內含：
    - 選擇性的 IPv6 健康情況探查，可用來判斷哪個後端集區執行個體狀況良好，進而可以接收新的流程。
    - 選擇性輸出規則，可完全透過宣告來控制輸出連線，以針對您的特定需求擴縮和調整此功能。
    - 選擇性的多個前端設定，可讓單一負載平衡器使用多個 IPv6 公用 IP 位址，相同的前端通訊協定和連接埠可跨前端位址重複使用。
    - 選擇性的 IPv6 連接埠，可使用負載平衡規則的*浮動 IP* 功能在後端執行個體上重複使用 
    - 注意:負載平衡不會執行任何通訊協定轉譯 (無 NAT64)。 
    - 注意:IPv6 只能負載平衡到 Azure VM 上的主要網路介面 (NIC)。 
- [標準 IPv6 內部 Load Balancer](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) 支援，以在 Azure VNET 中建立具有復原性的多層式應用程式。   
- 基本的 IPv6 公用 Load Balancer 支援，可與舊版部署相容
- [保留的 IPv6 公用 IP 位址和位址範圍](ipv6-public-ip-address-prefix.md)可提供穩定且可預測的 IPv6 位址，可讓您輕鬆地將公司和客戶 Azure 裝載應用程式加入白名單。
- 執行個體層級的公用 IP 可直接對個別 VM 提供 IPv6 網際網路連線能力。
- [將 IPv6 新增至現有的僅限 IPv4 部署](ipv6-add-to-existing-vnet-powershell.md) - 這項功能可讓您輕鬆地將 IPv6 連線能力新增至現有的僅限 IPv4 部署，而不需要重新建立部署。  在此程序中，IPv4 網路流量不會受到影響，因此，視您的應用程式和作業系統而定，您甚至可以將 IPv6 新增到即時服務。    
- 讓網際網路用戶端使用其選擇、具有 Azure DNS IPv6 (AAAA) 記錄支援的通訊協定，順暢地存取雙重堆疊應用程式。 
- 建立雙重堆疊應用程式，以使用 IPv6 的虛擬機器擴展集自動擴縮為您的負載。
- [虛擬網路 (VNET) 對等互連](virtual-network-peering-overview.md) - 區域內和全域對等互連 - 可讓您順暢地連線雙重堆疊 VNET - 對等互連網路中 VM 上的 IPv4 和 IPv6 端點都能夠彼此通訊。 當您將部署轉換成雙重堆疊時，甚至可以將雙重堆疊與僅限 IPv4 的 VNET 對等互連。 
- IPv6 疑難排解和診斷適用於負載平衡器計量/警示和網路監看員功能，例如封包擷取、NSG 流量記錄、連線疑難排解和連線監視。   

## <a name="scope"></a>影響範圍
Azure VNET 的 IPv6 是基礎功能集，可讓客戶在 Azure 中裝載雙重堆疊 (IPv4+IPv6) 應用程式。  我們打算在一段時間後將 IPv6 支援新增至更多 Azure 網路功能，最終則要提供 Azure PaaS 服務的雙重堆疊版本，但在此同時，所有 Azure PaaS 服務都可透過雙重堆疊虛擬機器上的 IPv4 端點來存取。   

## <a name="limitations"></a>限制
目前的 Azure 虛擬網路 IPv6 版本有下列限制：
- Azure 虛擬網路的 IPv6 適用於使用所有部署方法的所有全球性 Azure 商業區域和美國政府區域。  
- 在已啟用 IPv6 的 VNET 中，ExpressRoute 閘道可用於僅限 IPv4 的流量。  支援 IPv6 流量已在我們的規劃中。   
- VPN 閘道不能用於已啟用 IPv6 的 VNET，不論是直接進行還是透過與 "UseRemoteGateway" 對等互連都不行。
- Azure 平台 (AKS 等) 不支援容器的 IPv6 通訊。  
- IPv6 只能負載平衡到 Azure VM 上的主要網路介面 (NIC)。 不支援將 IPv6 流量負載平衡至次要 NIC。    
- 不支援僅限 IPv6 的虛擬機器或虛擬機器擴展集，每個 NIC 都必須包含至少一個 IPv4 IP 設定。 
- 將 IPv6 新增至現有的 IPv4 部署時，不能將 IPv6 範圍新增至具有現有資源導覽連結的 VNET。  
- Azure 公用 DNS 目前支援轉送 IPv6 的正向 DNS，但尚未支援反向 DNS。   

## <a name="pricing"></a>定價

IPv6 Azure 資源和頻寬的收費費率與 IPv4 相同。 IPv6 沒有額外或不同的費用。 您可以找到[公用 IP 位址](https://azure.microsoft.com/pricing/details/ip-addresses/)、[網路頻寬](https://azure.microsoft.com/pricing/details/bandwidth/)或 [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) 的定價詳細資料。

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Azure PowerShell 部署 IPv6 雙重堆疊應用程式](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)。
- 了解如何[使用 Azure CLI 部署 IPv6 雙重堆疊應用程式](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)。
- 了解如何[使用 Resource Manager 範本 (JSON) 部署 IPv6 雙重堆疊應用程式](ipv6-configure-standard-load-balancer-template-json.md)
