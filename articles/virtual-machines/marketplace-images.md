---
title: 使用 Azure PowerShell 指定 Marketplace 採購方案資訊
description: 瞭解如何在共用映射庫中建立映射時，指定 Azure Marketplace 購買方案詳細資料。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 3de79e5cb3db2d0c52d13826900ec7160271edf9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86225063"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>在建立映射時提供 Azure Marketplace 採購方案資訊

如果您要在共用資源庫中建立映射，並使用原本從 Azure Marketplace 映射建立的來源，您可能需要追蹤購買方案資訊。 本文說明如何尋找 VM 的採購方案資訊，然後在建立映射定義時使用該資訊。 我們也會討論如何使用映射定義中的資訊，以簡化為映射建立 VM 時的採購方案資訊。

如需尋找和使用 Marketplace 映射的詳細資訊，請參閱 [尋找和使用 Azure Marketplace 映射](./windows/cli-ps-findimage.md)。


## <a name="get-the-source-vm-information"></a>取得來源 VM 資訊
如果您仍有原始 VM，您可以使用 New-azvm 從該 VM 取得方案、發行者和 sku 資訊。 此範例會在*myResourceGroup*資源群組中取得名為*myVM*的 VM，然後顯示購買方案資訊。

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan.Publisher
$vm.Plan.Name
$vm.Plan.Product
```

## <a name="create-the-image-definition"></a>建立映射定義

取得您想要用來儲存影像的映射庫。 您可以先列出所有資源庫。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

然後，為您要使用的資源庫建立變數。 在此範例中，我們會 `$gallery` 在*myGalleryRG*資源群組中建立名為 *>mygalleryrg*的變數。

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

使用  `-PurchasePlanPublisher` 、和參數建立映射定義 `-PurchasePlanProduct` `-PurchasePlanName` 。

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

然後使用 [新的-new-azgalleryimageversion](/powershell/module/az.compute/new-azgalleryimageversion)建立您的映射版本。 您可以從 [VM](image-version-vm-powershell.md#create-an-image-version)、 [受控映射](image-version-managed-image-powershell.md#create-an-image-version)、 [VHD\snapshot](image-version-snapshot-powershell.md#create-an-image-version)或 [其他映射版本](image-version-another-gallery-powershell.md#create-the-image-version)建立映射版本。 


## <a name="create-the-vm"></a>建立 VM

當您從映射建立 VM 時，您可以使用映射定義中的資訊，使用 [>set-azvmplan](/powershell/module/az.compute/set-azvmplan)傳遞發行者資訊。


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>接下來的步驟

如需尋找和使用 Marketplace 映射的詳細資訊，請參閱 [尋找和使用 Azure Marketplace 映射](./windows/cli-ps-findimage.md)。