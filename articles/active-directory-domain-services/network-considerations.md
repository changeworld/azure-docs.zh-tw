---
title: Azure AD Domain Services 的網路規劃和連接 |Microsoft Docs
description: 瞭解一些虛擬網路設計考慮，以及在執行 Azure Active Directory Domain Services 時用於連線的資源。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: c811240beea896683f891d9513a657b0689b8824
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87488647"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 的虛擬網路設計考慮和設定選項

Azure Active Directory Domain Services （Azure AD DS）提供驗證和管理服務給其他應用程式和工作負載。 網路連線是關鍵元件。 若未正確設定虛擬網路資源，應用程式和工作負載就無法與通訊，並使用 Azure AD DS 提供的功能。 規劃您的虛擬網路需求，確保 Azure AD DS 可以視需要為您的應用程式和工作負載提供服務。

本文概述 Azure 虛擬網路支援 Azure AD DS 的設計考慮和需求。

## <a name="azure-virtual-network-design"></a>Azure 虛擬網路設計

若要提供網路連線能力，並允許應用程式和服務對 Azure AD DS 受控網域進行驗證，您可以使用 Azure 虛擬網路和子網。 在理想的情況下，受控網域應該部署到自己的虛擬網路中。

您可以在相同的虛擬網路中包含個別的應用程式子網，以裝載您的管理 VM 或輕量應用程式工作負載。 針對較大型或複雜的應用程式工作負載，對等互連到 Azure AD DS 虛擬網路的個別虛擬網路，通常是最適當的設計。

其他設計選擇是有效的，前提是您符合虛擬網路和子網的下列各節中所述的需求。

當您針對 Azure AD DS 設計虛擬網路時，適用下列考慮事項：

* Azure AD DS 必須部署到與您的虛擬網路相同的 Azure 區域中。
    * 目前，您只能為每個 Azure AD 租使用者部署一個受控網域。 受控網域會部署到單一區域。 請確定您在[支援 AZURE AD DS 的區域](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)中建立或選取虛擬網路。
* 請考慮其他 Azure 區域與裝載應用程式工作負載之虛擬網路的鄰近性。
    * 若要將延遲降至最低，請將您的核心應用程式保持在與受控網域的虛擬網路子網靠近或相同的區域中。 您可以使用 Azure 虛擬網路之間的虛擬網路對等互連或虛擬私人網路（VPN）連線。 下一節將討論這些連接選項。
* 虛擬網路不能依賴受控網域所提供之服務以外的 DNS 服務。
    * Azure AD DS 提供自己的 DNS 服務。 虛擬網路必須設定為使用這些 DNS 服務位址。 您可以使用條件轉寄站來完成其他命名空間的名稱解析。
    * 您不能使用自訂 DNS 伺服器設定，從其他 DNS 伺服器（包括在 Vm 上）中引導查詢。 虛擬網路中的資源必須使用受控網域所提供的 DNS 服務。

> [!IMPORTANT]
> 啟用服務之後，您就無法將 Azure AD DS 移至不同的虛擬網路。

受控網域會連線到 Azure 虛擬網路中的子網。 為 Azure AD DS 設計此子網，請注意下列事項：

* 受控網域必須部署在自己的子網中。 請勿使用現有的子網或閘道子網。
* 在部署受控網域期間，會建立網路安全性群組。 此網路安全性群組包含正確服務通訊所需的規則。
    * 請勿以您自己的自訂規則建立或使用現有的網路安全性群組。
* 受控網域需要3-5 個 IP 位址。 請確定您的子網 IP 位址範圍可提供此數目的位址。
    * 限制可用的 IP 位址可能會導致受控網域無法維護兩個網域控制站。

下列範例圖表概述受控網域具有自己的子網的有效設計、有外部連線的閘道子網，以及應用程式工作負載位於虛擬網路內的連線子網中：

