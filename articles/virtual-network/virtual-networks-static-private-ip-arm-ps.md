---
title: 使用靜態私人 IP 位址創建 VM - Azure PowerShell
description: 瞭解如何使用 PowerShell 創建具有私人 IP 位址的虛擬機器。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1745ca176fac18b4903686cb556670531ee40a1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244754"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>使用 PowerShell 創建具有靜態私人 IP 位址的虛擬機器

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以使用靜態私人 IP 位址創建虛擬機器 （VM）。 如果要從子網中分配哪個位址分配給 VM，請分配靜態私人 IP 位址，而不是動態位址。 瞭解有關[靜態私人 IP 位址的更多。](virtual-network-ip-addresses-overview-arm.md#allocation-method) 要將分配給現有 VM 的私人 IP 位址從動態更改為靜態，或使用公共 IP 位址，請參閱[添加、更改或刪除 IP 位址](virtual-network-network-interface-addresses.md)。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

您可以從本機電腦或使用 Azure Cloud Shell 來完成下列步驟。 若要使用您的本機電腦，請確定已[安裝 Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要使用 Azure Cloud Shell，請選取後續任何命令方塊右上角的 [試試看]****。 Cloud Shell 可讓您登入 Azure。

1. 如果使用 Cloud Shell，請跳至步驟 2。 開啟命令工作階段，然後使用 `Connect-AzAccount` 登入 Azure。
2. 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立資源群組。 下列範例會在美國東部 Azure 區域中建立一個資源群組：

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. 使用[新阿茲虛擬網路子網配置](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)和新[-Az虛擬網路](/powershell/module/az.network/new-azvirtualnetwork)命令創建子網配置和虛擬網路：

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. 在虛擬網路中創建網路介面，使用[New-AzNetwork 介面 IpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig)和新[-AzNetwork 介面](/powershell/module/az.network/new-aznetworkinterface)命令將私人 IP 位址從子網分配給網路介面：

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. 使用[New-AzVMConfig 創建](/powershell/module/Az.Compute/New-AzVMConfig)VM 配置，然後使用[New-AzVM](/powershell/module/az.Compute/New-azVM)創建 VM。 出現提示時，提供使用者名和密碼以用作 VM 的登錄憑據：

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> 雖然您可以將私人 IP 位址設置添加到作業系統，但我們建議在閱讀將[私人 IP 位址添加到作業系統](virtual-network-network-interface-addresses.md#private)之前不要這樣做。
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> 要從 Internet 訪問 VM，您必須為 VM 分配公共 IP 位址。 您還可以將動態私人 IP 位址分配更改為靜態配置。 有關詳細資訊，請參閱[添加或更改 IP 位址](virtual-network-network-interface-addresses.md)。 此外，建議通過將網路安全性群組與網路介面、在 中創建網路介面的子網或兩者相關聯來限制網路流量到 VM。 有關詳細資訊，請參閱[管理網路安全性群組](manage-network-security-group.md)。

## <a name="clean-up-resources"></a>清除資源

您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 來移除不再需要的資源群組，以及其所包含的所有資源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

- 詳細瞭解專用[IP 位址](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)並將[靜態私人 IP 位址](virtual-network-network-interface-addresses.md#add-ip-addresses)分配給 Azure 虛擬機器。
- 瞭解有關創建[Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和[Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)虛擬機器的更多資訊。
