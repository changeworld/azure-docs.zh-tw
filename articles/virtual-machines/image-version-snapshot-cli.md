---
title: CLI-從共用映射庫中的快照集或 VHD 建立映射
description: 瞭解如何使用 Azure CLI，從共用映射庫中的快照集或 VHD 建立映射。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 585d3729a886f3a01dff6dcd9afdab63669c05b5
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225066"
---
# <a name="create-an-image-from-a-vhd-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>使用 Azure CLI，從共用映射庫中的 VHD 或快照集建立映射

如果您有想要遷移到共用映射資源庫的現有快照集或 VHD，您可以直接從 VHD 或快照集建立共用映射庫映射。 測試新的映射之後，您就可以刪除來源 VHD 或快照集。 您也可以使用[Azure PowerShell](image-version-snapshot-powershell.md)，從共用映射庫中的 VHD 或快照集建立映射。

映射庫中的映射有兩個元件，我們將在此範例中建立：
- **映射定義**會攜帶影像的相關資訊，以及使用它的需求。 這包括映射是 Windows 或 Linux、特製化還是一般化、版本資訊，以及最小和最大記憶體需求。 這是映像類型的定義。 
- 使用共用映射資源庫時，**映射版本**是用來建立 VM 的。 您可以視需要為環境準備多個映像版本。 當您建立 VM 時，映射版本會用來為 VM 建立新的磁片。 映像版本可以使用多次。


## <a name="before-you-begin"></a>開始之前

若要完成本文，您必須擁有快照集或 VHD。 

如果您想要包含資料磁片，資料磁片大小不能超過 1 TB。

當您完成本文時，請視需要取代資源名稱。

## <a name="find-the-snapshot-or-vhd"></a>尋找快照集或 VHD 

您可以使用[az snapshot list](/cli/azure/snapshot#az-snapshot-list)來查看資源群組中可用的快照集清單。 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

您也可以使用 VHD，而不是快照集。 若要取得 VHD，請使用[az disk list](/cli/azure/disk#az-disk-list)。 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

一旦您擁有快照集或 VHD 的識別碼，並將它指派給名為的變數， `$source` 以便稍後使用。

您可以使用相同的程式來取得您想要包含在映射中的任何資料磁片。 將它們指派給變數，然後在您建立映射版本之後使用這些變數。


## <a name="find-the-gallery"></a>尋找資源庫

您將需要映射庫的相關資訊，才能建立映射定義。

使用[az sig list](/cli/azure/sig#az-sig-list)列出可用映射資源庫的相關資訊。 請記下資源庫名稱，供圖庫用來供稍後使用。

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>建立映像定義

映像定義會建立映像的邏輯群組。 它們可用來管理影像的相關資訊。 映像定義名稱可以由大寫或小寫字母、數字、點、虛線和句點組成。 

建立映射定義時，請確定具有所有正確的資訊。 在此範例中，我們假設快照集或 VHD 來自于使用中的 VM，而且尚未一般化。 如果 VHD 或快照集是在執行適用于 Windows 或[waagent](https://github.com/Azure/WALinuxAgent)的 Sysprep 或 Linux) 之後， (建立的， `-deprovision` `-deprovision+user` 請將 `-OsState` 變更為 `generalized` 。 

若要深入了解您可以為映像定義指定哪些值，請參閱[映像定義](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)。

使用 [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)，在資源庫中建立映像定義。

在此範例中，映像定義會命名為 myImageDefinition  ，而且適用於[特製化](./linux/shared-image-galleries.md#generalized-and-specialized-images)的 Linux OS 映像。 若要使用 Windows OS 建立映像的定義，請使用 `--os-type Windows`。 

在此範例中，資源庫名為*myGallery*，它位於*myGalleryRG*資源群組中，而映射定義名稱將會是*mImageDefinition*。

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

使用[az image 圖庫 create-映射版本](/cli/azure/sig/image-version#az-sig-image-version-create)來建立映射版本。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此範例中，我們的映射版本為*1.0.0* ，而我們將在*美國中南部*區域建立1個複本，並在*美國東部 2*區域中使用區域多餘的儲存體來建立1個複本。 選擇複寫的目的地區域時，請記住，您也必須包含 VHD 或快照的*來源*區域，做為複寫的目標。

在參數中傳遞快照集或 VHD 的識別碼 `--os-snapshot` 。


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

如果您想要在映射中包含資料磁片，則必須將 `--data-snapshot-luns` 參數設定為 LUN 編號，並將 `--data-snapshots` 設定為數據磁片或快照集的識別碼。

> [!NOTE]
> 您必須等候映像版本完全完成建立和複寫後，才能使用相同的受控映像來建立另一個映像版本。
>
> 您也可以在建立映射版本時新增，以將所有映射版本複本儲存在[區域冗余儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)中 `--storage-account-type standard_zrs` 。
>

## <a name="next-steps"></a>後續步驟

從[特製化映射版本](vm-specialized-image-version-cli.md)建立 VM。

如需有關如何提供採購方案資訊的詳細資訊，請參閱[在建立映射時提供 Azure Marketplace 購買方案資訊](marketplace-images.md)。