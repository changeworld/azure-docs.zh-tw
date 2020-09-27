---
title: Azure AD Domain Services 的網路規劃和連接 |Microsoft Docs
description: 瞭解當您執行 Azure Active Directory Domain Services 時，所使用的一些虛擬網路設計考慮和資源。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 6e2b3badcda872db3ddb1d237b813615a1332ad0
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396326"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 的虛擬網路設計考慮和設定選項

Azure Active Directory Domain Services (Azure AD DS) 提供驗證和管理服務給其他應用程式和工作負載。 網路連線是重要的元件。 如果沒有正確設定的虛擬網路資源，應用程式和工作負載就無法與 Azure AD DS 所提供的功能通訊並使用。 規劃您的虛擬網路需求，以確保 Azure AD DS 可以視需要提供您的應用程式和工作負載。

本文概述 Azure 虛擬網路支援 Azure AD DS 的設計考慮和需求。

## <a name="azure-virtual-network-design"></a>Azure 虛擬網路設計

若要提供網路連線能力，並允許應用程式和服務對 Azure AD DS 受控網域進行驗證，請使用 Azure 虛擬網路和子網。 在理想情況下，應該將受控網域部署到自己的虛擬網路中。

您可以在相同的虛擬網路中包含個別的應用程式子網，以裝載您的管理 VM 或輕量應用程式工作負載。 針對較大型或複雜的應用程式工作負載，對等互連至 Azure AD DS 虛擬網路的個別虛擬網路，通常是最適當的設計。

如果您符合下列各節中針對虛擬網路和子網所述的需求，則其他設計選項都是有效的。

當您設計 Azure AD DS 的虛擬網路時，適用下列考慮事項：

* Azure AD DS 必須部署到與您的虛擬網路相同的 Azure 區域。
    * 目前，您只能在每個 Azure AD 租使用者上部署一個受控網域。 受控網域會部署到單一區域。 請確定您已在 [支援 DS Azure AD 的區域](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)中建立或選取虛擬網路。
* 請考慮其他 Azure 區域與裝載應用程式工作負載的虛擬網路之間的距離。
    * 若要將延遲降至最低，請將您的核心應用程式保持在與受控網域的虛擬網路子網接近或相同的區域中。 您可以使用虛擬網路對等互連或虛擬私人網路， (Azure 虛擬網路之間的 VPN) 連接。 下一節將討論這些連接選項。
* 虛擬網路無法依賴受控網域所提供的服務以外的 DNS 服務。
    * Azure AD DS 提供自己的 DNS 服務。 虛擬網路必須設定為使用這些 DNS 服務位址。 您可以使用條件式轉寄站來完成其他命名空間的名稱解析。
    * 您無法使用自訂的 DNS 伺服器設定，從其他 DNS 伺服器（包括在 Vm 上）中指示查詢。 虛擬網路中的資源必須使用受控網域所提供的 DNS 服務。

> [!IMPORTANT]
> 當您啟用服務之後，就無法將 Azure AD DS 移至不同的虛擬網路。

受控網域會連接到 Azure 虛擬網路中的子網。 針對 Azure AD DS 設計此子網，請考慮下列事項：

* 受控網域必須部署在它自己的子網中。 請勿使用現有的子網或閘道子網。
* 部署受控網域時，會建立網路安全性群組。 此網路安全性群組包含正確的服務通訊所需的規則。
    * 請勿使用您自己的自訂規則來建立或使用現有的網路安全性群組。
* 受控網域需要 3-5 IP 位址。 請確定您的子網 IP 位址範圍可提供此數目的位址。
    * 限制可用的 IP 位址可防止受控網域維護兩個網域控制站。

下列範例圖概述有效的設計，其中受控網域具有自己的子網、外部連線有一個閘道子網，而應用程式工作負載位於虛擬網路內的連線子網中：

