---
title: 使用 Azure CLI 擷取 Linux VM 的受控映像
description: 使用 Azure CLI 擷取要用於大量部署的 Azure VM 受控映像。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 10/08/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.openlocfilehash: 53fb11216e65ebead43c02a7153d937c37b841a0
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681055"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>如何建立虛擬機器或 VHD 的受控映像

若要建立虛擬機器 (VM) 的多個複本以在 Azure 中用於開發和測試，請擷取 VM 或 OS VHD 的受控映像。 若要大規模建立、儲存和共用映像，請參閱[共用映像資源庫](../shared-images-cli.md)。

一個受控映像最多可支援 20 個同時部署。 嘗試從相同的受控映像同時建立 20 個以上的虛擬機器時，因為單一 VHD 的儲存體效能限制之故，可能會導致佈建逾時。 若要同時建立 20 個以上的 VM，請使用[共用映像資源庫](../shared-image-galleries.md)中設定為每 20 個並行虛擬機器部署 1 個複本的映像。

若要建立受控映像，您必須移除個人帳戶資訊。 在下列步驟中，您將取消佈建現有的 VM、將它解除配置，然後建立映像。 您可以使用此映像，在您訂用帳戶的任何資源群組中建立 VM。

若要建立一份現有 Linux VM 的副本以進行備份或偵錯，或是從內部部署 VM 上傳特定的 Linux VHD，請參閱[從自訂的磁碟映像上傳及建立 Linux VM](upload-vhd.md)。  

您可以使用 **Azure VM Image Builder (公開預覽)** 服務來建立自訂映像，而不需要學習任何工具或設定組建管道，只要提供映像設定，映像產生器就會建立映像。 如需詳細資訊，請參閱[開始使用 Azure VM Image Builder](../image-builder-overview.md)。

建立映像之前，您需要下列項目：

* 在 Resource Manager 部署模型中使用受控磁碟建立的 Azure VM。 如果您尚未建立 Linux VM，可以使用[入口網站](quick-create-portal.md)、[Azure CLI](quick-create-cli.md) 或 [Resource Manager 範本](create-ssh-secured-vm-from-template.md)。 視需要設定 VM。 例如，[新增資料磁碟](add-disk.md)、套用更新，並安裝應用程式。 

* 安裝最新的 [Azure CLI](/cli/azure/install-az-cli2)，並使用 [az login](/cli/azure/reference-index#az-login) 來登入 Azure 帳戶。

## <a name="prefer-a-tutorial-instead"></a>偏好使用教學課程？

如需本文的簡化版本，以進行測試、評估或深入了解 Azure 中的 VM，請參閱[使用 CLI 建立 Azure VM 的自訂映像](tutorial-custom-images.md)。  否則，請繼續參閱這裡以瞭解整體情況。


## <a name="step-1-deprovision-the-vm"></a>步驟 1:取消佈建 VM
首先使用 Azure VM 代理程式來取消佈建 VM，以將電腦特定的檔案和資料刪除。 在來源 Linux VM 上使用 `waagent` 命令搭配 `-deprovision+user` 參數。 如需詳細資訊，請參閱 [Azure Linux 代理程式使用者指南](../extensions/agent-linux.md)。

1. 使用 SSH 用戶端連線到 Linux VM。
2. 在 SSH 視窗中，輸入下列命令：
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > 只在您要擷取作為映像的 VM 上執行這個命令。 此命令不能保證映像檔中的所有機密資訊都會清除完畢或適合轉散發。 `+user` 參數也會移除最後一個佈建的使用者帳戶。 若要在 VM 中保留使用者帳戶認證，僅使用 `-deprovision`。
 
3. 輸入 **y** 繼續。 您可以新增 `-force` 參數，便不用進行此確認步驟。
4. 在命令完成之後，請輸入 **exit** 關閉 SSH 用戶端。  此時 VM 仍會在執行中。

## <a name="step-2-create-vm-image"></a>步驟 2:建立 VM 映像
使用 Azure CLI 將 VM 標記為一般化，並擷取映像。 在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 *myResourceGroup*、*myVnet* 和 *myVM*。

1. 使用 [az vm deallocate](/cli/azure/vm) 解除配置已取消佈建的 VM。 下列範例會解除配置名為 myResourceGroup 資源群組中名為 myVM 的 VM。  
   
    ```azurecli
    az vm deallocate \
        --resource-group myResourceGroup \
        --name myVM
    ```
    
    等到 VM 完全解除配置，再繼續進行。 這可能需要幾分鐘才能完成。  VM 會在解除配置期間關閉。

2. 使用 [az vm generalize](/cli/azure/vm)，將 VM 標記為一般化。 下列範例會將名為 myResourceGroup 的資源群組中名為 myVM 的 VM 標記為一般化。
   
    ```azurecli
    az vm generalize \
        --resource-group myResourceGroup \
        --name myVM
    ```

    已一般化的 VM 無法再重新啟動。

3. 使用 [az image create](/cli/azure/image#az-image-create) 建立 VM 資源的映像。 下列範例會使用名為 myVM 的 VM 資源，在 myResourceGroup 資源群組中建立名為 myImage 的映像。
   
    ```azurecli
    az image create \
        --resource-group myResourceGroup \
        --name myImage --source myVM
    ```
   
   > [!NOTE]
   > 此映像與來源 VM 建立於相同的資源群組中。 您可以從此映像，在您訂用帳戶的任何資源群組中建立 VM。 從管理觀點來看，您可能想為您的 VM 資源和映像建立特定的資源群組。
   >
   > 如果您想要將映像儲存於區域復原的儲存體中，則需要在支援[可用性區域](../../availability-zones/az-overview.md)且包含 `--zone-resilient true` 參數的區域中建立它。
   
此命令會傳回描述 VM 映像的 JSON。 儲存此輸出以供日後參考。

## <a name="step-3-create-a-vm-from-the-captured-image"></a>步驟 3：從擷取的映像建立 VM
使用您以 [az vm create](/cli/azure/vm) 建立的映像來建立 VM。 下列範例會從名為 myImage 的映像建立名為 myVMDeployed 的 VM。

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>在另一個資源群組中建立 VM 

您可以從某個映像，在您訂用帳戶的任何資源群組中建立 VM。 若要在與映像不同的資源群組中建立 VM，請指定您映像的完整資源識別碼。 使用 [az image list](/cli/azure/image#az-image-list) 來檢視映像清單。 輸出類似於下列範例：

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

下列範例藉由指定映像資源識別碼，進而使用 [az vm create](/cli/azure/vm#az-vm-create) 在與來源映像不同的資源群組中建立 VM。

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>步驟 4：驗證部署

透過 SSH 連線到您建立的虛擬機器，以驗證部署並開始使用新的 VM。 若要透過 SSH 連接，請利用 [az vm show](/cli/azure/vm#az-vm-show) 尋找您 VM 的 IP 位址或 FQDN。

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>後續步驟
若要大規模建立、儲存和共用映像，請參閱[共用映像資源庫](../shared-images-cli.md)。