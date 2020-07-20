---
title: 使用 CLI 從另一個資源庫複製映射版本
description: 從具有 Azure CLI 的另一個資源庫複製映射版本。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 764fe98ad20aa29506b4fba723762124e24af245
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224327"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>使用 Azure CLI 從另一個資源庫複製映射

如果您的組織中有多個資源庫，您也可以從儲存在其他資源庫中的現有映射版本建立映射版本。 例如，您可能有開發和測試資源庫，可用於建立和測試新的映射。 當他們準備好在生產環境中使用時，您可以使用此範例將它們複製到生產資源庫。 您也可以使用[Azure PowerShell](image-version-another-gallery-powershell.md)，從另一個資源庫中的映射建立映射。



## <a name="before-you-begin"></a>開始之前

若要完成本文，您必須擁有現有的來源資源庫、映射定義和映射版本。 您也應該有目的地資源庫。 

來源映射版本必須複寫到目的地資源庫所在的區域。 

當您完成本文時，請視需要取代資源名稱。



## <a name="get-information-from-the-source-gallery"></a>從來源資源庫取得資訊

您將需要來源映射定義的資訊，以便在新的圖庫中建立複本。

使用[az sig list](/cli/azure/sig#az-sig-list)尋找來源資源庫的相關資訊，以列出可用映射庫的相關資訊。

```azurecli-interactive 
az sig list -o table
```

使用[az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list)來列出圖庫中的映射定義。 在此範例中，我們會搜尋*myGalleryRG*資源群組中名為*myGallery*的圖庫映射定義。

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

列出圖庫中的映射版本，使用[az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list)來尋找您想要複製到新圖庫中的映射版本。 在此範例中，我們會尋找屬於*myImageDefinition*映射定義一部分的所有映射版本。

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

一旦您擁有所需的所有資訊，您就可以使用[az sig image 版本 show](/cli/azure/sig/image-version#az-sig-image-version-show)來取得來源映射版本的識別碼。

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>建立映射定義 

您需要建立符合來源映射版本映射定義的映射定義。 您可以在新的資源庫中，使用[az sig image-definition show](/cli/azure/sig/image-definition#az-sig-image-definition-show)來查看重新建立映射定義所需的所有資訊。

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

輸出會如下所示：

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Linux",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

使用上述輸出中的資訊，在新的資源庫中建立新的映射定義。


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>建立映像版本

使用[az image 圖庫 create-image 版本](/cli/azure/sig/image-version#az-sig-image-version-create)來建立版本。 您必須傳入受控映像的識別碼，以作為建立映像版本的基準。 您可以使用 [az image list](/cli/azure/image?view#az-image-list) 來取得資源群組中映像的相關資訊。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此範例中，我們的映射版本為*1.0.0* ，而我們將在*美國中南部*區域建立1個複本，並使用區域冗余儲存體在*美國東部*區域中建立1個複本。


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> 您必須等候映像版本完全完成建立和複寫後，才能使用相同的受控映像來建立另一個映像版本。
>
> 建立映像版本時，您也可以藉由新增 `--storage-account-type  premium_lrs`，將映像儲存在「進階」儲存體，或新增 `--storage-account-type  standard_zrs`，將映像儲存在[區域備援儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)。
>

## <a name="next-steps"></a>後續步驟

從[一般化](vm-generalized-image-version-cli.md)或[特製](vm-specialized-image-version-cli.md)化映射版本建立 VM。

此外，試用[Azure 映射產生器 (預覽) ](./linux/image-builder-overview.md)可以協助將映射版本的建立自動化，甚至可以用它來更新並[從現有的映射版本建立新的映射版本](./linux/image-builder-gallery-update-image-version.md)。 

如需有關如何提供採購方案資訊的詳細資訊，請參閱[在建立映射時提供 Azure Marketplace 購買方案資訊](marketplace-images.md)。