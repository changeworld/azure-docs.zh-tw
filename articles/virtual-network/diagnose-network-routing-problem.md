---
title: 診斷 Azure 虛擬機器路由問題 | Microsoft Docs
description: 了解如何藉由檢視虛擬機器的有效路由來診斷虛擬機器路由問題。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: 13d74fbb4a7c133ca2365fd2cbfce4b3d2bea72e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75350619"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>診斷虛擬機器路由問題

在本文中，您將了解如何藉由檢視對虛擬機器 (VM) 中的網路介面而言有效的路由，對來診斷路由問題。 Azure 會為每個虛擬網路子網路建立數個預設路由。 您可以在路由表中定義路由，並建立該路由表與子網路的關聯，以覆寫 Azure 的預設路由。 您所建立的路由、Azure 的預設路由和任何路由的組合若透過邊界閘道協定 (BGP) 經由 Azure VPN 閘道 (如果您的虛擬網路連線至內部部署網路) 從您的內部部署網路進行傳播，對子網路中的所有網路介面而言都將是有效路由。 如果您不熟悉虛擬網路、網路介面或路由概念，請參閱[虛擬網路概觀](virtual-networks-overview.md)、[網路介面](virtual-network-network-interface.md)及[路由概觀](virtual-networks-udr-overview.md)。

## <a name="scenario"></a>狀況

