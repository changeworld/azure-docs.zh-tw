---
title: Azure AD 網域服務的網路規劃和連接 |微軟文件
description: 在運行 Azure 活動目錄域服務時,瞭解用於連接的一些虛擬網路設計注意事項和資源。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: e610bf94dfdee4e2765e4fae4259f18a9f1036b5
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639984"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Azure AD 網域服務的虛擬網路設計注意事項和設定選項

由於 Azure 活動目錄域服務 (AD DS) 為其他應用程式和工作負荷提供身份驗證和管理服務,網路連接是一個關鍵元件。 如果沒有正確配置的虛擬網路資源,應用程式和工作負載就無法與 Azure AD DS 提供的功能進行通信和使用。 規劃虛擬網路要求,以確保 Azure AD DS 能夠根據需要為應用程式和工作負荷提供服務。

本文概述了支援 Azure AD DS 的 Azure 虛擬網路的設計注意事項和要求。

## <a name="azure-virtual-network-design"></a>Azure 虛擬網路設計

要提供網路連接並允許應用程式和服務根據 Azure AD DS 進行身份驗證,請使用 Azure 虛擬網路和子網。 理想情況下,Azure AD DS 應部署到其自己的虛擬網路中。 您可以在同一虛擬網路中包括單獨的應用程式子網,以承載管理 VM 或輕型應用程式工作負載。 與 Azure AD DS 虛擬網路對等的較大或複雜的應用程式工作負荷的單獨虛擬網路通常是最合適的設計。 其他設計選擇有效,前提是您滿足以下虛擬網路和子網部分中概述的要求。

在為 Azure AD DS 設計虛擬網路時,需要考慮以下因素:

* Azure AD DS 必須部署到與虛擬網路相同的 Azure 區域中。
    * 此時,您只能部署每個 Azure AD 租戶的一個 Azure AD DS 託管域。 Azure AD DS 託管域部署到單個區域。 請確保在[支援 Azure AD DS 的區域](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)中建立或選擇虛擬網路。
* 請考慮其他 Azure 區域和承載應用程式工作負載的虛擬網路的鄰近程度。
    * 要最小化延遲,請使核心應用程式靠近或位於 Azure AD DS 託管域的虛擬網路子網路的虛擬網路子網或同一區域。 可以使用 Azure 虛擬網路之間的虛擬網路對等互連或虛擬專用網路 (VPN) 連接。 以下部分將討論這些連接選項。
* 虛擬網路不能依賴 Azure AD DS 提供的 DNS 服務以外的 DNS 服務。
    * Azure AD DS 提供其自己的 DNS 服務。 必須將虛擬網路配置為使用這些 DNS 服務位址。 可以使用條件轉寄器完成其他命名空間的名稱解析。
    * 不能使用自訂 DNS 伺服器設置來引導來自其他 DNS 伺服器(包括在 VM 上)的查詢。 虛擬網路中的資源必須使用 Azure AD DS 提供的 DNS 服務。

> [!IMPORTANT]
> 啟用服務後,無法將 Azure AD DS 移動到其他虛擬網路。

Azure AD DS 託管域連接到 Azure 虛擬網路中的子網。 使用以下注意事項為 Azure AD DS 設計此子網路:

* Azure AD DS 必須部署在其自己的子網中。 不要使用現有子網或網關子網。
* 在部署 Azure AD DS 託管域期間創建網路安全組。 此網路安全組包含正確服務通信所需的規則。
    * 不要使用自己的自定義規則創建或使用現有的網路安全組。
* Azure AD DS 需要 3-5 個 IP 位址。 確保您的子網 IP 位址範圍可以提供此數量的位址。
    * 限制可用的 IP 位址可能會阻止 Azure AD 域服務維護兩個域控制器。

下面的範例圖概述了一個有效的設計,其中 Azure AD DS 有自己的子網,有用於外部連接的閘道網路,並且應用程式工作負載位於虛擬網路中的連接子網中:

