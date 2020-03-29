---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bae66078a1bcb1d80f0798b1d501598fa785fb80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241216"
---
## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]**** 即可。 您還可以通過 訪問[https://shell.azure.com/powershell](https://shell.azure.com/powershell)在單獨的瀏覽器選項卡中啟動雲外殼。 選擇 **"複製"** 以複製代碼塊，將其粘貼到雲外殼中，然後按 Enter 以運行它。


## <a name="get-the-managed-image"></a>取得受控映像

您可以使用 [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage) 來查看資源群組中的可用映像清單。 當您知道映像名稱和其所位於的資源群組之後，您可以再次使用 `Get-AzImage` 來取得該映像物件，並將它儲存在變數中以供日後使用。 此範例會從 "myResourceGroup" 資源群組取得名為 *myImage* 的映象，然後將它指派至 *$managedImage* 變數。 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>建立映像資源庫 

映像資源庫是用於啟用映像共用的主要資源。 資源庫名稱允許的字元為大寫或小寫字母、數字、點和句點。 庫名稱不能包含破折號。 資源庫名稱在您的訂用帳戶內必須是唯一的。 

使用 [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery) 建立映像資源庫。 下列範例會在 *myGalleryRG* 資源群組中建立名為 *myGallery* 的資源庫。

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>建立映像定義 

圖像定義為圖像創建邏輯分組。 它們用於管理有關在其中創建的映射版本的資訊。 圖像定義名稱可以由大寫字母或小寫字母、數位、點、破折號和句點組成。 有關可以為圖像定義指定的值的詳細資訊，請參閱[圖像定義](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)。

使用[新阿茲加圖圖像標題](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)創建圖像定義。 在此範例中，資源庫映像名為 *myGalleryImage*。

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>建立映像版本

使用[New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)從託管映射創建映射版本。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*主要版本*。*次要版本*。*補丁*。

在此範例中，映像版本為 *1.0.0*，且它會被複寫到「美國中西部」** 和「美國中南部」** 資料中心。 選擇複製的目的地區域時，請記住，還必須將*源*區域作為複製目標。


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

將映像複寫到所有目標區域可能需要花上一點時間，因此我們已建立可追蹤進度的作業。 若要查看作業的進度，請輸入 `$job.State`。

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 您需要等待映射版本完全完成正在構建和複製，然後才能使用相同的託管映射創建另一個映射版本。 
>
> 您還可以在創建映射版本時添加圖像`-StorageAccountType Standard_ZRS`版本，將映射版本存儲在[區域冗余存儲](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)中。
>


## <a name="share-the-gallery"></a>共用庫

我們建議您在圖像庫級別共用存取權限。 使用電子郵件地址和[Get-AzADUser](/powershell/module/az.resources/get-azaduser) Cmdlet 獲取使用者的物件識別碼，然後使用[New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment)授予他們訪問庫的存取權限。 將此示例alinne_montes@contoso.com中的示例電子郵件替換為您自己的資訊。

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