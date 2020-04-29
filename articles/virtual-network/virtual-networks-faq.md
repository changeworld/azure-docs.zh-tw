---
title: Azure 虛擬網路常見問題集
titlesuffix: Azure Virtual Network
description: Microsoft Azure 虛擬網路的常見問題解答。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: kumud
ms.openlocfilehash: e45d5393833973889b28a95ec86b89593a091f99
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244806"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Azure 虛擬網路的常見問題 (FAQ)

## <a name="virtual-network-basics"></a>虛擬網路基本概念

### <a name="what-is-an-azure-virtual-network-vnet"></a>什麼是 Azure 虛擬網路 (VNet)？
Azure 虛擬網路 (VNet) 是您的網路在雲端中的身分。 它是專屬於您訂用帳戶的 Azure 雲端邏輯隔離。 您可以使用 VNet 在 Azure 中佈建和管理虛擬私人網路 (VPN)，也可以選擇性地連結 VNet 與其他 Azure 中的 VNet，或連結您的內部部署 IT 基礎結構，以便建立混合式或跨單位的解決方案。 您所建立的每個 VNet 都有自己的 CIDR 區塊，且只要 CIDR 區塊沒有重迭，就可以連結至其他 Vnet 和內部部署網路。 針對 VNet 和分割的 VNet 子網路，您也擁有 DNS 伺服器設定的控制項。

您可以使用 VNet：

* 建立專用的私人僅限雲端 VNet。 針對您的解決方案，有時候您不需要跨單位組態。 當您建立 VNet 時，您的服務和 VNet 中的 VM 可以直接且安全地在雲端中彼此通訊。 您仍然可以設定 VM 的端點連線，以及需要網際網路通訊作為解決方案一部分的服務。

* 安全地擴充您的資料中心。 您可以使用 VNet 建置傳統的站台對站台 (S2S) VPN，安全地擴充資料中心容量。 S2S VPN 使用 IPSEC 在您的公司 VPN 閘道與 Azure 之間提供安全連接。

* 啟用混合式雲端案例。 VNet 可讓您彈性地支援許多混合式雲端案例。 您可以將雲端型應用程式安全地連接到任何類型的內部部署系統，例如大型主機和 Unix 系統。