![建議的子網路設計](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>連接到 Azure AD DS 虛擬網路

如上一節所述,只能在 Azure 中的單個虛擬網路中創建 Azure AD 域服務託管域,並且每個 Azure AD 租戶只能創建一個託管域。 基於此體系結構,您可能需要將承載應用程式工作負載的一個或多個虛擬網路連接到 Azure AD DS 虛擬網路。

可以使用以下方法之一連接託管在其他 Azure 虛擬網路中的應用程式工作負荷:

* 虛擬網路對等互連
* 虛擬私人網路 (VPN)

### <a name="virtual-network-peering"></a>虛擬網路對等互連

虛擬網路對等互連是透過 Azure 骨幹網路來連接同一區域中兩個虛擬網路的機制。 全域虛擬網路對等互連可以跨 Azure 區域連接虛擬網路。 一旦對等,兩個虛擬網路允許資源(如 VM)直接使用專用 IP 位址相互通信。 使用虛擬網路對等互連,可以部署 Azure AD DS 託管域,應用程式工作負載部署在其他虛擬網路中。

![使用對等互連的虛擬網路連線](./media/active-directory-domain-services-design-guide/vnet-peering.png)

有關詳細資訊,請參閱[Azure 虛擬網路對等概述](../virtual-network/virtual-network-peering-overview.md)。

### <a name="virtual-private-networking-vpn"></a>虛擬私人網路 (VPN)

您可以將虛擬網路連接到另一個虛擬網路 (VNet 到 VNet),其方式與將虛擬網路配置為本機網站位置的方式相同。 兩個連接都使用 VPN 閘道使用 IPsec/IKE 創建安全隧道。 此連接模型允許您將 Azure AD DS 部署到 Azure 虛擬網路,然後連接本地位置或其他雲端。

![使用 VPN 閘道進行虛擬網路連線](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

有關使用虛擬專用網路的詳細資訊,請閱讀使用[Azure 門戶設定 VNet 到 VNet VPN 閘道連接](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。

## <a name="name-resolution-when-connecting-virtual-networks"></a>連線虛擬網路時的名稱解析

連接到 Azure AD 網域服務虛擬網路的虛擬網路通常有自己的 DNS 設置。 連接虛擬網路時,它不會自動為連接虛擬網路配置名稱解析,以解決 Azure AD DS 託管域提供的服務。 必須配置連接虛擬網路上的名稱解析,以使應用程式工作負荷能夠找到 Azure AD 域服務。

您可以使用支援連接虛擬網路的 DNS 伺服器上的條件 DNS 轉寄站啟用名稱解析,或者使用 Azure AD 網域服務虛擬網路中相同的 DNS IP 位址。

## <a name="network-resources-used-by-azure-ad-ds"></a>Azure AD DS 使用的網路資源

Azure AD DS 託管域在部署期間創建一些網路資源。 這些資源是成功操作和管理 Azure AD DS 託管域所必需的,不應手動配置。

| Azure 資源                          | 描述 |
|:----------------------------------------|:---|
| 網路介面卡                  | Azure AD DS 在 Windows 伺服器上作為 Azure VM 運行的兩個域控制器 (DC) 上託管託管域。 每個 VM 都有一個虛擬網路介面,可連接到虛擬網路子網。 |
| 動態標準公共 IP 位址      | Azure AD DS 使用標準的 SKU 公共 IP 位址與同步和管理服務通訊。 有關公共 IP 位址的詳細資訊,請參閱[Azure 中的 IP 位址類型和分配方法](../virtual-network/virtual-network-ip-addresses-overview-arm.md)。 |
| Azure 標準負載均衡器            | Azure AD DS 使用標準 SKU 負載均衡器進行網路位址轉換 (NAT) 和負載平衡(與安全 LDAP 一起使用時)。 有關 Azure 負載均衡器的詳細資訊,請參閱[什麼是 Azure 負載均衡器?](../load-balancer/load-balancer-overview.md) |
| 網路位址轉換 (NAT) 規則 | Azure AD DS 在負載均衡器上建立並使用三個 NAT 規則 - 一個用於安全 HTTP 流量的規則,以及兩個用於安全 PowerShell 遠端處理的規則。 |
| 負載平衡器規則                     | 當為 TCP 連接埠 636 上的安全 LDAP 配置 Azure AD DS 託管域時,將創建三個規則並在負載均衡器上使用以分配流量。 |

> [!WARNING]
> 不要刪除或修改 Azure AD DS 創建的任何網路資源,例如手動配置負載均衡器或規則。 如果刪除或修改任何網路資源,則可能發生 Azure AD DS 服務中斷。

## <a name="network-security-groups-and-required-ports"></a>網路安全群組和所需連接埠

[網路安全組 (NSG)](../virtual-network/virtual-networks-nsg.md)包含允許或拒絕網路流量到 Azure 虛擬網路中的流量的規則清單。 部署 Azure AD DS 時將創建網路安全組,該 DS 包含一組規則,允許服務提供身份驗證和管理功能。 此預設網路安全組與 Azure AD DS 託管域部署到的虛擬網路子網相關聯。

Azure AD DS 需要以下網路安全組規則才能提供身份驗證和管理服務。 不要編輯或刪除 Azure AD DS 託管域部署的虛擬網路子網的這些網路安全組規則。

| 連接埠號碼 | 通訊協定 | 來源                             | Destination | 動作 | 必要 | 目的 |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | Azure ActiveDirectory 網域服務 | 任意         | Allow  | 是      | 與 Azure AD 租戶同步。 |
| 3389        | TCP      | 公司網路鋸                         | 任意         | Allow  | 是      | 管理您的域。 |
| 5986        | TCP      | Azure ActiveDirectory 網域服務 | 任意         | Allow  | 是      | 管理您的域。 |

> [!WARNING]
> 不要手動編輯這些網路資源和配置。 將配置錯誤的網路安全組或使用者定義的路由表與部署 Azure AD DS 的子網相關聯時,可能會中斷 Microsoft 的服務和管理域的能力。 Azure AD 租戶和 Azure AD DS 託管域之間的同步也會中斷。
>
> 如果使用安全 LDAP,則可以添加所需的 TCP 埠 636 規則,以根據需要允許外部流量。 添加此規則不會將網路安全組規則置於不受支援的狀態。 有關詳細資訊,請參閱[鎖定網路上的安全 LDAP 訪問](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)
>
> 網路安全組還存在 *「允許VnetInBound」、「**允許Azure載入平衡」、**拒絕「已綁定*」、「*允許VnetOut」、**允許網路出界*和 *「拒絕全部出界」* 的預設規則。 不要編輯或刪除這些預設規則。
>
> Azure SLA 不適用於已應用了配置不正確的網路安全組和/或使用者定義的路由表阻止 Azure AD DS 更新和管理域的部署。

### <a name="port-443---synchronization-with-azure-ad"></a>連接埠 443 - 與 Azure AD 同步

* 用於將 Azure AD 租戶與 Azure AD DS 託管域同步。
* 如果沒有對此埠的訪問,Azure AD DS 託管域就無法與 Azure AD 租戶同步。 使用者可能無法登錄,因為對其密碼的更改不會同步到 Azure AD DS 託管域。
* 默認情況下,使用**AzureActiveDirectoryDomainDomain 服務**標記限制對此埠的 IP 位址的入站訪問。
* 不要限制從此埠的出站訪問。

### <a name="port-3389---management-using-remote-desktop"></a>連接埠 3389 - 使用遠端桌面

* 用於與 Azure AD DS 託管域中的網域控制器的遠端桌面連接。
* 默認網路安全組規則使用*CorpNetSaw*服務標記進一步限制流量。
    * 此服務標記只允許 Microsoft 公司網路上的安全訪問工作站使用遠端桌面到 Azure AD DS 託管域。
    * 僅允許出於業務理由進行訪問,例如對於管理或故障排除方案。
* 此規則可以設置為 *「拒絕*」,並且僅在需要時設置為 *「允許*」。 大多數管理和監視任務都是使用 PowerShell 遠端處理執行的。 RDP 僅在 Microsoft 需要遠端連接到託管域進行高級故障排除的罕見情況下使用。

> [!NOTE]
> 如果嘗試編輯此網路安全組規則,則無法從門戶手動選擇*CorpNetSaw*服務標記。 您必須使用 Azure PowerShell 或 Azure CLI 手動配置使用*CorpNetSaw*服務標記的規則。

### <a name="port-5986---management-using-powershell-remoting"></a>連接埠 5986 - 使用 PowerShell 遠端處理進行管理

* 用於在 Azure AD DS 託管域中使用 PowerShell 遠端處理執行管理任務。
* 如果沒有對此埠的訪問,無法更新、配置、備份或監視 Azure AD DS 託管域。
* 對於使用基於資源管理器的虛擬網路的 Azure AD DS 託管域,可以將對此埠的入站存取限制為*AzureActiveDirectory Domain 服務*標記。
    * 對於使用基於經典虛擬網路的傳統 Azure AD DS 託管域, 您可以將此埠的入站訪問限制為以下源 IP 位址:52.180.183.8、23.101.0.70、52.225.184.198 *、52.179.126.223*、 *13.74.249.156*、 *52.187.117.83*、 *52.161.13.95*、 *104.40.156.18**52.180.183.8**23.101.0.70**52.225.184.198*和*104.40.87.209*.

    > [!NOTE]
    > 2017 年,Azure AD 網域服務可用於在 Azure 資源管理器網路中託管。 自那時以來,我們一直能夠使用 Azure 資源管理器的現代功能構建更安全的服務。 由於 Azure 資源管理器部署完全取代了經典部署,因此 Azure AD DS 經典虛擬網路部署將於 2023 年 3 月 1 日停用。
    >
    > 有關詳細資訊,請參閱[官方棄用通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>使用者定義的路由

默認情況下,不會創建使用者定義的路由,並且 Azure AD DS 不需要正常工作。 如果需要使用路由表,請避免對*0.0.0.0*路由進行任何更改。 對此路由的更改會中斷 Azure AD 域服務,並將託管域置於不受支持的狀態。

還必須將入站流量從相應 Azure 服務標記中包含的 IP 位址路由到 Azure AD 域服務子網。 有關服務標記及其關聯 IP 位址的詳細資訊,請參閱[Azure IP 範圍和服務標記 - 公共雲](https://www.microsoft.com/en-us/download/details.aspx?id=56519)。

> [!CAUTION]
> 這些 Azure 資料中心 IP 範圍可以更改而不通知。 確保您有流程來驗證您擁有最新的 IP 位址。

## <a name="next-steps"></a>後續步驟

有關 Azure AD DS 使用的一些網路資源和連線選項的詳細資訊,請參閱以下文章:

* [Azure 虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure 網路安全性群組](../virtual-network/security-overview.md)
