---
title: 使用 Azure PowerShell 部署 Azure 專用主機
description: 使用 Azure PowerShell 將 VM 部署到專用主機。
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.openlocfilehash: a228a83d711c84d2aa994e6de7d90af48cca7f28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530932"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>使用 Azure PowerShell 將 VM 部署到專用主機

本文將指導您如何創建 Azure[專用主機](dedicated-hosts.md)來託管虛擬機器 （VM）。 

請確保已安裝 Azure PowerShell 版本 2.8.0 或更高版本，並且已登錄到 中的`Connect-AzAccount`Azure 帳戶。 

## <a name="limitations"></a>限制

- 專用主機當前不支援虛擬機器縮放集。
- 專用主機可用的大小和硬體類型因地區而異。 請參閱主機[定價頁面](https://aka.ms/ADHPricing)以瞭解更多資訊。

## <a name="create-a-host-group"></a>建立主機群組

**主機組**是表示專用主機集合的資源。 在區域和可用性區域中創建主機組，並將主機添加到其中。 在規劃高可用性時，還有其他選項。 您可以將以下一個或兩個選項用於專用主機： 
- 跨多個可用性區域的跨範圍。 在這種情況下，您需要在每個要使用的區域中具有一個主機組。
- 跨映射到物理機架的多個容錯域。 
 
在這兩種情況下，都需要為主機組提供容錯域計數。 如果不想跨越組中的容錯域，請使用容錯域計數 1。 

您還可以決定同時使用可用性區域和容錯域。 本示例在區域 1 中創建一個主機組，其中有 2 個容錯域。 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>創建主機

現在，讓我們在主機組中創建一個專用主機。 除了主機的名稱外，還需要為主機提供 SKU。 主機 SKU 捕獲支援的 VM 系列以及專用主機的硬體生成。

有關主機 SKU 和定價的詳細資訊，請參閱[Azure 專用主機定價](https://aka.ms/ADHPricing)。

如果為主機組設置容錯域計數，系統將要求您為主機指定容錯域。 在此示例中，我們將主機的容錯域設置為 1。


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>建立 VM

在專用主機上創建虛擬機器。 

如果在創建主機組時指定了可用性區域，則創建虛擬機器時需要使用相同的區域。 對於此示例，由於我們的主機組位於區域 1 中，我們需要在區域 1 中創建 VM。  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> 如果在沒有足夠的資源的主機上創建虛擬機器，則虛擬機器將以 FAILED 狀態創建。 

## <a name="check-the-status-of-the-host"></a>檢查主機的狀態

您可以使用 使用 參數使用[GetAzHost](/powershell/module/az.compute/get-azhost)檢查主機運行狀況狀態以及仍可部署到主機的`-InstanceView`虛擬機器數。

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

輸出類似如下範例：

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="add-an-existing-vm"></a>添加現有 VM 

您可以將現有 VM 添加到專用主機，但 VM 必須首先處於"停止_交易"的位置。 在將 VM 移動到專用主機之前，請確保 VM 配置受支援：

- VM 大小必須與專用主機的大小相同。 例如，如果您的專用主機是 DSv3，則 VM 大小可以Standard_D4s_v3，但它不可能是Standard_A4_v2。 
- VM 需要與專用主機位於同一區域。
- VM 不能是鄰近放置組的一部分。 在將 VM 移動到專用主機之前，請從接近放置組中刪除 VM。 有關詳細資訊，請參閱將[VM 移出鄰近放置組](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- VM 不能位於可用性集中。
- 如果 VM 位於可用性區域中，則它必須與主機組位於同一可用性區域。 VM 和主機組的可用性地區設定必須匹配。

將變數的值替換為您自己的資訊。

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>清除

即使未部署虛擬機器，也會為專用主機向您收費。 您應該刪除當前不使用的任何主機以節省成本。  

僅當不再有虛擬機器使用主機時，才能刪除主機。 使用[Remove-AzVM](/powershell/module/az.compute/remove-azvm)刪除 VM。

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

刪除 VM 後，可以使用[Delete-AzHost](/powershell/module/az.compute/remove-azhost)刪除主機。

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

刪除所有主機後，可以使用[刪除 AzHostGroup](/powershell/module/az.compute/remove-azhostgroup)刪除主機組。 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

您還可以使用[刪除-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)在單個命令中刪除整個資源組。 這將刪除組中創建的所有資源，包括所有 VM、主機和主機組。
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>後續步驟

- [此處](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)找到的示例範本，它同時使用區域和容錯域，以實現區域中的最大恢復能力。

- 您還可以使用[Azure 門戶](dedicated-hosts-portal.md)部署專用主機。
