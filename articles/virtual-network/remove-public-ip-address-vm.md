---
title: 將公共 IP 位址與 Azure VM 分離
titlesuffix: Azure Virtual Network
description: 瞭解如何將公共 IP 位址與 VM 分離
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: kumud
ms.openlocfilehash: 1c27af30f97ea967d170b2cccaefb2e95f8fedaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900749"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>將公共 IP 位址與 Azure VM 分離 

在本文中，您將瞭解如何將公共 IP 位址與 Azure 虛擬機器 （VM） 分離。

可以使用 Azure[門戶](#azure-portal)、Azure[命令列介面](#azure-cli)（CLI） 或[PowerShell](#powershell)將公共 IP 位址與 VM 分離。

## <a name="azure-portal"></a>Azure 入口網站

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 流覽到要取消公共 IP 位址關聯的虛擬機器，然後選擇它。
3. 在 VM 頁中，選擇 **"概述**"，選擇公共 IP 位址，如下圖所示：

   ![選擇公共 IP](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. 在公共 IP 位址頁中，選擇 **"概述**"，然後選擇 **"分離**"，如下圖所示：

    ![分離公共 IP](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. 在**分離公共 IP 位址**中，選擇 **"是**"。

## <a name="azure-cli"></a>Azure CLI

安裝[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)，或使用 Azure 雲外殼。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 在隨後的 CLI 命令中選擇"**試用"** 按鈕。 選擇 **"試用"** 可調用雲外殼，您可以使用該雲外殼登錄到 Azure 帳戶。

1. 如果在 Bash 中本地使用 CLI，請使用 登錄到`az login`Azure。
2. 公共 IP 位址與連接到 VM 的網路介面的 IP 配置相關聯。 使用[az 網路 nic-ip 配置更新](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update)命令將公共 IP 位址與 IP 配置分離。 下面的示例將名為*myVMPublicIP*的公共 IP 位址與名為*myVMVMNic*的現有網路介面的*IP*配置分離，該配置連接到名為*myVM*的名為 myVM 的 VM 的資源組中名為*myVM。*
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   如果您不知道連接到 VM 的網路介面的名稱，請使用[az vm nic 清單](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list)命令查看它們。 例如，以下命令列出了連接到名為 myVM 的 VM 中的名為*myVM*的網路介面的名稱，這些資源組中名為*myResourceGroup*：

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     輸出包括一條或多條類似于以下示例的行：
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     在前面的示例中 *，myVMVMNic*是網路介面的名稱。

   - 如果您不知道網路介面的 IP 配置的名稱，請使用 az 網路 nic [ip 配置清單](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list)命令來檢索它們。 例如，以下命令列出了名為*myVMVMNic*的網路介面的公共 IP 配置的名稱，該資源組中名為*myVMVMNic*：

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - 如果您不知道網路介面的公共 IP 配置的名稱，請使用[az 網路 nic ip 配置顯示顯示](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show)命令來檢索它們。 例如，以下命令列出了名為*myVMVMNic*的網路介面的公共 IP 配置的名稱，該資源組中名為*myVMVMNic*：

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

安裝[PowerShell](/powershell/azure/install-az-ps)或使用 Azure 雲外殼。 Azure Cloud Shell 是免費的殼層，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 PowerShell，可與您的帳戶搭配使用。 在隨後的 PowerShell 命令中選擇"**試用"** 按鈕。 選擇 **"試用"** 可調用雲外殼，您可以使用該雲外殼登錄到 Azure 帳戶。

1. 如果在本地使用 PowerShell，請使用 登錄到 Azure。 `Connect-AzAccount`
2. 公共 IP 位址與連接到 VM 的網路介面的 IP 配置相關聯。 使用[獲取-AzNetwork 介面](/powershell/module/Az.Network/Get-AzNetworkInterface)命令獲取網路介面。 將公共 IP 位址值設置為 null，然後使用[Set-AzNetwork 介面](/powershell/module/Az.Network/Set-AzNetworkInterface)命令將新的 IP 配置寫入網路介面。

   下面的示例將名為*myVMPublicIP*的公共 IP 位址與連接到名為*myVM* *的*VM 的網路介面分離。 所有資源都位於名為*myResourceGroup 的資源組中*。
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - 如果您不知道連接到 VM 的網路介面的名稱，請使用[Get-AzVM](/powershell/module/Az.Compute/Get-AzVM)命令查看它們。 例如，以下命令列出了連接到名為 myVM 的 VM 中的名為*myVM*的網路介面的名稱，這些資源組中名為*myResourceGroup*：

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     輸出包括一條或多條與以下示例類似的行。 在示例輸出中 *，myVMVMNic*是網路介面的名稱。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 如果您不知道網路介面的 IP 配置的名稱，請使用[Get-AzNetwork 介面](/powershell/module/Az.Network/Get-AzNetworkInterface)命令來檢索它們。 例如，以下命令列出了名為*myVMNic*的網路介面的 IP 配置的名稱，該資源組中名為*myVMNic*：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     輸出包括一條或多條與以下示例類似的行。 在示例輸出中 *，ipconfigmyVM*是 IP 配置的名稱。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>後續步驟

- 瞭解如何[將公共 IP 位址與 VM 相關聯](associate-public-ip-address-vm.md)。
