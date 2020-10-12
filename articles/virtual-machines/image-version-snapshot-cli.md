---
title: CLI-從共用映射庫中的快照集或受控磁片建立映射
description: 瞭解如何使用 Azure CLI，從共用映射庫中的快照集或受控磁片建立映射。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e694630d8bcd7879d9405152c4141fb6e5bad4e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297088"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>使用 Azure CLI 在共用映射庫中建立受控磁片或快照集的映射

如果您想要將現有的快照集或受控磁片遷移到共用映射庫，您可以直接從受控磁片或快照集建立共用映射庫映射。 測試新映射之後，您可以刪除來源受控磁片或快照集。 您也可以使用 [Azure PowerShell](image-version-snapshot-powershell.md)，從共用映射庫中的受控磁片或快照集建立映射。

映射庫中的影像有兩個元件，我們將在此範例中建立這些元件：
- **映射定義**會攜帶映射的相關資訊，以及使用它的需求。 這包括映射是 Windows 或 Linux、特製化或一般化、版本資訊，以及最小和最大記憶體需求。 這是映像類型的定義。 
- **映射版本**是用來在使用共用映射庫時建立 VM 的版本。 您可以視需要為環境準備多個映像版本。 當您建立 VM 時，映射版本會用來建立 VM 的新磁片。 映像版本可以使用多次。


## <a name="before-you-begin"></a>開始之前

若要完成這篇文章，您必須有快照集或受控磁片。 

如果您想要包含資料磁片，資料磁片大小不能超過 1 TB。

當您完成本文時，請視需要取代資源名稱。

## <a name="find-the-snapshot-or-managed-disk"></a>尋找快照集或受控磁片 

您可以使用 [az snapshot list](/cli/azure/snapshot#az-snapshot-list)查看資源群組中可用的快照集清單。 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

您也可以使用受控磁片，而不是快照集。 若要取得受控磁片，請使用 [az disk list](/cli/azure/disk#az-disk-list)。 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

當您擁有快照集或受控磁片的識別碼，並將它指派給 `$source` 稍後要使用的變數。

您可以使用相同的程式來取得您想要包含在映射中的任何資料磁片。 將這些變數指派給變數，稍後當您建立映射版本時，請使用這些變數。


## <a name="find-the-gallery"></a>尋找資源庫

您將需要映射庫的相關資訊，才能建立映射定義。

使用 [az sig list](/cli/azure/sig#az-sig-list)列出可用映射資源庫的相關資訊。 請記下資源庫的資源群組，以供稍後使用。

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>建立映像定義

映像定義會建立映像的邏輯群組。 它們可用來管理影像的相關資訊。 映像定義名稱可以由大寫或小寫字母、數字、點、虛線和句點組成。 

進行映射定義時，請確定已有所有正確的資訊。 在此範例中，我們假設快照集或受控磁片來自使用中的 VM，但尚未進行一般化。 如果受控磁片或快照集是在執行 Windows、 [waagent](https://github.com/Azure/WALinuxAgent)或 Linux) 的 Sysprep 之後 (， `-deprovision` 請將 `-deprovision+user` 變更 `-OsState` 為 `generalized` 。 

若要深入了解您可以為映像定義指定哪些值，請參閱[映像定義](./linux/shared-image-galleries.md#image-definitions)。

使用 [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)，在資源庫中建立映像定義。

在此範例中，映像定義會命名為 myImageDefinition  ，而且適用於[特製化](./linux/shared-image-galleries.md#generalized-and-specialized-images)的 Linux OS 映像。 若要使用 Windows OS 建立映像的定義，請使用 `--os-type Windows`。 

在此範例中，資源庫名為 *>mygalleryrg*，它位於 *myGalleryRG* 資源群組中，而映射定義名稱將會是 *mImageDefinition*。

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>建立映像版本

使用 [az 映射庫建立](/cli/azure/sig/image-version#az-sig-image-version-create)映射版本建立映射版本。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此範例中，我們的映射版本為 *1.0.0* ，我們將在 *美國中南部* 區域中建立1個複本，並使用區域多餘的儲存體在 *美國東部 2* 區域中建立1個複本。 選擇要複寫的目的地區域時，請記住，您也必須將受控磁片或快照集的 *來源* 區域納入為複寫目標。

在參數中傳遞快照集或受控磁片的識別碼 `--os-snapshot` 。


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

如果您想要在映射中包含資料磁片，則必須同時包含 `--data-snapshot-luns` 設定為 LUN 編號的參數，以及 `--data-snapshots` 設定為數據磁片或快照集的識別碼。

> [!NOTE]
> 您必須等候映像版本完全完成建立和複寫後，才能使用相同的受控映像來建立另一個映像版本。
>
> 您也可以在建立映射版本時新增，以將所有的映射版本複本儲存在 [區域重複的儲存體](../storage/common/storage-redundancy.md) 中 `--storage-account-type standard_zrs` 。
>

## <a name="next-steps"></a>後續步驟

從 [特製化映射版本](vm-specialized-image-version-cli.md)建立 VM。

如需有關如何提供採購方案資訊的詳細資訊，請參閱 [在建立映射時提供 Azure Marketplace 採購方案資訊](marketplace-images.md)。
