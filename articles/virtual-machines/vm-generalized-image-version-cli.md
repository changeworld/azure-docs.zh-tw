---
title: 使用 Azure CLI 從一般化映射建立 VM
description: 使用 Azure CLI，從一般化映射版本建立 VM。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.openlocfilehash: 60f5813cf5ff0d52b57f7a4b55d86195051f217f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999066"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-cli"></a>使用 CLI 從一般化映射版本建立 VM

從儲存在共用映射資源庫中的[一般化映射版本](./linux/shared-image-galleries.md#generalized-and-specialized-images)建立 VM。 如果您想要使用特製化映射來建立 VM，請參閱[從特製化映射建立 vm](vm-specialized-image-version-powershell.md)。 


## <a name="get-the-image-id"></a>取得映射識別碼

使用[az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list)來列出資源庫中的映射定義，以查看定義的名稱和識別碼。

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>建立 VM

使用 [az vm create](/cli/azure/vm#az-vm-create) 建立 VM。 若要使用最新版本的映射，請將設定 `--image` 為映射定義的識別碼。 

視需要取代此範例中的資源名稱。 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

您也可以使用參數的映射版本識別碼來使用特定版本 `--image` 。 例如，若要使用 image *1.0.0*版的型別： `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` 。

## <a name="next-steps"></a>接下來的步驟

[Azure 映射產生器（預覽）](./linux/image-builder-overview.md)可協助自動建立映射版本，您甚至可以使用它來更新和[建立現有映射版本的新映射版本](./linux/image-builder-gallery-update-image-version.md)。 
