---
title: 教學課程 - 透過 Azure CLI 使用擴展集中的自訂 VM 映像
description: 了解如何使用 Azure CLI 來建立可用於部署虛擬機器擴展集的自訂 VM 映像
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 62cf7c979be83454ae2433befcdbf4f5d8e5524f
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516538"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>教學課程：使用 Azure CLI 建立及使用虛擬機器擴展集的自訂映像
當您建立擴展集時，您會指定部署 VM 執行個體時所要使用的映像。 若要減少部署 VM 執行個體後的工作數量，您可以使用自訂的 VM 映像。 此自訂 VM 映像包括任何必要的應用程式安裝或組態。 在擴展集中建立的任何 VM 執行個體都會使用自訂 VM 映像，並已可以處理您的應用程式流量。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立共用映像庫
> * 建立特製化映像定義
> * 建立映像版本
> * 從特製化映像建立擴展集
> * 共用映像庫


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 2.4.0 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="overview"></a>概觀

[共用映像資源庫](shared-image-galleries.md)可簡化跨組織共用自訂映像。 自訂映像類似 Marketplace 映像，但您要自行建立它們。 自訂映像可用於啟動程序設定，例如，預先載入應用程式、應用程式設定和其他 OS 設定。 

共用映像庫可讓您與其他人共用您的自訂 VM 映像。 選擇您要共用的映像、您要開放使用的區域，以及您要共用的對象。 

## <a name="create-and-configure-a-source-vm"></a>建立並設定來源 VM

首先，請使用 [az group create](/cli/azure/group) 來建立資源群組，然後使用 [az vm create](/cli/azure/vm)來建立 VM。 接著，此 VM 會用來當作映像的來源。 下列範例會在名為 myResourceGroup  的資源群組中建立名為 myVM  的 VM：

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> VM 的 **識別碼** 會顯示在 [az vm create](/cli/azure/vm) 命令的輸出中。 將此識別碼複製到安全的位置，以便您稍後在本教學課程中使用。

VM 的公用 IP 位址也會顯示在 [az vm create](/cli/azure/vm)命令的輸出中。 透過 SSH 連線至 VM 的公用 IP 位址，如下所示：

```console
ssh azureuser@<publicIpAddress>
```

若要自訂您的 VM，請安裝基本 Web 伺服器。 部署擴展集中的 VM 執行個體時，其中就會有執行 Web 應用程式所需的所有必要套件。 請依下列方式使用 `apt-get` 安裝 NGINX  ：

```bash
sudo apt-get install -y nginx
```

當您完成時，請輸入 `exit` 來中斷 SSH 連線。

## <a name="create-an-image-gallery"></a>建立映像資源庫 

映像資源庫是用於啟用映像共用的主要資源。 

資源庫名稱允許的字元為大寫或小寫字母、數字、點和句點。 資源庫名稱不能包含連字號。   資源庫名稱在您的訂用帳戶內必須是唯一的。 

