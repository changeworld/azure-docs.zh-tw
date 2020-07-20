---
title: 從特製化映射建立 VM
description: 使用共用映射資源庫中的特製化映射來建立 VM。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: fdf1e6cf15279a0ff5be4b45385a13a3b967d22e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85374622"
---
# <a name="create-a-vm-using-a-specialized-image"></a>使用特製化映射建立 VM 

從儲存在共用映射資源庫中的特製化映射版本建立 VM。 如果想要使用一般化映射版本建立 VM，請參閱[使用一般化映射建立 vm](vm-generalized-image-version-powershell.md)。

一旦有了特製化映射版本，您就可以建立一或多個新的 Vm。 使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Cmdlet。 

在此範例中，我們會使用映射定義識別碼，以確保您的新 VM 會使用最新版本的映射。 您也可以使用的映射版本識別碼，來使用特定版本 `Set-AzVMSourceImage -Id` 。 例如，若要使用 image *1.0.0*版的型別： `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` 。 

請注意，使用特定映射版本表示自動化可能會失敗，因為該特定映射版本無法使用，因為它已從區域中刪除或移除。 除非需要特定映射版本，否則建議使用映射定義識別碼來建立新的 VM。

視需要取代此範例中的資源名稱。 


```azurepowershell-interactive

# Create some variables for the new VM.

$resourceGroup = "mySIGSpecializedRG"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition


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

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig

```

## <a name="attach-the-data-disk"></a>連接資料磁碟
如果您的映射包含資料磁片，您需要將資料磁片連結至 VM。

```azurepowershell-interactive

$vm = Get-AzVM -Name $vmName -ResourceGroupName $resourceGroup 

$lun = $imageVersion.StorageProfile.DataDiskImages.Lun

Add-AzVMDataDisk `
   -CreateOption FromImage `
   -SourceImageUri $imageversion.Id `
   -Lun $lun `
   -Caching $imageVersion.StorageProfile.DataDiskImages.HostCaching `
   -DiskSizeInGB $imageVersion.StorageProfile.DataDiskImages.SizeInGB `
   -VM $vm

```


## <a name="next-steps"></a>後續步驟
[Azure 映射產生器（預覽）](./linux/image-builder-overview.md)可協助自動建立映射版本，您甚至可以使用它來更新和[建立現有映射版本的新映射版本](./linux/image-builder-gallery-update-image-version.md)。 

您也可以使用範本建立共用映像庫資源。 有數個 Azure 快速入門範本可以使用： 

- [建立共用映像資源庫](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共用映像資源庫中建立映像定義](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共用映像資源庫中建立映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [從映像版本建立 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

如需共用映像資源庫的詳細資訊，請參閱[概觀](./windows/shared-image-galleries.md)。 若遇到任何問題，請參閱[針對共用映像資源庫問題進行疑難排解](./windows/troubleshooting-shared-images.md)。
