---
title: 配置和驗證虛擬網路或 VPN 連接
description: 配置和驗證各種 Azure VPN 和虛擬網路部署的分步指南
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099065"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>配置和驗證虛擬網路或 VPN 連接

本演練提供分步指導，用於配置和驗證各種 Azure VPN 和虛擬網路部署。 方案包括傳輸路由、網路到網路連接、邊境閘道協定 （BGP）、多網站連接和點對點連接。

Azure VPN 閘道可在 Azure 中安排幾乎任何類型的連接虛擬網路拓撲時實現靈活性。 例如，您可以連接虛擬網路：

- 跨區域。
- 在虛擬網路類型（Azure 資源管理器與經典）之間。
- 在 Azure 中或本地混合環境中。
- 在不同的訂閱中。 

## <a name="network-to-network-vpn-connection"></a>網路到網路 VPN 連接

通過 VPN 將虛擬網路連接到另一個虛擬網路（網路到網路）類似于將虛擬網路連接到本地網站位置。 這兩種連線類型都使用 VPN 閘道通過 IPsec 和 IKE 提供安全隧道。 虛擬網路可位於相同或不同的區域，以及來自相同或不同的訂用帳戶。

![使用 IPsec 進行網路到網路連接](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
如果您的虛擬網路位於同一區域，則可能需要考慮使用虛擬網路對等互連來連接它們。 虛擬網路對等互連不使用 VPN 閘道。 它增加了輸送量並減少了延遲。 要配置虛擬網路對等互連連接，請選擇"**配置並驗證 VNet 對等互連**"。

如果虛擬網路是通過 Azure 資源管理器部署模型創建的，請選擇"**配置和驗證資源管理器 VNet 到資源管理器 VNet 的連接**"以配置 VPN 連接。

如果其中一個虛擬網路是通過 Azure 經典部署模型創建的，另一個是通過資源管理器創建的，請選擇"**配置經典 VNet"到資源管理器 VNet 連接**以配置 VPN 連接。

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>為同一區域中的兩個虛擬網路配置虛擬網路對等互連

在開始實現和配置 Azure 虛擬網路對等互連之前，請確保滿足以下先決條件：

* 對等互連的虛擬網路必須存在於同一個 Azure 區域中。
* 對等虛擬網路必須具有不重疊的 IP 位址空間。
* 虛擬網路對等互連是介於兩個虛擬網路之間。 對等互連之間沒有派生的傳遞關係。 例如，如果 VNetA 與 VNetB 對等，並且 VNetB 與 VNetC 對等，則 VNetA*不會*與 VNetC 對等。

滿足要求時，可以按照教程：通過使用 Azure 門戶創建和配置對等互連，[將虛擬網路與虛擬網路對等互連連接起來](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)。

要檢查對等互連配置，請使用以下方法：

1. 使用具有必要[角色和許可權](virtual-network-manage-peering.md#permissions)的帳戶登錄到[Azure 門戶](https://portal.azure.com/)。
2. 在入口網站頂端包含「搜尋資源」**** 文字的方塊中，輸入「虛擬網路」****。 當搜尋結果中出現**虛擬網路**時加以選取。
3. 在顯示的**虛擬網路**邊欄選項卡中，選擇要為其創建對等互連的虛擬網路。
4. 在為虛擬網路顯示的窗格中，在 **"設置"** 部分中選擇**對等互連**。
5. 選擇對等互連並查看配置結果。

![用於檢查虛擬網路對等互連配置的選擇](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
對於 Azure PowerShell，運行命令[獲取 AzureRm 虛擬網路對等互連](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0)以獲取虛擬網路對等互連。 以下是範例：

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>將資源管理器虛擬網路連接到另一個資源管理器虛擬網路

可以直接配置從一個資源管理器虛擬網路到另一個資源管理器虛擬網路的連接。 或者，您可以使用 IPsec 配置連接。

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>配置資源管理器虛擬網路之間的 VPN 連接

要配置沒有 IPsec 的資源管理器虛擬網路之間的連接，請參閱[使用 Azure 門戶配置網路到網路 VPN 閘道連接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)。

要配置兩個資源管理器虛擬網路之間的 IPsec 連接，請按照步驟 1 到 5 在 Azure 門戶中為每個虛擬網路[創建網站到網站連接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)。

> [!Note]
> 這些步驟僅適用于同一訂閱中的虛擬網路。 如果您的虛擬網路位於不同的訂閱中，則必須使用 PowerShell 進行連接。 請參閱 [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps) 文件。

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>驗證資源管理器虛擬網路之間的 VPN 連接

![與 Azure 資源管理器虛擬網路的經典虛擬網路連接](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

要檢查 VPN 連接配置是否正確，請按照以下說明操作。

> [!Note] 
> 這些步驟中虛擬網路元件後的數位對應于上圖中的數位。

1. 確保連接的虛擬網路中沒有重疊的位址空間。
2. 驗證 Azure 資源管理器虛擬網路 （1） 的位址範圍是否在**連線物件**實例 （4） 中準確定義。
3. 驗證 Azure 資源管理器虛擬網路 （6） 的位址範圍是否在**連線物件**實例 （3） 中準確定義。
4. 驗證預共用金鑰在連線物件上是否匹配。
5. 驗證 Azure 資源管理器虛擬網路閘道 VIP （2） 是否在**連線物件**實例 （4） 中準確定義。
6. 驗證 Azure 資源管理器虛擬網路閘道 VIP （5） 是否在**連線物件**實例 （3） 中準確定義。

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>將經典虛擬網路連接到資源管理器虛擬網路

您可以在不同訂閱中的虛擬網路和不同區域之間創建連接。 還可以連接已連接到本地網路的虛擬網路，只要已將閘道類型配置為基於路由。

要配置經典虛擬網路和資源管理器虛擬網路之間的連接，請參閱[使用 Azure 門戶連接來自不同部署模型的虛擬網路](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal)。

![與 Azure 資源管理器虛擬網路的經典虛擬網路連接](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

要在將經典虛擬網路連接到 Azure 資源管理器虛擬網路時檢查配置，請按照這些說明操作。

> [!Note] 
> 這些步驟中虛擬網路元件後的數位對應于上圖中的數位。 

1. 確保連接的虛擬網路中沒有重疊的位址空間。
2. 驗證 Azure 資源管理器虛擬網路 （6） 的位址範圍是否在經典本地網路定義 （3） 中準確定義。
3. 驗證經典虛擬網路 （1） 的位址範圍是否在 Azure 資源管理器**連線物件**實例 （4） 中準確定義。
4. 驗證經典虛擬網路閘道 VIP （2） 是否在 Azure 資源管理器**連線物件**實例 （4） 中準確定義。
5. 驗證 Azure 資源管理器虛擬網路閘道 （5） 是否在經典**本地網路定義**實例 （3） 中準確定義。
6. 驗證預共用金鑰是否匹配在兩個連接的虛擬網路上：
   - 經典虛擬網路：**本地網路定義**（3）
   - Azure 資源管理器虛擬網路：**連線物件**（4）

## <a name="create-a-point-to-site-vpn-connection"></a>創建點對點 VPN 連接

點到網站（下圖中的*P2S）* 配置允許您創建從單個用戶端電腦到虛擬網路的安全連線。 當您要從遠端位置（如從家庭或會議）連接到虛擬網路時，點對點連接非常有用。 當您只有幾個用戶端需要連接到虛擬網路時，它們也很有用。 

點對點 VPN 連接通過本機 Windows VPN 用戶端從用戶端電腦啟動。 連線用戶端會使用憑證進行驗證。

![點對站連線](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

點對點連接不需要 VPN 設備。 它們通過安全通訊端隧道協定 （SSTP） 創建 VPN 連接。 您可以使用各種部署工具和部署模型將點到網站連接連接到虛擬網路：

* [使用 Azure 門戶配置指向虛擬網路的點對點連接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [使用 Azure 門戶（經典）配置指向虛擬網路的點對點連接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [使用 PowerShell 配置指向虛擬網路的點對點連接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>驗證點對點連接

文章[疑難排解：Azure 點到網站連接問題](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems)會解決點到網站連接的常見問題。

## <a name="create-a-multisite-vpn-connection"></a>創建多網站 VPN 連接

您可以將網站到網站（下圖中的*S2S）* 連接添加到已具有網站到網站連接、從網站到網站的連接或網路到網路到網路連接的虛擬網路。 這種連接通常稱為*多網站*配置。 

![多網站連接](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure 目前使用兩種部署模型：Resource Manager 和傳統。 這兩種型號並不完全相容。 要配置具有不同型號的多網站連接，請參閱以下文章：

* [將網站到網站的連接添加到具有現有 VPN 閘道連接的虛擬網路](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [將網站到網站的連接添加到具有現有 VPN 閘道連接的虛擬網路（經典）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> 這些文章中的步驟不適用於 Azure ExpressRoute 和網站到網站共存的連接配置。 有關詳細資訊，請參閱[ExpressRoute 和網站到網站共存的連接](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)。

## <a name="configure-transit-routing"></a>配置中轉路由

傳輸路由是在特定路由方案中，您可以在菊輪鍊拓撲中連接多個網路。 此路由使鏈兩端的虛擬網路中的資源能夠通過之間的虛擬網路相互通信。 如果沒有傳輸路由，通過集線器對等的網路或設備無法相互連接。

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>在點對點連接中配置傳輸路由

設想一個方案，在其中，您希望在 VNetA 和 VNetB 之間配置網站到網站 VPN 連接。 您還需要為用戶端配置點對點 VPN 以連接到 VNetA 的閘道。 然後，您希望為點到站台用戶端啟用傳輸路由，以連接到 VNetB，VNetB 通過 VNetA。 

當 VNetA 和 VNetB 之間的網站到網站 VPN 上啟用 BGP 時，支援此方案。 有關詳細資訊，請參閱[關於指向網站的 VPN 路由](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)。

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>在快速路由連接中配置傳輸路由

Azure ExpressRoute 可讓您透過連線提供者所提供的專用私人連線，將內部部署網路擴充至 Microsoft 雲端。 透過 ExpressRoute，您可以建立 Microsoft 雲端服務的連線，例如 Microsoft Azure、Office 365 和 Dynamics 365。 如需詳細資訊，請參閱 [ExpressRoute 概觀](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)。

![與 Azure 虛擬網路的專用對等互連連接](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> 我們建議，如果 VNetA 和 VNetB 位於同一地緣政治區域，則將[兩個虛擬網路連結到 ExpressRoute 電路](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm)，而不是配置傳輸路由。 如果您的虛擬網路位於不同的地緣政治區域，如果您擁有[ExpressRoute 高級](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium)版，也可以將它們直接連結到您的電路。 

如果您有 ExpressRoute 和網站到網站共存，則不支援傳輸路由。 有關詳細資訊，請參閱使用[PowerShell 配置快速路由和網站到網站](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)。

如果已啟用 ExpressRoute 將本地網路連接到 Azure 虛擬網路，則可以在要具有傳輸路由的虛擬網路之間啟用對等互連。 要允許本地網路連接到遠端虛擬網路，必須配置[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)。 

> [!Note]
> 虛擬網路對等互連僅適用于同一區域中的虛擬網路。

要檢查是否已為虛擬網路對等互連配置了傳輸路由，請按照以下說明操作：

1. 使用具有必要[角色和許可權](virtual-network-manage-peering.md#permissions)的帳戶登錄到[Azure 門戶](https://portal.azure.com/)。
2. [在 VNetA 和 VNetB 之間創建對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)，如前面的關係圖所示。 
3. 在為虛擬網路顯示的窗格中，在 **"設置"** 部分中選擇**對等互連**。
4. 選擇要查看的對等互連。 然後，選擇 **"配置"** 以驗證是否已在連接到 ExpressRoute 電路的 VNetA 網路上啟用了**閘道傳輸**，並在未連接到 ExpressRoute 電路的遠端 VNetB 網路上**使用遠端閘道**。

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>在虛擬網路對等互連連接中配置傳輸路由

當虛擬網路已對等互連時，您也可以將對等互連虛擬網路中的閘道設定為內部部署網路的傳輸點。 要在虛擬網路對等互連中配置傳輸路由，請參閱[網路到網路連接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)。

> [!Note]
> 通過不同的部署模型創建的虛擬網路之間的對等關係不支援閘道傳輸。 對等關係中的兩個虛擬網路都必須通過資源管理器創建才能進行閘道傳輸才能工作。

要檢查是否已為虛擬網路對等互連配置了傳輸路由，請按照以下說明操作：

1. 使用具有必要[角色和許可權](virtual-network-manage-peering.md#permissions)的帳戶登錄到[Azure 門戶](https://portal.azure.com/)。
2. 在入口網站頂端包含「搜尋資源」**** 文字的方塊中，輸入「虛擬網路」****。 當搜尋結果中出現**虛擬網路**時加以選取。
3. 在顯示的 **"虛擬網路**"邊欄選項卡中，選擇要檢查對等互連設置的虛擬網路。
4. 在所選虛擬網路的窗格中，在 **"設置"** 部分中選擇**對等。**
5. 選擇要查看的對等互連。 驗證是否已啟用 **"允許閘道傳輸**"，並在**配置**下**使用遠端閘道**。

![用於檢查已配置虛擬網路對等互連的傳輸路線的選擇](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>在網路到網路連接中配置傳輸路由

要配置虛擬網路之間的傳輸路由，必須使用資源管理器部署模型和 PowerShell 在所有中間網路到網路連接上啟用 BGP。 有關說明，請參閱[如何使用 PowerShell 在 Azure VPN 閘道上配置 BGP。](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)

通過經典部署模型可以通過 Azure VPN 閘道傳輸流量，但這依賴于網路設定檔中的靜態定義的位址空間。 通過經典部署模型，Azure 虛擬網路和 VPN 閘道尚未支援 BGP。 如果沒有 BGP，手動定義傳輸位址空間容易出錯，我們不建議這樣做。

> [!Note]
> 通過使用 Azure 經典門戶或使用經典門戶中的網路設定檔來配置傳統的網路到網路連接。 不能通過 Azure 資源管理器部署模型或 Azure 門戶創建或修改經典虛擬網路。 有關經典虛擬網路的傳輸路由的詳細資訊，請參閱 Microsoft[開發人員博客](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)。

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>在網站到網站連接中配置傳輸路由

要配置本地網路和具有網站到網站連接的虛擬網路之間的傳輸路由，必須使用資源管理器部署模型和 PowerShell 在所有中間網站到網站連接上啟用 BGP。 有關如何使用 PowerShell 提供說明，[瞭解如何在 Azure VPN 閘道上配置 BGP。](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)

通過經典部署模型可以通過 Azure VPN 閘道傳輸流量，但這依賴于網路設定檔中的靜態定義的位址空間。 通過經典部署模型，Azure 虛擬網路和 VPN 閘道尚未支援 BGP。 如果沒有 BGP，手動定義傳輸位址空間容易出錯，我們不建議這樣做。

> [!Note]
> 通過使用 Azure 經典門戶或使用經典門戶中的網路設定檔配置經典網站到網站連接。 不能通過 Azure 資源管理器部署模型或 Azure 門戶創建或修改經典虛擬網路。 有關經典虛擬網路的傳輸路由的詳細資訊，請參閱 Microsoft[開發人員博客](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)。

## <a name="configure-bgp-for-a-vpn-gateway"></a>設定適用於 VPN 閘道的 BGP

BGP 是互聯網上用於在兩個或多個網路之間交換路由和可訪問性資訊的標準路由式通訊協定。 在 Azure 虛擬網路的上下文中使用 BGP 時，它啟用 Azure VPN 閘道和本地 VPN 設備（稱為 BGP 對等體或鄰居）。 它們交換"路由"，這些閘道將通知兩個閘道這些首碼通過所涉及的閘道或路由器的可用性和可訪問性。 

BGP 還可以通過將 BGP 閘道從一個 BGP 對等體到所有其他 BGP 對等體學習的路由傳播到多個網路之間，實現傳輸路由。 有關詳細資訊，請參閱使用[Azure VPN 閘道的 BGP 概述](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)。

### <a name="configure-bgp-for-a-vpn-connection"></a>為 VPN 連接配置 BGP

要配置使用 BGP 的 VPN 連接，請參閱[如何使用 PowerShell 在 Azure VPN 閘道上配置 BGP。](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)

通過為其創建自治系統 （AS） 編號，在虛擬網路閘道上啟用 BGP。 基本閘道不支援 BGP。 要檢查閘道的 SKU，請轉到 Azure 門戶中的**VPN 閘道**邊欄選項卡的 **"概述"** 部分。 如果您的 SKU 是**基本**，您必須將 SKU（請參閱[調整閘道大小](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)）更改為**VpnGw1**。 

檢查 SKU 將導致 20 到 30 分鐘的停機時間。 一旦閘道具有正確的 SKU，即可使用[Set-AzureRm 虛擬網路閘道](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0)PowerShell 命令添加 AS 編號。 配置 AS 編號後，將自動為閘道提供 BGP 對等 IP。

您必須手動提供`LocalNetworkGateway`AS 編號和 BGP 對等位址。 可以使用[New-AzureRm 本地網路閘道](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0)或[集 AzureRm 本地網路閘道](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0)PowerShell 命令來設置`ASN`和`-BgpPeeringAddress`值。 某些 AS 編號是為 Azure 保留的，不能像[有關 BGP 和 Azure VPN 閘道](../vpn-gateway/vpn-gateway-bgp-overview.md#faq)中所述使用它們。

連線物件必須啟用 BGP。 您可以通過[新 AzureRm 虛擬網路閘道連接](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)或[設置 AzureRm 虛擬網路閘道連接](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)將`-EnableBGP``$True`值設置為 。

### <a name="validate-the-bgp-configuration"></a>驗證 BGP 配置

要檢查 BGP 配置是否正確，可以運行`get-AzureRmVirtualNetworkGateway`和`get-AzureRmLocalNetworkGateway`命令。 然後，您將注意到零件中`BgpSettingsText`與 BGP 相關的輸出。 例如：

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>創建高可用活動/活動 VPN 連接

活動/主動和活動/備用閘道之間的主要區別是：

* 您必須創建兩個具有兩個公共 IP 位址的閘道 IP 配置。
* 您必須設置**啟用活動功能**標誌。
* 閘道 SKU 必須是**VpnGw1、VpnGw2**或**VpnGw3**。 **VpnGw2**

要實現跨本地和網對網路連接的高可用性，應部署多個 VPN 閘道，並在網路和 Azure 之間建立多個並行連接。 有關連接選項和拓撲的概述，請參閱[高度可用的跨界和網路到網路連接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)。

要創建活動/主動的跨界和網路到網路連接，請按照與[Azure VPN 閘道配置活動/活動 S2S VPN 連接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell)中的說明來配置處於活動/活動模式的 Azure VPN 閘道。

> [!Note]  
> * 將位址添加到啟用 BGP 的活動/活動模式的本地網路閘道時，*僅添加 BGP 對等體的 /32 位址*。 如果添加更多位址，它們將被視為靜態路由，並優先于 BGP 路由。
> * 對於連接到 Azure 的本地網路，必須使用不同的 BGP AS 編號。 （如果它們相同，則如果本地 VPN 設備已使用 ASN 與其他 BGP 鄰居對等，則必須更改虛擬網路 AS 編號。

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>部署後更改 Azure VPN 閘道類型

不能直接將 Azure 虛擬網路閘道類型從基於策略更改為基於路由，或者直接更改為其他方式。 您必須首先刪除閘道。 之後，IP 位址和預共用金鑰將不會保留。 然後，您可以創建所需類型的新閘道。 

要刪除和創建閘道，請按照以下步驟操作：

1. 刪除與原始閘道關聯的任何連接。
2. 使用 Azure 門戶、PowerShell 或經典 PowerShell 刪除閘道： 
   * [使用 Azure 門戶刪除虛擬網路閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [使用 PowerShell 刪除虛擬網路閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [使用 PowerShell 刪除虛擬網路閘道（經典）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. 按照[創建 VPN 閘道](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway)中的步驟創建所需類型的新閘道並完成 VPN 設置。

> [!Note]
> 此過程大約需要 60 分鐘。

## <a name="next-steps"></a>後續步驟

* [為 Azure VM 之間的連線問題疑難排解](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

