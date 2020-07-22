---
title: 教學課程 - 使用 Azure CLI 建立自訂的 VM 映像
description: 在本教學課程中，您會了解如何使用 Azure CLI 在 Azure 中建立自訂虛擬機器映像
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc
ms.reviewer: akjosh
ms.openlocfilehash: bd4da23ed3f4b2dc92652493c77efbcbc0542066
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86510118"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>教學課程：使用 Azure CLI 建立 Azure VM 的自訂映像

自訂映像類似 Marketplace 映像，但您要自行建立它們。 自訂映像可用於啟動程序設定，例如，預先載入應用程式、應用程式設定和其他 OS 設定。 在本教學課程中，您將建立自己的 Azure 虛擬機器自訂映像。 您會了解如何：

> [!div class="checklist"]
> * 建立共用映像庫
> * 建立映像定義
> * 建立映像版本
> * 從映像建立 VM 
> * 共用映像庫


本教學課程會使用 [Azure Cloud Shell](../../cloud-shell/overview.md) 內的 CLI，這會不斷更新至最新版本。 若要開啟 Cloud Shell，請選取任何程式碼區塊頂端的 [試試看]。

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.4.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="overview"></a>概觀

[共用映像資源庫](shared-image-galleries.md)可簡化跨組織共用自訂映像。 自訂映像類似 Marketplace 映像，但您要自行建立它們。 自訂映像可用於啟動程序設定，例如，預先載入應用程式、應用程式設定和其他 OS 設定。 

共用映像庫可讓您與其他人共用您的自訂 VM 映像。 選擇您要共用的映像、您要開放使用的區域，以及您要共用的對象。 

共用映像庫具有多個資源類型：

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>開始之前

下列步驟將詳細說明如何將現有 VM 轉換成可重複使用的自訂映像，以便讓您用來建立新的 VM 執行個體。

若要完成本教學課程中的範例，您目前必須具有虛擬機器。 如有需要，您可以查看 [CLI 快速入門](quick-create-cli.md)來建立要用於本教學課程的 VM。 逐步完成教學課程之後，請視需要取代資源名稱。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看] 即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製] 即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

## <a name="create-an-image-gallery"></a>建立映像資源庫 

映像資源庫是用於啟用映像共用的主要資源。 

資源庫名稱允許的字元為大寫或小寫字母、數字、點和句點。 資源庫名稱不能包含連字號。   資源庫名稱在您的訂用帳戶內必須是唯一的。 

使用 [az sig create](/cli/azure/sig#az-sig-create) 建立映像資源庫。 下列範例會在「美國東部」建立名為 myGalleryRG 的資源群組，以及名為 myGallery 的資源庫。

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>取得 VM 的相關資訊

您可以使用 [az vm list](/cli/azure/vm#az-vm-list)查看可用的 VM 清單。 

```azurecli-interactive
az vm list --output table
```

當您知道 VM 名稱及其所在的資源群組後，請使用 [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view)來取得 VM 的識別碼。 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

複製 VM 的識別碼，以供稍後使用。

## <a name="create-an-image-definition"></a>建立映像定義

映像定義會建立映像的邏輯群組。 並且可用來管理在其中建立的映像版本相關資訊。 

映像定義名稱可以由大寫或小寫字母、數字、點、虛線和句點組成。 

若要深入了解您可以為映像定義指定哪些值，請參閱[映像定義](./shared-image-galleries.md#image-definitions)。

使用 [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)，在資源庫中建立映像定義。 

在此範例中，映像定義會命名為 myImageDefinition，而且適用於[特製化](./shared-image-galleries.md#generalized-and-specialized-images)的 Linux OS 映像。 

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

從輸出複製映像定義的識別碼，以供稍後使用。

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
> 建立映像版本時，您也可以藉由新增 `--storage-account-type  premium_lrs`，將映像儲存在「進階」儲存體，或新增 `--storage-account-type  standard_zrs`，將映像儲存在[區域備援儲存體](../../storage/common/storage-redundancy.md)。
>

 
## <a name="create-the-vm"></a>建立 VM

使用 [az vm create](/cli/azure/vm#az-vm-create) 建立 VM，並使用 --specialized 參數來指出映像是特製化映像。 

使用 `--image` 的映像定義識別碼，從可用的最新映像版本建立 VM。 您也可以藉由提供 `--image` 的映像版本識別碼，從特定版本建立 VM。 

在此範例中，我們會從最新版本的 myImageDefinition 映像建立 VM。

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>共用資源庫

您可以使用角色型存取控制 (RBAC) 來跨訂用帳戶共用映像。 您可以在資源庫、映像定義或映像版本層級上共用映像。 具有映像版本 (甚至可跨訂用帳戶) 讀取權限的使用者，都能夠使用映像版本來部署 VM。

我們建議您在資源庫層級上與其他使用者共用。 若要取得資源庫的物件識別碼，請使用 [az sig show](/cli/azure/sig#az-sig-show)。

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

使用物件識別碼作為範圍，以及使用電子郵件地址和 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) 對使用者授與共用映像庫的存取權。 將 `<email-address>` 和 `<gallery iD>` 取代為您的個人資訊。

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

如需使用 RBAC 共用資源的詳細資訊，請參閱[使用 RBAC 和 Azure CLI 管理存取](../../role-based-access-control/role-assignments-cli.md)。

## <a name="azure-image-builder"></a>Azure Image Builder

Azure 也提供以 Packer 為基礎的服務：[Azure VM Image Builder](./image-builder-overview.md)。 只要在範本中描述您的自訂，其就會處理映像建立作業。 

## <a name="next-steps"></a>後續步驟

您在本教學課程中建立了自訂 VM 映像。 您已了解如何︰

> [!div class="checklist"]
> * 建立共用映像庫
> * 建立映像定義
> * 建立映像版本
> * 從映像建立 VM 
> * 共用映像庫

請前進到下一個教學課程，了解高可用性的虛擬機器。

> [!div class="nextstepaction"]
> [建立高可用性 VM](tutorial-availability-sets.md)
