---
title: 在 Azure Kubernetes Service 上啟用主機型加密（AKS）
description: 瞭解如何在 Azure Kubernetes Service （AKS）叢集中設定主機型加密
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: d2b34d8c3090eb6ae3f1445ff1fc663d90367977
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517717"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes Service （AKS）上以主機為基礎的加密（預覽）

使用以主機為基礎的加密，儲存在 AKS 代理程式節點 vm 的 VM 主機上的資料會在待用時加密，並將流量加密至儲存體服務。 這表示暫存磁片會在待用時使用平臺管理的金鑰進行加密。 OS 和資料磁片的快取會以平臺管理的金鑰或客戶管理的金鑰進行待用加密，視這些磁片上設定的加密類型而定。 根據預設，使用 AKS 時，OS 和資料磁片會使用平臺管理的金鑰進行待用加密，這表示這些磁片的快取也預設會以平臺管理的金鑰加密。  在 Azure Kubernetes Service 中，您可以遵循[攜帶您自己的金鑰（BYOK）與 Azure 磁片](azure-disk-customer-managed-keys.md)來指定自己的受控金鑰。 這些磁片的快取也會使用您在此步驟中指定的金鑰進行加密。


## <a name="before-you-begin"></a>開始之前

這項功能只能在叢集建立或節點集區建立時設定。

> [!NOTE]
> 以主機為基礎的加密可在支援 Azure 受控磁片之伺服器端加密的[azure 區域][supported-regions]中取得，而且只適用于特定[支援的 VM 大小][supported-sizes]。

### <a name="prerequisites"></a>先決條件

- 請確定您已 `aks-preview` 安裝 CLI 擴充功能 v 0.4.55 或更新版本
- 請確定您已 `EncryptionAtHost` 啟用 [功能] 旗標 `Microsoft.Compute` 。
- 請確定您已 `EnableEncryptionAtHostPreview` 啟用 [功能] 旗標 `Microsoft.ContainerService` 。

### <a name="register-encryptionathost--preview-features"></a>註冊 `EncryptionAtHost` 預覽功能

若要建立使用主機型加密的 AKS 叢集，您必須 `EnableEncryptionAtHostPreview` 在您的訂用帳戶上啟用和 `EncryptionAtHost` 功能旗標。

`EncryptionAtHost`使用[az feature register][az-feature-register]命令來註冊功能旗標，如下列範例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.Compute" --name "EncryptionAtHost"

az feature register --namespace "Microsoft.ContainerService"  --name "EnableEncryptionAtHostPreview"
```

狀態需要幾分鐘的時間才會顯示「已註冊」**。 您可以使用 [az feature list][az-feature-list] 命令檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Compute/EncryptionAtHost')].{Name:name,State:properties.state}"

az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableEncryptionAtHostPreview')].{Name:name,State:properties.state}"
```

準備好時，請 `Microsoft.ContainerService` `Microsoft.Compute` 使用[az provider register][az-provider-register]命令重新整理和資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.Compute

az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> AKS 預覽功能是自助加入宣告。 預覽會以「原樣」和「可用」的方式提供，並從服務等級協定中排除，並享有有限擔保。 AKS 預覽的部分是由客戶支援，以最大的方式來涵蓋。 因此，這些功能並不適用于生產環境使用。 如需其他資訊，請參閱下列支援文章：
>
> - [AKS 支援原則](support-policies.md)
> - [Azure 支援常見問題集](faq.md)

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 延伸模組

若要建立以主機為基礎的加密 AKS 叢集，您需要最新的*AKS-preview* CLI 擴充功能。 使用[az extension add][az-extension-add]命令來安裝*aks-preview* Azure CLI 擴充功能，或使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>限制

- 只能在新的節點集區或新叢集上啟用。
- 只能在支援 Azure 受控磁片之伺服器端加密的[azure 區域][supported-regions]中啟用，而且只能使用特定[支援的 VM 大小][supported-sizes]。
- 需要以虛擬機器擴展集（VMSS）為基礎的 AKS 叢集和節點集區做為*VM 集合類型*。

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>在新叢集上使用以主機為基礎的加密（預覽）

設定叢集代理程式節點，以在建立叢集時使用以主機為基礎的加密。 使用 `--aks-custom-headers` 旗標來設定 `EnableEncryptionAtHost` 標頭。

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

如果您想要建立沒有主機型加密的叢集，您可以省略自訂參數來執行此動作 `--aks-custom-headers` 。

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>在現有叢集上使用以主機為基礎的加密（預覽）

您可以藉由將新的節點集區新增至您的叢集，在現有叢集上啟用主機型加密。 設定新的節點集區，以使用以主機為基礎的加密（使用旗標） `--aks-custom-headers` 。

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

如果您想要在沒有主機型加密功能的情況下建立新的節點集區，您可以省略自訂參數來執行此動作 `--aks-custom-headers` 。

## <a name="next-steps"></a>後續步驟

請參閱[AKS 叢集安全性的最佳作法][best-practices-security]深入瞭解[主機型加密](../virtual-machines/linux/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/linux/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/linux/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