### <a name="how-do-i-get-started"></a>如何開始？
請造訪[虛擬網路檔](https://docs.microsoft.com/azure/virtual-network/)以開始使用。 本內容提供所有 VNet 功能的概觀和部署資訊。

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>我可以使用不包含跨單位連線的 VNet 嗎？
是。 您可以使用 VNet，而不用將它連線到您的單位。 例如，您可以在 Azure VNet 中單獨執行 Microsoft Windows Server Active Directory 網域控制站和 SharePoint 伺服器陣列。

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>我可以在一或多個 VNet 與我的內部部署資料中心之間執行 WAN 最佳化嗎？
是。 您可以透過 Azure Marketplace，部署來自多個廠商的 [WAN 最佳化網路的虛擬應用裝置](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization)。

## <a name="configuration"></a>設定

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>我可以使用哪些工具來建立 VNet？
您可以使用下列工具來建立或設定 VNet：

* Azure 入口網站
* PowerShell
* Azure CLI
* 網路組態檔 (netcfg - 僅適用於傳統 VNet)。 請參閱[使用網路組態檔設定 VNet](virtual-networks-using-network-configuration-file.md)一文。

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>我可以在 VNet 中使用哪些位址範圍？
[RFC 1918](https://tools.ietf.org/html/rfc1918) 中定義的任何 IP 位址範圍。 例如：10.0.0.0/16。 您無法新增下列位址範圍︰
* 224.0.0.0/4 (多點傳送)
* 255.255.255.255/32 (廣播)
* 127.0.0.0/8 (回送)
* 169.254.0.0/16 (連結-本機)
* 168.63.129.16/32 (內部 DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>我可以在 VNet 擁有公用 IP 位址嗎？
是。 如需有關公用 IP 位址範圍的詳細資訊，請參閱[建立虛擬網路](manage-virtual-network.md#create-a-virtual-network)。 您無法直接從網際網路存取公用 IP 位址。

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>我的 VNet 中的子網路數目是否有限制？
是。 如需詳細資訊，請參閱 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)。 子網路位址空間不能互相重疊。

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>在這些子網路內使用 IP 位址是否有任何限制？
是。 Azure 會在每個子網路中保留 5 個 IP 位址。 這些是 node.js，也就是子網的最後一個位址： x. x. x. 3。 在 Azure 服務的每個子網中，會保留 x. x. x x. 3。   
- x.x.x.x。0：網路位址
- x. x. 1：由 Azure 為預設閘道保留
- x. x. 2、x. x. 3：由 Azure 保留以將 Azure DNS 的 Ip 對應到 VNet 空間
- x.x.x.x：網路廣播位址

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>VNet 和子網路的大小限制為何？
支援的最小 IPv4 子網為/29，最大值為/8 （使用 CIDR 子網定義）。  IPv6 子網的大小必須完全為/64。  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>我可以使用 VNet 將 VLAN 帶到 Azure 嗎？
否。 VNet 是 Layer-3 重疊。 Azure 不支援任何 Layer-2 語意。

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>可以在我的 Vnet 和子網路上指定自訂路由原則嗎？
是。 您可以建立路由表，並且將它與子網路產生關聯。 如需 Azure 中路由的詳細資訊，請參閱[路由概觀](virtual-networks-udr-overview.md#custom-routes)。

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNet 是否支援多點傳送或廣播？
否。 不支援多點傳送與廣播。

### <a name="what-protocols-can-i-use-within-vnets"></a>我可以在 VNet 中使用哪些通訊協定？
您可以在 VNet 中使用 TCP、UDP 和 ICMP TCP/IP 通訊協定。 VNet 內可支援單點傳播，但透過單點傳播 (來源連接埠 UDP/68 / 目的地連接埠 UDP/67) 的動態主機設定通訊協定 (DHCP) 除外。 多點傳送、廣播、IP-in-IP 封裝式封包和 Generic Routing Encapsulation (GRE) 封包在 VNet 內會遭到封鎖。 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>我可以在 VNet 中偵測我的預設路由器嗎？
否。

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>我可以使用 tracert 來診斷連線嗎？
否。

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>我可以在建立 VNet 之後新增子網路嗎？
是。 只要子網路位址範圍不是其他子網路的一部分，而且虛擬網路的位址範圍中有可用空間，子網路就可以隨時新增至 VNet。

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>我可以在建立子網路之後修改其大小嗎？
是。 如果其中沒有部署任何 VM 或服務，您可以新增、移除、展開或縮小子網路。

### <a name="can-i-modify-subnets-after-i-created-them"></a>我可以在建立子網路之後進行修改嗎？
是。 您可以加入、移除和修改 VNet 所使用的 CIDR 區塊。

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>如果我在 VNet 中執行服務，我可以連線到網際網路嗎？
是。 部署在 VNet 中的所有服務皆可輸出連線到網際網路。 若要深入了解 Azure 中的輸出網際網路連線，請參閱[輸出連線](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 如果您想要輸入連線到透過 Resource Manager 所部署的資源，該資源必須已獲派公用 IP 位址。 若要深入了解公用 IP 位址，請參閱[公用 IP 位址](virtual-network-public-ip-address.md)。 Azure 中部署的每個 Azure 雲端服務皆已指派公開可定址的 VIP。 您必須定義 PaaS 角色的輸入端點和虛擬機器的端點，啟用這些服務以接受來自網際網路的連接。

### <a name="do-vnets-support-ipv6"></a>VNet 是否支援 IPv6？
是，Vnet 可以是僅限 IPv4 或雙堆疊（IPv4 + IPv6）。  如需詳細資訊，請參閱[Azure 虛擬網路的 IPv6 總覽](./ipv6-overview.md)。

### <a name="can-a-vnet-span-regions"></a>VNet 可以跨區域嗎？
否。 VNet 僅限於單一區域。 不過，虛擬網路可以跨越可用性區域。 若要深入了解可用性區域，請參閱[可用性區域概觀](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 您可以使用虛擬網路對等互連，與不同區域中的虛擬網路連線。 如需詳細資訊，請參閱[虛擬網路對等互連概觀](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>我可以將 VNet 連線到 Azure 中的另一個 VNet 嗎？
是。 您可以使用下列其中一個項目，將一個 VNet 連線到另一個 VNet︰
- **虛擬網路對等互連**：如需詳細資訊，請參閱 [VNet 對等互連概觀](virtual-network-peering-overview.md)
- **Azure VPN 閘道**：如需詳細資訊，請參閱[設定 VNet 對 VNet 連線](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 

## <a name="name-resolution-dns"></a>名稱解析 (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>適用於 VNet 的 DNS 選項為何？
使用 [ [VM 和角色執行個體的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md) ] 頁面上的決策資料表來引導您完成所有可用的 DNS 選項。

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>我可以指定適用於 VNet 的 DNS 伺服器嗎？
是。 您可以在 VNet 設定中指定 DNS 伺服器 IP 位址。 此設定會套用為 VNet 中所有虛擬機器的預設 DNS 伺服器。

### <a name="how-many-dns-servers-can-i-specify"></a>我可以指定多少部 DNS 伺服器？
請參考 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)。

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>我可以在建立虛擬網路之後修改 DNS 伺服器嗎？
是。 您可以隨時針對 VNet 變更 DNS 伺服器清單。 如果您變更 DNS 伺服器清單，您必須在 VNet 中所有受影響的 Vm 上執行 DHCP 租用更新，新的 DNS 設定才會生效。 對於執行 Windows OS 的 Vm，您可以直接在`ipconfig /renew` VM 上輸入來完成這項操作。 如需其他 OS 類型，請參閱 DHCP 租用更新檔中的特定 OS 類型。 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>什麼是 Azure 提供的 DNS，以及它是否可搭配 VNet 使用？
Azure 提供的 DNS 是由 Microsoft 所提供的多租用戶 DNS 服務。 Azure 會註冊您在此服務中的所有 VM 和雲端服務角色執行個體。 這個服務可根據相同雲端服務內所包含 VM 和角色的主機名稱，以及根據 VM 的 FQDN 和相同 VNet 中的角色執行個體提供名稱解析功能。 若要深入了解 DNS，請參閱[虛擬機器與雲端服務角色執行個體的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)。

VNet 中的前 100 個雲端服務具有使用 Azure 所提供 DNS 進行跨租用戶名稱解析的限制。 如果您使用自己的 DNS 伺服器，則不適用這項限制。

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>我是否可以根據每個虛擬機器或雲端服務來覆寫 DNS 設定？
是。 您可以根據每個虛擬機器或雲端服務設定 DNS 伺服器，以便覆寫預設網路設定。 不過，建議您盡可能使用全網路 DNS。

### <a name="can-i-bring-my-own-dns-suffix"></a>我可以加上自己的 DNS 尾碼嗎？
否。 您無法針對 VNet 指定自訂的 DNS 尾碼。

## <a name="connecting-virtual-machines"></a>連接虛擬機器

### <a name="can-i-deploy-vms-to-a-vnet"></a>我可以將 VM 部署至 VNet 嗎？
是。 所有連接至透過 Resource Manager 部署模型部署的 VM 網路介面 (NIC) 必須連接到 VNet。 透過傳統部署模型部署的 VM 可以選擇連接至 VNet。

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>我可以將哪些類型的 IP 位址指派至 VM？
* **私人︰** 在每部 VM 內指派至每個 NIC。 您可使用靜態或動態方法來指派位址。 系統會根據您在 VNet 子網路設定中所指定的範圍來指派私人 IP 位址。 即使沒有連線至 VNet，透過傳統部署模型所部署的資源也會獲派私人 IP 位址。 配置方法的行為會隨著資源是以 Resource Manager 或傳統部署模型所部署的而有所不同： 

  - **Resource Manager**：以動態或靜態方法指派的私人 IP 位址會持續指派給虛擬機器 (Resource Manager)，直到資源刪除為止。 差異在於，使用靜態時是由您選取要指派的位址，使用動態時則是由 Azure 選擇。 
  - **傳統**：如果虛擬機器 (傳統) VM 在處於已停止 (已解除配置) 狀態之後又重新啟動，以動態方法所指派的私人 IP 位址可能會變更。 如果您需要確保透過傳統部署模型所部署之資源的私人 IP 位址永遠不會變更，請使用靜態方法來指派私人 IP 位址。

* **公用︰** 選擇性地指派至與透過 Azure Resource Manager 部署模型部署的 VM 所連接的 NIC。 您可使用靜態或動態配置方法來指派位址。 所有透過傳統部署模型部署的 VM 與雲端服務角色執行個體，皆存在於受指派動態** 公用虛擬 IP (VIP) 位址的雲端服務內。 公用*靜態* IP 位址，稱為[保留的 IP 位址](virtual-networks-reserved-public-ip.md)，可選擇性地被指派為 VIP。 您可以將公用 IP 位址指派至透過傳統部署模型部署的個別 VM 或雲端服務角色執行個體。 這些位址稱為[執行個體層級公用 IP (ILPIP](virtual-networks-instance-level-public-ip.md)位址，並可動態指派。

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>我可以為稍後建立的 VM 保留私人 IP 位址嗎？
否。 您不能保留私人 IP 位址。 如果有可用的私人 IP 位址，則會由 DHCP 伺服器指派至虛擬機器或角色執行個體。 該虛擬機器可能不是您想要指派私人 IP 位址的目的地。 不過，您可以將已建立虛擬機器的私人 IP 位址變更為任何可用的私人 IP 位址。

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>VNet 中的私人 IP 位址會根據 VM 進行變更嗎？
視情況而定。 如果是透過 Resource Manager 來部署 VM，不論 IP 位址是使用靜態還是動態配置方法指派的，都不會變更。 如果虛擬機器是透過傳統部署模型所部署的，當虛擬機器在處於已停止 (已解除配置) 狀態之後又啟動，動態 IP 位址會變更。 當虛擬機器遭到刪除時，位址會從透過任一種部署模型所部署的虛擬機器中釋放出來。

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>我可以在 VM 作業系統中將 IP 位址手動指派至 NIC 嗎？
是，但是除非必要 (例如，在指派多個 IP 位址給虛擬機器時)，否則不建議這麼做。 如需詳細資訊，請參閱[將多個 IP 位址新增至虛擬機器](virtual-network-multiple-ip-addresses-portal.md#os-config)。 如果指派給 Azure NIC (連結至虛擬機器) 的 IP 位址變更，且虛擬機器作業系統中的 IP 位址不同，您就會失去與虛擬機器的連線。

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>若我在作業系統內停止雲端服務部署位置或關閉 VM，我的 IP 位址會受到影響嗎？
不執行任何動作。 IP 位址 (公用 VIP、公用與私人) 仍會指派至雲端服務部署位置或 VM。

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>我可以將 VM 從子網路移動至 VNet 中的其他子網路而不需重新部署嗎？
是。 您可以在[如何將 VM 或角色執行個體移至不同的子網路](virtual-networks-move-vm-role-to-subnet.md)一文內找到更多資訊。

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>我可以針對 VM 設定靜態 MAC 位址嗎？
否。 MAC 位址無法以靜態方式設定。

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>當我建立虛擬機器之後 MAC 位址會保持相同的狀態嗎？
是，若是建立透過 Resource Manager 和傳統部署模型部署的 VM，MAC 位址會維持不變，直至刪除為止。 以往，當 VM 狀態為已停止 (已重新分配) 時，系統會釋放 MAC 位址，但現在即使 VM 狀態為重新分配時，MAC 位址仍會保持不變。 在網路介面遭到刪除或指派給主要網路介面之主要 IP 組態的私人 IP 位址遭到變更之前，MAC 位址會保持指派給網路介面。 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>我可以從 VNet 中的 VM 連線到網際網路嗎？
是。 部署在 VNet 中的所有 VM 與雲端服務角色執行個體皆可連線到網際網路。

## <a name="azure-services-that-connect-to-vnets"></a>連線到 VNet 的 Azure 服務

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>我可以搭配使用 Azure App Service Web Apps 和 VNet 嗎？
是。 您可以使用 ASE （App Service 環境）在 VNet 內部署 Web Apps、使用 VNet 整合將應用程式的後端連線到 Vnet，以及使用服務端點鎖定應用程式的輸入流量。 如需詳細資訊，請參閱下列文章：

* [App Service 網路功能](../app-service/networking-features.md)
* [在 App Service 環境中建立 Web Apps](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [將您的應用程式與 Azure 虛擬網路整合](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [App Service 存取限制](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>我可以在 VNet 中使用 Web 和背景工作角色 (PaaS) 部署雲端服務嗎？
是。 您可以 (選擇性地) 在 VNet 內部署雲端服務角色執行個體。 若要這樣做，請在服務組態的網路組態區段中，指定 VNet 名稱和角色/子網路對應。 您不需要更新任何二進位檔。

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>是否可以將虛擬機器擴展集連接到 VNet？
是。 您必須將虛擬機器擴展集連線到 VNet。

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>是否有可以從中將資源部署至 VNet 的完整 Azure 服務清單？
是，如需詳細資訊，請參閱 [Azure 服務的虛擬網路整合](virtual-network-for-azure-services.md)。

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>如何限制從 VNet 存取 Azure PaaS 資源？

透過某些 Azure PaaS 服務（例如 Azure 儲存體和 Azure SQL Database）部署的資源，可以透過使用虛擬網路服務端點或 Azure 私人連結來限制對 VNet 的網路存取。 如需詳細資訊，請參閱[虛擬網路服務端點總覽](virtual-network-service-endpoints-overview.md)、 [Azure 私用連結總覽](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>我可以將服務移入和移出 VNet 嗎？
否。 您無法將服務移入和移出 VNet。 若要將資源移至另一個 VNet，您必須刪除並重新部署資源。

## <a name="security"></a>安全性

### <a name="what-is-the-security-model-for-vnets"></a>什麼是 VNet 的安全性模型？
VNet 會與另一個 VNet，以及其他裝載於 Azure 基礎結構中的服務隔離。 VNet 是一種信任邊界。

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>我可以將輸入或輸出流量限制為與 VNet 連接的資源嗎？
是。 您可以將 [網路安全性群組](security-overview.md) 套用至 VNet、連接至 VNet 的 NIC 或同時在兩者以內的獨立子網路。

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>我可以在與 VNet 連線的資源之間實作防火牆嗎？
是。 您可以透過 Azure Marketplace 部署來自多個廠商的[防火牆網路虛擬應用裝置](https://azure.microsoft.com/marketplace/?term=firewall)。

### <a name="is-there-information-available-about-securing-vnets"></a>我可以怎樣取得關於保護 VNet 的資訊？
是。 如需詳細資訊，請參閱 [Azure 網路安全性概觀](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="apis-schemas-and-tools"></a>API、結構描述和工具

### <a name="can-i-manage-vnets-from-code"></a>我可以從程式碼管理 VNet 嗎？
是。 您可以在[Azure Resource Manager](/rest/api/virtual-network)和[傳統](https://go.microsoft.com/fwlink/?LinkId=296833)部署模型中使用適用于 vnet 的 REST api。

### <a name="is-there-tooling-support-for-vnets"></a>是否有工具支援 VNet？
是。 深入了解如何使用：
- Azure 入口網站來透過 [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) 與 [傳統](virtual-networks-create-vnet-classic-pportal.md)部署模型部署 VNet。
- PowerShell 來透過 [Resource Manager](/powershell/module/az.network)與[傳統](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0)部署模型管理 VNet。
- Azure 命令列介面 (CLI)，來部署與管理透過 [Resource Manager](/cli/azure/network/vnet) 與[傳統](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources)部署模型所部署的 VNet。  

## <a name="vnet-peering"></a>VNet 對等互連

### <a name="what-is-vnet-peering"></a>什麼是 VNet 對等互連？
VNet 對等互連 (或虛擬網路對等互連) 可讓您將虛擬網路連線。 虛擬網路之間的 VNet 對等互連連線可讓您私下透過 IPv4 位址在虛擬網路之間路由傳送流量。 所對等互連 VNet 中的虛擬機器可以彼此通訊，彷彿它們位於相同的網路內。 這些虛擬網路可位於相同或不同的區域 (也稱為全域 VNet 對等互連)。 VNet 對等互連連線也可以跨 Azure 訂用帳戶來建立。

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>我是否可對不同區域中的 VNet 建立對等互連連線？
是。 全域 VNet 可讓您對不同區域中的對等 VNet 建立對等互連。 全球 VNet 對等互連適用于所有 Azure 公用區域、中國雲端區域和政府雲端區域。 您無法從 Azure 公用區域全域對等互連至國家雲端區域。

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>全域 VNet 對等互連和負載平衡器有哪些相關聯的條件約束？
如果兩個不同區域中的兩個虛擬網路是透過全域 VNet 對等互連所對等互連，您就無法連線到位於基本 Load Balancer 後方的資源，Load Balancer 的前端 IP。 Standard Load Balancer 不存在此限制。
下列資源可以使用基本負載平衡器，這表示您無法透過全域 VNet 對等互連上的 Load Balancer 前端 IP 來連接它們。 不過，您可以使用全域 VNet 對等互連，直接透過其私人 VNet Ip 連線到資源（如果允許的話）。 
- 基本負載平衡器後方的 Vm
- 具有基本負載平衡器的虛擬機器擴展集 
- Redis 快取 
- 應用程式閘道（v1） SKU
- Service Fabric
- SQL MI
- API 管理
- Active Directory 網域服務（新增）
- Logic Apps
- HDInsight
-   Azure Batch
- App Service 環境

您可以透過 ExpressRoute 或透過 VNet 閘道的 VNet 對 VNet 來連接到這些資源。

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>如果我的虛擬網路屬於不同 Azure Active Directory 租用戶中的訂用帳戶，我是否可啟用 VNet 對等互連？
是。 如果您的訂用帳戶屬於不同的 Azure Active Directory 租用戶，則無法建立 VNet 對等互連 (不論是本機還是全域)。 您可以透過 PowerShell 或 CLI 執行此動作。 尚不支援入口網站。

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>我的 VNet 對等互連連線處於「已起始」** 狀態，但為何無法連線？
如果您的對等互連連線處於*起始*狀態，這表示您只建立了一個連結。 必須建立雙向連結，才能建立成功的連線。 例如，若要建立 VNet A 對 VNet B 的對等互連，則必須建立從 VNetA 到 VNetB 和從 VNetB 到 VNetA 的連結。 建立這兩個連結會將狀態變更為 [*已連線*]。

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>我的 VNet 對等互連連線處於「已中斷連線」** 狀態，為何我無法將連線對等互連？
如果您的 VNet 對等互連連線處於*中斷*連線的狀態，則表示已刪除其中一個已建立的連結。 若要重新建立對等互連連線，您必須刪除該連結並加以重新建立。

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>我的 VNet 是否可與不同訂用帳戶中的 VNet 對等互連？
是。 您可以跨訂用帳戶和跨區域建立 VNet 的對等互連。

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>我是否可將兩個具有相同或重疊位址範圍的 VNet 對等互連？
否。 位址空間不可重疊，才能啟用 VNet 對等互連。

### <a name="how-much-do-vnet-peering-links-cost"></a>VNet 對等互連連結的費用為何？
建立 VNet 對等互連連線無需收費。 透過對等互連連線的資料傳輸才會收費。 請參閱[這裡](https://azure.microsoft.com/pricing/details/virtual-network/)。

### <a name="is-vnet-peering-traffic-encrypted"></a>VNet 對等互連流量是否會加密？
否。 在對等互連的 VNet 中，資源之間的流量是私人且隔離的。 流量會完全保留在 Microsoft 骨幹上。

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>為什麼我的對等互連連線處於*中斷*線上狀態？
一個 VNet 對等互連連結遭到刪除時，VNet 對等互連連線即會進入「中斷連線」** 狀態。 您必須將兩個連結都刪除，以重新建立成功的對等互連連線。

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>如果我建立 VNetA 到 VNetB 的對等互連，然後又建立 VNetB 到 VNetC 的對等互連，這是否表示 VNetA 與 VNetC 之間會有對等互連？
否。 目前不支援轉移的對等互連。 為此，您必須直接建立 VNetA 與 VNetC 的對等互連。

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>對等互連連線是否有任何頻寬限制？
否。 VNet 對等互連 (不論本機或全域) 並未施加任何頻寬限制。 頻寬只受限於 VM 或計算資源。

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>如何對 VNet 對等互連問題進行疑難排解？
以下是您可以嘗試的[疑難排解員指南](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues)。

## <a name="virtual-network-tap"></a>虛擬網路 TAP

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>哪些 Azure 區域可用於虛擬網路 TAP？
虛擬網路點預覽適用于所有 Azure 區域。 受監視的網路介面、虛擬網路 TAP 資源以及收集器或分析解決方案，都必須部署在相同的區域。

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>虛擬網路 TAP 是否支援鏡像封包的任何篩選功能？
虛擬網路 TAP 預覽版不支援篩選功能。 將 TAP 設定新增到網路介面時，網路介面上所有連入和連出流量的深層複本將會串流處理到 TAP 目的地。

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>是否可以將多個 TAP 設定新增到受監視的網路介面？
受監視的網路介面只能有一個 TAP 設定。 請洽詢個別的[合作夥伴解決方案](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions)，以取得將流量分散到您選擇之分析工具的多個複本的功能。

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>相同的虛擬網路 TAP 資源，是否可以彙總來自多個虛擬網路中受監視的網路介面的流量？
是。 相同的虛擬網路 TAP 資源可用來在相同訂用帳戶或不同訂用帳戶中，彙總對等互連虛擬網路中之受監視的網路介面的鏡像流量。 虛擬網路 TAP 資源和目的地負載平衡器或目的地網路介面，必須位於相同的訂用帳戶中。 所有訂用帳戶都必須位於相同的 Azure Active Directory 租用戶下。

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>如果我啟用網路介面上的虛擬網路 TAP 設定，是否有任何關於生產流量的效能考量？

虛擬網路點為預覽狀態。 在預覽期間，沒有任何服務等級協定。 該功能不應該用於生產工作負載。 當使用點設定啟用虛擬機器網路介面時，會使用 Azure 主機上配置給虛擬機器的相同資源來傳送生產環境流量，以便用來執行鏡像功能並傳送鏡像封包。 選取正確的 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器大小，以確保有足夠的資源可供虛擬機器傳送生產流量和鏡像流量。

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>虛擬網路 TAP 是否支援 [Linux](create-vm-accelerated-networking-cli.md) 或 [Windows](create-vm-accelerated-networking-powershell.md) 的加速網路？

您將能夠在連結到已啟用加速網路之虛擬機器上的網路介面上，新增 TAP 設定。 但是，新增 TAP 設定會影響虛擬機器的效能與延遲，因為 Azure 加速網路目前不支援鏡像流量的卸載。

## <a name="virtual-network-service-endpoints"></a>虛擬網路服務端點

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>對 Azure 服務設定服務端點的正確作業順序為何？
透過服務端點來保護 Azure 服務資源的步驟有兩個：
1. 針對 Azure 服務開啟服務端點。
2. 在 Azure 服務上設定 VNet ACL。

第一個步驟是網路端作業，第二個步驟是服務資源端作業。 根據授與系統管理員角色的 RBAC 權限，兩個步驟可以是由相同的或不同的系統管理員來執行。 我們建議您先針對您的虛擬網路開啟服務端點，再於 Azure 服務端設定 VNet ACL。 因此，必須按照上列循序執行步驟，以設定 VNet 服務端點。

>[!NOTE]
> 您必須先完成上述兩個作業，才能限制 Azure 服務存取所允許的 VNet 和子網路。 只在網路端針對 Azure 服務開啟服務端點不能提供您受限制的存取。 此外，您也必須在 Azure 服務端設定 VNet ACL。

某些服務（例如 SQL 和 CosmosDB）允許上述序列的例外狀況通過**IgnoreMissingVnetServiceEndpoint**旗標。 一旦將旗標設定為**True**，就可以在 Azure 服務端設定 VNet acl，再于網路端設定服務端點。 Azure 服務提供此旗標來協助客戶避免連線中斷，若 IP 防火牆是設定在 Azure 服務上，在網路端開啟服務端點可能會導致連線中斷，因為來源 IP 從公用 IPv4 位址變更為私人位址。 先在 Azure 服務端設定 VNet ACL，再於網路端設定服務端點有助於避免連線中斷。

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>是否所有的 Azure 服務都位在客戶提供的 Azure 虛擬網路中？ VNet 服務端點如何與 Azure 服務搭配運作？

否，並非所有的 Azure 服務都位在客戶的虛擬網路中。 大部分的 Azure 資料服務（例如 Azure 儲存體、Azure SQL 和 Azure Cosmos DB）都是可透過公用 IP 位址存取的多租使用者服務。 您可以在[這裡](virtual-network-for-azure-services.md)深入了解 Azure 服務的虛擬網路整合。 

當您使用 VNet 服務端點功能 (在網路端開啟 VNet 服務端點並在 Azure 服務端設定適當的 VNet ACL) 時，從允許的 VNet 和子網路存取 Azure 服務會受限。

### <a name="how-does-vnet-service-endpoint-provide-security"></a>VNet 服務端點如何提供安全性？

VNet 服務端點功能（在網路端開啟 VNet 服務端點並在 Azure 服務端設定適當的 VNet Acl）會限制 Azure 服務對允許的 VNet 和子網的存取，進而提供網路層級安全性和 Azure 服務流量的隔離。 使用 VNet 服務端點的所有流量都流經 Microsoft 骨幹，因此提供與公用網際網路的另一層隔離。 此外，客戶可選擇完全移除公用網際網路對 Azure 服務資源的存取權，僅允許來自其虛擬網路的流量 (透過 IP 防火牆和 VNet ACL 的組合)，進而保護 Azure 服務資源不會被未經授權的使用者存取。      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>VNet 服務端點保護的是 VNet 資源或是 Azure 服務？
VNet 服務端點是協助保護 Azure 服務資源。 VNet 資源是由「網路安全性群組」(NSG) 來保護。

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>使用 VNet 服務端點是否有任何成本？

否，使用 VNet 服務端點沒有額外成本。

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>如果虛擬網路和 Azure 服務資源屬於不同子訂用帳戶，我是否可以開啟 VNet 服務端點並設定 VNet ACL？

是，可以的。 虛擬網路和 Azure 服務資源可以位在相同或不同的訂用帳戶中。 唯一的需求是虛擬網路和 Azure 服務資源必須在相同的 Active Directory (AD) 租用戶下。

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>如果虛擬網路和 Azure 服務資源屬於不同 AD 租用戶，是否可開啟 VNet 服務端點並設定 VNet ACL？
否，跨 AD 租用戶的情況不支援 VNet 服務端點和 VNet ACL。

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>透過 Azure 虛擬網路閘道（VPN）或 ExpressRoute 閘道連線的內部部署裝置 IP 位址，是否可透過 VNet 服務端點存取 Azure PaaS 服務？
根據預設，從內部部署網路無法觸達放到虛擬網路保護的 Azure 服務資源。 如果需要允許來自內部部署的流量，您也必須允許內部部署或 ExpressRoute 中的公用 (通常是 NAT) IP 位址。 透過 Azure 服務資源的 IP 防火牆設定，可以新增這些 IP 位址。

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>我可以使用 VNet 服務端點功能來保護虛擬網路內或跨多個虛擬網路中的多個子網的 Azure 服務嗎？
若要將 Azure 服務保護到虛擬網路或多個虛擬網路內的多個子網，請在每個子網上的網路端啟用服務端點，然後在 Azure 服務端設定適當的 VNet Acl，以保護所有子網的 Azure 服務資源。
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>如何篩選從虛擬網路流向 Azure 服務的輸出流量，但仍然使用服務端點？
如果您想檢查或篩選從虛擬網路送到 Azure 服務的流量，可以在虛擬網路內部署網路虛擬設備。 接著，您可以將服務端點套用到網路虛擬設備部署所在的子網路，並透過 VNet ACL 將 Azure 服務資源只保護到該子網路。 如果您想要使用網路虛擬設備篩選，讓來自虛擬網路的 Azure 服務只能存取特定 Azure 資源，則此案例可能也有幫助。 如需詳細資訊，請參閱[使用網路虛擬設備輸出](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)。

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>當您存取已從 VNet 外部啟用虛擬網路存取控制清單（ACL）的 Azure 服務帳戶時，會發生什麼事？
傳回 HTTP 403 或 HTTP 404 錯誤。

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>在不同區域中建立的虛擬網路是否可存取另一個區域中的 Azure 服務帳戶? 
是，對於大部分的 Azure 服務，在不同區域中建立的虛擬網路可透過 VNet 服務端點存取位在另一個區域中的 Azure 服務。 例如，如果 Azure Cosmos DB 帳戶位於美國西部或美國東部，而虛擬網路位於多個區域，則虛擬網路可存取 Azure Cosmos DB。 儲存體和 SQL 是例外，它們本質上具有地區性，且虛擬網路和 Azure 服務都需要在相同區域中。
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Azure 服務可以同時具有 VNet ACL 和 IP 防火牆嗎？
是，VNet ACL 和 IP 防火牆可以並存。 這兩個功能彼此互補以確保隔離和安全性。
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>如果刪除的虛擬網路或子網路有針對 Azure 服務開啟的服務端點，會發生什麼事？
VNet 和子網域的刪除是獨立的作業，即使已針對 Azure 服務開啟服務端點也支援。 如果 Azure 服務已設定 VNet Acl，針對那些 Vnet 和子網，當已開啟 VNet 服務端點的 VNet 或子網被刪除時，與該 Azure 服務相關聯的 VNet ACL 資訊將會停用。
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>如果已刪除已啟用 VNet 服務端點的 Azure 服務帳戶，會發生什麼事？
Azure 服務帳戶的刪除是獨立的作業，即使已在網路端啟用服務端點，且已在 Azure 服務端設定 VNet Acl，也支援這項作業。 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>啟用 VNet 服務端點的資源 (如子網路中的 VM) 的 IP 位址會發生什麼事？
當虛擬網路服務端點啟用時，您虛擬網路中的資源用來傳送流量至 Azure 服務的來源 IP 位址，會從使用公用 IPV4 位址切換為使用 Azure 虛擬網路的私人位址。 請注意，這可能會導致先前在 Azure 服務上設定為公用 IPV4 位址的特定 IP 防火牆失敗。 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>服務端點路由是否一律會優先處理？
服務端點會新增系統路由，其優先順序高於 BGP 路由，並提供服務端點流量的最佳路由。 服務端點接受的服務流量，一律都是直接從您的虛擬網路到 Microsoft Azure 骨幹網路上的服務。 如需 Azure 如何選取路由的詳細資訊，請參閱[Azure 虛擬網路流量路由](virtual-networks-udr-overview.md)。
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>子網路上的 NSG 如何搭配服務端點運作？
為了與 Azure 服務建立連線，NSG 需要允許輸出連線。 如果您的 NSG 對所有網際網路輸出流量都是開放的，服務端點流量應該就能運作。 您也可以使用「服務」標籤，限制輸出流量只有流向服務 IP 的流量。  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>設定服務端點需要哪些權限？
擁有虛擬網路寫入權的使用者，可以在虛擬網路上個別設定服務端點。 若要保護 VNet 的 Azure 服務資源，使用者必須擁有所要新增之子網的**virtualNetworks/subnet/joinViaServiceEndpoint/action**許可權。 此權限預設會隨附在內建的服務管理員角色中，可藉由建立自訂角色加以修改。 深入了解內建角色以及如何將特定權限指派給[自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json)。
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>是否能篩選流向 Azure 服務的虛擬網路流量，僅允許特定 Azure 服務資源的流量經過 VNet 服務端點？ 

虛擬網路 (VNet) 服務端點原則可讓您篩選 Azure 服務的虛擬網路流量，僅允許特定 Azure 服務資源經過服務端點。 服務端點原則可針對流向 Azure 服務的虛擬網路流量提供細部的存取控制。 您可以[在此](virtual-network-service-endpoint-policies-overview.md)深入了解服務端點原則。

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Azure Active Directory （Azure AD）是否支援 VNet 服務端點？

Azure Active Directory （Azure AD）不會以原生方式支援服務端點。 您可以在[這裡](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)看到支援 VNet 服務端點的 Azure 服務完整清單。 請注意，[支援服務端點的服務] 底下所列的 "AzureActiveDirectory" 標記會用來支援服務端點，以 ADLS Gen 1。 對於 ADLS Gen 1，Azure Data Lake Storage Gen1 的虛擬網路整合會在您的虛擬網路與 Azure Active Directory （Azure AD）之間使用虛擬網路服務端點安全性，以在存取權杖中產生額外的安全性宣告。 這些宣告隨後會用來對 Data Lake Storage Gen1 帳戶驗證虛擬網路並允許存取。 深入瞭解[Azure Data Lake Store Gen 1 VNet 整合](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>從我的 VNet 能設定的 VNet 服務端點數量是否有限制？
虛擬網路中的 VNet 服務端點總數沒有限制。 對於 Azure 服務資源（例如 Azure 儲存體帳戶），服務可能會強制執行用來保護資源的子網數目限制。 下表顯示一些範例限制： 

|||
|---|---|
|Azure 服務| VNet 規則的限制|
|Azure 儲存體| 100|
|Azure SQL| 128|
|Azure SQL 資料倉儲|  128|
|Azure 金鑰保存庫|    127|
|Azure Cosmos DB|   64|
|Azure 事件中樞|   128|
|Azure 服務匯流排| 128|
|Azure Data Lake Store V1|  100|
 
>[!NOTE]
> 限制會隨 Azure 服務而變更。 請參閱個別的服務文件以取得服務詳細資料。 




  



