---
title: 在 Azure 中調整 Windows VM 的大小
description: 變更 Azure 虛擬機器所使用的 VM 大小。
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: sizes
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: 4860422fefb5a95fe41912b4898f02867f7b3aeb
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832241"
---
# <a name="resize-a-windows-vm"></a>調整 Windows VM 大小

本文說明如何將 VM 移至不同的[vm 大小](../sizes.md)。

建立虛擬機器 (VM) 後，您可以透過變更 VM 的大小來放大或縮小 VM。 在某些情況下，您必須先解除配置 VM。 如果新的大小不適用於目前裝載 VM 的硬體叢集上，就會發生此情況。

如果您的 VM 使用進階儲存體，請確實選擇 **s** 版本的大小，以取得進階儲存體支援。 例如，請選擇 Standard_E4**s**_v3，而不是 Standard_E4_v3。

## <a name="use-the-portal"></a>使用入口網站

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
1. 開啟虛擬機器的頁面。
1. 在左側功能表中，選取 [**大小**]。
1. 從可用大小清單中選擇新的大小，然後選取 [重**設大小**]。


如果虛擬機器目前正在執行，變更其大小將會使其重新開機。 停止虛擬機器可能會顯示其他大小。

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>使用 PowerShell 來調整不在可用性設定組中的 VM 大小

設定一些變數。 使用您自己的資訊取代這些值。

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

列出裝載 VM 的硬體叢集上適用的 VM 大小。 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

如果您要的大小有列出，請執行以下命令以調整 VM 大小。 如果所需的大小未列出，請移至步驟 3。
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

如果您要的大小未列出，請執行以下命令以解除配置 VM、調整其大小，然後重新啟動 VM。 **\<newVMsize>** 將取代為您想要的大小。
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> 解除配置 VM 會將指派給 VM 的任何動態 IP 位址釋出。 不會影響作業系統和資料磁碟。 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>使用 PowerShell 來調整可用性設定組中 VM 的大小

如果可用性設定組中的 VM 新大小，不適用於目前裝載 VM 的硬體叢集，則必須解除配置可用性設定組中所有的 VM，才能調整 VM 大小。 在已調整某個 VM 的大小後，您也可能需要更新可用性設定組中其他 VM 的大小。 若要調整可用性設定組中 VM 的大小，請執行下列步驟。

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

列出裝載 VM 的硬體叢集上適用的 VM 大小。 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

如果所需的大小有列出，請執行以下命令調整 VM 大小。 如果未列出，請移至下一節。
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
如果您要的大小未列出，請繼續進行下列步驟，以解除配置可用性設定組中所有的 VM、調整 VM 大小，然後重新啟動 VM。

停止可用性設定組中的所有 VM。
   
```powershell
$availabilitySetName = "<availabilitySetName>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines |  Stop-AzVM -Force -NoWait  
```

將可用性設定組中的 VM 調整大小後重新啟動。
   
```powershell
$availabilitySetName = "<availabilitySetName>"
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines | Foreach-Object { $_.HardwareProfile.VmSize = $newSize }
$virtualMachines | Update-AzVM
$virtualMachines | Start-AzVM
```

## <a name="next-steps"></a>後續步驟

如需更多的擴充性，請執行多個 VM 實例並向外延展。如需詳細資訊，請參閱[自動調整虛擬機器擴展集中的 Windows 機器](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)。