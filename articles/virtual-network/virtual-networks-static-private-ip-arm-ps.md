---
title: 建立具有靜態私人 IP 位址的 VM-Azure PowerShell
description: 瞭解如何使用 PowerShell 建立具有私人 IP 位址的虛擬機器。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: ced76b73a8a08e6886cf0cef04c74a82d05c75dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84708122"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>使用 PowerShell 建立具有靜態私人 IP 位址的虛擬機器

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以使用靜態私人 IP 位址建立虛擬機器 (VM) 。 如果您想要選取子網中指派給 VM 的位址，請指派靜態私人 IP 位址，而不是動態位址。 深入瞭解 [靜態私人 IP 位址](virtual-network-ip-addresses-overview-arm.md#allocation-method)。 若要將指派給現有 VM 的私人 IP 位址從動態變更為靜態，或使用公用 IP 位址，請參閱 [新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

您可以從本機電腦或使用 Azure Cloud Shell 來完成下列步驟。 若要使用您的本機電腦，請確定已[安裝 Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要使用 Azure Cloud Shell，請選取後續任何命令方塊右上角的 [試試看]****。 Cloud Shell 可讓您登入 Azure。

1. 如果使用 Cloud Shell，請跳至步驟 2。 開啟命令工作階段，然後使用 `Connect-AzAccount` 登入 Azure。
2. 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立資源群組。 下列範例會在美國東部 Azure 區域中建立一個資源群組：

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. 使用 [>new-azvirtualnetworksubnetconfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 和 [new-azvirtualnetwork](/powershell/module/az.network/new-azvirtualnetwork) 命令建立子網設定和虛擬網路：

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

4. 在虛擬網路中建立網路介面，並使用 [AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) 和 [get-aznetworkinterface](/powershell/module/az.network/new-aznetworkinterface) 命令，將子網中的私人 IP 位址指派給網路介面：

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

5. 使用 [>new-azvmconfig](/powershell/module/Az.Compute/New-AzVMConfig)建立 vm 設定，然後使用 [新的-new-azvm](/powershell/module/az.Compute/New-azVM)建立 vm。 出現提示時，請提供要作為 VM 登入認證使用的使用者名稱和密碼：

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> 雖然您可以將私人 IP 位址設定新增至作業系統，但在讀取 [將私人 ip 位址新增至作業系統](virtual-network-network-interface-addresses.md#private)之前，建議您不要這麼做。
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> 若要從網際網路存取 VM，您必須將公用 IP 位址指派給 VM。 您也可以將動態私人 IP 位址指派變更為靜態指派。 如需詳細資訊，請參閱 [新增或變更 IP 位址](virtual-network-network-interface-addresses.md)。 此外，建議您將網路安全性群組與網路介面、您在中建立網路介面的子網或兩者建立關聯，以限制 VM 的網路流量。 如需詳細資訊，請參閱 [管理網路安全性群組](manage-network-security-group.md)。

## <a name="clean-up-resources"></a>清除資源

您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 來移除不再需要的資源群組，以及其所包含的所有資源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [私人 ip 位址](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) ，並將 [靜態私人 ip 位址](virtual-network-network-interface-addresses.md#add-ip-addresses) 指派給 Azure 虛擬機器。
- 深入瞭解如何建立 [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器。
