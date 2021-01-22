---
title: 從 VM 建立映像
description: 瞭解如何從 Azure 中的 VM 在共用映射庫中建立映射。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8aa2623722b5e75480e68324d76e4a1493501df
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685135"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>使用 Azure CLI 從 Azure 中的 VM 建立映射版本

如果您有想要用來建立多個相同 Vm 的現有 VM，您可以使用該 VM，利用 Azure CLI 在共用映射庫中建立映射。 您也可以使用 [Azure PowerShell](image-version-vm-powershell.md)從 VM 建立映射。

**映射版本** 是您在使用共用映射庫時，用來建立 VM 的版本。 您可以視需要為環境準備多個映像版本。 當您使用映射版本建立 VM 時，映射版本會用來建立新 VM 的磁片。 映像版本可以使用多次。


## <a name="before-you-begin"></a>開始之前

若要完成這篇文章，您必須有現有的共用映射庫。 

您也必須在 Azure 中有現有的 VM，與您的資源庫位於相同的區域中。 

如果 VM 已連接資料磁片，資料磁片大小不能超過 1 TB。

當您完成本文時，請視需要取代資源名稱。

## <a name="get-information-about-the-vm"></a>取得 VM 的相關資訊

您可以使用 [az vm list](/cli/azure/vm#az-vm-list)查看可用的 VM 清單。 

```azurecli-interactive
az vm list --output table
```

當您知道 VM 名稱及其所在的資源群組後，請使用 [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view)來取得 VM 的識別碼。 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>建立映像定義

映像定義會建立映像的邏輯群組。 並且可用來管理在其中建立的映像版本相關資訊。 

映像定義名稱可以由大寫或小寫字母、數字、點、虛線和句點組成。 

請確定您的映像定義是正確的類型。 如果您已一般化 VM (使用適用於 Windows 的 Sysprep，或適用於 Linux 的 waagent -deprovision)，則應該使用 `--os-state generalized` 建立一般化映像定義。 如果您想要在不移除現有使用者帳戶的情況下使用 VM，請使用 `--os-state specialized` 建立特製化映像定義。

若要深入了解您可以為映像定義指定哪些值，請參閱[映像定義](./shared-image-galleries.md#image-definitions)。

使用 [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)，在資源庫中建立映像定義。

在此範例中，映像定義會命名為 myImageDefinition  ，而且適用於[特製化](./shared-image-galleries.md#generalized-and-specialized-images)的 Linux OS 映像。 若要使用 Windows OS 建立映像的定義，請使用 `--os-type Windows`。 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>建立映像版本

使用 [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create)，從 VM 建立映像版本。  

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此範例中，我們的映像版本是 1.0.0，而我們將使用區域備援儲存體，在「美國中西部」區域中建立 2 個複本、在「美國中南部」區域中建立 1 個複本，以及在「美國東部 2」區域中建立 1 個複本。 複寫區域必須包含來源 VM 所在的區域。

將此範例中 `--managed-image` 的值取代為上一個步驟中的 VM 識別碼。

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> 您必須等候映像版本完全完成建立和複寫後，才能使用相同的受控映像來建立另一個映像版本。
>
> 您也可以在 `--storage-account-type  premium_lrs` 建立映射版本時新增或 [區域多餘的儲存體](../storage/common/storage-redundancy.md) ，藉以將映射儲存在 Premium 儲存體中 `--storage-account-type  standard_zrs` 。
>

## <a name="next-steps"></a>下一步

使用 Azure CLI 從 [一般化映射](vm-generalized-image-version-cli.md) 建立 VM。

如需有關如何提供採購方案資訊的詳細資訊，請參閱 [在建立映射時提供 Azure Marketplace 採購方案資訊](marketplace-images.md)。