---
title: 中斷公用 IP 位址與 Azure VM 的關聯
titlesuffix: Azure Virtual Network
description: 了解如何中斷公用 IP 位址與 VM 的關聯
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: 0665cbd7aa21575337999fb5c59478955c764048
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934189"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>中斷公用 IP 位址與 Azure VM 的關聯 

在本文中，您將了解如何中斷公用 IP 位址與 Azure 虛擬機器 (VM) 的關聯。

您可使用 [Azure 入口網站](#azure-portal)、Azure [命令列介面](#azure-cli) (CLI) 或 [PowerShell](#powershell) 來中斷公用 IP 位址與 VM 的關聯。

## <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽或搜尋想要中斷與公用 IP 位址關聯的虛擬機器，然後加以選取。
3. 在 [VM] 頁面中，選取 [概觀]，然後選取 [公用 IP 位址]，如下圖所示：

   ![選取公用 IP](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. 在 [公用 IP 位址] 頁面中，選取 [概觀]，然後選取 [中斷關聯]，如下圖所示：

    ![中斷公用 IP 的關聯](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. 在 [中斷公用 IP 位址關聯] 中，選取 [是]。

## <a name="azure-cli"></a>Azure CLI

安裝 [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 或使用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 在接下來的 CLI 命令中，選取 [試用] 按鈕。 選取 [試用]，會叫用可登入 Azure 帳戶的 Cloud Shell。

1. 如果在 Bash 中使用本機 CLI，請使用 `az login` 登入 Azure。
2. 公用 IP 位址會與連接至 VM 的網路介面 IP 設定建立關聯。 使用 [az network nic-ip-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update) 命令來中斷公用 IP 位址與 IP 設定的關聯。 下列範例會將公用 IP 位址 *myVMPublicIP* 與現有 *myVMVMNic* 網路介面的 *ipconfigmyVM* IP 設定中斷關聯，該網路介面連接至 *myResourceGroup* 資源群組中的 *myVM* VM。
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   如果您不知道連接至 VM 的網路介面名稱，請使用 [az vm nic list](/cli/azure/vm/nic#az-vm-nic-list) 命令來加以查看。 例如，下列命令會列出連接至 *myResourceGroup* 資源群組中 *myVM* VM 的網路介面名稱：

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     此輸出包含一或多行，與下列範例類似：
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     在上述範例中，*myVMVMNic* 是網路介面的名稱。

   - 如果您不知道網路介面的 IP 設定名稱，請使用 [az network nic ip-config list](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-list) 命令來取得名稱。 例如，下列命令會列出 *myResourceGroup* 資源群組中 *myVMVMNic* 網路介面的公用 IP 設定名稱：

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - 如果您不知道網路介面的公用 IP 設定名稱，請使用 [az network nic ip-config show](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-show) 命令來取得名稱。 例如，下列命令會列出 *myResourceGroup* 資源群組中 *myVMVMNic* 網路介面的公用 IP 設定名稱：

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

安裝 [PowerShell](/powershell/azure/install-az-ps) 或使用 Azure Cloud Shell。 Azure Cloud Shell 是免費的殼層，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 PowerShell，可與您的帳戶搭配使用。 在接下來的 PowerShell 命令中，選取 [試用] 按鈕。 選取 [試用]，會叫用可登入 Azure 帳戶的 Cloud Shell。

1. 如果在本機使用 PowerShell，請使用 `Connect-AzAccount` 登入 Azure。
2. 公用 IP 位址會與連接至 VM 的網路介面 IP 設定建立關聯。 使用 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 命令來取得網路介面。 將 [公用 IP 位址] 值設定為 Null，然後使用 [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) 命令，將新的 IP 設定寫入至網路介面。

   下列範例會將公用 IP 位址 *myVMPublicIP* 與現有 *myVMVMNic* 網路介面中斷關聯，該網路介面連接至名為 *myVM* 的 VM。 所有資源都位於名為 *myResourceGroup* 的資源群組中。
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - 如果您不知道連接至 VM 的網路介面名稱，請使用 [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) 命令加以查看。 例如，下列命令會列出連接至 *myResourceGroup* 資源群組中 *myVM* VM 的網路介面名稱：

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     此輸出包含一或多行，與下列範例類似。 在範例輸出中，*myVMVMNic* 是網路介面的名稱。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 如果您不知道網路介面的 IP 設定名稱，請使用 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 命令來取得名稱。 例如，下列命令會列出 *myResourceGroup* 資源群組中 *myVMVMNic* 網路介面的 IP 設定名稱：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     此輸出包含一或多行，與下列範例類似。 在範例輸出中，*ipconfigmyVM* 是 IP 設定的名稱。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>後續步驟

- 了解如何[將公用 IP 位址與 VM 建立關聯](associate-public-ip-address-vm.md)。
