---
title: 使用 Azure PowerShell 部署 Azure 專用主機
description: 使用 Azure PowerShell 將 Vm 部署到專用主機。
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.openlocfilehash: 5cd82635f3aec2cca251e122aadf96f70d377c8a
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190528"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>使用 Azure PowerShell 將 Vm 部署到專用主機

本文會引導您瞭解如何建立 Azure[專用主機](dedicated-hosts.md)來裝載您的虛擬機器（vm）。 

請確定您已安裝 Azure PowerShell 2.8.0 或更新版本，且已使用 `Connect-AzAccount`登入 Azure 帳戶。 

## <a name="limitations"></a>限制

- 專用主機目前不支援虛擬機器擴展集。
- 支援下列 VM 系列： DSv3、ESv3 和 Fsv2。 

## <a name="create-a-host-group"></a>建立主機群組

**主機群組**是代表專用主機集合的資源。 您會在區域和可用性區域中建立主機群組，並在其中新增主機。 在規劃高可用性時，還有其他選項。 您可以使用下列其中一個或兩個選項搭配您的專用主機： 
- 跨越多個可用性區域。 在此情況下，您必須在想要使用的每個區域中都有一個主機群組。
- 跨越多個對應至實體機架的容錯網域。 
 
不論是哪一種情況，您都必須提供主機群組的容錯網域計數。 如果您不想要跨越群組中的容錯網域，請使用容錯網域計數1。 

您也可以決定使用可用性區域和容錯網域。 這個範例會在區域1中建立具有2個容錯網域的主機群組。 


```powershell
$rgName = "myDHResourceGroup"
$location = "East US"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>建立主機

現在讓我們在主機群組中建立專用主機。 除了主機的名稱之外，您還必須提供主機的 SKU。 主機 SKU 會捕捉支援的 VM 系列，以及專用主機的硬體世代。


如需主機 Sku 和定價的詳細資訊，請參閱[Azure 專用主機定價](https://aka.ms/ADHPricing)。

如果您為主機群組設定容錯網域計數，系統會要求您指定主機的容錯網域。 在此範例中，我們會將主機的容錯網域設定為1。


```powershell
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

如果您在建立主機群組時指定了可用性區域，則在建立虛擬機器時，您必須使用相同的區域。 在此範例中，因為我們的主機群組位於區域1，所以我們需要在區域1中建立 VM。  


```powershell
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
> 如果您在沒有足夠資源的主機上建立虛擬機器，虛擬機器將會以失敗狀態建立。 

## <a name="check-the-status-of-the-host"></a>檢查主機的狀態

您可以使用[GetAzHost](/powershell/module/az.compute/get-azhost)搭配 `-InstanceView` 參數，檢查主機健全狀況狀態，以及您仍然可以部署到主機的虛擬機器數目。

```
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

## <a name="clean-up"></a>清除

即使沒有部署虛擬機器，仍會向您收取專用主機的費用。 您應該刪除目前未使用的任何主機來節省成本。  

只有在沒有任何較長的虛擬機器使用時，您才能刪除主機。 使用[update-azvm](/powershell/module/az.compute/remove-azvm)刪除 vm。

```powershell
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

刪除 Vm 之後，您可以使用[AzHost](/powershell/module/az.compute/remove-azhost)刪除主機。

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

刪除所有主機之後，您可以使用[AzHostGroup](/powershell/module/az.compute/remove-azhostgroup)刪除主機群組。 

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

您也可以使用[remove-azresourcegroup](/powershell/module/az.resources/remove-azresourcegroup)，在單一命令中刪除整個資源群組。 這會刪除群組中建立的所有資源，包括所有 Vm、主機和主機群組。
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>後續步驟

- [這裡](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有範例範本，它會使用區域和容錯網域來取得區域中的最大復原。

- 您也可以使用[Azure 入口網站](dedicated-hosts-portal.md)部署專用主機。