您嘗試連線至 VM，但連線失敗。 若要判斷為何無法連線至 VM，您可以使用 Azure [入口網站](#diagnose-using-azure-portal)、[PowerShell](#diagnose-using-powershell) 或 [Azure CLI](#diagnose-using-azure-cli) 來檢視網路介面的有效路由。

下列步驟假設您具有可檢視有效路由的現有 VM。 如果您沒有現有的 VM，請先部署 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM，用以完成本文中的工作。 本文中的範例適用于名為*myVM*且網路介面名為*myVMNic1*的 VM。 VM 和網路介面皆位於名為 *myResourceGroup* 的資源群組，且位於「美國東部」** 區域。 請針對您要診斷問題的 VM，適當地變更步驟中的值。

## <a name="diagnose-using-azure-portal"></a>使用 Azure 入口網站進行診斷

1. 使用具有[必要權限](virtual-network-network-interface.md#permissions)的 Azure 帳戶登入 Azure [入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站頂端的搜尋方塊中，輸入執行中 VM 的名稱。 當 VM 的名稱出現在搜尋結果中時，請加以選取。
3. 在左側的 [**設定**] 底下，選取 [**網路**]，然後選取網路介面資源的名稱來流覽。
     ![查看網路介面](./media/diagnose-network-routing-problem/view-nics.png)
4. 在左側選取 [**有效路由**]。 系統會顯示名為**myVMNic1**之網路介面的有效路由，如下圖所示![：查看有效路由](./media/diagnose-network-routing-problem/view-effective-routes.png)

    如果有多個網路介面連結至 VM，您可以選取任何網路介面以檢視其有效路由。 由於每個網路介面可能位於不同的子網路中，因此每個網路介面可能會有不同的有效路由。

    在上圖顯示的範例中，列出的路由是 Azure 為每個子網路建立的預設路由。 您的清單至少會有這些路由，但也可能會有其他路由，視您為虛擬網路啟用的功能而定，例如，您讓虛擬網路可與其他虛擬網路對等互連，或可透過 Azure VPN 閘道連線至您的內部部署網路。 若要深入了解每個路由，以及您可檢視的其他網路介面路由，請參閱[虛擬網路流量路由](virtual-networks-udr-overview.md)。 如果您的清單中有大量路由，您可以選取 [下載]**** 來下載含有路由清單的 .csv 檔案，試著簡化作業。

雖然您在前幾個步驟中是透過 VM 來檢視有效路由，但您也可以透過下列途徑來檢視有效路由：
- **個別網路介面**：了解如何[檢視網路介面](virtual-network-network-interface.md#view-network-interface-settings)。
- **個別路由表**：了解如何[檢視路由表](manage-route-table.md#view-details-of-a-route-table)。

## <a name="diagnose-using-powershell"></a>使用 PowerShell 進行診斷

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以執行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中採用的命令，或從您的電腦執行 PowerShell。 Azure Cloud Shell 是免費的互動式殼層。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 如果您從電腦執行 PowerShell，您需要 Azure PowerShell 模組1.0.0 版或更新版本。 請在您的電腦上執行 `Get-Module -ListAvailable Az`，以尋找已安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行`Connect-AzAccount` PowerShell，則也需要使用具有[必要許可權](virtual-network-network-interface.md#permissions)的帳戶來登入 Azure。

取得具有[AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)之網路介面的有效路由。 下列範例會從名為*myResourceGroup*的資源群組中，取得名為*myVMNic1*之網路介面的有效路由：

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

若要了解輸出中傳回的資訊，請參閱[路由概觀](virtual-networks-udr-overview.md)。 只有在 VM 處於執行狀態時，才會傳回輸出。 如果有多個網路介面連結至 VM，您可以檢閱每個網路介面的有效路由。 由於每個網路介面可能位於不同的子網路中，因此每個網路介面可能會有不同的有效路由。 如果仍有通訊問題，請參閱[其他診斷](#additional-diagnosis)和[考量](#considerations)。

如果您不知道網路介面的名稱，但知道該網路介面所附加至的 VM 名稱，則下列命令會針對所有附加至 VM 的網路介面，傳回其識別碼：

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

您會收到類似於下列範例的輸出：

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

在先前的輸出中，網路介面名稱是*myVMNic1*。

## <a name="diagnose-using-azure-cli"></a>使用 Azure CLI 進行診斷

您可以執行 [Azure Cloud Shell](https://shell.azure.com/bash) 中採用的命令，或從您的電腦執行 CLI。 本文需要 Azure CLI 2.0.32 版或更新的版本。 執行 `az --version` 來了解安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 如果您在本機執行 Azure CLI，還需要執行 `az login` 並使用具有[必要權限](virtual-network-network-interface.md#permissions)的帳戶登入 Azure。

使用 [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) 取得網路介面的有效路由。 下列範例會從名為*myResourceGroup*的資源群組中，取得名為*myVMNic1*之網路介面的有效路由：

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

若要了解輸出中傳回的資訊，請參閱[路由概觀](virtual-networks-udr-overview.md)。 只有在 VM 處於執行狀態時，才會傳回輸出。 如果有多個網路介面連結至 VM，您可以檢閱每個網路介面的有效路由。 由於每個網路介面可能位於不同的子網路中，因此每個網路介面可能會有不同的有效路由。 如果仍有通訊問題，請參閱[其他診斷](#additional-diagnosis)和[考量](#considerations)。

如果您不知道網路介面的名稱，但知道該網路介面所附加至的 VM 名稱，則下列命令會針對所有附加至 VM 的網路介面，傳回其識別碼：

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>解決問題

要解決路由問題通常需要：

- 新增自訂路由以覆寫 Azure 的其中一個預設路由。 了解如何[新增自訂路由](manage-route-table.md#create-a-route)。
- 變更或移除可能導致路由至不當位置的自訂路由。 了解如何[變更](manage-route-table.md#change-a-route)或[刪除](manage-route-table.md#delete-a-route)自訂路由。
- 確定您已定義的任何自訂路由所屬的路由表已與網路介面所在的子網路相關聯。 了解如何[建立路由表與子網路的關聯](manage-route-table.md#associate-a-route-table-to-a-subnet)。
- 確定您已部署的裝置 (例如 Azure VPN 閘道或網路虛擬設備) 是可執行的。 使用網路監看員的 [VPN 診斷](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json)功能判斷 Azure VPN 閘道是否有任何問題。

如果您仍有通訊問題，請參閱[考慮](#considerations)和其他診斷。

## <a name="considerations"></a>考量

對通訊問題進行疑難排解時，請考量以下幾點︰

- 路由所依據的是您所定義的路由、邊界閘道協定 (BGP) 和系統路由之間的最長首碼比對 (LPM)。 如果有多個路由有相同的 LPM 相符項，則會根據路由來源，以[路由概觀](virtual-networks-udr-overview.md#how-azure-selects-a-route)中所列的順序選取路由。 有有效路由時，您將只會看到所有可用的路由中符合 LPM 的有效路由。 了解如何評估網路介面的路由，有助於對可能會影響 VM 通訊的特定路由進行疑難排解。
- 如果您已定義網路虛擬設備 (NVA) 的自訂路由，並以 *VirtualAppliance* 作為下一個躍點類型，請確定接收流量的 NVA 已啟用 IP 轉送，否則封包會被捨棄。 深入了解如何[啟用網路介面的 IP 轉送](virtual-network-network-interface.md#enable-or-disable-ip-forwarding)。 此外，作業系統或 NVA 內的應用程式也必須能夠轉送網路流量，而進行相關設定。
- 如果您已建立 0.0.0.0/0 的路由，則所有輸出網際網路流量都會路由至您所指定的下一個躍點，例如 NVA 或 VPN 閘道。 我們通常將建立此類路由的作業稱為強制通道。 使用 RDP 或 SSH 通訊協定從網際網路連至 VM 的遠端連線可能無法以此路由執行，這取決於下一個躍點處理流量的方式。 符合下列其中一個條件時，可以啟用強制通道︰
    - 透過下一個躍點類型的 *VPN 閘道*建立路由，而使用站對站 VPN 時。 深入了解如何[設定強制通道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
    - 如果在使用站對站 VPN 或 ExpressRoute 線路時透過 BGP 經由虛擬網路閘道通告了 0.0.0.0/0 (預設路由)。 深入了解如何搭配使用 BGP 與[站對站 VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering)。
- 若要讓虛擬網路對等互連流量正常運作，對等互連虛擬網路的首碼範圍必須要有下一個躍點類型為 *VNet 對等互連*的系統路由。 如果這類路由不存在，且虛擬網路對等互連連結已**連線**：
    - 請在等候數秒後重試。 這是新建立的對等互連連結，有時候需要較長的時間才能將路由傳播到子網路中的所有網路介面。 若要深入了解虛擬網路對等互連，請參閱[虛擬網路對等互連概觀](virtual-network-peering-overview.md)和[管理虛擬網路對等互連](virtual-network-manage-peering.md)。
    - 網路安全性群組規則可能會影響通訊。 如需詳細資訊，請參閱[診斷虛擬機器網路流量篩選問題](diagnose-network-traffic-filter-problem.md)。
- 雖然 Azure 會為每個 Azure 網路介面指派預設路由，但如果您有多個網路介面連結至 VM，則在 VM 的作業系統內只會為主要網路介面指派預設路由 (0.0.0.0/0) 或閘道。 了解如何為連結至 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 或 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) VM 的次要網路介面建立預設路由。 深入了解[主要和次要網路介面](virtual-network-network-interface-vm.md#constraints)。

## <a name="additional-diagnosis"></a>其他診斷

* 若要執行快速測試，針對以某位置為目標的流量判斷其下一個躍點類型，請使用 Azure 網路監看員的[下一個躍點](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)功能。 下一個躍點會指出以指定位置為目標的流量所使用的下一個躍點類型。
* 如果沒有任何會造成 VM 網路通訊失敗的路由，則問題可能是由執行於 VM 作業系統內的防火牆軟體所致
* 如果您要使用[強制通道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)透過 VPN 閘道或 NVA 將流量傳至內部部署裝置，您可能無法從網際網路連線至 VM，視您為裝置設定路由的方式而定。 請確認您為裝置設定的路由會將流量路由至 VM 的公用或私人 IP 位址。
* 使用網路監看員的[連線疑難排解](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)功能，判斷輸出通訊問題的路由、篩選和 OS 內部原因。

## <a name="next-steps"></a>後續步驟

- 了解[路由表和路由](manage-route-table.md)的所有工作、屬性和設定。
- 了解所有的[下一個躍點類型、系統路由和 Azure 選取路由的方式](virtual-networks-udr-overview.md)。
