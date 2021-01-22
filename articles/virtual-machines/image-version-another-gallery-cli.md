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
ms.openlocfilehash: 753e7503612502cde12f821ea331625406daa7a8
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675972"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>使用 Azure CLI 從另一個資源庫複製映射

如果您的組織中有多個資源庫，您也可以從儲存在其他資源庫中的現有映射版本建立映射版本。 例如，您可能會有開發和測試資源庫，可用於建立及測試新的映射。 當他們準備好用於生產環境時，您可以使用此範例將它們複製到生產資源庫。 您也可以使用 [Azure PowerShell](image-version-another-gallery-powershell.md)，從另一個資源庫中的映射建立映射。



## <a name="before-you-begin"></a>開始之前

若要完成這篇文章，您必須擁有現有的來源資源庫、映射定義和映射版本。 您也應該有目的地資源庫。 

來源映射版本必須複寫到目的地資源庫所在的區域。 

當您完成本文時，請視需要取代資源名稱。



## <a name="get-information-from-the-source-gallery"></a>從來源資源庫取得資訊

您將需要來源映射定義中的資訊，才能在新的資源庫中建立它的複本。

使用 [az sig list](/cli/azure/sig#az-sig-list) 列出可用映射資源庫的相關資訊，以尋找來源資源庫的相關資訊。

```azurecli-interactive 
az sig list -o table
```

使用 [az sig 映射定義清單](/cli/azure/sig/image-definition#az-sig-image-definition-list)，列出資源庫中的映射定義。 在此範例中，我們會在 *myGalleryRG* 資源群組中搜尋名為 *>mygalleryrg* 的資源庫中的映射定義。

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

在資源庫中列出映射的版本，並使用 [az sig image-version 清單](/cli/azure/sig/image-version#az-sig-image-version-list) 來尋找您想要複製到新資源庫的映射版本。 在此範例中，我們要尋找屬於 *myImageDefinition* 映射定義一部分的所有映射版本。

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

一旦擁有您需要的所有資訊之後，您就可以使用 [az sig image version show](/cli/azure/sig/image-version#az-sig-image-version-show)來取得來源映射版本的識別碼。

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>建立映射定義 

您必須建立與來源映射版本的映射定義相符的映射定義。 您可以在新的資源庫中，看到使用 [az sig 映射定義顯示](/cli/azure/sig/image-definition#az-sig-image-definition-show)重新建立映射定義所需的所有資訊。

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

輸出會看起來類似這樣：

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

使用 [az 映射庫建立版本建立映射版本](/cli/azure/sig/image-version#az-sig-image-version-create)。 您必須傳入受控映像的識別碼，以作為建立映像版本的基準。 您可以使用 [az image list](/cli/azure/image?view#az-image-list) 來取得資源群組中映像的相關資訊。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此範例中，我們的映射版本為 *1.0.0* ，我們將在 *美國中南部* 區域中建立1個複本，並使用區域多餘的儲存體在 *美國東部* 區域建立1個複本。


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
> 您也可以在 `--storage-account-type  premium_lrs` 建立映射版本時新增或 [區域多餘的儲存體](../storage/common/storage-redundancy.md) ，藉以將映射儲存在 Premium 儲存體中 `--storage-account-type  standard_zrs` 。
>

## <a name="next-steps"></a>下一步

從 [一般化](vm-generalized-image-version-cli.md) 或 [特製](vm-specialized-image-version-cli.md) 化映射版本建立 VM。

此外，試用 [Azure 映射產生器 (preview) ](./image-builder-overview.md) 可協助自動建立映射版本，您甚至可以使用它來更新和 [建立現有映射版本的新映射版本](./linux/image-builder-gallery-update-image-version.md)。 

如需有關如何提供採購方案資訊的詳細資訊，請參閱 [在建立映射時提供 Azure Marketplace 採購方案資訊](marketplace-images.md)。