---
title: 在 Azure Kubernetes Service (AKS) 上啟用 Ultra 磁片支援
description: 瞭解如何在 Azure Kubernetes Service (AKS) 叢集中啟用和設定 Ultra 磁片
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 049c2682a8f61bb658083b0418a4fcf99dc477a5
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900039"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Azure Kubernetes Service (preview 上使用 Azure ultra 磁片) 

[Azure ultra 磁片](../virtual-machines/disks-enable-ultra-ssd.md) 針對具狀態應用程式提供高輸送量、高 IOPS 和一致的低延遲磁片儲存體。 Ultra 磁片的一個主要優點是能夠以動態方式變更 SSD 的效能以及您的工作負載，而不需要重新開機您的代理程式節點。 Ultra 磁片適用于需要大量資料的工作負載。

## <a name="before-you-begin"></a>開始之前

這項功能只能在建立叢集時設定，或在建立節點集區時設定。

> [!IMPORTANT]
> Azure ultra 磁片需要在支援這些磁片的可用性區域和區域中部署 nodepools，以及僅限特定的 VM 系列。 請參閱 [**Ultra 磁片 GA 範圍和限制**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)。

### <a name="register-the-enableultrassd-preview-feature"></a>註冊 `EnableUltraSSD` 預覽功能

若要建立可利用 Ultra 磁片的 AKS 叢集或節點集區，您必須 `EnableUltraSSD` 在訂用帳戶上啟用功能旗標。

`EnableUltraSSD`使用[az feature register][az-feature-register]命令註冊功能旗標，如下列範例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

狀態需要幾分鐘的時間才會顯示「已註冊」  。 您可以使用 [az feature list][az-feature-list] 命令檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

當您準備好時，請使用 [az provider register][az-provider-register]命令重新整理 *>microsoft.containerservice* 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 延伸模組

若要建立可使用 Ultra 磁片的 AKS 叢集或節點集區，您需要最新的 *AKS-preview* CLI 擴充功能。 使用 [az extension add][az-extension-add]命令安裝 *aks-preview* Azure CLI 擴充功能，或使用 [az extension update][az-extension-update]命令安裝任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>限制
- 查看 [ **ULTRA 磁片 GA 範圍和限制**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- Ultra 磁片的支援大小範圍介於100到1500之間

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>建立可使用 Ultra 磁片的新叢集

使用下列 CLI 命令，建立能夠利用 Ultra 磁片的 AKS 叢集。 使用 `--aks-custom-headers` 旗標來設定此 `EnableUltraSSD` 功能。

建立 Azure 資源群組：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

建立支援 Ultra 磁片的 AKS 叢集。

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

如果您想要建立沒有 ultra 磁片支援的叢集，您可以省略自訂參數來進行 `--aks-custom-headers` 。

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>在現有的叢集上啟用 Ultra 磁片

您可以藉由將新的節點集區新增至支援 ultra 磁片的叢集，在現有的叢集上啟用 ultra 磁片。 使用旗標，將新的節點集區設定為使用主機型加密 `--aks-custom-headers` 。

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

如果您想要在不支援 ultra 磁片的情況下建立新的節點集區，您可以省略自訂參數來這麼做 `--aks-custom-headers` 。

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>以儲存體類別動態使用 ultra 磁片

若要在我們的部署或具狀態集合中使用 ultra 磁片，您可以使用 [儲存類別來進行動態](azure-disks-dynamic-pv.md)布建。

### <a name="create-the-storage-class"></a>建立儲存類別

儲存體類別可用來定義如何搭配永續性磁碟區動態建立儲存體單位。 如需有關 Kubernetes 儲存體類別的詳細資訊，請參閱 [Kubernetes 儲存體類別][kubernetes-storage-classes]。

在此情況下，我們將建立參考 ultra 磁片的儲存類別。 建立名為 `azure-ultra-disk-sc.yaml` 的檔案，然後將下列資訊清單複製進來。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

使用 [kubectl apply][kubectl-apply] 命令建立儲存體類別，並指定您的 *yaml* 檔案：

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>建立永續性磁碟區宣告

永續性磁碟區宣告 (PVC) 可用來根據儲存體類別，動態佈建儲存體。 在此情況下，PVC 可以使用先前建立的儲存類別來建立 ultra 磁片。

建立名為 `azure-ultra-disk-pvc.yaml` 的檔案，然後將下列資訊清單複製進來。 宣告會要求名為 `ultra-disk` 的磁片，其大小為 *1000 GB* ，且具有 *>readwriteonce* 存取權。 *Ultra 磁片 sc* 儲存類別會指定為儲存類別。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

使用 [kubectl apply][kubectl-apply] 命令建立永久性磁片區宣告，並指定您的 *azure-yaml* 檔案：

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>使用永續性磁碟區

建立永續性磁碟區宣告，並成功佈建磁碟之後，就能建立可存取磁碟的 Pod。 下列資訊清單所建立的基本 NGINX pod，會使用名為 *ultra 磁片* 的永久性磁片區宣告，將 Azure 磁片掛接在路徑上 `/mnt/azure` 。

建立名為 `nginx-ultra.yaml` 的檔案，然後將下列資訊清單複製進來。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: ultra-disk
```

使用 [kubectl apply][kubectl-apply] 命令建立 Pod，如下列範例所示：

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

您現在已有一個 Azure 磁碟掛接在 `/mnt/azure` 目錄中的執行中 Pod。 此設定會在透過 `kubectl describe pod nginx-ultra` 檢查您的 Pod 時顯示，如下列精簡範例所示：

```console
$ kubectl describe pod nginx-ultra

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```


## <a name="next-steps"></a>下一步

- 如需 ultra 磁片的詳細資訊，請參閱 [使用 Azure ultra 磁片](../virtual-machines/disks-enable-ultra-ssd.md)。
- 如需儲存體最佳作法的詳細資訊，請參閱 [Azure Kubernetes Service (AKS 中儲存體和備份的最佳作法) ][operator-best-practices-storage]

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
[premium-storage]: ../virtual-machines/disks-types.md
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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
