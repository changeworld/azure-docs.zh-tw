---
title: '在 Azure Kubernetes Service (AKS 上啟用 (CSI) 驅動程式的容器存放裝置介面) '
description: 瞭解如何在 Azure Kubernetes Service (AKS) 叢集中，為 Azure 磁片和 Azure 檔案儲存體的 (CSI) 驅動程式啟用容器存放裝置介面。
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 54764b16ba63d5656f61152cfe40ef50475192a5
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085652"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>啟用容器儲存體介面 (CSI) Azure 磁片的驅動程式和 Azure 檔案儲存體 Azure Kubernetes Service (AKS)  (preview) 

容器存放裝置介面 (CSI) 是在 Kubernetes 上將任意區塊和檔案儲存體系統公開至容器化工作負載的標準。 藉由採用和使用 CSI，Azure Kubernetes Service (AKS) 可以撰寫、部署及反覆運算外掛程式，以在 Kubernetes 中公開新的或改善現有的儲存系統，而不需要觸控核心 Kubernetes 程式碼並等候其發行週期。

AKS 上的 CSI 儲存體驅動程式支援可讓您以原生方式使用：
- 可以用來建立 Kubernetes *>datadisk*資源的[*Azure 磁片*](azure-disk-csi.md)。 磁片可以使用由高效能 Ssd （或 Azure 標準儲存體）支援的 Azure 進階儲存體（由一般 Hdd 或標準 Ssd 支援）。 針對大部分的生產和開發工作負載，請使用進階儲存體。 Azure 磁片會掛接為 *>readwriteonce*，因此僅適用于單一 pod。 針對可同時由多個 pod 存取的儲存體磁片區，請使用 Azure 檔案儲存體。
- [*Azure 檔案儲存體*](azure-files-csi.md)，可用來將 Azure 儲存體帳戶所支援的 SMB 3.0 共用掛接至 pod。 使用 Azure 檔案儲存體，您可以在多個節點和 pod 之間共用資料。 Azure 檔案儲存體可以使用由標準 Hdd 或 Azure 進階儲存體支援的 Azure 標準儲存體，並支援高效能 Ssd。

> [!IMPORTANT]
> 從 Kubernetes 版本1.21 開始，Kubernetes 只會使用 CSI 驅動程式和預設值。 這些驅動程式是 Kubernetes 中儲存體支援的未來。
>
> *樹狀結構中的驅動程式* 是指核心 Kubernetes 程式碼中的目前儲存驅動程式，與新的 CSI 驅動程式（即外掛程式）的一部分。

## <a name="limitations"></a>限制

- 這項功能只能在建立叢集時設定。
- 支援 CSI 驅動程式的最小 Kubernetes 次要版本為 v 1.17。
- 在預覽期間，預設的儲存類別仍會是 [相同的樹狀目錄儲存類別](concepts-storage.md#storage-classes)。 這項功能正式推出後，預設的儲存類別將會是 `managed-csi` 儲存類別，而樹狀結構內的儲存類別將會移除。
- 在第一個預覽階段，僅支援 Azure CLI。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>註冊 `EnableAzureDiskFileCSIDriver` 預覽功能

若要建立可使用適用于 Azure 磁片的 CSI 驅動程式和 Azure 檔案儲存體的 AKS 叢集，您必須 `EnableAzureDiskFileCSIDriver` 在訂用帳戶上啟用功能旗標。

`EnableAzureDiskFileCSIDriver`使用[az feature register][az-feature-register]命令註冊功能旗標，如下列範例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

狀態需要幾分鐘的時間才會顯示「已註冊」**。 使用 [az feature list][az-feature-list] 命令來確認註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

當您準備好時，請使用[az provider register][az-provider-register]命令重新整理 *>microsoft.containerservice*資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 延伸模組

若要建立可使用 CSI 儲存驅動程式的 AKS 叢集或節點集區，您需要最新的 *AKS-preview* Azure CLI 擴充功能。 使用[az extension add][az-extension-add]命令安裝*aks-preview* Azure CLI 擴充功能。 或使用 [az extension update][az-extension-update] 命令安裝任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>建立可使用 CSI 儲存驅動程式的新叢集

建立新的叢集，以使用適用于 Azure 磁片的 CSI 儲存體驅動程式，並使用下列 CLI 命令來 Azure 檔案儲存體。 使用 `--aks-custom-headers` 旗標來設定此 `EnableAzureDiskFileCSIDriver` 功能。

建立 Azure 資源群組：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

建立具有 CSI 儲存驅動程式支援的 AKS 叢集：

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

如果您想要在樹狀結構儲存體驅動程式（而非 CSI 儲存驅動程式）中建立叢集，您可以省略自訂參數來進行 `--aks-custom-headers` 。


藉由執行下列動作，檢查您可以附加到此節點的 Azure 磁片磁碟機數目：

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>後續步驟

- 若要使用適用于 Azure 磁片的 CSI 磁片磁碟機，請參閱 [使用具有 CSI 驅動程式的 azure 磁片](azure-disk-csi.md)。
- 若要使用 CSI 磁片磁碟機進行 Azure 檔案儲存體，請參閱 [使用 Azure 檔案儲存體搭配 CSI 驅動程式](azure-files-csi.md)。
- 如需儲存體最佳作法的詳細資訊，請參閱 [Azure Kubernetes Service 中儲存和備份的最佳作法][operator-best-practices-storage]。

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true