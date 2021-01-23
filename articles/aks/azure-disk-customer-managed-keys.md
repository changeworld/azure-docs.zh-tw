---
title: '使用客戶管理的金鑰來加密 Azure Kubernetes Service (AKS 中的 Azure 磁片) '
description: 將您自己的金鑰 (BYOK) ，以加密 AKS OS 和資料磁片。
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 60a7e36039500ccb8a46fd1f5998c23c37174689
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728130"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS 中使用 Azure 磁片 (BYOK) 攜帶您自己的金鑰) 

Azure 儲存體會加密待用儲存體帳戶中的所有資料。 根據預設，資料是以使用 Microsoft 管理的金鑰加密。 若要進一步控制加密金鑰，您可以提供客戶管理的金鑰，以用於 AKS 叢集的 OS 和資料磁片的靜態加密。 深入瞭解 [Linux][customer-managed-keys-linux] 和 [Windows][customer-managed-keys-windows]上客戶管理的金鑰。

## <a name="limitations"></a>限制
* 資料磁片加密支援僅限於執行 Kubernetes 1.17 版和更新版本的 AKS 叢集。
* 只有在建立 AKS 叢集時，才可以啟用使用客戶管理的金鑰來加密 OS 和資料磁片。

## <a name="prerequisites"></a>Prerequisites
* 使用 Key Vault 加密受控磁片時，您必須啟用 *Azure Key Vault* 的虛刪除和清除保護。
* 您需要 Azure CLI 2.11.1 版或更新版本。

## <a name="create-an-azure-key-vault-instance"></a>建立 Azure Key Vault 實例

使用 Azure Key Vault 實例來儲存您的金鑰。  您可以選擇性地使用 Azure 入口網站 [設定客戶管理的金鑰，Azure Key Vault][byok-azure-portal]

建立新的 *資源群組*，然後建立新的 *Key Vault* 實例，並啟用虛刪除和清除保護。  請確定每個命令都使用相同的區域和資源組名。

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

## <a name="create-an-instance-of-a-diskencryptionset"></a>建立 DiskEncryptionSet 的實例

將 *myKeyVaultName* 取代為您的金鑰保存庫名稱。  您也需要將 *金鑰* 儲存在 Azure Key Vault 中，才能完成下列步驟。  將您現有的金鑰儲存在先前步驟所建立的 Key Vault 中，或 [產生新的金鑰][key-vault-generate] ，並將下列 *myKeyName* 取代為您的金鑰名稱。
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>將 DiskEncryptionSet 存取權授與 key vault

使用您在先前步驟中建立的 DiskEncryptionSet 和資源群組，並將存取權授與給存取 Azure Key Vault 的 DiskEncryptionSet 資源。

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>建立新的 AKS 叢集並將 OS 磁片加密

建立 **新的資源群組** 和 AKS 叢集，然後使用您的金鑰來加密作業系統磁片。 只有在 Kubernetes 版本大於1.17 時，才支援客戶管理的金鑰。 

> [!IMPORTANT]
> 請務必為您的 AKS 叢集建立新的資源群組

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

當新的節點集區新增至上面建立的叢集時，會使用建立期間提供的客戶管理金鑰來加密作業系統磁片。

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a> (選用) 加密您的 AKS 叢集資料磁片
如果未提供 v 1.17.2 的資料磁片金鑰，則會使用 OS 磁片加密金鑰來加密資料磁片，而且您也可以使用其他金鑰來加密 AKS 資料磁片。

> [!IMPORTANT]
> 確定您有適當的 AKS 認證。 服務主體必須具有部署 diskencryptionset 之資源群組的參與者存取權。 否則，您會收到錯誤，表示服務主體沒有許可權。

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

建立名為 byok 的檔案 **-yaml** ，其中包含下列資訊。  將 myAzureSubscriptionId、myResourceGroup 和 myDiskEncrptionSetName 取代為您的值，並套用 yaml。  請務必使用您的 DiskEncryptionSet 部署所在的資源群組。  如果您使用 Azure Cloud Shell，則可以使用 vi 或 nano 來建立此檔案，就像在虛擬或實體系統上運作一樣：

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
接下來，在您的 AKS 叢集中執行此部署：
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>後續步驟

檢查 [AKS 叢集安全性的最佳作法][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions