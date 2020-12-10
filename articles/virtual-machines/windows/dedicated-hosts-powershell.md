---
title: 使用 Azure PowerShell 部署 Azure 專用主機
description: 使用 Azure PowerShell 將 Vm 部署到專用主機。
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/12/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 2f8f2d9eb14e1272af126c9a6d6663f41aaee33f
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005081"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>使用 Azure PowerShell 將 Vm 部署到專用主機

本文會引導您瞭解如何建立 Azure [專用主機](../dedicated-hosts.md)來裝載您的虛擬機器 (VM)。 

請確定您已安裝 Azure PowerShell 2.8.0 版或更新版本，且您已使用中的 Azure 帳戶登入 `Connect-AzAccount` 。 

## <a name="limitations"></a>限制

- 專用主機目前不支援虛擬機器擴展集。
- 專用主機可用的大小和硬體類型因區域而異。 若要深入瞭解，請參閱主機[定價頁面](https://aka.ms/ADHPricing)。

## <a name="create-a-host-group"></a>建立主機群組

**主機群組** 是代表專用主機集合的資源。 您會在區域和可用性區域中建立主機群組，並在其中新增主機。 在規劃高可用性時，還有其他選項。 您可以使用下列其中一個或兩個選項搭配您的專用主機： 
- 跨越多個可用性區域。 在此情況下，您必須在想要使用的每個區域中都有一個主機群組。
- 跨越多個對應至實體機架的容錯網域。 
 
不論是哪一種情況，您都必須為主機群組提供容錯網域計數。 如果您不想在您的群組中跨越容錯網域，請使用容錯網域計數 1。 

您也可以決定同時使用可用性區域和容錯網域。 此範例會在區域1中建立具有2個容錯網域的主機群組。 


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


新增 `-SupportAutomaticPlacement true` 參數，以將 vm 和擴展集實例自動放置在主機群組內的主機上。 如需詳細資訊，請參閱 [手動與自動放置 ](../dedicated-hosts.md#manual-vs-automatic-placement)。


## <a name="create-a-host"></a>建立主機

現在，讓我們在主機群組中建立專用主機。 除了主機的名稱之外，您還必須提供主機的 SKU。 主機 SKU 會擷取支援的 VM 系列，以及專用主機的硬體世代。

如需主機 SKU 和定價的詳細資訊，請參閱 [Azure 專用主機定價](https://aka.ms/ADHPricing)。

如果您為主機群組設定容錯網域計數，系統會要求您指定主機的容錯網域。 在此範例中，我們會將主機的容錯網域設定為1。


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

在專用主機上建立虛擬機器。 

如果您在建立主機群組時指定了可用性區域，則必須在建立虛擬機器時使用相同的區域。 在此範例中，因為我們的主機群組位於區域1，所以我們需要在區域1中建立 VM。  


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
> 如果您在沒有足夠資源的主機上建立虛擬機器，虛擬機器將會以「失敗」狀態建立。 

## <a name="check-the-status-of-the-host"></a>檢查主機的狀態

您可以使用 [GetAzHost](/powershell/module/az.compute/get-azhost) 搭配參數，檢查主機健全狀況狀態，以及您仍然可以部署到主機的虛擬機器數目 `-InstanceView` 。

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

## <a name="create-a-scale-set"></a>建立擴展集 

當您部署擴展集時，請指定主機群組。

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

如果您想要手動選擇要將擴展集部署至哪個主機，請新增 `--host` 主機的名稱。



## <a name="add-an-existing-vm"></a>新增現有的 VM 

您可以將現有的 VM 新增至專用主機，但必須先將 VM Stop\Deallocated。 將 VM 移至專用主機之前，請確定支援 VM 設定：

- VM 大小必須與專用主機位於相同大小的系列中。 例如，如果您的專用主機是 DSv3，則可以 Standard_D4s_v3 VM 大小，但不能是 Standard_A4_v2。 
- VM 必須位於與專用主機相同的區域中。
- VM 不能是鄰近放置群組的一部分。 將 VM 移至專用主機之前，請先從鄰近放置群組移除 VM。 如需詳細資訊，請參閱 [將 VM 移出鄰近位置群組](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- VM 不能位於可用性設定組中。
- 如果 VM 位於可用性區域中，它必須與主機群組位於相同的可用性區域。 VM 和主機群組的可用性區域設定必須相符。

將變數的值取代為您自己的資訊。

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

即使您沒有部署虛擬機器，仍會向您收取專用主機的費用。 您應該刪除目前未使用的任何主機以節省成本。  

只有當不再有任何虛擬機器使用主機時，才能刪除主機。 使用 [Remove-new-azvm](/powershell/module/az.compute/remove-azvm)刪除 vm。

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

刪除 Vm 之後，您可以使用 [AzHost](/powershell/module/az.compute/remove-azhost)刪除主機。

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

一旦刪除所有主機，您可以使用 [AzHostGroup](/powershell/module/az.compute/remove-azhostgroup)刪除主機群組。 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

您也可以使用 [>new-azresourcegroup](/powershell/module/az.resources/remove-azresourcegroup)，在單一命令中刪除整個資源群組。 這麼做會刪除群組中建立的所有資源，包括所有 VM、主機和主機群組。
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>後續步驟

- [這裡](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有範例範本，範例中使用區域和容錯網域來獲得區域中的最大復原。

- 您也可以使用 [Azure 入口網站](../dedicated-hosts-portal.md)部署專用主機。