![建議的子網路設計](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>連接到 Azure AD DS 虛擬網路

如上一節所述，您只能在 Azure 中的單一虛擬網路中建立受控網域，而且每個 Azure AD 租使用者只能建立一個受控網域。 根據此架構，您可能需要將裝載應用程式工作負載的一或多個虛擬網路連線到受控網域的虛擬網路。

您可以使用下列其中一種方法來連接裝載于其他 Azure 虛擬網路中的應用程式工作負載：

* 虛擬網路對等互連
* 虛擬私人網路 (VPN)

### <a name="virtual-network-peering"></a>虛擬網路對等互連

虛擬網路對等互連是透過 Azure 骨幹網路來連接同一區域中兩個虛擬網路的機制。 全域虛擬網路對等互連可以跨 Azure 區域連接虛擬網路。 一旦對等互連之後，這兩個虛擬網路可讓 Vm 等資源直接使用私人 IP 位址彼此通訊。 使用虛擬網路對等互連可讓您使用部署在其他虛擬網路中的應用程式工作負載來部署受控網域。

![使用對等互連的虛擬網路連線](./media/active-directory-domain-services-design-guide/vnet-peering.png)

如需詳細資訊，請參閱[Azure 虛擬網路對等互連總覽](../virtual-network/virtual-network-peering-overview.md)。

### <a name="virtual-private-networking-vpn"></a>虛擬私人網路 (VPN)

您可以將虛擬網路連線到另一個虛擬網路（VNet 對 VNet），方法與將虛擬網路設定為內部部署網站位置的方式相同。 這兩個連線都使用 VPN 閘道，來建立使用 IPsec/IKE 的安全通道。 此連線模型可讓您將受控網域部署到 Azure 虛擬網路，然後連線到內部部署位置或其他雲端。

![使用 VPN 閘道的虛擬網路連線能力](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

如需有關使用虛擬私人網路的詳細資訊，請參閱[使用 Azure 入口網站來設定 vnet 對 VNET VPN 閘道聯](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)機。

## <a name="name-resolution-when-connecting-virtual-networks"></a>連接虛擬網路時的名稱解析

連線到受控網域之虛擬網路的虛擬網路通常會有自己的 DNS 設定。 當您連線到虛擬網路時，它不會自動設定連線虛擬網路的名稱解析，以解析受控網域所提供的服務。 連線虛擬網路上的名稱解析必須設定為可讓應用程式工作負載找到受控網域。

您可以在支援連線虛擬網路的 DNS 伺服器上使用條件式 DNS 轉寄站，或使用來自受控網域之虛擬網路的相同 DNS IP 位址，啟用名稱解析。

## <a name="network-resources-used-by-azure-ad-ds"></a>Azure AD DS 所使用的網路資源

受控網域會在部署期間建立一些網路資源。 需要這些資源才能成功操作和管理受控網域，且不應手動設定。

| Azure 資源                          | 說明 |
|:----------------------------------------|:---|
| 網路介面卡                  | 在做為 Azure Vm 的 Windows Server 上執行的兩個網域控制站（Dc）上，Azure AD DS 裝載受控網域。 每個 VM 都有一個虛擬網路介面，可連線到您的虛擬網路子網。 |
| 動態標準公用 IP 位址      | Azure AD DS 會使用標準 SKU 公用 IP 位址與同步處理和管理服務進行通訊。 如需公用 IP 位址的詳細資訊，請參閱[Azure 中的 IP 位址類型和配置方法](../virtual-network/virtual-network-ip-addresses-overview-arm.md)。 |
| Azure 標準負載平衡器            | Azure AD DS 會使用標準 SKU 負載平衡器來進行網路位址轉譯（NAT）和負載平衡（搭配安全 LDAP 使用時）。 如需有關 Azure 負載平衡器的詳細資訊，請參閱[什麼是 Azure Load Balancer？](../load-balancer/load-balancer-overview.md) |
| 網路位址轉譯（NAT）規則 | Azure AD DS 會在負載平衡器上建立和使用三個 NAT 規則-一條用於保護 HTTP 流量的規則，以及用於保護 PowerShell 遠端的兩個規則。 |
| 負載平衡器規則                     | 針對 TCP 通訊埠636上的安全 LDAP 設定受控網域時，會建立三個規則，並在負載平衡器上用來散發流量。 |

> [!WARNING]
> 請勿刪除或修改 Azure AD DS 建立的任何網路資源，例如手動設定負載平衡器或規則。 如果您刪除或修改任何網路資源，可能會發生 Azure AD DS 服務中斷。

## <a name="network-security-groups-and-required-ports"></a>網路安全性群組和所需的埠

[網路安全性群組（NSG）](../virtual-network/virtual-networks-nsg.md)包含一份規則清單，可允許或拒絕對 Azure 虛擬網路中流量的網路流量。 當您部署受控網域時，會建立一個網路安全性群組，其中包含一組可讓服務提供驗證和管理功能的規則。 此預設網路安全性群組會與您的受控網域部署所在的虛擬網路子網相關聯。

受控網域需要下列網路安全性群組規則，才能提供驗證和管理服務。 請勿針對您的受控網域部署所在的虛擬網路子網，編輯或刪除這些網路安全性群組規則。

| 連接埠號碼 | 通訊協定 | 來源                             | Destination | 動作 | 必要 | 目的 |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | 任意         | 允許  | 是      | 與您的 Azure AD 租使用者同步處理。 |
| 3389        | TCP      | CorpNetSaw                         | 任意         | 允許  | 是      | 管理您的網域。 |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | 任意         | 允許  | 是      | 管理您的網域。 |

已建立的 Azure 標準負載平衡器需要執行這些規則。 此網路安全性群組會保護 Azure AD DS，而且能讓受控網域正確運作。 請勿刪除此網路安全性群組。 負載平衡器不會正常運作。

> [!WARNING]
> 請勿手動編輯這些網路資源和設定。 當您將設定錯誤的網路安全性群組或使用者定義的路由表與受控網域部署所在的子網產生關聯時，您可能會中斷 Microsoft 服務和管理網域的能力。 您的 Azure AD 租使用者與受控網域之間的同步處理也會中斷。
>
> 如果您使用安全 LDAP，您可以新增必要的 TCP 埠636規則，以允許外部流量（如有需要）。 新增此規則並不會將您的網路安全性群組規則放在不受支援的狀態。 如需詳細資訊，請參閱透過[網際網路鎖定安全 LDAP 存取](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)
>
> *AllowVnetInBound*、 *AllowAzureLoadBalancerInBound*、 *DenyAllInBound*、 *AllowVnetOutBound*、 *AllowInternetOutBound*和*DenyAllOutBound*的預設規則也存在於網路安全性群組。 請勿編輯或刪除這些預設規則。
>
> Azure SLA 不適用於未正確設定的網路安全性群組和/或使用者定義的路由表已套用的部署，其封鎖了 Azure AD DS 來更新和管理您的網域。

### <a name="port-443---synchronization-with-azure-ad"></a>埠 443-與 Azure AD 同步處理

* 用來同步處理您的 Azure AD 租使用者與受控網域。
* 若沒有此埠的存取權，您的受控網域將無法與您的 Azure AD 租使用者同步。 使用者可能無法登入，因為其密碼的變更不會同步處理至您的受控網域。
* 預設會使用**AzureActiveDirectoryDomainServices**服務標籤來限制對此埠到 IP 位址的輸入存取。
* 請勿限制來自此埠的輸出存取。

### <a name="port-3389---management-using-remote-desktop"></a>埠 3389-使用遠端桌面管理

* 用於遠端桌面連線到受控網域中的網域控制站。
* 預設的網路安全性群組規則會使用*CorpNetSaw*服務標籤來進一步限制流量。
    * 此服務標籤只允許 Microsoft 公司網路上的安全存取工作站使用遠端桌面至受控網域。
    * 只有商業理由才允許存取，例如用於管理或疑難排解案例。
* 此規則可以設定為 [*拒絕*]，而且只會在需要時設定為 [*允許*]。 大部分的管理和監視工作都是使用 PowerShell 遠端執行。 RDP 僅適用于 Microsoft 需要遠端連線到受控網域以進行疑難排解的罕見事件。

> [!NOTE]
> 如果您嘗試編輯此網路安全性群組規則，就無法從入口網站手動選取*CorpNetSaw*服務標記。 您必須使用 Azure PowerShell 或 Azure CLI，手動設定使用*CorpNetSaw*服務標記的規則。

### <a name="port-5986---management-using-powershell-remoting"></a>埠 5986-使用 PowerShell 遠端執行管理

* 用來在您的受控網域中使用 PowerShell 遠端執行管理工作。
* 若沒有此埠的存取權，您的受控網域將無法更新、設定、備份或受監視。
* 針對使用以 Resource Manager 為基礎之虛擬網路的受控網域，您可以將此埠的輸入存取限制為*AzureActiveDirectoryDomainServices*服務標籤。
    * 針對使用傳統虛擬網路的舊版受控網域，您可以將此埠的輸入存取限制為下列來源 IP 位址： *52.180.183.8*、 *23.101.0.70*、 *52.225.184.198*、 *52.179.126.223*、 *13.74.249.156*、 *52.187.117.83*、 *52.161.13.95*、 *104.40.156.18*和*104.40.87.209*。

    > [!NOTE]
    > 在2017中，Azure AD Domain Services 變成可供 Azure Resource Manager 網路中的主機使用。 之後，我們就可以使用 Azure Resource Manager 的現代化功能來建立更安全的服務。 由於 Azure Resource Manager 部署完全取代傳統部署，Azure AD DS 傳統虛擬網路部署將于2023年3月1日淘汰。
    >
    > 如需詳細資訊，請參閱[官方淘汰通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>使用者定義的路由

預設不會建立使用者定義的路由，Azure AD DS 也不需要這樣才能正確運作。 如果您需要使用路由表，請避免對*0.0.0.0*路由進行任何變更。 此路由的變更會中斷 Azure AD DS，並將受控網域置於不受支援的狀態。

您也必須將輸入流量從個別 Azure 服務標籤中包含的 IP 位址路由傳送至受控網域的子網。 如需服務標籤及其相關聯 IP 位址的詳細資訊，請參閱[AZURE IP 範圍和服務標籤-公用雲端](https://www.microsoft.com/en-us/download/details.aspx?id=56519)。

> [!CAUTION]
> 這些 Azure 資料中心 IP 範圍可能會變更，恕不另行通知。 請確定您有處理常式，可驗證您是否擁有最新的 IP 位址。

## <a name="next-steps"></a>後續步驟

如需 Azure AD DS 所使用的部分網路資源和連線選項的詳細資訊，請參閱下列文章：

* [Azure 虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure 網路安全性群組](../virtual-network/security-overview.md)
