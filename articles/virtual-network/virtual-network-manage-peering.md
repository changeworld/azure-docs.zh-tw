---
title: 建立、變更或刪除 Azure 虛擬網路對等互連 | Microsoft Docs
description: 建立、變更或刪除虛擬網路對等互連。 使用虛擬網路對等互連時，您可以連接相同區域和跨區域的虛擬網路。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: altambaw
ms.openlocfilehash: dc8db3f1eccce2bb85f03d51fcfd1c4113823d49
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222661"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>建立、變更或刪除虛擬網路對等互連

了解如何建立、變更或刪除虛擬網路對等互連。 虛擬網路對等互連可讓您透過 Azure 骨幹網路，將同一區域或跨區域 (也稱為全域 VNet 對等互連) 的虛擬網路連線。 對等互連建立好之後，系統仍會將虛擬網路視為獨立資源來進行管理。 如果您不熟悉虛擬網路同儕節點，您可以在[虛擬網路同儕節點概觀](virtual-network-peering-overview.md)或透過完成[教學課程](tutorial-connect-virtual-networks-portal.md)來深入了解。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在完成本文任一節的步驟之前，請先完成下列工作︰

- 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果使用入口網站，請開啟 https://portal.azure.com ，並使用具有[必要的權限](#permissions)而可與對等互連搭配運作的帳戶來登入。
- 如果使用 PowerShell 命令來完成這篇文章中的工作，請在 [Azure Cloud Shell](https://shell.azure.com/powershell) \(英文\) 中執行命令，或從您的電腦執行 PowerShell。 Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 本教學課程需要 Azure PowerShell 模組 1.0.0 版或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您要在本機執行 PowerShell，則還需要使用具有[必要的權限](#permissions)而可與對等互連搭配運作的帳戶來執行 `Connect-AzAccount`，以建立與 Azure 的連線。
- 如果使用命令列介面 (CLI) 命令來完成這篇文章中的工作，請在 [Azure Cloud Shell](https://shell.azure.com/bash) \(英文\) 中執行命令，或從您的電腦執行 CLI。 本教學課程需要 Azure CLI 2.0.31 版或更新版本。 執行 `az --version` 來了解安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 如果您要在本機執行 Azure CLI，則還需要使用具有[必要的權限](#permissions)而可與對等互連搭配運作的帳戶來執行 `az login`，以建立與 Azure 的連線。

您登入或連線到 Azure 的帳戶必須指派給「[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」角色，或指派給已獲指派 [[許可權](#permissions)] 中所列適當動作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="create-a-peering"></a>建立對等互連

建立對等互連之前，請先熟悉需求和限制與[必要的權限](#permissions)。

1. 在 Azure 入口網站頂端的搜尋方塊中輸入「虛擬網路」。 當搜尋結果中出現 **虛擬網路** 時加以選取。 如果清單中出現 [虛擬網路 (傳統)] 選項，請勿選取此選項，因為您並無法從透過傳統部署模型所部署的虛擬網路建立對等互連。
2. 在清單中選取您想要為其建立對等互連的虛擬網路。
3. 在 [設定] 底下，選取 [對等互連]。
4. 選取 [+ 新增]  。 
5. <a name="add-peering"></a>輸入或選取下列設定的值：
    - **名稱︰** 對等互連的名稱必須是虛擬網路中的唯一名稱。
    - **虛擬網路部署模型：** 選取您想要對等互連之虛擬網路是透過哪個部署模型所部署的。
    - **我知道資源識別碼：** 如果您有權讀取所要對等互連的虛擬網路，請讓此核取方塊保持未選取狀態。 如果您無權讀取所要對等互連的虛擬網路或訂用帳戶，請核取此方塊。 針對在核取方塊時所出現的 [資源識別碼] 方塊，輸入您想要對等互連之虛擬網路的完整資源識別碼。 您輸入的資源識別碼所屬的虛擬網路，必須和此虛擬網路位於相同的，或[支援之不同的 ](#requirements-and-constraints)Azure[ 區域](https://azure.microsoft.com/regions)中。 完整的資源識別碼看起來會像這樣 `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>` 。 您可以檢視虛擬網路的屬性，以取得虛擬網路的資源識別碼。 若要了解如何檢視虛擬網路的屬性，請參閱＜[管理虛擬網路](manage-virtual-network.md#view-virtual-networks-and-settings)＞。 如果訂用帳戶與不同的 Azure Active Directory 租用戶相關聯，而不是與其所含虛擬網路要建立對等互連的訂用帳戶相關聯，首先請將每個租用戶的使用者新增為相對租用戶的[來賓使用者](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory)。
    - **訂用帳戶：** 選取您想要對等互連之虛擬網路的 [訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)。 視帳戶有權讀取的訂用帳戶而定，系統會列出一或多個訂用帳戶。 如果您已核取 [資源識別碼] 核取方塊，則無法使用這項設定。
    - **虛擬網路：** 選取您想要對等互連的虛擬網路。 您可以選取透過任一 Azure 部署模型建立的虛擬網路。 如果您要選取不同區域中的虛擬網路，則必須選取[支援區域](#cross-region)中的虛擬網路。 您必須有權讀取虛擬網路，該虛擬網路才會顯示在清單中。 如果虛擬網路雖有列出卻呈現灰色，原因可能是該虛擬網路的位址空間與此虛擬網路的位址空間重疊。 如果虛擬網路的位址空間重疊，您就無法讓這些位址空間對等互連。 如果您已核取 [資源識別碼] 核取方塊，則無法使用這項設定。
    - **允許虛擬網路存取：** 如果您想要透過預設流程啟用兩個虛擬網路之間的通訊，請選取 [ **已啟用** ] (預設) `VirtualNetwork` 。 讓虛擬網路能夠彼此通訊，會讓連線到任一虛擬網路的資源能夠以相同的頻寬和延遲彼此通訊，彷彿這些資源是連線到相同的虛擬網路。 兩個虛擬網路中的資源之間所進行的所有通訊都是透過 Azure 私人網路來完成。 當 **啟用** 此設定時，網路安全性群組的 **VirtualNetwork** 服務標記包含虛擬網路和對等互連虛擬網路。  (若要深入瞭解網路安全性群組服務標籤，請參閱 [網路安全性群組總覽](./network-security-groups-overview.md#service-tags)) 。如果您不想讓流量預設流向對等互連虛擬網路，請選取 [ **停用** ]。 如果您已使用另一個虛擬網路對等互連虛擬網路，但偶爾想要停用兩個虛擬網路之間的預設流量，則可以選取 [ **已停用** ]。 您可能會發現啟用/停用功能比起先刪除再重新建立對等互連更為方便。 停用此設定時，流量預設不會在對等互連虛擬網路之間流動;不過，如果透過包含適當的 IP 位址或應用程式安全性群組的 [網路安全性群組](./network-security-groups-overview.md) 規則明確允許流量，流量仍可能會流動。
      > [!WARNING]
      > 停用 [ **允許虛擬網路存取** ] 設定只會變更 **VirtualNetwork** 服務標記的定義。 它 *不會* 完全防止對等連線的流量流動，如這項設定描述中所述。    
    - **允許轉寄的流量：** 若要允許虛擬網路中網路虛擬設備 (不是源自虛擬網路)「所轉送的」流量透過對等互連流向此虛擬網路，請選取此方塊。 例如，假設有三個虛擬網路，分別名為 Spoke1、Spoke2 及 Hub。 每個支點 (Spoke) 虛擬網路與中樞 (Hub) 虛擬網路之間都有對等互連，但支點虛擬網路之間並沒有對等互連。 在中樞虛擬網路中已部署一個網路虛擬設備，並且在每個支點虛擬網路都套用了使用者定義的路由，可透過該網路虛擬設備來路由傳送子網路之間的流量。 如果未核取此核取方塊以進行每個輪輻虛擬網路和中樞虛擬網路之間的對等互連，流量就不會在輪輻虛擬網路之間流動，因為中樞不會轉送虛擬網路之間的流量。 啟用這項功能雖能允許轉送的流量通過對等互連，卻不會建立任何使用者定義的路由或網路虛擬設備。 使用者定義的路由和網路虛擬設備是另外建立的。 了解[使用者定義的路由](virtual-networks-udr-overview.md#user-defined)。 如果透過「Azure VPN 閘道」在虛擬網路之間轉送流量，您就無須檢查此設定。
    - **允許閘道傳輸：** 如果您有連結到此虛擬網路的虛擬網路閘道，且想要讓來自對等互連虛擬網路的流量能夠流經閘道，請核取此方塊。 例如，此虛擬網路可能會透過虛擬網路閘道連結到內部部署網路。 此閘道可以是 ExpressRoute 或 VPN 閘道。 核取此方塊可允許來自對等互連虛擬網路的流量，流經連結到此虛擬網路的閘道，再流向內部部署網路。 如果您核取此方塊，對等互連的虛擬網路將無法設定閘道。 在設定從其他虛擬網路到這個虛擬網路的對等互連時，在對等互連的虛擬網路上必須選取 [使用遠端閘道] 核取方塊。 如果您讓此方塊保持未核取狀態 (此為預設值)，來自對等互連虛擬網路的流量仍會流到此虛擬網路，但無法流經連結到此虛擬網路的虛擬網路閘道。 如果同儕節點位在虛擬網路 (資源管理員) 和虛擬網路 (傳統) 之間，則閘道必須位在虛擬網路 (資源管理員) 之中。

       除了將流量轉送到內部部署網路之外，VPN 閘道也可以在已與閘道所在虛擬網路對等互連的虛擬網路之間轉送網路流量，而不需要虛擬網路彼此對等互連。 當您想要使用中樞虛擬網路中的 VPN 閘道 (請參閱針對 **允許轉寄的流量** 說明的中樞和支點範例)，來路由傳送彼此未對等互連之支點虛擬網路間的流量時，使用 VPN 閘道轉寄流量相當有用。 若要深入了解如何允許使用閘道來進行傳輸，請參閱[設定 VPN 閘道以在虛擬網路對等互連中進行傳輸](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 此案例需要實作指定虛擬網路閘道作為下一個躍點類型的使用者定義路由。 了解[使用者定義的路由](virtual-networks-udr-overview.md#user-defined)。 您只能指定 VPN 閘道作為使用者定義路由中的下一個躍點類型，不能指定 ExpressRoute 閘道作為使用者定義路由中的下一個躍點類型。

    - **使用遠端閘道：** 核取此方塊可讓來自此虛擬網路的流量能夠流經連結到所要對等互連之虛擬網路的虛擬網路閘道。 例如，您要對等互連的虛擬網路已連結 VPN 閘道，而能夠與內部部署網路通訊。  核取此方塊將可讓來自此虛擬網路的流量流經連結到對等互連虛擬網路的 VPN 閘道。 如果您核取此方塊，對等互連的虛擬網路必須有連結的虛擬網路閘道，而且必須已核取 [允許閘道傳輸] 核取方塊。 如果您讓此方塊保持未核取狀態 (此為預設值)，來自對等互連虛擬網路的流量仍可流到此虛擬網路，但無法流經連結到此虛擬網路的虛擬網路閘道。
    
      此虛擬網路只能有一個對等互連啟用此設定。

      如果您已在虛擬網路中設定閘道，就無法使用遠端閘道。 若要深入瞭解如何使用閘道來進行傳輸，請參閱 [設定 VPN 閘道以在虛擬網路對等互連中進行傳輸](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        
    > [!NOTE]
    > 如果您使用虛擬網路閘道將內部部署流量傳遞至對等互連 VNet，則內部部署 VPN 裝置的對等互連 VNet IP 範圍必須設定為「有趣」的流量。 否則，您的內部部署資源將無法與對等互連 VNet 中的資源進行通訊。

6. 選取 [確定] 以對您選取的虛擬網路新增對等互連。

如需在不同訂用帳戶和部署模型的虛擬網路之間實作對等互連的逐步指示，請參閱[後續步驟](#next-steps)。

### <a name="commands"></a>命令

- **Azure CLI**：[az network vnet peering create](/cli/azure/network/vnet/peering)
- **PowerShell**： [新增-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>檢視或變更對等互連設定

變更對等互連之前，請先熟悉需求和限制與[必要的權限](#permissions)。

1. 在入口網站頂端的搜尋方塊中輸入「虛擬網路」。 當搜尋結果中出現 **虛擬網路** 時加以選取。 如果清單中出現 [虛擬網路 (傳統)] 選項，請勿選取此選項，因為您並無法從透過傳統部署模型所部署的虛擬網路建立對等互連。
2. 在清單中選取您想要為其變更對等互連設定的虛擬網路。
3. 在 [設定] 底下，選取 [對等互連]。
4. 選取您想要檢視或變更設定的對等互連。
5. 變更適當的設定。 請參閱建立對等互連的 [步驟 5](#add-peering) 中每項設定的選項。
6. 選取 [儲存]。

**命令**

- **Azure CLI**：[az network vnet peering list](/cli/azure/network/vnet/peering) 可列出虛擬網路的對等互連，[az network vnet peering show](/cli/azure/network/vnet/peering) 可顯示特定對等互連的設定，而 [az network vnet peering update](/cli/azure/network/vnet/peering) 則可變更對等互連設定。
- **PowerShell**： [AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) 以取得 view 對等互連設定，並 [設定 AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) 變更設定。

## <a name="delete-a-peering"></a>刪除對等互連

在刪除對等互連之前，請確定您的帳戶具有[必要的權限](#permissions)。

若您刪除對等互連，來自虛擬網路的流量將不會再流到對等互連的虛擬網路。 當透過 Resource Manager 所部署的虛擬網路建立了對等互連時，每個虛擬網路都會與其他虛擬網路對等互連。 從某個虛擬網路刪除對等互連時雖會停用虛擬網路之間的通訊功能，卻不會從其他虛擬網路刪除對等互連。 位於其他虛擬網路之對等互連的對等互連狀態為 [已中斷連線]。 在重新建立第一個虛擬網路中的對等互連，且兩個虛擬網路的對等互連狀態都變為 [已連線] 之前，您將無法重新建立對等互連。

如果您想要讓虛擬網路在某些時候進行通訊，卻又不想永遠啟用此通訊功能，您不必刪除對等互連，相反地，您可以將 [允許虛擬網路存取] 設定設為 [停用]。 若要了解如何操作，請閱讀本文建立對等互連一節的步驟 6。 您可能會發現停用和啟用網路存取比起先刪除再重新建立對等互連更為簡單。

1. 在入口網站頂端的搜尋方塊中輸入「虛擬網路」。 當搜尋結果中出現 **虛擬網路** 時加以選取。 如果清單中出現 [虛擬網路 (傳統)] 選項，請勿選取此選項，因為您並無法從透過傳統部署模型所部署的虛擬網路建立對等互連。
2. 在清單中選取您想要為其刪除對等互連的虛擬網路。
3. 在 [設定] 底下，選取 [對等互連]。
4. 在您想要刪除的對等互連右側，選取 [...]，選取 [刪除]，然後選取 [是] 以從第一個虛擬網路刪除對等互連。
5. 完成上述步驟即可從對等互連中的其他虛擬網路刪除對等互連。

**命令**

- **Azure CLI**：[az network vnet peering delete](/cli/azure/network/vnet/peering)
- **PowerShell**： [移除-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>需求和限制

- <a name="cross-region"></a>您可以將相同區域或不同區域中的虛擬網路對等互連。 不同區域中的對等互連虛擬網路也稱為 *全域 VNet 對等互連*。 
- 建立全域對等互連時，對等互連的虛擬網路可以存在於任何 Azure 公用雲端區域或中國雲端區域或政府雲端區域中。 您無法對多個雲端進行對等互連。 例如，Azure 公用雲端中的 VNet 無法對等互連至 Azure 中國雲端中的 VNet。
- 一個虛擬網路中的資源無法與全域對等互連虛擬網路中的基本內部負載平衡器的前端 IP 位址通訊。 基本負載平衡器只能在相同區域內提供支援。 VNet 對等互連和全域 VNet 對等互連可支援 Standard Load Balancer。 使用基本負載平衡器的服務，將無法透過全域 VNet 對等互連運作，如下所述 [。](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- 您可以使用遠端閘道，或允許在全域對等互連的虛擬網路和本地對等互連的虛擬網路中進行閘道傳輸。
- 虛擬網路可位於相同或不同的訂用帳戶。 當您將不同訂用帳戶中的虛擬網路對等互連時，這兩個訂用帳戶可以與相同或不同的 Azure Active Directory 租用戶相關聯。 如果您還沒有 AD 租使用者，您可以 [建立一個](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant)。
- 要建立對等互連的虛擬網路必須有非重疊的 IP 位址空間。
- 一旦虛擬網路與另一個虛擬網路對等互連，您便無法在虛擬網路中新增或刪除位址範圍。 若要新增或移除位址範圍，請刪除對等互連，新增或移除位址範圍，然後重新建立對等互連。 若要在虛擬網路新增或移除位址範圍，請參閱[管理虛擬網路](manage-virtual-network.md)。
- 您可以將透過 Resource Manager 所部署的兩個虛擬網路對等互連，或將透過 Resource Manager 所部署的虛擬網路與透過傳統部署模型所部署的虛擬網路對等互連。 您無法將兩個透過傳統部署模型所建立的虛擬網路對等互連。 如果您不熟悉 Azure 部署模型，請閱讀[了解 Azure 部署模型](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。 您可以使用 [VPN 閘道](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V)，將兩個透過傳統部署模型建立的虛擬網路加以連線。
- 將兩個透過 Resource Manager 所建立的虛擬網路對等互連時，您必須為對等互連中的每個虛擬網路設定對等互連。 您會看到下列其中一種對等互連狀態類型： 
  - *起始：* 當您從第一個虛擬網路建立第二個虛擬網路的對等互連時，即會 *起始* 對等互連狀態。 
  - *已連線：* 當您建立從第二個虛擬網路到第一個虛擬網路的對等互連時，其對等互連狀態為 [已 *連線*]。 如果您查看第一個虛擬網路的對等互連狀態，您會看到其狀態 *從 [已起始]* 變更為 [ *已連線*]。 在兩個虛擬網路對等互連的對等互連狀態都已 *連線* 之前，無法成功建立對等互連。
- 在將透過 Resource Manager 所建立的虛擬網路與透過傳統部署模型所建立的虛擬網路對等互連時，您只會對透過 Resource Manager 所部署的虛擬網路設定對等互連。 您無法對虛擬網路 (傳統) 設定對等互連，也無法在兩個透過傳統部署模型所部署的虛擬網路之間設定對等互連。 當您從虛擬網路 (Resource Manager) 對虛擬網路 (傳統) 建立對等互連時，對等互連狀態先是 [更新中]，然後很快就會變為 [已連線]。
- 兩個虛擬網路之間建立了對等互連。 對等互連本身不是可轉移的。 如果您在下列項目之間建立對等互連：
  - VirtualNetwork1 & VirtualNetwork2-VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3-VirtualNetwork2 & VirtualNetwork3


  則 VirtualNetwork1 與 VirtualNetwork3 之間並無法透過 VirtualNetwork2 而建立對等互連。 如果您想要在 VirtualNetwork1 和 VirtualNetwork3 之間建立虛擬網路對等互連，就必須在 VirtualNetwork1 和 VirtualNetwork3 之間建立對等互連。 則 VirtualNetwork1 與 VirtualNetwork3 之間並無法透過 VirtualNetwork2 而建立對等互連。 如果您想要讓 VirtualNetwork1 和 VirtualNetwork3 直接進行通訊，您必須在 VirtualNetwork1 和 VirtualNetwork3 之間建立明確的對等互連，或透過中樞網路中的 NVA 進行。  
- 您無法使用預設的 Azure 名稱解析在對等互連的虛擬網路中解析名稱。 若要在其他虛擬網路中解析名稱，您必須使用[適用於私人網域的 Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或自訂 DNS 伺服器。 若要了解如何設定自有 DNS 伺服器，請參閱[使用專屬 DNS 伺服器的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。
- 相同區域的對等互連虛擬網路中的資源可彼此通訊，且通訊時會有相同的頻寬和延遲，彷彿這些資源是位於相同的虛擬網路中。 不過，每個虛擬機器大小各有其網路頻寬上限。 若要深入了解不同虛擬機器大小的網路頻寬上限，請參閱 [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器大小。
- 虛擬網路可以既與另一個虛擬網路對等互連，又同時連線到另一個具有 Azure 虛擬網路閘道的虛擬網路。 當虛擬網路同時透過對等互連和閘道進行連線時，虛擬網路之間的流量會透過對等互連組態來流動，而不會透過閘道。
- 成功設定虛擬網路對等互連之後，必須再次下載「點對站」VPN 用戶端，以確保新的路由下載至用戶端。
- 我們會針對使用虛擬網路對等互連的輸入和輸出流量收取少許費用。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/virtual-network)。

## <a name="permissions"></a>權限

您用來處理虛擬網路同儕節點的帳戶必須指派給下列角色：

- [網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)：適用於透過資源管理員部署的虛擬網路。
- [傳統網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)：適用於透過傳統部署模型部署的虛擬網路。

如果您的帳戶未指派給上述其中一個角色，則必須指派給[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，且該角色已獲指派下表中的必要動作：

| 動作                                                          | 名稱 |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | 建立從虛擬網路 A 到虛擬網路 B 的對等互連時所需。虛擬網路 A 必須為虛擬網路 (資源管理員)          |
| Microsoft.Network/virtualNetworks/peer/action                   | 建立從虛擬網路 B (資源管理員) 到虛擬網路 A 的對等互連時所需                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | 建立從虛擬網路 B (傳統) 到虛擬網路 A 的對等互連時所需                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | 讀取虛擬網路對等互連   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 刪除虛擬網路對等互連 |

## <a name="next-steps"></a>後續步驟

- 虛擬網路對等互連是建立於虛擬網路之間，而這兩個虛擬網路是透過存在於相同或不同訂用帳戶中的相同或不同部署模型所建立。 完成下列其中一個案例的教學課程：

  |Azure 部署模型             | 訂用帳戶  |
  |---------                          |---------|
  |兩者皆使用 Resource Manager              |[相同](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[不同](create-peering-different-subscriptions.md)|
  |一個使用 Resource Manager、一個使用傳統部署模型  |[相同](create-peering-different-deployment-models.md)|
  |                                   |[不同](create-peering-different-deployment-models-subscriptions.md)|

- 瞭解如何建立 [中樞和輪輻網路拓撲](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 範例指令碼，或使用 Azure [Resource Manager 範本](template-samples.md)建立虛擬網路對等互連
- 建立並指派虛擬網路的[Azure 原則定義](./policy-reference.md)