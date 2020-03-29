---
title: 將公共 IP 位址關聯到虛擬機器
titlesuffix: Azure Virtual Network
description: 瞭解如何將公共 IP 位址與虛擬機器關聯。
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 5acda69ce08bc493d5349b084d1cfafc8432145b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647453"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>將公共 IP 位址關聯到虛擬機器

在本文中，您將瞭解如何將公共 IP 位址與現有虛擬機器 （VM） 相關聯。 如果要從 Internet 連接到 VM，VM 必須具有與其關聯的公共 IP 位址。 如果要使用公共 IP 位址創建新 VM，可以使用[Azure 門戶](virtual-network-deploy-static-pip-arm-portal.md)[、Azure 命令列介面 （CLI）](virtual-network-deploy-static-pip-arm-cli.md)或[PowerShell](virtual-network-deploy-static-pip-arm-ps.md)執行此操作。 公用 IP 位址需要少許費用。 如需詳細資料，請參閱[定價](https://azure.microsoft.com/pricing/details/ip-addresses/)。 每個訂閱可以使用的公共 IP 位址數有限制。 有關詳細資訊，請參閱[限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)。

可以使用 Azure[門戶](#azure-portal)、Azure[命令列介面](#azure-cli)（CLI） 或[PowerShell](#powershell)將公共 IP 位址與 VM 相關聯。

## <a name="azure-portal"></a>Azure 入口網站

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 流覽或搜索要向其添加公共 IP 位址的虛擬機器，然後選擇它。
3. 在 **"設置"** 下，選擇 **"網路**"，然後選擇要向其添加公共 IP 位址的網路介面，如下圖所示：

   ![選擇網路介面](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > 公共 IP 位址與連接到 VM 的網路介面相關聯。 在上圖中，VM 只有一個網路介面。 如果 VM 有多個網路介面，它們都會顯示，並且您可以選擇要將公共 IP 位址關聯的網路介面。

4. 選擇**IP 配置**，然後選擇 IP 配置，如下圖所示：

   ![選擇 IP 配置](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > 公共 IP 位址與網路介面的 IP 配置相關聯。 在上圖中，網路介面具有一個 IP 配置。 如果網路介面有多個 IP 配置，它們都會顯示在清單中，並且您可以選擇要將公共 IP 位址關聯的 IP 配置。

5. 選擇 **"已啟用"，** 然後選擇**IP 位址（*配置所需的設置*）。** 選擇現有的公共 IP 位址，該位址會自動關閉 **"選擇公共 IP 位址**"框。 如果您沒有列出任何可用的公共 IP 位址，則需要創建一個公共 IP 位址。 要瞭解如何，請參閱[創建公共 IP 位址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 選擇 **"保存**"，如下圖所示，然後關閉 IP 配置的框。

   ![啟用公共 IP 位址](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > 顯示的公共 IP 位址與 VM 存在於同一區域中的 IP 位址。 如果該區域中創建了多個公共 IP 位址，則所有位址都將顯示在此處。 如果其中任何位址已灰顯，則這是因為該位址已關聯到其他資源。

6. 查看分配給 IP 配置的公共 IP 位址，如下圖所示。 顯示 IP 位址可能需要幾秒鐘的時間。

   ![查看分配的公共 IP 位址](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > 位址是從每個 Azure 區域中使用的位址集區中分配的。 要查看每個區域中使用的位址集區的清單，請參閱[Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 分配的位址可以是用於該區域的池中的任何位址。 如果需要從區域中的特定池分配位址，請使用[公共 IP 位址首碼](public-ip-address-prefix.md)。

7. [允許網路流量到 VM](#allow-network-traffic-to-the-vm)與網路安全性群組中的安全規則。

## <a name="azure-cli"></a>Azure CLI

安裝[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)，或使用 Azure 雲外殼。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 在隨後的 CLI 命令中選擇"**試用"** 按鈕。 選擇 **"試用"** 可調用雲外殼，您可以使用該雲外殼登錄到 Azure 帳戶。

1. 如果在 Bash 中本地使用 CLI，請使用 登錄到`az login`Azure。
2. 公共 IP 位址與連接到 VM 的網路介面的 IP 配置相關聯。 使用[az 網路 nic-ip 配置更新](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update)命令將公共 IP 位址與 IP 配置相關聯。 下面的示例將名為*myVMPublicIP*的現有公共 IP 位址與現有網路介面名為*ipconfigmyVM*的 IP 配置相關聯，該配置名為*myVMVMNic，* 該配置存在於名為*myResourceGroup*的資源組中。
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - 如果沒有現有的公共 IP 位址，請使用[az 網路公共 ip 創建](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)命令創建一個。 例如，以下命令在名為*myResourceGroup*的資源組中創建名為*myVMPublicIP*的公共 IP 位址。
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > 前面的命令為可能需要自訂的多個設置創建具有預設值的公共 IP 位址。 要瞭解有關所有公共 IP 位址設置的更多內容，請參閱[創建公共 IP 位址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 該位址是從每個 Azure 區域使用的公共 IP 位址池中分配的。 要查看每個區域中使用的位址集區的清單，請參閱[Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

   - 如果您不知道連接到 VM 的網路介面的名稱，請使用[az vm nic 清單](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list)命令查看它們。 例如，以下命令列出了連接到名為 myVM 的 VM 中的名為*myVM*的網路介面的名稱，這些資源組中名為*myResourceGroup*：

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     輸出包括一條或多條類似于以下示例的行：
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     在前面的示例中 *，myVMVMNic*是網路介面的名稱。

   - 如果您不知道網路介面的 IP 配置的名稱，請使用 az 網路 nic [ip 配置清單](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list)命令來檢索它們。 例如，以下命令列出了名為*myVMNic*的網路介面的 IP 配置的名稱，該資源組中名為*myVMNic*：

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. 使用[az vm 清單 ip 位址](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses)命令查看分配給 IP 配置的公共 IP 位址。 下面的示例顯示分配給名為 myVM 的現有 VM 的資源*組中名為**myVM*的 IP 位址。

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > 位址是從每個 Azure 區域中使用的位址集區中分配的。 要查看每個區域中使用的位址集區的清單，請參閱[Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 分配的位址可以是用於該區域的池中的任何位址。 如果需要從區域中的特定池分配位址，請使用[公共 IP 位址首碼](public-ip-address-prefix.md)。

4. [允許網路流量到 VM](#allow-network-traffic-to-the-vm)與網路安全性群組中的安全規則。

## <a name="powershell"></a>PowerShell

安裝[PowerShell](/powershell/azure/install-az-ps)或使用 Azure 雲外殼。 Azure Cloud Shell 是免費的殼層，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 PowerShell，可與您的帳戶搭配使用。 在隨後的 PowerShell 命令中選擇"**試用"** 按鈕。 選擇 **"試用"** 可調用雲外殼，您可以使用該雲外殼登錄到 Azure 帳戶。

1. 如果在本地使用 PowerShell，請使用 登錄到 Azure。 `Connect-AzAccount`
2. 公共 IP 位址與連接到 VM 的網路介面的 IP 配置相關聯。 使用[Get-Az 虛擬網路和](/powershell/module/Az.Network/Get-AzVirtualNetwork)[獲取-Az 虛擬網路子網配置](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig)命令獲取網路介面中的虛擬網路和子網。 接下來，使用[Get-AzNetwork 介面](/powershell/module/Az.Network/Get-AzNetworkInterface)命令獲取網路介面和[Get-AzPublicIp位址](/powershell/module/az.network/get-azpublicipaddress)命令來獲取現有的公共 IP 位址。 然後使用[Set-AzNetwork 介面 IpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig)命令將公共 IP 位址與 IP 配置和[Set-AzNetwork 介面](/powershell/module/Az.Network/Set-AzNetworkInterface)命令相關聯，將新的 IP 配置寫入網路介面。

   下面的示例將名為*myVMPublicIP*的現有公共 IP 位址與名為*myVMVMNic*的現有網路介面的*IP*配置相關聯，該介面位於名為*myVMVNet*的虛擬網路中名為 myVMSubnet 的子網中。 *myVMVNet* 所有資源都位於名為*myResourceGroup 的資源組中*。
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - 如果沒有現有的公共 IP 位址，請使用[New-AzPublicIp位址](/powershell/module/Az.Network/New-AzPublicIpAddress)命令創建一個公共 IP 位址。 例如，以下命令在*東部*區功能變數名稱為*myResourceGroup*的資源組中創建一個名為*myVMPublicIP* *的動態*公共 IP 位址。
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > 前面的命令為可能需要自訂的多個設置創建具有預設值的公共 IP 位址。 要瞭解有關所有公共 IP 位址設置的更多內容，請參閱[創建公共 IP 位址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 該位址是從每個 Azure 區域使用的公共 IP 位址池中分配的。 要查看每個區域中使用的位址集區的清單，請參閱[Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

   - 如果您不知道連接到 VM 的網路介面的名稱，請使用[Get-AzVM](/powershell/module/Az.Compute/Get-AzVM)命令查看它們。 例如，以下命令列出了連接到名為 myVM 的 VM 中的名為*myVM*的網路介面的名稱，這些資源組中名為*myResourceGroup*：

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     輸出包括一條或多條與以下示例類似的行。 在示例輸出中 *，myVMVMNic*是網路介面的名稱。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 如果您不知道網路介面中的虛擬網路或子網的名稱，請使用 命令`Get-AzNetworkInterface`查看資訊。 例如，以下命令獲取名為*myVMNic*的網路介面的虛擬網路和子網資訊，這些資訊位於名為*myResourceGroup 的資源*組中：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     輸出包括一條或多條與以下示例類似的行。 在示例輸出中 *，myVMVNET*是虛擬網路的名稱 *，myVM Subnet*是子網的名稱。
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - 如果您不知道網路介面的 IP 配置的名稱，請使用[Get-AzNetwork 介面](/powershell/module/Az.Network/Get-AzNetworkInterface)命令來檢索它們。 例如，以下命令列出了名為*myVMNic*的網路介面的 IP 配置的名稱，該資源組中名為*myVMNic*：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     輸出包括一條或多條與以下示例類似的行。 在示例輸出中 *，ipconfigmyVM*是 IP 配置的名稱。
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. 使用[獲取-AzPublicIp位址](/powershell/module/az.network/get-azpublicipaddress)命令查看分配給 IP 配置的公共 IP 位址。 下面的示例顯示分配給名為*myVMPublicIP*的公共 IP 位址的位址，該位址位於名為*myResourceGroup*的資源組中。

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   如果您不知道分配給 IP 配置的公共 IP 位址的名稱，則運行以下命令以獲取它：

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   輸出包括一條或多條與以下示例類似的行。 在示例輸出中 *，myVMPublicIP*是分配給 IP 配置的公共 IP 位址的名稱。

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > 位址是從每個 Azure 區域中使用的位址集區中分配的。 要查看每個區域中使用的位址集區的清單，請參閱[Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 分配的位址可以是用於該區域的池中的任何位址。 如果需要從區域中的特定池分配位址，請使用[公共 IP 位址首碼](public-ip-address-prefix.md)。

4. [允許網路流量到 VM](#allow-network-traffic-to-the-vm)與網路安全性群組中的安全規則。

## <a name="allow-network-traffic-to-the-vm"></a>允許網路流量到 VM

在可以從 Internet 連接到公共 IP 位址之前，請確保在可能與網路介面關聯的任何網路安全性群組中打開必要的埠，網路介面位於該子網中，或兩者兼而有之。 儘管安全性群組將流量篩選到網路介面的私人 IP 位址，但一旦入站 Internet 流量到達公共 IP 位址，Azure 就會將公共位址轉換為私人 IP 位址，因此，如果網路安全性群組阻止流量，與公共 IP 位址的通信失敗。 您可以使用[門戶](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal)[、CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)或[PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)查看網路介面及其子網的有效安全規則。

## <a name="next-steps"></a>後續步驟

使用網路安全性群組允許向 VM 的入站互聯網流量。 要瞭解如何創建網路安全性群組，請參閱[使用網路安全性群組](manage-network-security-group.md#work-with-network-security-groups)。 要瞭解有關網路安全性群組的更多資訊，請參閱[安全性群組](security-overview.md)。