使用 [az sig create](/cli/azure/sig#az-sig-create) 建立映像資源庫。 下列範例會在「美國東部」  建立名為 myGalleryRG  的資源群組，以及名為 myGallery  的資源庫。

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>建立映像定義

映像定義會建立映像的邏輯群組。 並且可用來管理在其中建立的映像版本相關資訊。 

映像定義名稱可以由大寫或小寫字母、數字、點、虛線和句點組成。 

請確定您的映像定義是正確的類型。 如果您已一般化 VM (使用適用於 Windows 的 Sysprep，或適用於 Linux 的 waagent -deprovision)，則應該使用 `--os-state generalized` 建立一般化映像定義。 如果您想要在不移除現有使用者帳戶的情況下使用 VM，請使用 `--os-state specialized` 建立特製化映像定義。

若要深入了解您可以為映像定義指定哪些值，請參閱[映像定義](../virtual-machines/linux/shared-image-galleries.md#image-definitions)。

使用 [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)，在資源庫中建立映像定義。

在此範例中，映像定義會命名為 myImageDefinition  ，而且適用於[特製化](../virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images)的 Linux OS 映像。 若要使用 Windows OS 建立映像的定義，請使用 `--os-type Windows`。 

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

> [!IMPORTANT]
> 映像定義的 **識別碼** 會顯示在命令的輸出中。 將此識別碼複製到安全的位置，以便您稍後在本教學課程中使用。


## <a name="create-the-image-version"></a>建立映像版本

使用 [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create)，從 VM 建立映像版本。  

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此範例中，我們的映像版本是 1.0.0  ，而我們即將在「美國中南部」  區域中建立 1 個複本，以及在「美國東部 2」  區域中建立 1 個複本。 複寫區域必須包含來源 VM 所在的區域。

將此範例中 `--managed-image` 的值取代為上一個步驟中的 VM 識別碼。

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> 您必須等候映像版本完全完成建立和複寫後，才能使用相同的受控映像來建立另一個映像版本。
>
> 建立映像版本時，您也可以藉由新增 `--storage-account-type  premium_lrs`，將映像儲存在「進階」儲存體，或新增 `--storage-account-type  standard_zrs`，將映像儲存在[區域備援儲存體](../storage/common/storage-redundancy.md)。
>




## <a name="create-a-scale-set-from-the-image"></a>從映像建立擴展集
使用 [`az vmss create`](/cli/azure/vmss#az-vmss-create) 從特製化映像建立擴展集。 

使用 [`az vmss create`](/cli/azure/vmss#az-vmss-create) 建立擴展集，並使用 --specialized 參數來指出映像是特製化映像。 

使用 `--image` 的映像定義識別碼，從可用的最新擴展集執行個體版本建立 VM。 您也可以藉由提供 `--image` 的映像版本識別碼，從特定版本建立擴展集執行個體。 

建立名為 myScaleSet  的擴展集，我們稍早建立的最新版 myImageDefinition  映像。

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

建立及設定所有擴展集資源和 VM 需要幾分鐘的時間。


## <a name="test-your-scale-set"></a>測試您的擴展集
若要允許流量觸達擴展集，並確認 Web 伺服器正常運作，請使用 [az network lb rule create](/cli/azure/network/lb/rule) 建立負載平衡器規則。 下列範例會建立名為 myLoadBalancerRuleWeb  的規則，其會允許 TCP  連接埠 80  上的流量：

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

若要查看作用中的擴展集，可使用 [az network public-ip show](/cli/azure/network/public-ip) 取得負載平衡器的公用 IP 位址。 下列範例會取得建立為擴展集一部分的 myScaleSetLBPublicIP  IP 位址︰

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

在您的 Web 瀏覽器中輸入公用 IP 位址。 預設 NGINX 網頁即會顯示，如下列範例所示：

![從自訂 VM 映像執行的 Nginx](media/tutorial-use-custom-image-cli/default-nginx-website.png)



## <a name="share-the-gallery"></a>共用資源庫

您可以使用 Azure 角色型存取控制 (Azure RBAC) 來跨訂用帳戶共用映像。 您可以在資源庫、映像定義或映像版本上共用映像。 具有映像版本 (甚至可跨訂用帳戶) 讀取權限的使用者，都能夠使用映像版本來部署 VM。

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

如需如何使用 Azure RBAC 來共用資源的詳細資訊，請參閱[使用 Azure CLI 新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-cli.md)。


## <a name="clean-up-resources"></a>清除資源
若要移除您的擴展集與其他資源，請使用 [az group delete](/cli/azure/group) 刪除資源群組及其所有資源。 `--no-wait` 參數不會等待作業完成，就會將控制項傳回給提示字元。 `--yes` 參數會確認您想要刪除資源，而不另外對您提示將要進行此作業。

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何使用 Azure CLI 來建立及使用擴展集的自訂 VM 映像：

> [!div class="checklist"]
> * 建立共用映像庫
> * 建立特製化映像定義
> * 建立映像版本
> * 從特製化映像建立擴展集
> * 共用映像庫

前往下一個教學課程，以了解如何將應用程式部署至擴展集。

> [!div class="nextstepaction"]
> [將應用程式部署至擴展集](tutorial-install-apps-cli.md)
