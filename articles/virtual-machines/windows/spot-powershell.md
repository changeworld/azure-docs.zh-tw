---
title: 使用 PowerShell 部署 Azure 點 Vm
description: 瞭解如何使用 Azure PowerShell 部署點 Vm 以節省成本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: f615ed5183142ca7684c7e705fa6a42bd3124d19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85514822"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>使用 Azure PowerShell 部署點 Vm


使用「[點 vm](spot-vms.md) 」可讓您以可觀的成本節約，利用我們未使用的容量。 Azure 基礎結構會在任何時間點回復，以找出虛擬機器的功能。 因此，針對可處理中斷的工作負載（例如批次處理作業、開發/測試環境、大型計算工作負載等），找出 Vm 很棒。

點 Vm 的定價是以區域和 SKU 為依據的變數。 如需詳細資訊，請參閱[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定價。 如需設定最大價格的詳細資訊，請參閱[找出 vm-定價](spot-vms.md#pricing)。

您可以選擇為 VM 設定您願意支付的最大價格（每小時）。 您可以使用最多5個小數位數，以美元（USD）來設定點 VM 的最大價格。 例如，此值 `0.98765` 是每小時 $0.98765 美元的最大價格。 如果您將最大價格設為 `-1` ，則不會根據價格來收回 VM。 VM 的價格將會是標準 VM 的目前價格或價格（這是較少的），只要有可用的容量和配額。


## <a name="create-the-vm"></a>建立 VM

使用[new-azvmconfig](/powershell/module/az.compute/new-azvmconfig)建立 spotVM 來建立設定。 包含 `-Priority Spot` ，並將設 `-MaxPrice` 為下列其中一項：
- `-1`因此不會根據價格來收回 VM。
- 美元金額，最多5位數。 例如， `-MaxPrice .98765` 表示一旦 spotVM 的價格每小時約 $. 98765，就會將 VM 解除配置。


此範例會建立不會根據定價解除配置的 spotVM （只有在 Azure 需要此容量時才會這麼做）。 收回原則會設定為解除配置 VM，使其可在稍後重新開機。 如果您想要在虛擬機器收回時刪除 VM 和基礎磁片，請 `-EvictionPolicy` 在中將設定為 `Delete` `New-AzVMConfig` 。


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

您也可以使用[Azure CLI](../linux/spot-cli.md)、[入口網站](spot-portal.md)或[範本](../linux/spot-template.md)來建立點 VM。

如果您遇到錯誤，請參閱[錯誤碼](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。