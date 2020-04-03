---
title: 在 Azure Kubernetes Service (AKS) 中以動態方式建立和使用 Azure 磁碟的永續性磁碟區
description: 瞭解如何在 Azure 庫伯奈斯服務 (AKS) 中使用 Azure 磁碟動態創建持久卷
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: f16a6134b1d2065668952ea11c0cab7398a3559a
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617478"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中以動態方式建立和使用 Azure 磁碟的永續性磁碟區

永續性磁碟區代表一塊已佈建來與 Kubernetes Pod 搭配使用的儲存體。 永續性磁碟區可供一個或多個 Pod 使用，且可以動態或靜態方式佈建。 本文示範如何在 Azure Kubernetes Service (AKS) 叢集中以動態方式建立 Azure 磁碟的永續性磁碟區，以供單一 Pod 使用。

> [!NOTE]
> 您只能使用*存取模式*類型 *ReadWriteOnce* 來掛接 Azure 磁碟，以讓它僅供 AKS 中單一 Pod 使用。 如果您需要在多個 Pod 之間共用永續性磁碟區，請使用 [Azure 檔案服務][azure-files-pvc]。

有關 Kubernets 卷的詳細資訊,請參閱[AKS 中應用程式的儲存選項][concepts-storage]。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您還需要 Azure CLI 版本 2.0.59 或更高版本安裝和配置。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="built-in-storage-classes"></a>內建儲存類

儲存體類別可用來定義如何搭配永續性磁碟區動態建立儲存體單位。 如需有關 Kubernetes 儲存體類別的詳細資訊，請參閱 [Kubernetes 儲存體類別][kubernetes-storage-classes]。

每個 AKS 叢集都包含兩個預先建立的儲存體類別，而且這兩個類別都設定為可搭配 Azure 磁碟使用：

* *default* 儲存體類別會佈建標準 Azure 磁碟。
    * 標準存儲由 HDD 支援,在仍可執行的同時提供經濟高效的存儲。 標準磁碟是經濟高效的開發工作和測試工作負載的理想選擇。
* *managed-premium* 儲存體類別會佈建進階 Azure 磁碟。
    * 進階磁碟是以 SSD 為基礎的高效能、低延遲磁碟為後盾。 最適合用於執行生產工作負載的 VM。 如果您叢集內的 AKS 節點使用進階儲存體，請選取 *managed-premium* 類別。
    
這些預設存儲類不允許在創建後更新卷大小。 要啟用此功能,請將*allowVolume 擴展:true*行添加到預設儲存類之一,或創建自己的自定義存儲類。 可以使用 指令編輯現有儲存`kubectl edit sc`類別 。 有關儲存類和創建自己的類的詳細資訊,請參閱[AKS 中應用程式的儲存選項][storage-class-concepts]。

使用 [kubectl get sc][kubectl-get] 命令來查看預先建立的儲存體類別。 以下範例顯示 AKS 叢集中可用的預先建立儲存體類別：

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> GiB 中會指定持續性磁碟區宣告，但 SKU 會針對特定的大小向 Azure 受控磁碟收費。 這些 SKU 範圍從 S4 或 P4 磁碟的 32GiB 到 S80 或 P80 磁碟的 32TiB(預覽版)。 進階受控磁碟的輸送量和 IOPS 效能，取決於 SKU 和 AKS 叢集中節點的執行個體大小。 如需詳細資訊，請參閱[受控磁碟的定價和效能][managed-disk-pricing-performance]。

## <a name="create-a-persistent-volume-claim"></a>建立永續性磁碟區宣告

永續性磁碟區宣告 (PVC) 可用來根據儲存體類別，動態佈建儲存體。 在此情況下，PVC 可以使用其中一個預先建立的儲存體類別，來建立標準或進階 Azure 受控磁碟。

建立名為 `azure-premium.yaml` 的檔案，然後將下列資訊清單複製進來。 宣告要求名為 `azure-managed-disk` 的磁碟，其大小為 *5 GB* 且具備 *ReadWriteOnce* 存取權。 系統會將儲存體類別指定為 *managed-premium* 儲存體類別。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> 若要建立使用標準儲存體的磁碟，請使用 `storageClassName: default` 而非 *managed-premium*。

