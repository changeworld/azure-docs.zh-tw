---
title: 虛擬機器縮放集的鄰近放置組預覽
description: 瞭解如何在 Azure 中為 Windows 虛擬機器縮放集創建和使用鄰近放置組。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273610"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>預覽：使用 PowerShell 創建和使用接近放置組

要盡可能接近 VM，實現盡可能低的延遲，應將比例集部署在[接近放置組中](co-location.md#preview-proximity-placement-groups)。

鄰近放置組是一種邏輯分組，用於確保 Azure 計算資源物理上彼此靠近。 接近放置組對於需要低延遲的工作負載非常有用。

> [!IMPORTANT]
> 鄰近放置組當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 在預覽期間，這些地區沒有接近放置組：**日本東部**、**澳大利亞東部****和印度中部**。


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


## <a name="create-a-scale-set"></a>建立擴展集

在使用[New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) `-ProximityPlacementGroup $ppg.Id`創建比例集時，在鄰近放置組中創建比例，用於引用接近放置組 ID。

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

您可以使用["獲取-鄰近放置組](/powershell/module/az.compute/get-azproximityplacementgroup)"在放置組中看到實例。

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>後續步驟

您還可以使用[Azure CLI](../virtual-machines/linux/proximity-placement-groups.md)創建鄰近放置組。
