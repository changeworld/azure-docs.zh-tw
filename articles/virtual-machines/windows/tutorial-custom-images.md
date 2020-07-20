---
title: 教學課程 - 使用 Azure PowerShell 建立自訂的 VM 映像
description: 在本教學課程中，您將了解如何使用 Azure PowerShell 來建立儲存在 Azure 共用映像庫中的 Windows 自訂虛擬機器映像。
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e8d68e5f2eeeb7363469535c027f258fbc9d7ed1
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2020
ms.locfileid: "85480485"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>教學課程：使用 Azure PowerShell 建立 Windows VM 映像

您可使用映像來啟動部署，並確保多個 VM 之間的一致性。 在本教學課程中，您將使用 PowerShell 來建立自己的 Azure 虛擬機器特製化映像，並將其儲存在共用映像庫中。 您會了解如何：

> [!div class="checklist"]
> * 建立共用映像庫
> * 建立映像定義
> * 建立映像版本
> * 從映像建立 VM 
> * 共用映像庫



## <a name="before-you-begin"></a>開始之前

下列步驟將詳細說明如何將現有 VM 轉換成可重複使用的自訂映像，以便讓您用來建立新的 VM。

若要完成本教學課程中的範例，您目前必須具有虛擬機器。 如有需要，您可以查看 [PowerShell 快速入門](quick-create-powershell.md)來建立要用於本教學課程的 VM。 逐步完成教學課程之後，請視需要取代資源名稱。

## <a name="overview"></a>概觀

[共用映像資源庫](shared-image-galleries.md)可簡化跨組織共用自訂映像。 自訂映像類似 Marketplace 映像，但您要自行建立它們。 自訂映像可用於啟動程序設定，例如，預先載入應用程式、應用程式設定和其他 OS 設定。 

共用映像庫可讓您與其他人共用您的自訂 VM 映像。 選擇您要共用的映像、您要開放使用的區域，以及您要共用的對象。 

共用映像庫具有多個資源類型：

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看] 即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製] 即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

## <a name="get-the-vm"></a>取得 VM

您可以使用 [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) 來查看資源群組中的可用 VM 清單。 當您知道 VM 名稱和資源群組之後，您可以再次使用 `Get-AzVM` 來取得該 VM 物件，並將其儲存在變數中以供日後使用。 此範例會從 "myResourceGroup" 資源群組取得名為 sourceVM 的 VM，然後將其指派至 $sourceVM 變數。 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 命令來建立資源群組。

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 在下列範例中，名為 myGalleryRG 的資源群組會建立在 EastUS 區域中：

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>建立映像資源庫 

映像資源庫是用於啟用映像共用的主要資源。 資源庫名稱允許的字元為大寫或小寫字母、數字、點和句點。 資源庫名稱不能包含連字號。 資源庫名稱在您的訂用帳戶內必須是唯一的。 

使用 [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery) 建立映像資源庫。 下列範例會在 *myGalleryRG* 資源群組中建立名為 *myGallery* 的資源庫。

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>建立映像定義 

映像定義會建立映像的邏輯群組。 並且可用來管理在其中建立的映像版本相關資訊。 映像定義名稱可以由大寫或小寫字母、數字、點、虛線和句點組成。 若要深入了解您可以為映像定義指定哪些值，請參閱[映像定義](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)。

使用 [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) 建立映像定義。 在此範例中，資源庫映像會命名為 myGalleryImage，並為特製化映像而建立。 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>建立映像版本

使用 [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) 從 VM 建立映像版本。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此範例中，映像版本為 1.0.0，並且會複寫到「美國東部」和「美國中南部」資料中心。 選擇要複寫的目的地區域時，您必須包含作為複寫目標的「來源」區域。

若要從 VM 建立映像版本，請使用 `$vm.Id.ToString()` 作為 `-Source`。

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVM.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

將映像複寫到所有目的地區域可能需要一些時間。


## <a name="create-a-vm"></a>建立 VM 

當您擁有特製化映像之後，您就可以建立一個或多個新的 VM。 使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Cmdlet。 若要使用映像，請使用 `Set-AzVMSourceImage`，並將 `-Id` 設為映像定義識別碼 (在此案例中為 $galleryImage.Id)，以永遠使用最新的映像版本。 

視需要取代此範例中的資源名稱。 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```


## <a name="share-the-gallery"></a>共用資源庫

我們建議您在映像庫層級上共用存取權。 使用電子郵件地址和 [Get-AzADUser](/powershell/module/az.resources/get-azaduser) Cmdlet 來取得使用者的物件識別碼，然後使用 [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) 來提供資源庫的存取權。 以您自己的資訊取代範例電子郵件 (在此範例中為 alinne_montes@contoso.com)。

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```
   
## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組及所有相關資源：

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure 也提供以 Packer 為基礎的服務：[Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview)。 只要在範本中描述您的自訂，其就會處理映像建立作業。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立特製化的 VM 映像。 您已了解如何︰

> [!div class="checklist"]
> * 建立共用映像庫
> * 建立映像定義
> * 建立映像版本
> * 從映像建立 VM 
> * 共用映像庫

請前進到下一個教學課程，以了解如何建立高可用性的虛擬機器。

> [!div class="nextstepaction"]
> [建立高可用性 VM](tutorial-availability-sets.md)



