---
title: 教學課程 - 透過 Azure PowerShell 使用擴展集中的自訂 VM 映像
description: 了解如何使用 Azure PowerShell 來建立可用於部署虛擬機器擴展集的自訂 VM 映像
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 8a0ee8565d22e935535bbe1e830d8399b2489f5c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527518"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>教學課程：使用 Azure PowerShell 建立及使用虛擬機器擴展集的自訂映像

當您建立擴展集時，您會指定部署 VM 執行個體時所要使用的映像。 若要減少部署 VM 執行個體後的工作數量，您可以使用自訂的 VM 映像。 此自訂 VM 映像包括任何必要的應用程式安裝或組態。 在擴展集中建立的任何 VM 執行個體都會使用自訂 VM 映像，並已可以處理您的應用程式流量。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立共用映像庫
> * 建立映像定義
> * 建立映像版本
> * 從映像建立擴展集 
> * 共用映像庫

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>開始之前

下列步驟將詳細說明如何將現有 VM 轉換成可重複使用的自訂映像，以便讓您用來建立擴展集。

若要完成本教學課程中的範例，您目前必須具有虛擬機器。 如有需要，您可以查看 [PowerShell 快速入門](quick-create-powershell.md)來建立要用於本教學課程的 VM。 逐步完成教學課程之後，請視需要取代資源名稱。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。


## <a name="get-the-vm"></a>取得 VM

您可以使用 [Get-AzVM](/powershell/module/az.compute/get-azvm) 來查看資源群組中的可用 VM 清單。 當您知道 VM 名稱和資源群組之後，您可以再次使用 `Get-AzVM` 來取得該 VM 物件，並將其儲存在變數中以供日後使用。 此範例會從 "myResourceGroup" 資源群組取得名為 sourceVM  的 VM，然後將其指派至 $vm  變數。 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```
## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立資源群組。

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 在下列範例中，名為 myGalleryRG  的資源群組會建立在 EastUS  區域中：

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>建立映像資源庫 

映像資源庫是用於啟用映像共用的主要資源。 資源庫名稱允許的字元為大寫或小寫字母、數字、點和句點。 資源庫名稱不能包含連字號。 資源庫名稱在您的訂用帳戶內必須是唯一的。 

使用 [New-AzGallery](/powershell/module/az.compute/new-azgallery) 建立映像資源庫。 下列範例會在 *myGalleryRG* 資源群組中建立名為 *myGallery* 的資源庫。

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>建立映像定義 

映像定義會建立映像的邏輯群組。 並且可用來管理在其中建立的映像版本相關資訊。 映像定義名稱可以由大寫或小寫字母、數字、點、虛線和句點組成。 若要深入了解您可以為映像定義指定哪些值，請參閱[映像定義](../virtual-machines/windows/shared-image-galleries.md#image-definitions)。

使用 [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) 建立映像定義。 在此範例中，資源庫映像會命名為 myGalleryImage  ，並為特製化映像而建立。 

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

使用 [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) 從 VM 建立映像版本。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此範例中，映像版本為 1.0.0  ，並且會複寫到「美國東部」  和「美國中南部」  資料中心。 選擇要複寫的目的地區域時，您必須包含作為複寫目標的「來源」  區域。

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

## <a name="create-a-scale-set-from-the-image"></a>從映像建立擴展集
現在，使用 [New-AzVmss](/powershell/module/az.compute/new-azvmss) 建立擴展集，以使用 `-ImageName` 參數來定義前一個步驟中建立的自訂 VM 映像。 為了將流量散發到個別的虛擬機器執行個體，也會建立負載平衡器。 負載平衡器包含在 TCP 連接埠 80 上分配流量的規則，同時允許 TCP 連接埠 3389 上的遠端桌面流量以及 TCP 連接埠 5985 上的 PowerShell 遠端處理。 出現提示時，請為擴展集中的 VM 執行個體提供適當的系統管理認證：

```azurepowershell-interactive
# Define variables for the scale set
$resourceGroupName = "myVMSSRG3"
$scaleSetName = "myScaleSet3"
$location = "East US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $galleryImage.Id

# Complete the configuration
 
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig 

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

建立及設定所有擴展集資源和 VM 需要幾分鐘的時間。


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

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組及所有相關資源：

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the scale set resource group
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure 也提供以 Packer 為基礎的服務：[Azure VM Image Builder](../virtual-machines/windows/image-builder-overview.md)。 只要在範本中描述您的自訂，其就會處理映像建立作業。 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何使用 Azure PowerShell 來建立及使用擴展集的自訂 VM 映像：

> [!div class="checklist"]
> * 建立共用映像庫
> * 建立映像定義
> * 建立映像版本
> * 從映像建立擴展集 
> * 共用映像庫

前往下一個教學課程，以了解如何將應用程式部署至擴展集。

> [!div class="nextstepaction"]
> [將應用程式部署至擴展集](tutorial-install-apps-powershell.md)
