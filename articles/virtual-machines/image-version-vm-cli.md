---
title: 從 VM 建立映像
description: 瞭解如何從 Azure 中的 VM，在共用映射資源庫中建立映射。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f53a6b63c744b0e3e41f7ad22270cd842da57674
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796573"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>使用 Azure CLI 在 Azure 中的 VM 建立映射版本

如果您有想要用來建立多個相同 Vm 的現有 VM，您可以使用該 VM，在使用 Azure CLI 的共用映射資源庫中建立映射。 您也可以使用[Azure PowerShell](image-version-vm-powershell.md)從 VM 建立映射。

**映射版本**是您在使用共用映射資源庫時用來建立 VM 的內容。 您可以視需要為環境準備多個映像版本。 當您使用映射版本來建立 VM 時，會使用映射版本來建立新 VM 的磁片。 映像版本可以使用多次。


## <a name="before-you-begin"></a>開始之前

若要完成本文，您必須擁有現有的共用映射資源庫。 

您也必須在 Azure 中有現有的 VM，位於與資源庫相同的區域中。 

如果 VM 已連接資料磁片，資料磁片大小不能超過 1 TB。

當您完成本文時，請視需要取代資源名稱。

## <a name="get-information-about-the-vm"></a>取得 VM 的相關資訊

您可以使用[az vm list](/cli/azure/vm#az-vm-list)來查看可用的 vm 清單。 

```azurecli-interactive
az vm list --output table
```

一旦您知道 VM 名稱及其所在的資源群組，請使用[az vm get-view](/cli/azure/vm#az-vm-get-instance-view)取得 VM 的識別碼。 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>建立映像定義

映射定義會建立影像的邏輯群組。 它們可用來管理在其中建立之映射版本的相關資訊。 

影像定義名稱可以由大寫或小寫字母、數位、點、虛線和句號組成。 

請確定您的映射定義是正確的類型。 如果您已一般化 VM （使用適用于 Windows 的 Sysprep，或 waagent-取消布建 Linux），則應該使用`--os-state generalized`來建立一般化映射定義。 如果您想要在不移除現有使用者帳戶的情況下使用 VM，請使用`--os-state specialized`建立特製化映射定義。

如需您可以為映射定義指定之值的詳細資訊，請參閱[影像定義](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)。

使用[az sig image 定義 create](/cli/azure/sig/image-definition#az-sig-image-definition-create)，在資源庫中建立映射定義。

在此範例中，映射定義名為*myImageDefinition*，適用于[特定](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images)的 Linux OS 映射。 若要使用 Windows 作業系統來建立映射的定義，請`--os-type Windows`使用。 

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


## <a name="create-the-image-version"></a>建立映射版本

使用[az image 圖庫建立](/cli/azure/sig/image-version#az-sig-image-version-create)映射版本，從虛擬機器建立映射版本。  

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式： *MajorVersion*。*MinorVersion*。*修補程式*。

在此範例中，我們的映射版本為*1.0.0* ，而我們將在*美國中西部*區域中建立2個複本，在*美國中南部*區域使用1個複本，而在*美國東部 2*區域中建立1個複本，並使用區域多餘的儲存體。 複寫區域必須包含來源 VM 所在的區域。

將此範例`--managed-image`中的值取代為上一個步驟中 VM 的識別碼。

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
> 您必須等候映射版本完全完成建立和複寫，才能使用相同的受控映射來建立另一個映射版本。
>
> 您也可以在建立映射版本時新增`--storage-account-type  premium_lrs` `--storage-account-type  standard_zrs` ，將您的映射儲存在 Premiun 儲存體中，其方式是新增或[區域多餘的儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)。
>

## <a name="next-steps"></a>後續步驟

使用 Azure CLI，從[一般化映射](vm-generalized-image-version-cli.md)建立 VM。
