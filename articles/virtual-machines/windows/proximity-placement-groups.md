---
title: PowerShell：使用鄰近放置群組
description: 瞭解如何使用 Azure PowerShell 來建立和使用鄰近放置群組。
services: virtual-machines
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 2401e8c160fd1c2ee3a734f374f1d4409c52ed16
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82098521"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>使用 PowerShell 將 Vm 部署至鄰近放置群組


若要盡可能接近 Vm，達到最低可能的延遲，您應該將它們部署在[鄰近放置群組](co-location.md#proximity-placement-groups)內。

鄰近放置群組是邏輯群組，可用來確保 Azure 計算資源實際位於彼此接近的位置。 鄰近放置群組很適合用於具備低延遲需求的工作負載。


## <a name="create-a-proximity-placement-group"></a>建立鄰近放置群組
使用 [New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) \(英文\) Cmdlet 來建立鄰近放置群組。 

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

## <a name="list-proximity-placement-groups"></a>列出鄰近放置群組

您可以使用 [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) Cmdlet 來列出所有鄰近放置群組。

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>建立 VM

當您使用 Update-azvm 建立 VM 時，請使用在鄰近放置群組中建立 VM `-ProximityPlacementGroup $ppg.Id` ，以參考[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)鄰近放置群組識別碼。

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

您可以使用[AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)查看放置群組中的 VM。

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>將現有的 VM 移到鄰近位置群組

您也可以將現有的 VM 新增至鄰近放置群組。 您必須先 stop\deallocate VM，然後更新 VM 並重新啟動。

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>將現有的 VM 移出近接位置群組

若要從鄰近放置群組移除 VM，您必須先 stop\deallocate VM，然後更新 VM 並重新啟動。

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>可用性設定組 (Availability Sets)
您也可以在鄰近放置群組中建立可用性設定組。 使用 `-ProximityPlacementGroup` 與[new-azavailabilityset 組](/powershell/module/az.compute/new-azavailabilityset)指令程式相同的參數來建立可用性設定組，而且在可用性設定組中建立的所有 vm 也會在相同的鄰近放置群組中建立。

若要在鄰近放置群組中新增或移除現有的可用性設定組，您必須先停止可用性設定組中的所有 Vm。 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>將現有的可用性設定組移至鄰近位置群組

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

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>將現有的可用性設定組移出鄰近位置群組

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

您也可以在鄰近放置群組中建立擴展集。 使用 `-ProximityPlacementGroup` 與[get-azvmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss)相同的參數來建立擴展集，而且所有實例將會在相同的鄰近放置群組中建立。


若要在鄰近放置群組中新增或移除現有的擴展集，您必須先停止擴展集。 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>將現有的擴展集移至鄰近位置群組

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>將現有的擴展集移出近接位置群組

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>後續步驟

您也可以使用 [Azure CLI](../linux/proximity-placement-groups.md) 來建立鄰近放置群組。
