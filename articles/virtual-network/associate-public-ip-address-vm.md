---
title: 將公用 IP 位址與虛擬機器建立關聯
titlesuffix: Azure Virtual Network
description: 瞭解如何使用 Azure 入口網站或 Azure CLI，將公用 IP 位址與虛擬機器建立關聯 (VM) 。
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 9b5fc9e4118d98905138b7f205f61d85a96b60b0
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035462"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>將公用 IP 位址與虛擬機器建立關聯

在本文中，您將瞭解如何將公用 IP 位址與 (VM) 的現有虛擬機器建立關聯。 如果您想要從網際網路連線至 VM，VM 必須具有相關聯的公用 IP 位址。 如果您想要使用公用 IP 位址建立新的 VM，您可以使用[Azure 入口網站](virtual-network-deploy-static-pip-arm-portal.md)、 [Azure 命令列介面 (CLI) ](virtual-network-deploy-static-pip-arm-cli.md)或[PowerShell](virtual-network-deploy-static-pip-arm-ps.md)來執行此動作。 公用 IP 位址需要少許費用。 如需詳細資料，請參閱[定價](https://azure.microsoft.com/pricing/details/ip-addresses/)。 您可以針對每個訂用帳戶使用的公用 IP 位址數目有所限制。 如需詳細資訊，請參閱[限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)。

您可以使用[Azure 入口網站](#azure-portal)、Azure[命令列介面](#azure-cli) (CLI) 或[PowerShell](#powershell) ，將公用 IP 位址與 VM 建立關聯。

## <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 流覽或搜尋您想要新增公用 IP 位址的虛擬機器，然後選取它。
3. 在 [**設定**] 底下，選取 [**網路**]，然後選取您想要新增公用 IP 位址的網路介面，如下圖所示：

   ![選取網路介面](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > 公用 IP 位址會與連接至 VM 的網路介面相關聯。 在上圖中，VM 只有一個網路介面。 如果 VM 有多個網路介面，它們會出現，而且您會選取要與公用 IP 位址產生關聯的網路介面。

4. 選取 [ **ip**設定]，然後選取 ip 設定，如下圖所示：

   ![選取 IP 設定](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > 公用 IP 位址會與網路介面的 IP 設定產生關聯。 在上圖中，網路介面有一個 IP 設定。 如果網路介面有多個 IP 設定，它們會出現在清單中，而且您會選取要與公用 IP 位址產生關聯的 IP 設定。

5. 選取 [**已啟用**]，然後選取 [ **IP 位址] (*設定所需的設定*) **。 選擇現有的公用 IP 位址，這會自動關閉 [**選擇公用 ip 位址**] 方塊。 如果您沒有列出任何可用的公用 IP 位址，則必須建立一個。 若要瞭解作法，請參閱[建立公用 IP 位址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 選取 [**儲存**]，如下圖所示，然後關閉 IP 設定的方塊。

   ![啟用公用 IP 位址](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > 出現的公用 IP 位址與 VM 位於相同區域中。 如果您在區域中建立了多個公用 IP 位址，則會顯示在這裡。 如果有任何一個呈現灰色，這是因為位址已與不同的資源相關聯。

6. 查看指派給 IP 設定的公用 IP 位址，如下圖所示。 可能需要幾秒鐘的時間才會顯示 IP 位址。

   ![View 已指派的公用 IP 位址](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > 此位址是從每個 Azure 區域中使用的位址集區所指派。 若要查看每個區域中使用的位址集區清單，請參閱[Microsoft Azure DATACENTER IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 指派的位址可以是用於區域之集區中的任何位址。 如果您需要從區域中的特定集區指派位址，請使用[公用 IP 位址首碼](public-ip-address-prefix.md)。

7. 允許在網路安全性群組中使用安全性規則[對 VM 進行網路流量](#allow-network-traffic-to-the-vm)。

## <a name="azure-cli"></a>Azure CLI

安裝 [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 或使用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 在接下來的 CLI 命令中，選取 [試用] 按鈕。 選取 [試用]，會叫用可登入 Azure 帳戶的 Cloud Shell。

1. 如果在 Bash 中使用本機 CLI，請使用 `az login` 登入 Azure。
2. 公用 IP 位址會與連接至 VM 的網路介面 IP 設定建立關聯。 使用[az network nic-ip-HTTPs update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update)命令，將公用 ip 位址與 ip 設定產生關聯。 下列範例會將名為*myVMPublicIP*的現有公用 IP 位址，與名為*myResourceGroup*之資源群組中名為*myVMVMNic*的現有網路*介面的 IP*設定產生關聯。
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - 如果您沒有現有的公用 IP 位址，請使用[az network public ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)命令來建立一個。 例如，下列命令會在名為*myResourceGroup*的資源群組中建立名為*MYVMPUBLICIP*的公用 IP 位址。
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > 先前的命令會針對您可能想要自訂的數個設定，建立具有預設值的公用 IP 位址。 若要深入瞭解所有公用 IP 位址設定，請參閱[建立公用 ip 位址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 此位址是從每個 Azure 區域所使用的公用 IP 位址集區所指派。 若要查看每個區域中使用的位址集區清單，請參閱[Microsoft Azure DATACENTER IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

   - 如果您不知道連接至 VM 的網路介面名稱，請使用 [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) 命令來加以查看。 例如，下列命令會列出連接至 *myResourceGroup* 資源群組中 *myVM* VM 的網路介面名稱：

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     此輸出包含一或多行，與下列範例類似：
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     在上述範例中，*myVMVMNic* 是網路介面的名稱。

   - 如果您不知道網路介面的 IP 設定名稱，請使用 [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) 命令來取得名稱。 例如，下列命令會列出 *myResourceGroup* 資源群組中 *myVMVMNic* 網路介面的 IP 設定名稱：

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. 使用[az vm list-ip 位址](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses)命令來查看指派給 IP 設定的公用 IP 位址。 下列範例顯示指派給名為*myResourceGroup*的資源群組中名為*MYVM*之現有 VM 的 IP 位址。

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > 此位址是從每個 Azure 區域中使用的位址集區所指派。 若要查看每個區域中使用的位址集區清單，請參閱[Microsoft Azure DATACENTER IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 指派的位址可以是用於區域之集區中的任何位址。 如果您需要從區域中的特定集區指派位址，請使用[公用 IP 位址首碼](public-ip-address-prefix.md)。

4. 允許在網路安全性群組中使用安全性規則[對 VM 進行網路流量](#allow-network-traffic-to-the-vm)。

## <a name="powershell"></a>PowerShell

安裝 [PowerShell](/powershell/azure/install-az-ps) 或使用 Azure Cloud Shell。 Azure Cloud Shell 是免費的殼層，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 PowerShell，可與您的帳戶搭配使用。 在接下來的 PowerShell 命令中，選取 [試用] 按鈕。 選取 [試用]，會叫用可登入 Azure 帳戶的 Cloud Shell。

1. 如果在本機使用 PowerShell，請使用 `Connect-AzAccount` 登入 Azure。
2. 公用 IP 位址會與連接至 VM 的網路介面 IP 設定建立關聯。 使用[new-azvirtualnetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork)和[new-azvirtualnetworksubnetconfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig)命令，取得網路介面所在的虛擬網路和子網。 接下來，使用[new-aznetworkinterface](/powershell/module/Az.Network/Get-AzNetworkInterface)命令來取得網路介面和[get-azpublicipaddress](/powershell/module/az.network/get-azpublicipaddress)命令，以取得現有的公用 IP 位址。 然後使用[AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig)命令，將公用 IP 位址與 IP 設定建立關聯，並使用[new-aznetworkinterface](/powershell/module/Az.Network/Set-AzNetworkInterface)命令將新的 ip 設定寫入網路介面。

   下列範例會將名為*myVMPublicIP*的現有公用 IP 位址，與名稱為*myVMVNet*之虛擬網路中名為*myVMSubnet*的子網中名為*myVMVMNic*的現有網路介面（名為*ipconfigmyVM* ）的 IP 設定產生關聯。 所有資源都位於名為 *myResourceGroup* 的資源群組中。
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - 如果您沒有現有的公用 IP 位址，請使用[get-azpublicipaddress](/powershell/module/Az.Network/New-AzPublicIpAddress)命令來建立一個。 例如，下列命令會在*eastus*區域中名為*myResourceGroup*的資源群組中建立名為*myVMPublicIP*的*動態*公用 IP 位址。
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > 先前的命令會針對您可能想要自訂的數個設定，建立具有預設值的公用 IP 位址。 若要深入瞭解所有公用 IP 位址設定，請參閱[建立公用 ip 位址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 此位址是從每個 Azure 區域所使用的公用 IP 位址集區所指派。 若要查看每個區域中使用的位址集區清單，請參閱[Microsoft Azure DATACENTER IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

   - 如果您不知道連接至 VM 的網路介面名稱，請使用 [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) 命令加以查看。 例如，下列命令會列出連接至 *myResourceGroup* 資源群組中 *myVM* VM 的網路介面名稱：

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     此輸出包含一或多行，與下列範例類似。 在範例輸出中，*myVMVMNic* 是網路介面的名稱。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 如果您不知道網路介面所在的虛擬網路或子網的名稱，請使用 `Get-AzNetworkInterface` 命令來查看資訊。 例如，下列命令會在名為*myResourceGroup*的資源群組中，取得名為*myVMVMNic*之網路介面的虛擬網路和子網資訊：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     此輸出包含一或多行，與下列範例類似。 在範例輸出中， *myVMVNET*是虛擬網路的名稱，而*myVMSubnet*是子網的名稱。
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - 如果您不知道網路介面的 IP 設定名稱，請使用 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 命令來取得名稱。 例如，下列命令會列出 *myResourceGroup* 資源群組中 *myVMVMNic* 網路介面的 IP 設定名稱：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     此輸出包含一或多行，與下列範例類似。 在範例輸出中，*ipconfigmyVM* 是 IP 設定的名稱。
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. 使用[get-azpublicipaddress](/powershell/module/az.network/get-azpublicipaddress)命令，即可查看指派給 IP 設定的公用 IP 位址。 下列範例顯示在名為*myResourceGroup*的資源群組中，指派給名為*MYVMPUBLICIP*之公用 IP 位址的位址。

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   如果您不知道指派給 IP 設定的公用 IP 位址名稱，請執行下列命令來取得它：

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   此輸出包含一或多行，與下列範例類似。 在範例輸出中， *myVMPublicIP*是指派給 IP 設定的公用 IP 位址的名稱。

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > 此位址是從每個 Azure 區域中使用的位址集區所指派。 若要查看每個區域中使用的位址集區清單，請參閱[Microsoft Azure DATACENTER IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 指派的位址可以是用於區域之集區中的任何位址。 如果您需要從區域中的特定集區指派位址，請使用[公用 IP 位址首碼](public-ip-address-prefix.md)。

4. 允許在網路安全性群組中使用安全性規則[對 VM 進行網路流量](#allow-network-traffic-to-the-vm)。

## <a name="allow-network-traffic-to-the-vm"></a>允許對 VM 的網路流量

在可以從網際網路連線到公用 IP 位址之前，請確定您已在可能已與網路介面、網路介面所在的子網路或兩者相關聯的任何網路安全性群組中，開啟所需的連接埠。 雖然安全性群組會篩選網路介面的私人 IP 位址流量，一旦輸入網際網路流量抵達公用 IP 位址，Azure 就會將公用位址轉譯為私人 IP 位址，因此，如果網路安全性群組防止流量流量，則與公用 IP 位址的通訊會失敗。 您可以使用[入口網站](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal)、[CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli) 或 [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)，來檢視網路介面及其子網路的有效安全性規則。

## <a name="next-steps"></a>後續步驟

允許使用網路安全性群組的輸入網際網路流量進入您的 VM。 若要瞭解如何建立網路安全性群組，請參閱使用[網路安全](manage-network-security-group.md#work-with-network-security-groups)組。 若要深入瞭解網路安全性群組，請參閱[安全性群組](security-overview.md)。
