---
title: 使用 PowerShell 部署 Azure 位置 Vm
description: 瞭解如何使用 Azure PowerShell 部署現成的 Vm，以節省成本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 8bcf90368e8d43dce2d10fa3744024bcbc7e4b52
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816536"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>使用 Azure PowerShell 部署現成的 Vm


使用現成的 [vm](../spot-vms.md) 可讓您以大幅節省的成本來利用未使用的容量。 Azure 基礎結構會在任何時間點恢復容量，以找出 Vm。 因此，找出 Vm 很適合用來處理中斷的工作負載，例如批次處理作業、開發/測試環境、大型計算工作負載等。

現成 Vm 的定價是根據區域和 SKU 的變數。 如需詳細資訊，請參閱 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定價。 如需設定最大價格的詳細資訊，請參閱 [找出 vm-定價](../spot-vms.md#pricing)。

您可以選擇針對 VM 設定您願意支付的最高價格（每小時）。 您可以使用最多5個小數位數，將位置 VM 的最大價格設定為美元 (USD) 。 例如，值的 `0.98765` 最大價格為每小時 $0.98765 美元。 如果您將最大價格設定為 `-1` ，將不會根據價格來收回 VM。 如果有可用的容量和配額，則 VM 的價格將是標準 VM 的目前價格或價格的價格。


## <a name="create-the-vm"></a>建立 VM

使用 [>new-azvmconfig](/powershell/module/az.compute/new-azvmconfig) 建立 spotVM 來建立設定。 包含 `-Priority Spot` 並設定 `-MaxPrice` 為下列任一項：
- `-1` 因此 VM 不會根據價格收回。
- 貨幣金額，最多5位數。 例如， `-MaxPrice .98765` 表示一旦 spotVM 的價格大約每小時98765，就會將 VM 解除配置。


這個範例會建立一個 spotVM，它只會在 Azure 需要容量回) 時，根據定價 (來解除配置。 收回原則設定為解除配置 VM，以便稍後可以重新開機。 如果您想要在 VM 收回時刪除 VM 和基礎磁片，請將設定 `-EvictionPolicy` 為 `Delete` 中的 `New-AzVMConfig` 。


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

建立 VM 之後，您可以查詢以查看資源群組中所有 Vm 的最大價格。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>後續步驟

您也可以使用 [Azure CLI](../linux/spot-cli.md)、 [入口網站](spot-portal.md) 或 [範本](../linux/spot-template.md)來建立點 VM。

如果您遇到錯誤，請參閱 [錯誤碼](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。