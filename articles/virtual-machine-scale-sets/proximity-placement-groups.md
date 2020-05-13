---
title: 虛擬機器擴展集的鄰近放置群組預覽
description: 瞭解如何在 Azure 中建立和使用 Windows 虛擬機器擴展集的鄰近放置群組。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 07/01/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 18cb1ae3e549995d7b4732025906329bc609f360
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124341"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>預覽：使用 PowerShell 建立和使用近接位置群組

若要盡可能接近 Vm，達到最低可能的延遲，您應該在[鄰近放置群組](co-location.md#preview-proximity-placement-groups)內部署擴展集。

鄰近放置群組是一種邏輯群組，用來確保 Azure 計算資源實際位於彼此接近的位置。 鄰近放置群組適用于需要低延遲的工作負載。

> [!IMPORTANT]
> 鄰近放置群組目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 在預覽期間，這些區域無法使用鄰近放置群組：**日本東部**、**澳大利亞東部**和**印度中部**。


## <a name="create-a-proximity-placement-group"></a>建立鄰近放置群組
使用[AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) Cmdlet 建立鄰近放置群組。 

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

您可以使用[AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) Cmdlet 來列出所有的鄰近放置群組。

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>建立擴展集

當您使用 Get-azvmss 建立擴展集時，請使用在鄰近放置群組中建立調整， `-ProximityPlacementGroup $ppg.Id` 以[New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss)參考鄰近放置群組識別碼。

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

您可以使用[AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)查看放置群組中的實例。

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>後續步驟

您也可以使用[Azure CLI](../virtual-machines/linux/proximity-placement-groups.md)來建立鄰近放置群組。
