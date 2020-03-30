---
title: 調整 Azure 中的 Windows VM 的大小
description: 更改用於 Azure 虛擬機器的 VM 大小。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: 6718804d4635edb2628b53017ab9d377928afad8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941729"
---
# <a name="resize-a-windows-vm"></a>調整 Windows VM 大小

本文介紹如何將 VM 移動到不同的 VM[大小](sizes.md)。

建立虛擬機器 (VM) 後，您可以透過變更 VM 的大小來放大或縮小 VM。 在某些情況下，您必須先解除配置 VM。 如果新的大小不適用於目前裝載 VM 的硬體叢集上，就會發生此情況。

如果您的 VM 使用進階儲存體，請確實選擇 **s** 版本的大小，以取得進階儲存體支援。 例如，請選擇 Standard_E4**s**_v3，而不是 Standard_E4_v3。

## <a name="use-the-portal"></a>使用入口網站

1. 打開[Azure 門戶](https://portal.azure.com)。
1. 打開虛擬機器的頁面。
1. 在左側功能表中，選擇 **"大小**"。
1. 從可用大小清單中選擇新大小，然後選擇 **"調整大小**"。


如果虛擬機器當前正在運行，更改其大小將導致重新開機它。 停止虛擬機器可能會顯示其他大小。

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>使用 PowerShell 調整不在可用性集中的 VM 的大小

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

如果您要的大小未列出，請執行以下命令以解除配置 VM、調整其大小，然後重新啟動 VM。 將**\<新的Vmsize>** 替換為所需的大小。
   
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

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>使用 PowerShell 調整可用性集中的 VM 大小

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
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

將可用性設定組中的 VM 調整大小後重新啟動。
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>後續步驟

對於其他可伸縮性，運行多個 VM 實例並橫向擴展。有關詳細資訊，請參閱[在虛擬機器縮放集中自動縮放 Windows 電腦](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)。

