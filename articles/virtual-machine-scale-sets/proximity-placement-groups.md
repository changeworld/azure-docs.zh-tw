---
title: 適用於虛擬機器擴展集的鄰近放置群組預覽
description: 了解在 Azure 中建立及使用適用於 Windows 虛擬機器擴展集的鄰近放置群組。
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt
ms.openlocfilehash: 8e455d4d016f97a466838c07fc7af2422f72cc00
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727092"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>預覽：使用 PowerShell 建立及使用鄰近放置群組

若要盡可能使 VM 彼此接近以達成最低的延遲，您應該將擴展集部署到[鄰近放置群組](co-location.md#preview-proximity-placement-groups)內。

鄰近放置群組是邏輯群組，可用來確保 Azure 計算資源實際位於彼此接近的位置。 鄰近放置群組很適合用於具備低延遲需求的工作負載。

> [!IMPORTANT]
> 鄰近放置群組目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 鄰近放置群組在預覽期間無法在這些區域中使用：**日本東部**、**澳大利亞東部**與**印度中部**。


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


## <a name="create-a-scale-set"></a>建立擴展集

在使用 [New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) \(英文\) 來建立擴展集時，請使用 `-ProximityPlacementGroup $ppg.Id` 來參考鄰近放置群組識別碼，以在鄰近放置群組中建立擴展。

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

您可以使用 [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) 來查看放置群組中的執行個體。

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>後續步驟

您也可以使用 [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) 來建立鄰近放置群組。
