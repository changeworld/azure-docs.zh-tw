---
title: 使用客戶管理的金鑰組 Azure 庫伯奈斯服務 （AKS） 中的 Azure 磁片進行加密
description: 自帶金鑰 （BYOK） 來加密 AKS 作業系統和資料磁片。
services: container-service
ms.topic: article
ms.date: 01/12/2020
ms.openlocfilehash: bb6ba5e6dd4ace9e33043079c0f435c10baf5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596499"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>在 Azure 庫伯奈斯服務 （AKS） 中攜帶自己的金鑰 （BYOK） 與 Azure 磁片

Azure 存儲在靜態存儲帳戶中加密所有資料。 預設情況下，資料使用 Microsoft 管理的金鑰進行加密。 為了對加密金鑰進行其他控制，可以提供[客戶管理的金鑰][customer-managed-keys]，用於 AKS 群集的作業系統和資料磁片的靜態加密。

> [!NOTE]
> 基於 BYOK Linux 和基於 Windows 的 AKS 群集在[Azure 區域][supported-regions]中可用，這些區域支援 Azure 託管磁片的伺服器端加密。

## <a name="before-you-begin"></a>開始之前

* 本文假定您正在創建新的*AKS 群集*。

* 使用金鑰保存庫加密託管磁片時，必須為*Azure 金鑰保存庫*啟用虛刪除和清除保護。

* 您需要 Azure CLI 版本 2.0.79 或更高版本和 aks 預覽 0.4.26 擴展

> [!IMPORTANT]
> AKS 預覽功能是自助服務加入宣告。 預覽版提供"根據"和"可用"，不在服務等級協定和有限保修中。 在盡力的基礎上，客戶支援部分覆蓋了 AKS 預覽。 因此，這些功能不適合生產用途。 有關其他操作，請參閱以下支援文章：
>
> * [AKS 支援策略](support-policies.md)
> * [Azure 支援常見問題集](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>安裝最新的 AKS CLI 預覽擴展

要使用客戶管理的金鑰，您需要*aks 預覽*CLI 擴展版本 0.4.26 或更高版本。 使用[az 擴展添加][az-extension-add]命令安裝*aks 預覽*Azure CLI 擴展，然後使用[az 擴展更新][az-extension-update]命令檢查任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>創建 Azure 金鑰保存庫實例

使用 Azure 金鑰保存庫實例存儲金鑰。  您可以選擇使用 Azure 門戶[使用 Azure 金鑰保存庫配置客戶管理的金鑰][byok-azure-portal]

創建新*的資源組*，然後創建新*的金鑰保存庫*實例，並啟用虛刪除和清除保護。  確保為每個命令使用相同的區域和資源組名稱。

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>創建磁片加密集的實例

將*myKeyVault 名稱*替換為金鑰保存庫的名稱。  您還需要存儲在 Azure 金鑰保存庫中的*金鑰*才能完成以下步驟。  將現有金鑰存儲在前面步驟中創建的金鑰保存庫中，或者[生成新金鑰][key-vault-generate]，並在下面用金鑰名稱替換*myKeyName。*
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>授予磁片加密集對金鑰保存庫的存取權限

使用在之前的步驟上創建的磁片加密集和資源組，並授予磁片加密集資源對 Azure 金鑰保存庫的存取權限。

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>創建新的 AKS 群集並加密作業系統磁片

創建新**的資源組**和 AKS 群集，然後使用金鑰加密 OS 磁片。 客戶管理的金鑰僅在大於 1.17 的 Kubernetes 版本中支援。 

> [!IMPORTANT]
> 確保為 AKS 群集創建新的 reruce 組

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

將新節點池添加到上述創建的群集時，創建期間提供的客戶託管金鑰將用於加密 OS 磁片。

## <a name="encrypt-your-aks-cluster-data-disk"></a>加密 AKS 群集資料磁片

您還可以使用自己的金鑰加密 AKS 資料磁片。

> [!IMPORTANT]
> 確保您擁有正確的 AKS 憑據。 服務主體需要具有對部署磁片加密集的資源組的參與者存取權限。 否則，您會收到一個錯誤，指出服務主體沒有許可權。

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

創建**由ok-azure-disk.yaml**調用的檔，其中包含以下資訊。  將我的 Azure 訂閱 Id、myResourceGroup 和 myDiskEncrptionSetName 替換為您的值，並應用"yaml"。  請確保使用部署磁片加密集的資源組。  如果使用 Azure 雲外殼，則可以使用 vi 或 nano 創建此檔，就像在虛擬或物理系統上工作時一樣：

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
接下來，在 AKS 群集中運行此部署：
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>限制

* BYOK 目前僅在某些[Azure 區域][supported-regions]的 GA 和預覽版中可用
* Kubernetes 版本 1.17 及以上支援的作業系統磁片加密   
* 僅適用于支援 BYOK 的區域
* 使用客戶管理的金鑰加密當前僅適用于新的 AKS 群集，無法升級現有群集
* 需要使用虛擬機器縮放集的 AKS 群集，不支援虛擬機器可用性集


## <a name="next-steps"></a>後續步驟

查看[AKS 群集安全的最佳實踐][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
