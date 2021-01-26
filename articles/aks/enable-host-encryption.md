---
title: 在 Azure Kubernetes Service (AKS) 上啟用主機型加密
description: 瞭解如何在 Azure Kubernetes Service (AKS) 叢集設定以主機為基礎的加密
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 531d1dc4169b5f4adecfb29c3e116049cb99c3c9
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787819"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes Service (AKS)  (preview) 上的主機型加密

使用以主機為基礎的加密時，儲存在 AKS 代理程式節點 Vm 之 VM 主機上的資料會在待用時加密，並將流量加密至儲存體服務。 這表示暫存磁片會使用平臺管理的金鑰進行待用加密。 作業系統和資料磁片的快取會使用平臺管理的金鑰或客戶管理的金鑰進行待用加密，視這些磁片上設定的加密類型而定。 根據預設，當使用 AKS 時，OS 和資料磁片會使用平臺管理的金鑰進行待用加密，這表示這些磁片的快取預設也會使用平臺管理的金鑰進行待用加密。  您可以使用「攜帶您自己的金鑰」來指定自己的受控金鑰， [ (BYOK) Azure Kubernetes Service 中的 Azure 磁片](azure-disk-customer-managed-keys.md)。 這些磁片的快取也會使用您在此步驟中指定的金鑰進行加密。


## <a name="before-you-begin"></a>開始之前

這項功能只能在建立叢集時設定，或在建立節點集區時設定。

> [!NOTE]
> 以主機為基礎的加密功能可在 [azure 區域][supported-regions] 中使用，這些區域支援 azure 受控磁片的伺服器端加密，且僅支援特定的 [支援 VM 大小][supported-sizes]。

### <a name="prerequisites"></a>先決條件

- 確定您已 `aks-preview` 安裝 CLI 擴充功能 v 0.4.55 或更高版本
- 確定您在 [ `EnableEncryptionAtHostPreview` 已啟用] 下有功能旗標 `Microsoft.ContainerService` 。

若要能夠針對您的 Vm 或虛擬機器擴展集使用主機加密，您必須在訂用帳戶上啟用此功能。 使用您的訂用帳戶識別碼，將電子郵件傳送至 encryptionAtHost@microsoft，以啟用訂用帳戶的功能。

### <a name="register-encryptionathost--preview-features"></a>註冊 `EncryptionAtHost`  預覽功能

> [!IMPORTANT]
> 您必須 encryptionAtHost@microsoft 使用您的訂用帳戶 id 來傳送電子郵件，以取得針對計算資源啟用的功能。 您無法自行為這些資源啟用。 您可以自行在 container service 上啟用它。

若要建立使用以主機為基礎之加密的 AKS 叢集，您必須 `EnableEncryptionAtHostPreview` 在訂用帳戶上啟用和 `EncryptionAtHost` 功能旗標。

`EncryptionAtHost`使用[az feature register][az-feature-register]命令註冊功能旗標，如下列範例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService"  --name "EnableEncryptionAtHostPreview"
```

狀態需要幾分鐘的時間才會顯示「已註冊」。 您可以使用 [az feature list][az-feature-list] 命令檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableEncryptionAtHostPreview')].{Name:name,State:properties.state}"
```

準備好時，請 `Microsoft.ContainerService` `Microsoft.Compute` 使用 [az provider register][az-provider-register] 命令重新整理和資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 延伸模組

若要建立以主機為基礎的加密 AKS 叢集，您需要最新的 *AKS-preview* CLI 擴充功能。 使用 [az extension add][az-extension-add]命令安裝 *aks-preview* Azure CLI 擴充功能，或使用 [az extension update][az-extension-update]命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>限制

- 只能在新的節點集區或新叢集上啟用。
- 只能在支援 Azure 受控磁片之伺服器端加密的 [azure 區域][supported-regions] 中啟用，而且只能使用特定 [支援的 VM 大小][supported-sizes]。
- 需要以虛擬機器擴展集為基礎的 AKS 叢集和節點集區 (VMSS) 作為 *VM 集類型*。

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>在新的叢集上使用以主機為基礎的加密 (預覽) 

設定叢集代理程式節點，以在建立叢集時使用以主機為基礎的加密。 使用 `--aks-custom-headers` 旗標來設定 `EnableEncryptionAtHost` 標頭。

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

如果您想要建立沒有以主機為基礎之加密的叢集，您可以省略自訂參數來這麼做 `--aks-custom-headers` 。

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>在現有的叢集上使用以主機為基礎的加密 (預覽) 

您可以藉由將新的節點集區新增至您的叢集，在現有的叢集上啟用主機型加密。 使用旗標，將新的節點集區設定為使用主機型加密 `--aks-custom-headers` 。

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

如果您想要在沒有以主機為基礎的加密功能的情況下建立新的節點集區，您可以省略自訂參數來建立新的節點集區 `--aks-custom-headers` 。

## <a name="next-steps"></a>後續步驟

請參閱 [AKS 叢集安全性的最佳作法，][best-practices-security] 深入瞭解 [主機型加密](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register