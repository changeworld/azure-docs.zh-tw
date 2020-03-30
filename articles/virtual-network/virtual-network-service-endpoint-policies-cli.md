---
title: 將資料外泄限制為 Azure 存儲 - Azure CLI
description: 在本文中，您將瞭解如何使用 Azure CLI 使用虛擬網路服務終結點策略限制和限制虛擬網路資料向 Azure 存儲資源滲透。
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: e01af052a936403162115965f2dc5b3ad46dd9cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271191"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>使用 Azure CLI 使用虛擬網路服務終結點策略管理資料滲入到 Azure 存儲帳戶

虛擬網路服務終結點策略使您能夠通過服務終結點從虛擬網路內對 Azure 存儲帳戶應用存取控制。 這是保護工作負載、管理允許哪些存儲帳戶以及允許資料滲漏的位置的關鍵。
在本文中，您將學會如何：

* 創建虛擬網路並添加子網。
* 為 Azure 存儲啟用服務終結點。
* 創建兩個 Azure 存儲帳戶，並允許從上面創建的子網進行網路訪問。
* 創建服務終結點策略，僅允許訪問其中一個存儲帳戶。
* 將虛擬機器 （VM） 部署到子網。
* 確認從子網對允許的存儲帳戶的訪問。
* 確認從子網對不允許的存儲帳戶的訪問被拒絕。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.28 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="create-a-virtual-network"></a>建立虛擬網路

建立虛擬網路之前，您必須為虛擬網路以及在本文中建立的所有其他資源，建立資源群組。 使用 [az group create](/cli/azure/group) 來建立資源群組。 下面的示例在*東部*位置創建名為*myResourceGroup*的資源組。

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet) 建立具有一個子網路的虛擬網路。

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>啟用服務端點 

在此示例中，為*Microsoft.存儲*創建了一個服務終結點，用於子網*專用*： 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>限制子網路的網路存取

使用 [az network nsg create](/cli/azure/network/nsg) 建立網路安全性群組。 下列範例會建立名為 myNsgPrivate** 的網路安全性群組。

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

請使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet) 將網路安全性群組與「私人」** 子網路建立關聯。 下列範例會將 myNsgPrivate** 網路安全性群組與「私人」** 子網路建立關聯：

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

使用 [az network nsg rule create](/cli/azure/network/nsg/rule) 來建立安全性規則。 下列規則允許對指派給 Azure 儲存體服務之公用 IP 位址的輸出存取： 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

每個網路安全性群組包含多個[預設安全規則](security-overview.md#default-security-rules)。 以下規則將覆蓋允許出站訪問所有公共 IP 位址的預設安全規則。 該`destination-address-prefix "Internet"`選項拒絕對所有公共 IP 位址的出站訪問。 上一個規則會因其具有較高優先順序而覆寫這項規則，從而允許對 Azure 儲存體之公用 IP 位址的存取。

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

以下規則允許 SSH 流量從任意位置入站到子網。 此規則會覆寫拒絕來自網際網路之所有輸入流量的預設安全性規則。 SSH 允許到子網，以便可以在後面的步驟中測試連接。

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>限制對 Azure 存儲帳戶的網路訪問

本節列出了通過服務終結點限制 Azure 存儲帳戶從虛擬網路中的給定子網進行的網路訪問的步驟。

### <a name="create-a-storage-account"></a>建立儲存體帳戶

創建兩個使用[az 存儲帳戶創建的](/cli/azure/storage/account)Azure 存儲帳戶。

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

創建存儲帳戶後，將存儲帳戶的連接字串檢索到具有[az 存儲帳戶顯示連接字串的](/cli/azure/storage/account)變數中。 在稍後步驟中，會使用連接字串來建立檔案共用。

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>檢視變數的內容，並記下輸出中傳回的 **AccountKey** 值，因為在稍後步驟中會用到它。

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>在儲存體帳戶中建立檔案共用

使用 [az storage share create](/cli/azure/storage/share) 在儲存體帳戶中建立檔案共用。 在稍後步驟中，會裝載此檔案共用，以確認其網路存取。

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>拒絕對存儲帳戶的所有網路訪問

根據預設，儲存體帳戶會接受來自任何網路用戶端的網路連線。 若要限制對選取網路的存取，請使用 [az storage account update](/cli/azure/storage/account) 將預設動作變更為「拒絕」**。 一旦網路存取遭到拒絕後，就無法從任何網路存取儲存體帳戶。

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>啟用從虛擬網路子網進行網路訪問

使用 [az storage account network-rule add](/cli/azure/storage/account/network-rule) 允許從「私人」** 子網路對儲存體帳戶的網路存取。

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>應用策略以允許訪問有效的存儲帳戶

Azure 服務終結點策略僅適用于 Azure 存儲。 因此，我們將為 Microsoft 啟用服務終結點 *。* 此子網上的存儲用於此示例設置。

服務終結點策略應用於服務終結點。 我們將從創建服務終結點策略開始。 然後，我們將在此策略下創建策略定義，以便為此子網將 Azure 存儲帳戶列入白名單

建立服務端點原則

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

將允許的存儲帳戶的資源 URI 保存在變數中。 在執行以下命令之前，*\<請將訂閱 id>* 替換為訂閱 ID 的實際值。

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

創建&添加策略定義，允許上述 Azure 存儲帳戶到服務終結點策略

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

並更新虛擬網路子網以與其關聯上一步中創建的服務終結點策略

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>驗證對 Azure 存儲帳戶的訪問限制

### <a name="create-the-virtual-machine"></a>建立虛擬機器

要測試對存儲帳戶的網路訪問，請將 VM 部署到子網。

在*專用*子網中創建具有[az vm 的 VM。](/cli/azure/vm) 如果預設金鑰位置中還沒有 SSH 金鑰，此命令將會建立這些金鑰。 若要使用一組特定金鑰，請使用 `--ssh-key-value` 選項。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

建立 VM 需要幾分鐘的時間。 在建立之後，請記下傳回之輸出中的 **publicIpAddress**。 在稍後的步驟中，這個位址可用來從網際網路存取虛擬機器。

### <a name="confirm-access-to-storage-account"></a>確認對儲存體帳戶的存取

使用 SSH 連線到 myVmPrivate** VM。 將*\<公共 Ip 位址>* 替換為*myVmPrivate VM*的公共 IP 位址。

```bash 
ssh <publicIpAddress>
```

建立裝載點的資料夾：

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

將 Azure 檔案共用裝載至您所建立的目錄。 在執行下面的命令之前，將*\<存儲帳戶金鑰>* 替換為 **$saConnectionString1**中的*帳戶金鑰*值。

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

您會收到 `user@myVmPrivate:~$` 提示字元。 Azure 檔案共用已成功裝載到 /mnt/MyAzureFileShare**。

### <a name="confirm-access-is-denied-to-storage-account"></a>確認對儲存體帳戶的存取遭到拒絕

從同一 VM *myVmPrivate*中，為裝載點創建一個目錄：

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

嘗試將 Azure 檔共用從*不允許存儲帳戶*裝載到您創建的目錄。 本文假設您已部署最新版本的 Ubuntu。 如果您是使用舊版的 Ubuntu，請參閱 [Linux 上的裝載](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，以取得有關裝載檔案共用的其他指示。 

在執行下面的命令之前，請將*\<存儲帳戶金鑰>* 替換為 **$saConnectionString2**中的*AccountKey*值。

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

訪問被拒絕，您會收到錯誤`mount error(13): Permission denied`，因為此存儲帳戶不在我們應用於子網的服務終結點策略的允許清單中。 

結束對 myVmPublic** VM 的 SSH 工作階段。

## <a name="clean-up-resources"></a>清除資源

請使用 [az group delete](/cli/azure) 來移除不再需要的資源群組以及其所包含的所有資源。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>後續步驟

在本文中，您將服務終結點策略應用於 Azure 存儲。" 您創建了 Azure 存儲帳戶，並且僅從虛擬網路子網對某些存儲帳戶（因此拒絕其他存儲帳戶）進行網路訪問。 要瞭解有關服務終結點策略的詳細資訊，請參閱[服務終結點策略概述](virtual-network-service-endpoint-policies-overview.md)。
