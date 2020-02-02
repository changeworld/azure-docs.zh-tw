---
title: 在 Azure Kubernetes Service （AKS）中使用客戶管理的金鑰來加密 Azure 磁片
description: 攜帶您自己的金鑰（BYOK）來加密 AKS OS 和資料磁片。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/12/2020
ms.author: mlearned
ms.openlocfilehash: 1852ba059d13a8886b739cac439798b4d031a531
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932628"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service （AKS）中攜帶您自己的金鑰（BYOK）與 Azure 磁片

Azure 儲存體會加密待用儲存體帳戶中的所有資料。 根據預設，資料會使用 Microsoft 管理的金鑰進行加密。 若要進一步控制加密金鑰，您可以為 AKS 叢集的 OS 和資料磁片提供[客戶管理的金鑰][customer-managed-keys]，以用於待用加密。

> [!NOTE]
> 在支援 Azure 受控磁片之伺服器端加密的[azure 區域][supported-regions]中，會提供 BYOK Linux 和 Windows 型的 AKS 叢集。

## <a name="before-you-begin"></a>開始之前

* 本文假設您要建立*新的 AKS*叢集。

* 使用 Key Vault 加密受控磁片時，您必須啟用*Azure Key Vault*的虛刪除和清除保護。

* 您需要 Azure CLI version 2.0.79 或更新版本，以及 aks-preview 0.4.26 擴充功能

> [!IMPORTANT]
> AKS 預覽功能是自助加入宣告。 預覽會以「原樣」和「可用」的方式提供，並從服務等級協定中排除，並享有有限擔保。 AKS 預覽的部分是由客戶支援，以最大的方式來涵蓋。 因此，這些功能並不適用于生產環境使用。 如需其他資訊，請參閱下列支援文章：
>
> * [AKS 支援原則](support-policies.md)
> * [Azure 支援常見問題集](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>安裝最新的 AKS CLI preview 擴充功能

若要使用客戶管理的金鑰，您需要*aks-preview* CLI 擴充功能版本0.4.26 或更高版本。 使用[az extension add][az-extension-add]命令來安裝*aks-preview* Azure CLI 擴充功能，然後使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>建立 Azure Key Vault 實例

使用 Azure Key Vault 實例來儲存您的金鑰。  您可以選擇性地使用 Azure 入口網站，透過[Azure Key Vault 設定客戶管理的金鑰][byok-azure-portal]

建立新的*資源群組*，然後建立新的*Key Vault*實例，並啟用虛刪除和清除保護。  請確定您對每個命令使用相同的區域和資源組名。

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

將*myKeyVaultName*取代為您的金鑰保存庫名稱。  您也將需要儲存在 Azure Key Vault 中的*金鑰*，才能完成下列步驟。  將您現有的金鑰儲存在先前步驟所建立的 Key Vault 中，或[產生新的金鑰][key-vault-generate]，並以您的金鑰名稱取代以下的*myKeyName* 。
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>授與金鑰保存庫的 DiskEncryptionSet 存取權

使用您在先前步驟中建立的 DiskEncryptionSet 和資源群組，並授與 DiskEncryptionSet 資源存取權給 Azure Key Vault。

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>建立新的 AKS 叢集，並將 OS 磁片加密

建立**新的資源群組**和 AKS 叢集，然後使用您的金鑰來加密 OS 磁片。 只有在 Kubernetes 版本大於1.17 時，才支援客戶管理的金鑰。 

> [!IMPORTANT]
> 請務必為您的 AKS 叢集建立新的資源群組

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

將新的節點集區新增至上面建立的叢集時，會使用在建立期間提供的客戶管理金鑰來加密 OS 磁片。

## <a name="encrypt-your-aks-cluster-data-disk"></a>加密您的 AKS 叢集資料磁片

您也可以使用自己的金鑰來加密 AKS 資料磁片。

> [!IMPORTANT]
> 請確定您有適當的 AKS 認證。 服務主體必須具有部署 diskencryptionset 之資源群組的參與者存取權。 否則，您會收到錯誤，建議服務主體沒有許可權。

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

建立名為**byok**的檔案，其中包含下列資訊。  將 myAzureSubscriptionId、myResourceGroup 和 myDiskEncrptionSetName 取代為您的值，並套用 yaml。  請務必使用您的 DiskEncryptionSet 部署所在的資源群組。  如果您使用 Azure Cloud Shell，則可以使用 vi 或 nano 建立此檔案，如同在虛擬或實體系統上運作一樣：

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

## <a name="limitations"></a>限制

* BYOK 目前僅適用于特定[Azure 區域][supported-regions]中的 GA 和預覽
* Kubernetes 1.17 版和更新版本支援的 OS 磁片加密   
* 僅適用于支援 BYOK 的區域
* 使用客戶管理的金鑰加密目前僅適用于新的 AKS 叢集，無法升級現有的叢集
* 需要使用虛擬機器擴展集的 AKS 叢集，不支援虛擬機器可用性設定組


## <a name="next-steps"></a>後續步驟

審查[AKS 叢集安全性的最佳做法][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