![建議的子網路設計](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>連接到 Azure AD DS 虛擬網路

如上一節所述，您只能在 Azure 中的單一虛擬網路中建立受控網域，且每個 Azure AD 租使用者只能建立一個受控網域。 根據此架構，您可能需要將一或多個裝載應用程式工作負載的虛擬網路連接到受控網域的虛擬網路。

您可以使用下列其中一種方法來連線到裝載于其他 Azure 虛擬網路中的應用程式工作負載：

* 虛擬網路對等互連
* 虛擬私人網路 (VPN)

### <a name="virtual-network-peering"></a>虛擬網路對等互連

虛擬網路對等互連是透過 Azure 骨幹網路來連接同一區域中兩個虛擬網路的機制。 全域虛擬網路對等互連可以跨 Azure 區域連接虛擬網路。 對等互連之後，這兩個虛擬網路可讓資源（例如 Vm）使用私人 IP 位址彼此直接通訊。 使用虛擬網路對等互連可讓您使用部署在其他虛擬網路中的應用程式工作負載來部署受控網域。

![使用對等互連的虛擬網路連線](./media/active-directory-domain-services-design-guide/vnet-peering.png)

如需詳細資訊，請參閱 [Azure 虛擬網路對等互連總覽](../virtual-network/virtual-network-peering-overview.md)。

### <a name="virtual-private-networking-vpn"></a>虛擬私人網路 (VPN)

您可以使用與將虛擬網路設定為內部部署網站位置相同的方式，將虛擬網路連線至另一個虛擬網路 (VNet 對 VNet) 。 這兩個連線都會使用 VPN 閘道來建立使用 IPsec/IKE 的安全通道。 此連接模型可讓您將受控網域部署至 Azure 虛擬網路，然後連線至內部部署位置或其他雲端。

![使用 VPN 閘道的虛擬網路連線能力](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

如需使用虛擬私人網路的詳細資訊，請參閱 [使用 Azure 入口網站來設定 vnet 對 VNET VPN 閘道聯](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)機。

## <a name="name-resolution-when-connecting-virtual-networks"></a>連接虛擬網路時的名稱解析

連線到受控網域虛擬網路的虛擬網路，通常會有自己的 DNS 設定。 當您連線到虛擬網路時，它不會自動設定連接虛擬網路的名稱解析，以解析受控網域所提供的服務。 連接的虛擬網路上的名稱解析必須設定為可讓應用程式工作負載找出受控網域。

您可以在支援連線虛擬網路的 DNS 伺服器上使用條件式 DNS 轉寄站，或從受控網域的虛擬網路使用相同的 DNS IP 位址，來啟用名稱解析。

## <a name="network-resources-used-by-azure-ad-ds"></a>Azure AD DS 使用的網路資源

受控網域會在部署期間建立一些網路資源。 需要這些資源，才能成功操作和管理受控網域，且不應手動設定。

| Azure 資源                          | 描述 |
|:----------------------------------------|:---|
| 網路介面卡                  | 在以 Azure Vm 形式在 Windows Server 上執行的兩個網域控制站上，Azure AD DS 裝載受控網域 (Dc) 。 每個 VM 都有一個虛擬網路介面，可連接到您的虛擬網路子網。 |
| 動態標準公用 IP 位址      | Azure AD DS 會使用標準 SKU 公用 IP 位址與同步處理和管理服務進行通訊。 如需公用 IP 位址的詳細資訊，請參閱 [Azure 中的 ip 網址類別型和配置方法](../virtual-network/public-ip-addresses.md)。 |
| Azure 標準負載平衡器            | Azure AD DS 會使用標準 SKU 負載平衡器來進行網路位址轉譯 (NAT) 以及搭配安全 LDAP) 使用時 (負載平衡。 如需 Azure 負載平衡器的詳細資訊，請參閱 [什麼是 Azure Load Balancer？](../load-balancer/load-balancer-overview.md) |
|  (NAT) 規則的網路位址轉譯 | Azure AD DS 會在負載平衡器上建立和使用三個 NAT 規則-一個適用于安全 HTTP 流量的規則，以及兩個安全的 PowerShell 遠端處理規則。 |
| 負載平衡器規則                     | 當受控網域設定在 TCP 埠636上的安全 LDAP 時，會在負載平衡器上建立並使用三個規則來散發流量。 |

> [!WARNING]
> 請勿刪除或修改 Azure AD DS 所建立的任何網路資源，例如手動設定負載平衡器或規則。 如果您刪除或修改任何網路資源，可能會發生 Azure AD DS 服務中斷。

## <a name="network-security-groups-and-required-ports"></a>網路安全性群組和必要的埠

[ (NSG) 的網路安全性群組](../virtual-network/security-overview.md)包含規則清單，可允許或拒絕 Azure 虛擬網路中流量的網路流量。 當您部署的受控網域包含一組可讓服務提供驗證和管理功能的規則時，就會建立網路安全性群組。 此預設網路安全性群組會與您的受控網域部署所在的虛擬網路子網相關聯。

受控網域需要下列網路安全性群組規則，才能提供驗證和管理服務。 請勿針對您的受控網域部署所在的虛擬網路子網，編輯或刪除這些網路安全性群組規則。

| 連接埠號碼 | 通訊協定 | 來源                             | Destination | 動作 | 必要 | 目的 |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | 任意         | Allow  | 是      | 與您的 Azure AD 租使用者同步處理。 |
| 3389        | TCP      | CorpNetSaw                         | 任意         | Allow  | 是      | 管理您的網域。 |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | 任意         | Allow  | 是      | 管理您的網域。 |

已建立的 Azure 標準負載平衡器需要執行這些規則。 此網路安全性群組會保護 Azure AD DS，而且能讓受控網域正確運作。 請勿刪除此網路安全性群組。 如果沒有它，負載平衡器將無法正常運作。

如有需要，您可以 [使用 Azure PowerShell 建立必要的網路安全性群組和規則](powershell-create-instance.md#create-a-network-security-group)。

> [!WARNING]
> 請勿手動編輯這些網路資源和設定。 當您將設定錯誤的網路安全性群組或使用者定義的路由表與部署受控網域的子網產生關聯時，您可能會中斷 Microsoft 服務及管理網域的能力。 您 Azure AD 租使用者與受控網域之間的同步處理也會中斷。
>
> 如果您使用安全 LDAP，則可以視需要新增必要的 TCP 埠636規則以允許外部流量。 新增此規則並不會將您的網路安全性群組規則放在不受支援的狀態。 如需詳細資訊，請參閱 [鎖定透過網際網路的安全 LDAP 存取](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)
>
> *AllowVnetInBound*、 *AllowAzureLoadBalancerInBound*、 *>denyallinbound*、 *AllowVnetOutBound*、 *>allowinternetoutbound*和 *>denyalloutbound*的預設規則也存在於網路安全性群組中。 請勿編輯或刪除這些預設規則。
>
> Azure SLA 不適用於未正確設定的網路安全性群組和/或使用者定義路由表已套用的部署，會封鎖 Azure AD DS 以更新和管理您的網域。

### <a name="port-443---synchronization-with-azure-ad"></a>埠 443-與 Azure AD 同步處理

* 用來同步處理您的 Azure AD 租使用者與您的受控網域。
* 若未存取此埠，您的受控網域將無法與您的 Azure AD 租使用者同步。 使用者可能無法登入，因為密碼的變更不會同步到您的受控網域。
* 預設會使用 **AzureActiveDirectoryDomainServices** 服務標籤，將此埠的輸入存取限制為 IP 位址。
* 請勿限制來自此埠的輸出存取。

### <a name="port-3389---management-using-remote-desktop"></a>埠 3389-使用遠端桌面管理

* 用於遠端桌面連線到受控網域中的網域控制站。
* 預設的網路安全性群組規則會使用 *CorpNetSaw* 服務標籤來進一步限制流量。
    * 此服務標籤只允許 Microsoft 公司網路上的安全存取工作站使用遠端桌面來管理受控網域。
    * 只有商業理由允許存取，例如管理或疑難排解案例。
* 此規則可設定為 [ *拒絕*]，而且只會在必要時設定為 [ *允許* ]。 大部分的管理和監視工作都是使用 PowerShell 遠端執行。 在罕見的情況下，Microsoft 需要從遠端連線到您的受控網域進行高階疑難排解，才會使用 RDP。

> [!NOTE]
> 如果您嘗試編輯此網路安全性群組規則，則無法從入口網站手動選取 *CorpNetSaw* 服務標記。 您必須使用 Azure PowerShell 或 Azure CLI 手動設定使用 *CorpNetSaw* 服務標記的規則。
>
> 例如，您可以使用下列腳本來建立允許 RDP 的規則： 
>
> `Get-AzureRmNetworkSecurityGroup -Name "nsg-name" -ResourceGroupName "resource-group-name" | Add-AzureRmNetworkSecurityRuleConfig -Name "new-rule-name" -Access "Allow" -Protocol "TCP" -Direction "Inbound" -Priority "priority-number" -SourceAddressPrefix "CorpNetSaw" -SourcePortRange "" -DestinationPortRange "3389" -DestinationAddressPrefix "" | Set-AzureRmNetworkSecurityGroup`

### <a name="port-5986---management-using-powershell-remoting"></a>埠 5986-使用 PowerShell 遠端系統管理管理

* 用來在受控網域中使用 PowerShell 遠端執行管理工作。
* 如果沒有此埠的存取權，您的受控網域就無法更新、設定、備份或監視。
* 針對使用以 Resource Manager 為基礎之虛擬網路的受控網域，您可以將此埠的輸入存取限制為 *AzureActiveDirectoryDomainServices* 服務標記。
    * 針對使用傳統虛擬網路的舊版受控網域，您可以將此埠的輸入存取限制為下列來源 IP 位址： *52.180.183.8*、 *23.101.0.70*、 *52.225.184.198*、 *52.179.126.223*、 *13.74.249.156*、 *52.187.117.83*、 *52.161.13.95*、 *104.40.156.18*和 *104.40.87.209*。

    > [!NOTE]
    > 在2017中，Azure AD Domain Services 可用於 Azure Resource Manager 網路中的主機。 從那時開始，我們可以使用 Azure Resource Manager 的新式功能來建立更安全的服務。 因為 Azure Resource Manager 部署完全取代傳統部署，所以 Azure AD DS 傳統虛擬網路部署將于2023年3月1日淘汰。
    >
    > 如需詳細資訊，請參閱 [官方淘汰通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>使用者定義的路由

依預設不會建立使用者定義的路由，且 Azure AD DS 無法正常運作時，不需要此路由。 如果您需要使用路由表，請避免對 *0.0.0.0* 路由進行任何變更。 此路由的變更會中斷 Azure AD DS，並使受控網域處於不支援的狀態。

您也必須將來自個別 Azure 服務標籤中所包含 IP 位址的輸入流量路由傳送至受控網域的子網。 如需有關服務標記及其相關 IP 位址的詳細資訊，請參閱 [AZURE IP 範圍和服務標籤-公用雲端](https://www.microsoft.com/en-us/download/details.aspx?id=56519)。

> [!CAUTION]
> 這些 Azure 資料中心 IP 範圍可能會變更，恕不另行通知。 確定您有進程可驗證您有最新的 IP 位址。

## <a name="next-steps"></a>後續步驟

如需 Azure AD DS 所使用的一些網路資源和連線選項的詳細資訊，請參閱下列文章：

* [Azure 虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure 網路安全性群組](../virtual-network/security-overview.md)