使用 [kubectl apply][kubectl-apply] 命令來建立並指定您的 *azure-premium.yaml* 檔案：

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>使用永續性磁碟區

建立永續性磁碟區宣告，並成功佈建磁碟之後，就能建立可存取磁碟的 Pod。 下列資訊清單所建立的基本 NGINX Pod，會使用名為 *azure-managed-disk* 的永續性磁碟區宣告，在 `/mnt/azure` 路徑上掛接 Azure 磁碟。 對於 Windows 伺服器容器(目前在 AKS 中預覽),使用 Windows 路徑約定(如 *"D:")* 指定*載入路徑*。

建立名為 `azure-pvc-disk.yaml` 的檔案，然後將下列資訊清單複製進來。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
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
        claimName: azure-managed-disk
```

使用 [kubectl apply][kubectl-apply] 命令建立 Pod，如下列範例所示：

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

您現在已有一個 Azure 磁碟掛接在 `/mnt/azure` 目錄中的執行中 Pod。 此設定會在透過 `kubectl describe pod mypod` 檢查您的 Pod 時顯示，如下列精簡範例所示：

```console
$ kubectl describe pod mypod

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

## <a name="back-up-a-persistent-volume"></a>備份永續性磁碟區

若要備份永續性磁碟區中的資料，請取得該磁碟區之受控磁碟的快照集。 您接著可以使用此快照集來建立已還原的磁碟並連接至 Pod，以作為還原資料的方式。

首先，使用 `kubectl get pvc` 命令來取得磁碟區名稱，例如，針對名為 *azure-managed-disk* 的 PVC：

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

此磁碟區名稱會形成基礎的 Azure 磁碟名稱。 使用 [az disk list][az-disk-list] 查詢磁碟識別碼，並提供您的 PVC 磁碟區名稱，如下列範例所示：

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

使用磁碟識別碼，利用 [az snapshot create][az-snapshot-create] 來建立快照集磁碟。 下列範例會在與 AKS 叢集相同的資源群組 (*MC_myResourceGroup_myAKSCluster_eastus*) 中，建立名為 *pvcSnapshot* 的快照集。 如果您在 AKS 叢集沒有存取權的資源群組中建立快照集並還原磁碟，則您可能會遇到權限問題。

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

根據您磁碟上的資料量而定，可能需要幾分鐘才能建立快照集。

## <a name="restore-and-use-a-snapshot"></a>還原並使用快照集

若要還原磁碟並將它與 Kubernetes Pod 搭配使用，當您使用 [az disk create][az-disk-create] 建立磁碟時，請使用該快照集作為來源。 如果您接著需要存取原始資料快照集，此作業會保留原始資源。 下列範例會從名為 *pvcSnapshot* 的快照集建立名為 *pvcRestored* 的磁碟：

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

若要將已還原的磁碟與 Pod 搭配使用，請在資訊清單中指定磁碟的識別碼。 使用 [az disk show][az-disk-show] 命令來取得磁碟識別碼。 下列範例會針對上一個步驟中建立的 *pvcRestored* 取得磁碟識別碼：

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

建立名為 `azure-restored.yaml` 的 Pod 資訊清單，並指定在上一個步驟中取得的磁碟 URI。 下列範例會建立基本的 NGINX Web 伺服器，其中已還原的磁碟已在 */mnt/azure* 上掛接為磁碟區：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
    image: nginx:1.15.5
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
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

使用 [kubectl apply][kubectl-apply] 命令建立 Pod，如下列範例所示：

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

您可以使用 `kubectl describe pod mypodrestored` 來檢視 Pod 的詳細資料，例如下列顯示磁碟區資訊的精簡範例：

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>後續步驟

有關相關的最佳實務,請參閱[AKS 中儲存和備份的最佳做法][operator-best-practices-storage]。

深入了解使用 Azure 磁碟的 Kubernetes 永續性磁碟區。

> [!div class="nextstepaction"]
> [Azure 磁碟的庫伯內斯外掛程式][azure-disk-volume]

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
