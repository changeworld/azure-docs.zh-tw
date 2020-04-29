---
title: 將公用 IP 位址與 Azure VM 中斷關聯
titlesuffix: Azure Virtual Network
description: 瞭解如何從 VM 中斷公用 IP 位址的關聯
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: f29e29f809faeeb486e5b6b9bacc84a61380a012
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144464"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>將公用 IP 位址與 Azure VM 中斷關聯 

在本文中，您將瞭解如何從 Azure 虛擬機器（VM）中斷公用 IP 位址的關聯。

您可以使用 [ [Azure 入口網站](#azure-portal)]、[Azure[命令列介面](#azure-cli)（CLI）] 或 [ [POWERSHELL](#powershell) ]，將公用 IP 位址與 VM 中斷關聯。

## <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 流覽或搜尋您要與公用 IP 位址解除關聯的虛擬機器，然後選取它。
3. 在 [VM] 頁面中，選取 [**總覽**]，選取公用 IP 位址，如下圖所示：

   ![選取公用 IP](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. 在 [公用 IP 位址] 頁面中，選取 [**總覽**] **，然後**選取 [中斷關聯]，如下圖所示：

    ![中斷關聯公用 IP](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. 在 [中斷關聯**公用 IP 位址**] 中，選取 **[是]**。

## <a name="azure-cli"></a>Azure CLI

安裝[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 在接下來的 CLI 命令中，選取 [**試試看**] 按鈕。 選取 [**試試看] 會**叫用可讓您使用登入 Azure 帳戶的 Cloud Shell。

1. 如果在 Bash 中于本機使用 CLI，請使用`az login`登入 Azure。
2. 公用 IP 位址會與連接至 VM 的網路介面的 IP 設定相關聯。 使用[az network nic-ip-HTTPs update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update)命令，將公用 ip 位址與 ip 設定中斷關聯。 下列範例會從名為*myVMVMNic*的現有網路*介面的 IP*設定中，分離名為*myVMPublicIP*的公用 ip 位址，該名稱會附加至名為*MyVM*的資源群組中名為*myResourceGroup*的 VM。
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   如果您不知道連接至 VM 的網路介面名稱，請使用[az VM nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list)命令來加以查看。 例如，下列命令會在名為*myResourceGroup*的資源群組中，列出附加至名為*myVM*之 VM 的網路介面名稱：

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     輸出包含一或多行，與下列範例類似：
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     在上述範例中， *myVMVMNic*是網路介面的名稱。

   - 如果您不知道網路介面的 IP 設定名稱，請使用[az network nic IP config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list)命令來取得它們。 例如，下列命令會針對名為*myResourceGroup*的資源群組中名為*myVMVMNic*的網路介面，列出其公用 IP 設定的名稱：

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - 如果您不知道網路介面的公用 IP 設定名稱，請使用[az network nic IP-config show](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show)命令來取得它們。 例如，下列命令會針對名為*myResourceGroup*的資源群組中名為*myVMVMNic*的網路介面，列出其公用 IP 設定的名稱：

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

安裝[PowerShell](/powershell/azure/install-az-ps)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是免費的殼層，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 PowerShell，可與您的帳戶搭配使用。 在接下來的 PowerShell 命令中選取 [**試試看**] 按鈕。 選取 [**試試看] 會**叫用可讓您使用登入 Azure 帳戶的 Cloud Shell。

1. 如果在本機使用 PowerShell，請使用`Connect-AzAccount`登入 Azure。
2. 公用 IP 位址會與連接至 VM 的網路介面的 IP 設定相關聯。 使用[new-aznetworkinterface](/powershell/module/Az.Network/Get-AzNetworkInterface)命令來取得網路介面。 將 [公用 IP 位址] 值設定為 null，然後使用[new-aznetworkinterface](/powershell/module/Az.Network/Set-AzNetworkInterface)命令將新的 ip 設定寫入網路介面。

   下列範例會從連接到名為*myVM*的*VM 的網路介面，分離*名為*myVMPublicIP*的公用 IP 位址。 所有資源都位於名為*myResourceGroup*的資源群組中。
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - 如果您不知道連接至 VM 的網路介面名稱，請使用[update-azvm](/powershell/module/Az.Compute/Get-AzVM)命令來加以查看。 例如，下列命令會在名為*myResourceGroup*的資源群組中，列出附加至名為*myVM*之 VM 的網路介面名稱：

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     輸出包含一或多行，與下面的範例類似。 在範例輸出中， *myVMVMNic*是網路介面的名稱。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 如果您不知道網路介面的 IP 設定名稱，請使用[new-aznetworkinterface](/powershell/module/Az.Network/Get-AzNetworkInterface)命令來取得它們。 例如，下列命令會針對名為*myResourceGroup*的資源群組中名為*myVMVMNic*的網路介面，列出 IP 設定的名稱：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     輸出包含一或多行，與下面的範例類似。 在範例輸出中， *ipconfigmyVM*是 IP 設定的名稱。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>後續步驟

- 瞭解如何[將公用 IP 位址與 VM 產生關聯](associate-public-ip-address-vm.md)。
