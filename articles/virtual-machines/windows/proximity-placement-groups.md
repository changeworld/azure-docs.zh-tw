---
title: 電源外殼：使用接近放置組
description: 瞭解如何使用 Azure PowerShell 創建和使用鄰近放置組。
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: f69e245d72a63b942896cdd9f4a2225cb4c1706d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208520"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>使用 PowerShell 將 VM 部署到鄰近放置組


要盡可能接近 VM，實現盡可能低的延遲，應將它們部署在[接近放置組中](co-location.md#proximity-placement-groups)。

鄰近放置組是一種邏輯分組，用於確保 Azure 計算資源物理上彼此靠近。 接近放置組對於需要低延遲的工作負載非常有用。


## <a name="create-a-proximity-placement-group"></a>建立鄰近放置群組
使用["新建-鄰近放置組](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup)"Cmdlet 創建接近放置組。 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>列出鄰近放置組

您可以使用["獲取-鄰近放置組](/powershell/module/az.compute/get-azproximityplacementgroup)"Cmdlet 列出所有鄰近放置組。

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>建立 VM

在使用[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)創建`-ProximityPlacementGroup $ppg.Id`VM 時，在鄰近放置組中創建 VM，用於引用接近放置組 ID。

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

您可以使用["獲取-鄰近放置組](/powershell/module/az.compute/get-azproximityplacementgroup)"在放置組中看到 VM。

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>將現有 VM 移動到鄰近放置組

您還可以將現有 VM 添加到鄰近放置組。 您需要首先停止\去分配 VM，然後更新 VM 並重新啟動。

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>將現有 VM 移出鄰近放置組

要從鄰近放置組中刪除 VM，需要首先停止\取消分配 VM，然後更新 VM 並重新啟動。

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>可用性設定組 (Availability Sets)
您還可以在鄰近放置組中創建可用性集。 使用與`-ProximityPlacementGroup`[New-Az可用性設置](/powershell/module/az.compute/new-azavailabilityset)Cmdlet 相同的參數來創建可用性集，並且在可用性集中創建的所有 VM 也將在同一接近放置組中創建。

要將現有可用性集添加到接近放置組，首先需要停止可用性集中的所有 VM。 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>將現有可用性集移動到鄰近放置組

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>將現有可用性集移出鄰近放置組

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>擴展集

您還可以在鄰近放置組中創建比例集。 使用與`-ProximityPlacementGroup`[New-AzVms](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss)相同的參數創建比例集，並將在同一鄰近放置組中創建所有實例。


要向鄰近放置組添加或刪除現有比例集，首先需要停止比例集。 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>將現有比例集移動到接近放置組

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>將設置的現有比例移出鄰近放置組

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>後續步驟

您還可以使用[Azure CLI](../linux/proximity-placement-groups.md)創建鄰近放置組。
